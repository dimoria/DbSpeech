### Гібридні бази даних: поєднання SQL і NoSQL для оптимальних рішень
#### 1. Вступ
Сучасні додатки часто потребують гнучкості NoSQL і структурованості SQL. Гібридний підхід дозволяє поєднати ці дві технології для створення ефективних і масштабованих систем. 

#### 2. Основи SQL і NoSQL
- **SQL (Structured Query Language):** Підтримує реляційні бази даних (PostgreSQL, MySQL) зі строгою схемою та використовується для транзакційних систем. 
- **NoSQL (Not Only SQL):** Пропонує безсхемні рішення (MongoDB, Cassandra), ідеальні для гнучкого зберігання документів, графів або ключ-значень.

#### 3. Коли обрати гібридний підхід?
Гібридні бази даних підходять для:
- Різних типів даних (структурованих та неструктурованих).
- Систем із високим навантаженням, що вимагають масштабування.
- Проектів, де важлива швидкість доступу до певних даних і транзакційна цілісність для інших.
- IoT-рішень, де зберігаються як структуровані параметри пристроїв, так і лог-файли у вигляді документів.
- Аналітичних платформ, що потребують обробки як статистичних, так і динамічних даних.

#### 4. Приклади реалізації гібридного підходу

##### Приклад 1: Інтеграція PostgreSQL (SQL) і MongoDB (NoSQL)

**Сценарій:** Інтернет-магазин із транзакціями (SQL) і динамічними метаданими продуктів (NoSQL).

1. **Збереження транзакцій у PostgreSQL:**
```sql
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    customer_id INT NOT NULL,
    order_date TIMESTAMP DEFAULT NOW()
);

INSERT INTO orders (customer_id) VALUES (1);
```

2. **Збереження метаданих продуктів у MongoDB:**
```python
from pymongo import MongoClient

client = MongoClient("mongodb://localhost:27017/")
db = client["store"]
products = db["products"]

product = {
    "product_id": 101,
    "name": "Smartphone",
    "specs": {
        "color": "black",
        "memory": "128GB"
    }
}
products.insert_one(product)
```

3. **Зв'язок між SQL і NoSQL через Python:**
```python
import psycopg2
from pymongo import MongoClient

# Підключення до PostgreSQL
pg_conn = psycopg2.connect(
    dbname="shop", user="user", password="password", host="localhost"
)
pg_cursor = pg_conn.cursor()
pg_cursor.execute("SELECT id FROM orders WHERE customer_id = %s", (1,))
order_id = pg_cursor.fetchone()[0]

# Підключення до MongoDB
mongo_client = MongoClient("mongodb://localhost:27017/")
products_db = mongo_client["store"]
products = products_db["products"]

product = products.find_one({"product_id": 101})
print(f"Order {order_id} includes product: {product['name']}")
```

##### Приклад 2: Логування та аналітика
**Сценарій:** Веб-додаток з аналітикою в режимі реального часу та транзакціями.

1. **Збереження аналітичних подій у MongoDB:**
```python
analytics = db["analytics"]
event = {
    "user_id": 1,
    "event": "page_view",
    "timestamp": "2025-01-01T10:00:00Z",
    "metadata": {
        "url": "https://example.com/page",
        "referrer": "https://google.com"
    }
}
analytics.insert_one(event)
```

2. **Запит аналітики з MongoDB для бізнес-логіки:**
```python
results = analytics.find({"event": "page_view"})
for result in results:
    print(result)
```

3. **Об'єднання аналітики з SQL-замовленнями:**
```python
pg_cursor.execute("SELECT * FROM orders WHERE customer_id = %s", (1,))
orders = pg_cursor.fetchall()
for order in orders:
    print(order)
```

#### 5. Переваги гібридного підходу
- Гнучкість у роботі з різними типами даних.
- Оптимізація продуктивності для різних запитів.
- Масштабованість NoSQL і надійність транзакцій SQL.
- Простота інтеграції та можливість розширення функціональності.
- Підтримка аналітики в реальному часі разом із традиційними звітами.

#### 6. Висновок
Гібридні бази даних є ідеальним рішенням для проектів, що поєднують різні вимоги до даних. Використання SQL і NoSQL разом забезпечує баланс між структурованістю та гнучкістю, що підходить для сучасних високонавантажених систем. Вони допомагають створювати масштабовані, продуктивні та функціональні платформи для різних бізнес-задач
