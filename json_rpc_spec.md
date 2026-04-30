Спецификация JSON-RPC протокола
Версия: JSON-RPC 2.0
Транспорт: HTTPS
Общая структура запроса
{
"jsonrpc": "2.0",
"method": "string",
"params": {},
"id": 1
}

Общая структура ответа (успех)
{
"jsonrpc": "2.0",
"result": {},
"id": 1
}

Общая структура ответа (ошибка)
{
"jsonrpc": "2.0",
"error": {
"code": 400,
"message": "Описание ошибки"
},
"id": 1
}

Коды ошибок
Код	Описание
-32700	Ошибка парсинга JSON
-32600	Невалидный запрос
-32601	Метод не найден
-32602	Неверные параметры
-32603	Внутренняя ошибка
401	Не авторизован
403	Нет прав
МЕТОДЫ API
1. Auth - аутентификация
Запрос
{
"jsonrpc": "2.0",
"method": "Auth",
"params": {
"login": "admin",
"password": "secret"
},
"id": 1
}

Успешный ответ
{
"jsonrpc": "2.0",
"result": {
"token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
"expires_in": 3600,
"user_id": 123
},
"id": 1
}

Ошибка
{
"jsonrpc": "2.0",
"error": {
"code": 401,
"message": "Invalid login or password"
},
"id": 1
}

2. GetMetadata - получение метаданных
Запрос
{
"jsonrpc": "2.0",
"method": "GetMetadata",
"params": {
"token": "jwt_token_123",
"type": "document",
"name": "Invoice"
},
"id": 2
}

Успешный ответ
{
"jsonrpc": "2.0",
"result": {
"metadata": [
{
"type": "document",
"name": "Invoice",
"uuid": "12345678-1234-1234-1234-123456789abc",
"attributes": [
{ "name": "Number", "type": "string" },
{ "name": "Date", "type": "datetime" },
{ "name": "Sum", "type": "number" }
]
}
]
},
"id": 2
}

3. ExecuteTool - выполнение функции
Запрос
{
"jsonrpc": "2.0",
"method": "ExecuteTool",
"params": {
"token": "jwt_token_123",
"tool": "createDocument",
"parameters": {
"documentType": "Invoice",
"fields": {
"Number": "INV-001",
"Sum": 15000
}
}
},
"id": 3
}

Успешный ответ
{
"jsonrpc": "2.0",
"result": {
"documentId": 456,
"status": "created",
"createdAt": "2024-01-15T10:30:00Z"
},
"id": 3
}

Доступные инструменты (tools)
Tool	Описание
createDocument	Создать документ
getDocument	Получить документ
updateDocument	Обновить документ
deleteDocument	Удалить документ
getCatalog	Получить справочник
