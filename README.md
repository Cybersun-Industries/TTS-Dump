# ss14-tts-api

### запуск в докере
```
docker build . -t ss14-tts-api:latest
docker rm -f ss14tts
docker run -it -d --name ss14tts -p 5000:5000 ss14-tts-api:latest
```

просмотр логов в докере:
```
docker logs ss14tts -f
```

### запуск без docker

windows: https://www.python.org/ftp/python/3.9.13/python-3.9.13-amd64.exe

!!! выше версии 3.9 не работет !!!

```
pip3 install -r ./requirements.txt --extra-index-url https://download.pytorch.org/whl/cu116
python ss14tts.py
```

### Прочие

конфиг:

```
[tts]
api_url="http://127.0.0.1:5000/tts"
api_token="test"
enabled=true
```


файл с бесплатными голосами:
> tts-voices.yml

копирнуть в /Resources/Prototypes/Corvax

### Скрипт быстрого запуска

```
#!/bin/bash

# Клонирование репозитория
repo_url="https://github.com/Rxup/ss14-tts-api.git"
repo_dir="ss14-tts-api"
if [ -d "$repo_dir" ]; then
    rm -rf "$repo_dir"
fi
git clone "$repo_url"

# Создание Docker-образа
docker_image_name="ss14-tts-api"
docker build -t "$docker_image_name" "$repo_dir"

# Переменные окружения
threads=$(nproc)  # Количество ядер процессора
apitoken="YOUR_API_TOKEN"  # Здесь укажите свой секретный ключ

# Запуск Docker-образа с авто-перезагрузкой и публикацией порта 5000
container_name="ss14-tts-api-container"
docker stop "$container_name" >/dev/null 2>&1
docker rm "$container_name" >/dev/null 2>&1
docker run -d \
    --name "$container_name" \
    -p 5000:5000 \
    --restart always \
    -e "threads=$threads" \
    -e "apitoken=$apitoken" \
    "$docker_image_name"

# Получение внешнего IP-адреса
external_ip=$(curl -s ifconfig.me)

# Вывод конфигурационных параметров
echo "[tts]"
echo "api_url=\"http://$external_ip:5000/tts\""
echo "api_token=\"$apitoken\""
echo "enabled=true"
```
