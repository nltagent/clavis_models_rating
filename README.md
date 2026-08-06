# Clavis.to — рейтинг моделей по числу запросов

Автоматически собирает статистику по всем моделям [Clavis.to](https://clavis.to/models)
(суммарное число запросов за последние 30 дней, по данным `total_requests`
из `https://api.clavis.to/catalog/model/{id}`) и раз в день обновляет
таблицу ниже через GitHub Actions.

Полная история по дням — в [`data/history.csv`](data/history.csv).
Снапшоты за каждый день — в [`data/snapshots/`](data/snapshots/).

## Текущий рейтинг

<!-- RATING_TABLE_START -->
_Обновлено: 2026-08-06 (UTC) · моделей в рейтинге: 187_

| # | Модель | Провайдер | Запросов (30 дн.) | Δ к пред. дню |
|---|--------|-----------|-------------------:|--------------:|
| 1 | gemini-3.1-pro-preview | Google | 4 879 | 🔺 +159 |
| 2 | gpt-5.6-sol | OpenAI | 4 417 | 🔺 +25 |
| 3 | deepseek-v4-pro | DeepSeek | 2 173 | 🔺 +22 |
| 4 | gpt-5.6-luna | OpenAI | 2 138 | 🔺 +18 |
| 5 | claude-sonnet-4-6 | Anthropic | 1 684 | 🔻 -4 |
| 6 | deepseek-v4-flash | DeepSeek | 1 542 | 🔺 +38 |
| 7 | gpt-5.5 | OpenAI | 1 431 | 🔻 -34 |
| 8 | gemini-3-flash-preview | Google | 1 411 | 🔻 -89 |
| 9 | gpt-5.4-mini | OpenAI | 1 019 | 🔺 +60 |
| 10 | gpt-5.6-terra | OpenAI | 990 | 🔺 +176 |
| 11 | claude-opus-4-7 | Anthropic | 987 | 0 |
| 12 | gpt-5-mini | OpenAI | 971 | 🔻 -3 |
| 13 | gemini-3.1-flash-lite | Google | 751 | 🔻 -1 |
| 14 | claude-opus-5 | Anthropic | 736 | 0 |
| 15 | gpt-5-nano | OpenAI | 681 | 0 |
| 16 | gemini-3.6-flash | Google | 647 | 🔺 +34 |
| 17 | claude-opus-4-8 | Anthropic | 641 | 🔻 -500 |
| 18 | claude-sonnet-5 | Anthropic | 492 | 🔻 -17 |
| 19 | gpt-5.4 | OpenAI | 463 | 🔻 -63 |
| 20 | text-embedding-3-small | OpenAI | 387 | 🔺 +184 |
| 21 | GLM-5.2 | Zhipu | 296 | 🔺 +264 |
| 22 | gemini-3.5-flash | Google | 290 | 🔻 -6 |
| 23 | kimi-k3 | Moonshot | 290 | 🔺 +272 |
| 24 | gemini-2.5-pro | Google | 238 | 🔻 -1 |
| 25 | claude-fable-5 | Anthropic | 178 | 0 |
<!-- RATING_TABLE_END -->

## Как это работает

1. `.github/workflows/daily-rating.yml` запускается каждый день по расписанию
   (см. cron в файле) и может быть запущен вручную через вкладку **Actions**.
2. `scripts/collect.py` тянет список моделей и статистику запросов из
   публичного API Clavis.to, сохраняет:
   - `data/snapshots/YYYY-MM-DD.csv` — срез за конкретный день;
   - `data/snapshots/errors_YYYY-MM-DD.csv` — модели, по которым не удалось
     получить данные;
   - `data/history.csv` — накопительная история (одна строка на модель на
     день), не перезаписывается, а дополняется.
3. `scripts/update_readme.py` читает `data/history.csv`, строит таблицу
   топ-25 моделей с дельтой к предыдущему дню и вставляет её в этот README
   между маркерами `RATING_TABLE_START` / `RATING_TABLE_END`.
4. Workflow коммитит и пушит изменения (`data/*` и `README.md`) обратно в
   репозиторий от имени `github-actions[bot]`.

## Локальный запуск

```bash
pip install -r requirements.txt
python scripts/collect.py
python scripts/update_readme.py
```

## Настройка расписания

По умолчанию — раз в день в 06:00 UTC. Поменять можно в
`.github/workflows/daily-rating.yml`, поле `cron`. Формат стандартный —
5 полей (минута, час, день месяца, месяц, день недели), время всегда в UTC.
