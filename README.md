# Loop Code Review

Автор кода плохо видит свои ошибки. Loop Code Review отдаёт код новому ревьюеру без истории чата. Ревьюер находит и исправляет баги, неудачную схему данных, лишнюю сложность и мусор до релиза.

## Установка

Отправьте агенту это сообщение.

```text
Установи скилл глобально https://github.com/di-sukharev/loop-code-review-skill
```

## Запуск

Напишите `/loop-code-review` в чате, где агент писал код. В Codex напишите `$loop-code-review`.

## Другие скиллы

- [Code Scout](https://github.com/di-sukharev/code-scout-skill) поручает поиск кода дешёвой модели.
- [Orchestration](https://github.com/di-sukharev/orchestration-skill) поручает чтение и написание кода дешёвой модели.
- [Loop Tasks](https://github.com/di-sukharev/loop-tasks-skill) запускает для каждой задачи нового агента с чистым контекстом.
- [Refactoring](https://github.com/di-sukharev/refactoring-skill) меняет код, только если следующая задача станет проще.

[Инструкция для агента](loop-code-review/SKILL.md) · [Лицензия MIT](LICENSE)
