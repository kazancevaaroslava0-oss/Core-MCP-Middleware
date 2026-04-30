# Архитектура взаимодействия Core-MCP-Middleware

## Общая схема



 Пользователь ────► LLM-агент  ────►   Python    ────►   1С:       
              
 
## Компоненты системы

### 1. Пользователь
- Инициирует запросы через веб-интерфейс
- Получает ответы от LLM-агента

### 2. LLM-агент
- Обрабатывает запросы пользователя
- Преобразует запрос в JSON-RPC вызов
- Отправляет вызов в Middleware

### 3. Python Middleware (MCP)
- Принимает JSON-RPC запросы
- Проверяет аутентификацию
- Преобразует UUID из Big-Endian в Little-Endian
- Отправляет запросы в 1С

### 4. HTTP-сервис 1С
- Принимает HTTP запросы
- Выполняет бизнес-логику
- Возвращает результат в JSON

## Проблема передачи UUID

### Что такое UUID?
Уникальный 128-битный идентификатор.

### Проблема: разный порядок байт
- 1С использует Little-Endian
- Python и LLM используют Big-Endian

### Решение в Middleware

```python
def convert_uuid_to_1c_format(uuid_str: str) -> str:
    """Преобразует UUID из Big-Endian в Little-Endian"""
    parts = uuid_str.split('-')
    time_low = parts[0]
    time_mid = parts[1]
    time_high = parts[2]
    
    time_low_le = bytes.fromhex(time_low)[::-1].hex()
    time_mid_le = bytes.fromhex(time_mid)[::-1].hex()
    time_high_le = bytes.fromhex(time_high)[::-1].hex()
    
    rest = parts[3:]
    result = f"{time_low_le}-{time_mid_le}-{time_high_le}-{rest[0]}-{rest[1]}"
    return result
Поток данных
Пользователь пишет запрос

LLM-агент формирует JSON-RPC

Middleware преобразует UUID

Middleware отправляет в 1С

1С возвращает результат

Middleware отправляет ответ LLM

LLM отвечает пользователю

Требования безопасности
Прямые SQL-запросы запрещены

Аутентификация через JWT

Только HTTPS

Стек технологий
Python 3.11 + FastAPI

JSON-RPC 2.0

JWT для аутентификации

Docker
