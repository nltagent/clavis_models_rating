# Clavis.to — рейтинг моделей по числу запросов

Автоматически собирает статистику по всем моделям [Clavis.to](https://clavis.to/models)
(суммарное число запросов за последние 30 дней, по данным `total_requests`
из `https://api.clavis.to/catalog/model/{id}`) и раз в день обновляет
таблицу ниже через GitHub Actions.

Полная история по дням — в [`data/history.csv`](data/history.csv).
Снапшоты за каждый день — в [`data/snapshots/`](data/snapshots/).

## Текущий рейтинг

<!-- RATING_TABLE_START -->
_Обновлено: 2026-08-08 (UTC) · моделей в рейтинге: 189_

| # | Модель | Провайдер | Запросов (30 дн.) | Δ к пред. дню |
|---|--------|-----------|-------------------:|--------------:|
| 1 | gemini-3.1-pro-preview | Google | 4 919 | 🔺 +14 |
| 2 | gpt-5.6-sol | OpenAI | 4 459 | 🔺 +42 |
| 3 | gpt-5.6-luna | OpenAI | 2 600 | 🔺 +401 |
| 4 | deepseek-v4-pro | DeepSeek | 2 213 | 🔺 +40 |
| 5 | claude-sonnet-4-6 | Anthropic | 1 680 | 🔻 -3 |
| 6 | gpt-5.5 | OpenAI | 1 646 | 🔻 -78 |
| 7 | deepseek-v4-flash | DeepSeek | 1 539 | 🔻 -4 |
| 8 | gemini-3-flash-preview | Google | 1 262 | 🔻 -117 |
| 9 | gpt-5.6-terra | OpenAI | 1 009 | 0 |
| 10 | claude-opus-4-7 | Anthropic | 994 | 🔺 +7 |
| 11 | gpt-5-mini | OpenAI | 986 | 🔺 +15 |
| 12 | claude-sonnet-5 | Anthropic | 869 | 🔺 +377 |
| 13 | gpt-5.4-mini | OpenAI | 858 | 🔻 -161 |
| 14 | claude-opus-4-8 | Anthropic | 846 | 🔺 +379 |
| 15 | claude-opus-5 | Anthropic | 736 | 0 |
| 16 | gemini-3.6-flash | Google | 691 | 🔺 +15 |
| 17 | gpt-5-nano | OpenAI | 682 | 0 |
| 18 | gemini-3.1-flash-lite | Google | 623 | 🔻 -128 |
| 19 | gpt-5.4 | OpenAI | 413 | 🔻 -22 |
| 20 | text-embedding-3-small | OpenAI | 386 | 0 |
| 21 | kimi-k3 | Moonshot | 336 | 🔺 +46 |
| 22 | gemini-3.5-flash | Google | 290 | 0 |
| 23 | GLM-5.2 | Zhipu | 289 | 🔻 -7 |
| 24 | claude-fable-5 | Anthropic | 177 | 0 |
| 25 | Aion 3.0 | Aion Labs | 173 | 🔺 +1 |
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
