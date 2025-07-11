# 🟡 SQLite Joins Practice – Homework 10.7

## 🔍 שאילתות SQL

### 🔸 הצג את כל הנוסעים שהשיגו מונית יחד עם פרטי המונית
```sql
SELECT p.name, t.driver_name, t.car_type 
FROM passengers p 
INNER JOIN taxis t ON p.taxi_id = t.id;
```

### 🔸 הצג את כל הנוסעים כולל כאלה שמצאו מונית וכאלה שלא
```sql
SELECT p.name, p.destination, t.driver_name, t.car_type
FROM passengers p
LEFT JOIN taxis t ON p.taxi_id = t.id;
```

### 🔸 הצג רק את הנוסעים שלא מצאו מונית
```sql
SELECT p.*
FROM passengers p
LEFT JOIN taxis t ON p.taxi_id = t.id
WHERE t.id IS NULL;
```

### 🔸 הצג את כל הנוסעים וכל המוניות — נוסעים בלי מונית + נוסעים עם מונית + מונית בלי נוסעים
```sql
SELECT p.*, t.*
FROM passengers p
FULL JOIN taxis t ON p.taxi_id = t.id;
```

### 🔸 הצג את כל הצירופים האפשריים בין נוסעים למוניות
```sql
SELECT p.name AS passenger_name, t.id AS taxi_id, t.car_type AS taxi
FROM passengers p
CROSS JOIN taxis t;
```

---

## 🐍 סקריפט Python מלא
```python
import sqlite3
import os

if os.path.exists("10.7.25copy.db"):
    os.remove("10.7.25copy.db")

conn = sqlite3.connect('10.7.25copy.db')
conn.row_factory = sqlite3.Row
cursor = conn.cursor()

cursor.execute('''
CREATE TABLE IF NOT EXISTS taxis (
    id INTEGER PRIMARY KEY,
    driver_name TEXT NOT NULL,
    car_type TEXT NOT NULL
);
''')

cursor.execute('''
CREATE TABLE IF NOT EXISTS passengers (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    destination TEXT,
    taxi_id INTEGER,
    FOREIGN KEY(taxi_id) REFERENCES taxis(id)
);
''')

data = [
    (1, 'Moshe Levi', 'Van'),
    (2, 'Rina Cohen', 'Sedan'),
    (3, 'David Azulay', 'Minibus'),
    (4, 'Maya Bar', 'Electric'),
    (5, 'Yossi Peretz', 'SUV')
]

cursor.executemany('''
INSERT INTO taxis(id, driver_name, car_type)
VALUES (?, ?, ?);
''', data)

data = [
    (1, 'Tamar', 'Jerusalem', 1),
    (2, 'Eitan', 'Haifa', 2),
    (3, 'Noa', 'Tel Aviv', None),
    (4, 'Lior', 'Eilat', 1),
    (5, 'Dana', 'Beer Sheva', None),
    (6, 'Gil', 'Ashdod', 3),
    (7, 'Moran', 'Netanya', None)
]

cursor.executemany('''
INSERT INTO passengers(id, name, destination, taxi_id)
VALUES (?, ?, ?, ?);
''', data)

print("INNER JOIN: נוסעים שהשיגו מונית יחד עם פרטי המונית")
cursor.execute("""
SELECT p.name, t.driver_name, t.car_type 
FROM passengers p 
INNER JOIN taxis t ON p.taxi_id=t.id
""")
for row in cursor.fetchall():
    print(dict(row))

print("LEFT JOIN: כל הנוסעים כולל כאלה שמצאו מונית וכאלה שלא")
cursor.execute("""
SELECT p.name, p.destination, t.driver_name, t.car_type
FROM passengers p
LEFT JOIN taxis t ON p.taxi_id = t.id;
""")
for row in cursor.fetchall():
    print(dict(row))

print("LEFT OUTER JOIN: רק הנוסעים שלא מצאו מונית")
cursor.execute("""
SELECT p.*
FROM passengers p
LEFT JOIN taxis t ON p.taxi_id = t.id
WHERE t.id IS NULL;
""")
for row in cursor.fetchall():
    print(dict(row))

print("FULL JOIN: נוסעים בלי מונית + נוסעים עם מונית + מונית בלי נוסעים")
cursor.execute("""
SELECT p.*, t.* from passengers p
FULL JOIN taxis t ON p.taxi_id=t.id;
""")
for row in cursor.fetchall():
    print(dict(row))

print("CROSS JOIN: כל הצירופים האפשריים בין נוסעים למוניות")
cursor.execute("""
SELECT p.name AS passenger_name, t.id AS taxi_id, t.car_type AS taxi
FROM passengers p
CROSS JOIN taxis t;
""")
for row in cursor.fetchall():
    print(dict(row))

conn.commit()
conn.close()
```