# DIY: Ваше собственное облако на базе Kubernetes (часть 1)

### [Главная][1]

## Подготавливаем плацдарм для вашего облака. Проблемы с которыми придётся столкнуться при подготовке и эксплуатации Kubernetes на bare metal и готовый рецепт провижининга инфраструктуры.

### Kubernetes в облаках

Когда вы эксплуатируете облачный Kubernetes, вы не беспокоитесь о persistent вольюмах, облачных балансировщиках, и процессе заказа нод. Всё это за вас делает облачный провайдер, принимая ваши запросы в виде Kubernetes объектов. Другими словами серверная часть от вас полностью скрыта, вам совершенно не хочется знать как именно это реализует облачный провайдер. Это не в вашей зоне ответственности.

![pic1](https://habrastorage.org/r/w1560/getpro/habr/upload_files/c27/bfa/f13/c27bfaf138e941d4aed0a51d239fcbb0.png)

Для вас Kubernetes предлагает удобные абстракции которые работают везде одинаково, и используя которые вы можете задеплоить своё приложение в любой Kubernetes любого облачного провайдера.

В облаке у вас всегда есть несколько отделяемых сущностей: отдельно Kubernetes control-plane, отдельно виртуалки, отдельно тома для хранения данных, отдельно балансировщик. Используя эти сущности вы можете создавать гибкие и высокодинамичные окружения.

Благодаря Kubernetes виртуальные машины теперь воспринимаются только как служебная сущность для утилизации облачных ресурсов. Вы больше не храните данные внутри виртуальных машин. Вы можете в любой момент удалить все ваши виртуальные машины и создать заново. Ваше приложение от этого не сломается. Kubernetes control-plane как содержал информацию о том что должно выполняться в вашем кластере так и продолжит её содержать. Балансировщик как посылал трафик на ваш workload так и продолжит это делать, просто поменяет endpoint для отправки трафика на новую ноду. А ваши данные будут надёжно сохранены в отдельных облачных persistent томах.

Данный подход является базовым при использовании Kubernetes в облаках. Причина по которой облачные провайдеры предлагают его довольно очевидна: чем проще система - тем она стабильнее и именно за этой простотой вы идёте покупать Kubernetes у облачного провайдера.

### Kubernetes на Bare Metal

Использовать Kubernetes в облаках действительно просто и удобно, чего нельзя сказать о bare metal установках. В мире bare metal Kubernetes наоборот становится непосильно сложным. Во первых потому что вся сеть, бэкенд хранилища, облачные балансировщики и прочее теперь запускается не снаружи, а внутри вашего кластера. Такую систему значительно сложнее обновлять и поддерживать в рабочем состоянии.

![pic2](https://habrastorage.org/r/w1560/getpro/habr/upload_files/e35/486/1e3/e354861e38b54ed38a7ca5a123968085.png)

Посудите сами: в облаке для обновления ноды вам достаточно удалить виртуальную машину и создать новую из нового образа. Она добавится в кластер и просто начнёт работать как новая нода. Это очень простой и часто используемый паттерн в мире Kubernetes. Многие перезаказывают себе виртуальные машины каждые несколько минут, просто потому что они могут использовать дешёвые спот-инстансы. Но когда у вас физический сервер вы не можете его просто удалить и пересоздать. Во первых потому что на нём чаще всего запущены какие-то кластерные службы, хранятся данные и процесс его обновления значительно усложнен.

Есть разные подходы в решении этой задачи, начиная от обновления in-place, как это делает **kubeadm**, **kubespray** и **k3s**, так и полноценной автоматизации провижининга физических узлов через **Cluster API** и **Metal3**.

Мне нравится гибридный подход предложенный **Talos Linux**, где вся система у вас описывается одним большим конфигурационным файлом. Большинство параметров этого файла могут примениться без перезагрузки и пересоздания ноды, включая и версию control-plane компонентов Kubernetes. Но в тоже время сохраняется максимальная декларативность свойственная Kubernetes.

Данный подход позволяет минимизировать излишнее влияние на кластерные службы при обновлении bare metal нод. В большинстве случаев вам не придётся мигрировать ваши виртуальные машины и перестраивать кластерную файловую систему при минорных обновлениях.

### Подготовка базы для вашего будущего облака

Итак, предположим вы решили строить ваше собственное облако. Для того чтобы с чего-то начать вам нужен базовый слой. Вам нужно подумать не только о том как вы установите Kubernetes на ваши сервера, но и о том как вы будете его обслуживать и обновлять. Учитывайте тот факт что вам придётся думать о таких вещах как обновление ядра, установке модулей, так и пакетов и патчей безопасности. То есть гораздо больше вещей о которых вам обычно не приходится беспокоиться при использовании готового Kubernetes в облаке.

Вы можете использовать стандартные дистрибутивы вроде **Ubuntu** или **Debian** или специализированные вроде **Flatcar Container Linux**, **Fedora Core** и **Talos Linux**. Каждый из них имеет свои плюсы и минусы.

Мы используем довольно много специфических модулей ядра вроде ZFS и DRBD и OpenvSwitch, потому решили пойти по пути формирования образа системы со всеми необходимыми модулями заранее. В этом случае Talos Linux оказался для нас наиболее удобным. Например такого конфига достаточно чтобы сформировать образ системы со всеми необходимыми модулями ядра:

```js
arch: amd64
platform: metal
secureboot: false
version: v1.6.4
input:
  kernel:
    path: /usr/install/amd64/vmlinuz
  initramfs:
    path: /usr/install/amd64/initramfs.xz
  baseInstaller:
    imageRef: ghcr.io/siderolabs/installer:v1.6.4
  systemExtensions:
    - imageRef: ghcr.io/siderolabs/amd-ucode:20240115
    - imageRef: ghcr.io/siderolabs/amdgpu-firmware:20240115
    - imageRef: ghcr.io/siderolabs/bnx2-bnx2x:20240115
    - imageRef: ghcr.io/siderolabs/i915-ucode:20240115
    - imageRef: ghcr.io/siderolabs/intel-ice-firmware:20240115
    - imageRef: ghcr.io/siderolabs/intel-ucode:20231114
    - imageRef: ghcr.io/siderolabs/qlogic-firmware:20240115
    - imageRef: ghcr.io/siderolabs/drbd:9.2.6-v1.6.4
    - imageRef: ghcr.io/siderolabs/zfs:2.1.14-v1.6.4
output:
  kind: installer
  outFormat: raw
```

Дальше вам достаточно передать его в imager:

```js
cat config.yaml | docker run --rm -i -v /dev:/dev --privileged "ghcr.io/siderolabs/imager:v1.6.4" - 
```

И на выходе вы получите образ со всем необходимым, который сможете использовать для установки Talos Linux на ваши сервера. Этот образ будет содержать в себе все необходимые прошивки и модули ядра.

Но встаёт вопрос, как доставить только что сформированный образ на ваши ноды?

Я довольно давно промышлял идеями PXE-загрузки, например проект **Kubefarm** о котором я [рассказывал](https://youtu.be/FDdPProqE8g) два года назад был полностью построен на использовании данного подхода. Но к сожалению он не отвечает на вопрос как задеплоить ваш самый первый родительский кластер. И сейчас мы подготовили простое решение которое поможет вам это сделать.

По сути, всё что вам нужно сделать - это [запустить временные](https://cozystack.io/docs/get-started/) **DHCP** и **PXE** серверы в docker-контейнерах. Затем ваши ноды загрузятся с вашего образа и вы сможете использовать простой debian-подобный скрипт который поможет вам забутстрапить ваши ноды.

![pic3](https://habrastorage.org/getpro/habr/upload_files/14d/8b2/9c8/14d8b29c8d61266bd0b761845a07d705.gif)

Данный скрипт позволяет вам за пять минут развернуть Kubernetes на bare metal и получить kubeconfig для доступа к нему. Но впереди нас ждёт ещё много нерешённых вопросов.

Исходники скрипта [talos-bootstrap](https://github.com/aenix-io/talos-bootstrap/) доступны на GitHub.

### Доставка системных компонентов

На данном этапе у вас уже есть кластер Kubernetes, способный запускать какие-либо нагрузки. Но этого пока недостаточно чтобы назвать его полнофункциональным. Другими словами, вам нужно настроить сеть и хранилище, а также установить необходимые кластерные расширения, вроде **KubeVirt** которое позволит вам запускать виртуальные машины. Не стоит забывать также о мониторинге и других общесистемных компонентах.

Традиционно эти вопросы решаются путём установки необходимых **Helm чартов** в ваш кластер. Вы можете сделать это и запустив `helm install` локально, но данный подход неудобен когда у вас есть несколько кластеров и вы хотите следить за обновлениями, а также поддерживать их однородность. На самом деле есть куча вариантов сделать это декларативно. Для решения этой задачи я бы советовал обратиться к лучшим практикам GitOps и воспользоваться такими инструментами как **ArgoCD** и **FluxCD**.

Когда ArgoCD удобен больше для ведения разработки, он имеет графический интерфейс и единый центр управления. FluxCD подходит больше для создания дистрибутива. Используя FluxCD вы можете описать какие чарты с какими параметрами должны быть запущены, а также описать их зависимости. Дальше FluxCD всё сделает за вас.

Выполнив однократную установку FluxCD в ваш новый кластер и настроив его соответствующим образом, вы позволите ему автоматически развернуть все необходимое, что автоматически установит все необходимые компоненты и приведёт кластер в ожидаемое состояние. К примеру, после установки **Cozystack** вы получите следующий набор предустановленных Helm-чартов:

|NAMESPACE|NAME|AGE|READY|STATUS|
|---|---|---|---|---|
|cozy-cert-manager|cert-manager|4m1s|True|Release reconciliation succeeded|
|cozy-cert-manager|cert-manager-issuers|4m1s|True|Release reconciliation succeeded|
|cozy-cilium|cilium|4m1s|True|Release reconciliation succeeded|
|cozy-cluster-api|capi-operator|4m1s|True|Release reconciliation succeeded|
|cozy-cluster-api|capi-providers|4m1s|True|Release reconciliation succeeded|
|cozy-dashboard|dashboard|4m1s|True|Release reconciliation succeeded|
|cozy-fluxcd|cozy-fluxcd|4m1s|True|Release reconciliation succeeded|
|cozy-grafana-operator|grafana-operator|4m1s|True|Release reconciliation succeeded|
|cozy-kamaji|kamaji|4m1s|True|Release reconciliation succeeded|
|cozy-kubeovn|kubeovn|4m1s|True|Release reconciliation succeeded|
|cozy-kubevirt-cdi|kubevirt-cdi|4m1s|True|Release reconciliation succeeded|
|cozy-kubevirt-cdi|kubevirt-cdi-operator|4m1s|True|Release reconciliation succeeded|
|cozy-kubevirt|kubevirt|4m1s|True|Release reconciliation succeeded|
|cozy-kubevirt|kubevirt-operator|4m1s|True|Release reconciliation succeeded|
|cozy-linstor|linstor|4m1s|True|Release reconciliation succeeded|
|cozy-linstor|piraeus-operator|4m1s|True|Release reconciliation succeeded|
|cozy-mariadb-operator|mariadb-operator|4m1s|True|Release reconciliation succeeded|
|cozy-metallb|metallb|4m1s|True|Release reconciliation succeeded|
|cozy-monitoring|monitoring|4m1s|True|Release reconciliation succeeded|
|cozy-postgres-operator|postgres-operator|4m1s|True|Release reconciliation succeeded|
|cozy-rabbitmq-operator|rabbitmq-operator|4m1s|True|Release reconciliation succeeded|
|cozy-redis-operator|redis-operator|4m1s|True|Release reconciliation succeeded|
|cozy-telepresence|telepresence|4m1s|True|Release reconciliation succeeded|
|cozy-victoria-metrics-operator|victoria-metrics-operator|4m1s|True|Release reconciliation succeeded|

В итоге вы получаете максимально повторяемое окружение, которое вы можете предоставить кому угодно и быть уверенным что оно работает точно также как и задумывалось. Именно таким подготовленным окружением и является проект **Cozystack**, попробовать который вы можете самостоятельно и абсолютно бесплатно.

[2 часть](/knowledge-base/DevOps/Собственное%20облако%20на%20базе%20Kubernetes.%20Часть%202)

[3 часть](/knowledge-base/DevOps/Собственное%20облако%20на%20базе%20Kubernetes.%20Часть%203)

[Оригинал статьи](https://habr.com/ru/companies/aenix/articles/795791/)

### [Главная][1]

[1]: /knowledge-base/