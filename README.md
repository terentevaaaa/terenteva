![image](https://github.com/user-attachments/assets/466b4f2f-99db-4141-828a-cf065f85451f)
Начала я с установки Oracle Linux с гостевыми дополнениями на виртуальную машину. Образ операционной системы был загружен с официального сайта, выбранная версия — LTS (Long-Term Support), чтобы обеспечить стабильность работы и минимизировать необходимость частых обновлений. Виртуальная машина была настроена с использованием 2 процессоров и 2 ГБ оперативной памяти, что достаточно для выполнения базовых задач.

Первым шагом была установка утилиты Wget, которая позволяет загружать файлы непосредственно из терминала. Это удобно для быстрого скачивания данных без необходимости использования графического интерфейса. Установка была выполнена с использованием команды: Также линукс предупреждает меня о использовании sudo

`sudo yum install wget`

![image](https://github.com/user-attachments/assets/2bcea23a-fd3f-4be9-ad7f-65fc9f028675)


Далее была установлена утилита Curl, которая поддерживает работу с различными протоколами, такими как HTTP, HTTPS, FTP и другими. Это особенно полезно для тестирования API или загрузки данных. Установка была выполнена следующей командой:

`sudo yum install curl`

![image](https://github.com/user-attachments/assets/d6e7e992-929e-422a-8632-142fb8712df8)


Для установки Docker потребовалось добавить официальный репозиторий. Поскольку Oracle Linux основан на CentOS, был использован репозиторий для CentOS. Репозиторий был загружен с помощью команды:

`sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo`

![image](https://github.com/user-attachments/assets/64832806-0930-451b-9aa5-8bd3aaaa8a36)


После добавления репозитория была выполнена установка Docker, включая основные компоненты: docker-ce, docker-ce-cli и containerd.io. Установка была произведена с использованием команды:

`sudo yum install docker-ce docker-ce-cli containerd.io`

![image](https://github.com/user-attachments/assets/09567a9f-cc62-4821-9c4e-91d3a40ad4c0)

Скрин подтверждения установки.

![image](https://github.com/user-attachments/assets/3fb12b25-8396-4f4e-af66-390cc03e3097)

Для автоматического запуска Docker при старте системы была выполнена команда:

`sudo systemctl enable docker --now`

![image](https://github.com/user-attachments/assets/342abf2a-5ac8-46cd-97ba-b6112877ee32)

Теперь Docker готов к работе.

Установка Docker Compose

Для управления несколькими контейнерами был установлен Docker Compose. Сначала была определена последняя версия через API GitHub:

`COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)`

![image](https://github.com/user-attachments/assets/3b0e8d27-98af-44d4-8cd4-a85aa90293b4)

Затем Docker Compose был загружен и установлен с помощью команды:

`sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose`

![image](https://github.com/user-attachments/assets/ea80cf39-bb62-48c4-8ed8-4fbb4702a9b1)

После установки были настроены права на выполнение файла Docker Compose, и была проверена его версия:

`sudo chmod +x /usr/bin/docker-compose`
`docker-compose --version`

![image](https://github.com/user-attachments/assets/9b4c2b56-7d65-4cfe-9d43-5b6bb242edd1)

Проверила права на файл:

`ls -l /usr/bin/docker-compose`

и файл стал исполняемым:

![image](https://github.com/user-attachments/assets/00ddc8f8-6877-4cd2-90c1-842341c52b83)

Права -rwxr-xr-x означают, что файл можно читать, писать и выполнять. 

Установка Grafana через Git 

Для мониторинга была установлена Grafana. Сначала был установлен Git, так как он отсутствовал в системе:

`git clone https://github.com/skl256/grafana_stack_for_docker.git`

![image](https://github.com/user-attachments/assets/36b1fe83-96e4-4d3a-ad17-ce874c471433)

Затем был клонирован репозиторий с необходимыми файлами для настройки Grafana:

`cd grafana_stack_for_docker`

Для организации структуры каталогов были созданы необходимые папки:

`sudo mkdir -p /mnt/common_volume/swarm/grafana/config`

`sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data}`

Права на созданные папки были изменены на текущего пользователя:

`sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana}`

Был создан конфигурационный файл grafana.ini, если он отсутствовал:
`touch /mnt/common_volume/grafana/grafana-config/grafana.ini`

Файлы конфигурации были скопированы в соответствующую директорию:

`cp config/* /mnt/common_volume/swarm/grafana/config/`

`mv grafana.yaml docker-compose.yaml` 

Файл grafana.yaml был переименован в docker-compose.yaml для использования Docker Compose:

`sudo docker compose up -d`

Запуск контейнеров был выполнен в фоновом режиме с помощью команды:

Скрин со всеми командами

![image](https://github.com/user-attachments/assets/a84f698f-563c-4b18-b7f5-882bfb40cb16)

и работа докера композа

результат

![image](https://github.com/user-attachments/assets/f73b1a96-cb59-45f2-884a-91dff6b285f2)

Команда `sudo docker compose up -d` позволяет запустить контейнеры, описанные в файле docker-compose.yml, в фоновом режиме (демонизированном режиме). Ключ -d (от англ. detached) обеспечивает запуск контейнеров без блокировки терминала, что позволяет продолжать работу в командной строке. Это особенно полезно при работе с долго работающими сервисами, такими как веб-серверы или базы данных.

`sudo docker compose up -d`

![image](https://github.com/user-attachments/assets/1097c6b8-62e8-4b21-84af-4b78f4e3b5fd)

Управление контейнерами
Для остановки контейнеров использовалась команда:

`sudo docker compose stop`

![image](https://github.com/user-attachments/assets/e8ac4837-81d3-481e-9c86-d5a07e23f4b0)

Для полного удаления контейнеров, сетей и других ресурсов использовалась команда:

`sudo docker compose down`

![image](https://github.com/user-attachments/assets/4d7136dd-413a-4fea-9737-5b26ff8dd6e9)

Для проверки состояния контейнеров использовалась команда:

`sudo docker compose ps`

![image](https://github.com/user-attachments/assets/de1c2592-2125-4d3c-b937-8ae5b35b634a)

`git clone https://github.com/terentevaaaa/terenteva.git`

Команда `git clone https://github.com/terentevaaaa/terenteva.git` клонирует удалённый репозиторий с GitHub на локальную машину. В результате создаётся папка с именем terenteva, содержащая все файлы и историю изменений из репозитория.

![image](https://github.com/user-attachments/assets/0fd52eaf-2621-4219-be97-123938dee46c)

`pwd`

Текущая рабочая директория была проверена с помощью команды:

![image](https://github.com/user-attachments/assets/f11e1ab4-f663-4fe8-9a89-b988de0bb68b)

Таким образом, были выполнены все необходимые шаги для настройки и запуска Docker, Docker Compose и Grafana на Oracle Linux.

Захожу в графану с данными admin admin

![image](https://github.com/user-attachments/assets/d3fd4a30-0d42-42e5-8b72-4ae373122914)

Попадаю на главную страницу графаны

![image](https://github.com/user-attachments/assets/c0e83f02-c2f5-4339-89eb-6596b56a6d29)

Создаю дэшборд

![image](https://github.com/user-attachments/assets/8afe15e1-4500-42d2-809d-b256362e77ce)

Добавляю визуализацию

![image](https://github.com/user-attachments/assets/b35d14a2-bc4d-4de0-9102-dcab47e7ecba)

Добавляю источник данных и выбираю прометиус

![image](https://github.com/user-attachments/assets/2c4ac338-f208-47b7-9bb4-8b18ab98669d)

Настраиваю аутентификацию и подключение

![image](https://github.com/user-attachments/assets/53694ed8-d712-496a-b5b0-3e3dd2778f07)

Нажимаю сохранить и проверить

![image](https://github.com/user-attachments/assets/84ce107b-c10e-46c7-a2a7-1087c779bdfc)

Импортирую 1860 дэшборд

![image](https://github.com/user-attachments/assets/4f0f7119-8692-4be7-a50a-530ae8166f75)

Готовая графана

![image](https://github.com/user-attachments/assets/2b17744d-3ce5-42d4-a80a-56adfbc842a1)


VICTORIA

Запускаю docker compose вместе с викторией метрикс

![image](https://github.com/user-attachments/assets/c6181b2b-37ac-4cfa-b5c7-10ace035cf61)

Создаю новое соединение

![image](https://github.com/user-attachments/assets/5898e568-7f0c-4d99-87e7-cac4226deb5a)

Выбираю прометиус, вставляю url виктории OILCOINT_metric1

![image](https://github.com/user-attachments/assets/30858e8b-b452-46bb-8357-fe06b33b38f0)

Выбираю режим код, и вставляю параметр 

![image](https://github.com/user-attachments/assets/1e27f89d-47a2-4397-8c7e-8077929cb9aa)

Дальше ввожу в терминал:

Первая команда:

`echo -e "# TYPE OILCOINT_metric1 gauge\nOILCOINT_metric1 0" | curl --data-binary @- http://localhost:8428/api/v1/import/prometheus`

Эта команда выполняет:

Создаёт метрику OILCOINT_metric1 типа gauge со значением 25

Отправляет эти данные на локальный сервер Prometheus через POST-запрос, используя API для импорта метрик в формате Prometheus.

Вторая команда:

`curl -G 'http://localhost:8428/api/v1/query' --data-urlencode 'query=OILCOINT_metric1'`

Эта команда:

Выполняет GET-запрос к API Prometheus

Запрашивает текущее значение метрики OILCOINT_metric1

Использует параметр URL-кодировки для безопасной передачи запроса

![image](https://github.com/user-attachments/assets/7ac201ed-b94f-41f6-9835-a098db2e3dae)

Создаю Dashboard с визуализацией виктории, вставляя параметр:

![image](https://github.com/user-attachments/assets/148c46f6-a9ee-4f92-b035-24e888b34040)

Также в самой виктории

![image](https://github.com/user-attachments/assets/2f774f37-0414-453a-aae4-7565ec92a104)

