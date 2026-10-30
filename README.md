# 🐹 pipelines-go


## 📖 О проекте

Простое Go-приложение (`Sum`, `main`), для которого настроен CI-пайплайн.
При каждом `push` и `pull request` в ветки `main` / `master` автоматически:

- 🧪 запускается линтинг через **golangci-lint**
- ✅ прогоняются тесты (`go test`) с покрытием
- 📦 сохраняется отчёт о покрытии как **артефакт**
- 🐳 собирается **Docker-образ** (без публикации, только на `push` в `main`)

---

## 📂 Структура проекта

```
.
├── .github/
│   └── workflows/
│       └── ci.yml          # GitHub Actions workflow
├── main.go                 # точка входа
├── sum.go                  # функция Sum
├── sum_test.go             # тесты
├── go.mod                  # модуль Go
├── Dockerfile              # multi-stage: golang → alpine
└── README.md
```

---

## ⚙️ Как работает пайплайн

| Шаг | Что делает |
|-----|-----------|
| **Checkout** | Клонирует репозиторий |
| **Setup Go** | Ставит Go **1.22** с кэшем модулей |
| **Download deps** | `go mod download` |
| **Lint** | `golangci-lint` |
| **Test** | `go test -v -coverprofile=coverage.out ./...` |
| **Upload artifact** | Сохраняет `coverage.out` |
| **Docker build** | `docker build -t my-go-app:test .` |

---

## 🐳 Проверка локально

### Сборка образа
<img width="1905" height="825" alt="photo_5413821073230340028_w" src="https://github.com/user-attachments/assets/5db3762e-b6ee-4441-b3f2-46b2dde758a6" />

```bash
docker build -t my-go-app:latest .
```

### Запуск контейнера

```bash
docker run --rm my-go-app:latest
```

Ожидаемый вывод:

```
Hello from Go app!
2 + 3 = 5
```

Зайти внутрь контейнера:

```bash
docker run -it --rm my-go-app:latest /bin/sh
```

Информация об ОС внутри контейнера:

```bash
cat /etc/os-release
```

Выход:

```bash
exit
```

---

## 🧪 Запуск тестов локально

Через Docker (без установки Go):

```bash
docker run --rm -v "$(pwd):/app" -w /app golang:1.22-alpine go test ./...
```

Ожидаемый вывод:

```
ok      my-go-app       0.002s
```

---

## 🏗️ Сборка бинарника локально

```bash
docker run --rm -v "$(pwd):/app" -w /app golang:1.22-alpine go build -o my-app .
docker run --rm -v "$(pwd):/app" -w /app alpine ./my-app
```

Ожидаемый вывод:

```
Hello from Go app!
2 + 3 = 5
```

---

## 🏗️ Стек

- **Go 1.22**
- **golangci-lint** — линтинг
- **go test** — тесты + покрытие
- **Docker** — multi-stage сборка

## 📄 Лицензия

MIT — свободно для учебных целей.
