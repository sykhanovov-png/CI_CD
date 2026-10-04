# Домашнее задание к занятию "`Что такое DevOps. CI/CD`" - `Суханов Олег`

---

### Задание 1

1. Установить Jenkins (без использования Docker).
2. Установить Go на машину с Jenkins.
3. Сделать форк репозитория с материалами задания на GitHub.
4. Создать в Jenkins **Freestyle Project**, подключить к нему репозиторий и выполнить запуск:
   - тестов: `go test .`
   - сборки образа: `docker build .`

## Используемое окружение

| Параметр | Значение |
|---|---|
| Платформа | Yandex Cloud |
| ОС | Ubuntu 20.04 LTS (focal) |
| Виртуальная машина | `jenkins-server` |
| Публичный IP | `93.77.163.130` |
| Java | OpenJDK 21.0.7 |
| Jenkins | 2.4xx (LTS) |
| Go | 1.25.8 linux/amd64 |
| Docker | 26.1.3 |
| Git | 2.25.1 |
| Репозиторий | `https://github.com/sykhanovov-png/sdvps-materials-CICD` |

## Ход выполнения

### 1. Создание виртуальной машины в Yandex Cloud

ВМ создана в консоли Yandex Cloud:

- Образ: **Ubuntu 20.04 LTS**
- 2 vCPU, 2 ГБ RAM, 20 ГБ SSD
- Публичный IP присвоен (используется для доступа к Jenkins)
- В **security group** открыт входящий TCP-порт **22-8080** для веб-интерфейса Jenkins

Подключение к ВМ по SSH:


ssh ubuntu@93.77.163.130

- `Установка Java 21`

Jenkins требует Java 21+.
Выполнено:
`sudo apt update`
`sudo apt install -y fontconfig openjdk-21-jre`
`java -version`


- `Установка Jenkins`

Добавлен актуальный APT-ключ и репозиторий Jenkins:

`sudo mkdir -p /etc/apt/keyrings`

`sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key`

  ![ci_cd1.png](/img/ci_cd1.png)


##### `echo 'deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/' \ | sudo tee/etc/apt/sources.list.d/jenkins.list`

##### `sudo apt update`
##### `sudo apt install -y jenkins`
##### `sudo systemctl start jenkins`
##### `sudo systemctl enable jenkins`
##### `sudo systemctl status jenkins`

![ci_cd2.png](/img/ci_cd2.png)
![ci_cd3.png](/img/ci_cd3.png)

Служба запущена и добавлена в автозагрузку. Jenkins слушает порт 8080 по умолчанию.

Получение начального пароля:

##### `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

### `Первичная настройка Jenkins`

Веб-интерфейс открыт по адресу http://93.77.163.130:8080

Введён начальный пароль, нажата кнопка Install suggested plugins

Дождались установки плагинов (3–5 минут)

Создан администратор (логин, пароль, email)

Нажата кнопка Start using Jenkins

Дополнительно установлены плагины:

-  Git plugin
-  Docker Pipeline


### `Установка Go`

Go установлен через PPA longsleep/golang-backports:

##### `sudo add-apt-repository ppa:longsleep/golang-backports -y`
##### `sudo apt update`
##### `sudo apt install -y golang-go`
##### `go version`

![ci_cd4.png](/img/ci_cd4.png)
![ci_cd5.png](/img/ci_cd5.png)

go version go1.25.8 linux/amd64

Проверено, что Go доступен пользователю jenkins:

##### `sudo -u jenkins bash -c 'which go && go version`

/sr/bin/go
go version go1.25.8 linux/amd64

Поскольку Go установлен системно через PPA, дополнительные настройки PATH не требуются.

### `Установка Docker`

Docker установлен из системного репозитория Ubuntu:

##### `sudo apt install -y docker.io`
##### `sudo systemctl start docker`
##### `sudo systemctl enable docker`

Пользователь jenkins добавлен в группу docker, чтобы сборка могла запускать Docker без sudo:

##### `sudo usermod -aG docker jenkins`
##### `sudo systemctl restart jenkins`

Проверка от имени jenkins:

##### `sudo -u jenkins docker version`

![ci_cd6.png](/img/ci_cd6.png)
![ci_cd7.png](/img/ci_cd7.png)
![ci_cd8.png](/img/ci_cd8.png)
![ci_cd9.png](/img/ci_cd9.png)
![ci_cd10.png](/img/ci_cd10.png)
![ci_cd11.png](/img/ci_cd11.png)
![ci_cd12.png](/img/ci_cd12.png)

### `Создание Freestyle Project в Jenkins`

Создан проект Freestyle project с именем test-CICD.

Шаги создания:

- На главной странице Jenkins — New Item (Новый Item).
-  Имя: test-CICD.

- Тип: Freestyle project (в русской локали — «Создать задачу со свободной конфигурацией»).


Раздел «Общие настройки»:

- Описание: Сборка Go-проекта и Docker-образа (ДЗ по CICD)

- Остальные галочки оставлены по умолчанию.

Раздел «Управление исходным кодом»:

- Тип: Git

- Repository URL: https://github.com/sykhanovov-png/sdvps-materials-CICD.git

- Credentials: - none - (публичный репозиторий)

- Branch Specifier: */main

- Раздел «Triggers»: оставлен пустым (сборка вручную).

- Раздел «Environment»: оставлен пустым.

 -Раздел «Шаги сборки» — добавлен шаг Execute shell (Выполнить shell) со следующим скриптом:

#!/bin/bash
set -e

echo "=== Workspace: $(pwd) ==="
ls -la

echo "=== Go version ==="
go version

echo "=== Running go test ==="
go test .

echo "=== Building Docker image ==="
docker build -t sdvps-go-app:latest .

echo "=== SUCCESS ==="








`При необходимости прикрепитe сюда скриншоты
![Название скриншота 1](ссылка на скриншот 1)`


---

### Задание 2

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6.

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота 2](ссылка на скриншот 2)`


---

### Задание 3

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6.

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`

### Задание 4

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6.

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`
