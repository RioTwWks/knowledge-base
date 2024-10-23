# Apache + Nginx

## Настройка Apache и Nginx на одном сервере

Нужно Apache пробросить на другой порт, например 8080:

```bash
echo "Listen 8080" | sudo tee /etc/apache2/ports.conf
```

Настроить UFW:

```bash
sudo ufw allow 8080
```

```bash
sudo ufw allow "Apache Full"
```