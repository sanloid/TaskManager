Сервис Taskmanager

Все ниже перечисленные ручки работают на gRPC, необходимо также поднимать gRPC-gateway, который будет принимать http-запросы, конвертировать их в gRPC и обратно

Ручка CreateTasks:

TaskmanagerCreateTasksRequest{
repeated Task Tasks = 0;
}

message Task {
int32 FirstNumber = 0;
int32 SecondNumber = 1;
TaskType TaskType = 2;
}

enum TaskType {
Addition = 0;
Multiplication = 1;
}

TaskmanagerCreateTasksResponse{
string Id = 0;
}

Ручка GetResult:

Идет в базу tasksaver’a и получает результат выполнения задачи.

message TaskmanagerGetResultRequest{
string Id = 0;
}

message TaskmanagerGetResultResponse{
int32 Result = 0;
Task ParentTask = 1;
}

Сервис Additioner

Держит у себя redis как кеш запросов и ответов по ним

Читает сообщения из топика additioner_request вида:

{
int32 FirstNumber
int32 SecondNumber
}

Выполняет умножение чисел и отправляет результат вида:
{
int32 Result = 0;
Task ParentTask = 1;
}
в топик tasksaver_result

Сервис Multiplicationer

Держит у себя redis как кеш запросов и ответов по ним

Читает сообщения из топика multiplicationer_request вида:

{
int32 FirstNumber
int32 SecondNumber
}

Выполняет умножение чисел и отправляет результат вида:
{
int32 Result = 0;
Task ParentTask = 1;
}
в топик tasksaver_result

Сервис Tasksaver

Читает сообщения из топика tasksaver_result вида:

{
int32 Result = 0;
Task ParentTask = 1;
}

и сохраняет их в базу (SQL или noSQL)

Дополнительное задание:

- добавить приоритеты задач (low, medium, high)
- добавить рейтлимит задач (мы не должны закидывать сервисы Additioner и Multiplicationer задачами до усеру)
- добавить метрики
- добавить трейсинг
- добавить swagger для http-ручек Taskmanager
- оптимизировать архитектуру
