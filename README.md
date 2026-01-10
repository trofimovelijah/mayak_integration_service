# mayak_integration_service
Работы по анализу нововведений для сервиса интеграций Платформы "Давайте соберёмся"

## 📚 Документация

Полная документация в порядке чтения размещена в директории [`docs/`](./docs/):

- **[Спецификация требований](./docs/01_specifications/SRS.md)**
- **[Системный дизайн](./docs/02_system_design/README.md)**
- **[Моделирование бизнес-процессов Платформы](./docs/03_bpmn/README.md)**
- **[Визуализация реализации требований](/docs/04_ui/README.md)**
- **[Анализ API](./docs/05_api/README.md)**
- **[Требования к данным интеграции](/docs/06_database/README.md)**

## Структура

```bash
├── docs
│   ├── 01_specifications/
│   │   ├── README.md
│   │   └── srs/                        # Спецификация требования
│   ├── 02_system_design/               # Диаграммы, компоненты и связи между ними
│   ├── 03_bpmn/
│   │   ├── as is/                      # BPMN-диаграммы бизнес-процессов as is
│   │   └── to be/                      # BPMN-диаграммы бизнес-процессов to be
│   │   ├── README.md
│   ├── 04_ui/
│   │   └── README.md
│   ├── 05_api/                         # API документация
│   │   ├── api_flow/                   # PUML диаграммы последовательности
│   │   ├── api.md                      # Описание интеграций
│   │   ├── openapi.yaml                # Спецификация OpenAPI
│   │   └── README.md
│   ├── 06_database/                    # Физическая модель и Словарь данных
│   │   ├── ER-model.drawio
│   │   └── README.md
│   └── README.md
├── images/
│   ├── diagrams/
│   │   ├── bpmn/                       # Модели процессов as is + to be
│   │   ├── database/                   # ER-диаграмма БД (3НФ)
│   │   └── SequenceDiagram/            # Диаграммы последовательности
│   ├── system_design/                  # Диаграммы C4
│   └── ui/                             # Макеты веб-интерфейса
├── LICENSE
└── README.md                           # Главный указатель
```

## Как просмотреть

**PUML диаграммы:**
1. Откройте https://www.plantuml.com/plantuml/uml/
2. Загрузите .puml файл
3. Нажмите Submit

**BPMN диаграммы:**
1. Откройте https://bpmn.io/
2. File → Open File → выберите .bpmn
