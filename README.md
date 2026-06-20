# 🎮 Esports News Poster

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Groq API](https://img.shields.io/badge/Groq-Powered-orange)](https://groq.com/)
[![Telegram Bot](https://img.shields.io/badge/Telegram-Bot-blue)](https://core.telegram.org/bots)

**Интеллектуальная система автоматической публикации киберспортивных новостей** с продвинутой дедупликацией, умной фильтрацией и генерацией контента через Groq LLM.

---

## 🎬 Демонстрация

```
🚀 ESPORTS-POSTER
============================================================
📥 Загрузка RSS...
  ✅ HLTV: 15
  ✅ Dot Esports: 20
  ✅ Escorenews: 12
  ✅ Cybersport.ru: 18
📦 Всего: 147

🔍 Фильтрация...
  ✅ PASS (esports=8): Team Spirit wins The International...
  🏢 BUSINESS: New CEO at gaming company...
  📢 PROMO: Subscribe to our newsletter...
  ✅ PASS (esports=6): NAVI announces roster changes...

📊 Итоги фильтрации:
   filtered=87, batch_dup=15, db_dup=23, diversity=4, same_subject=3, passed=15
✅ Кандидатов: 15 из 147

🎯 Топ кандидаты:
  1. [es=8] [HLTV] FaZe Clan wins IEM Katowice...
  2. [es=7] [Dot Esports] New Dota 2 patch notes released...
  3. [es=6] [Cybersport.ru] Обновление баланса в CS2...

📝 Генерация: FaZe Clan wins IEM Katowice...
  🤖 llama-3.3-70b-versatile (попытка 1)
  ✅ [llama-3.3-70b-versatile]: 876 симв.
  📤 Отправка поста...
✅ ОПУБЛИКОВАНО [cs][HLTV]: FaZe Clan wins IEM Katowice...

🏁 Готово!
```

---

## ✨ Ключевые возможности

### 🧠 **Интеллектуальная фильтрация**
- ✅ **Игровые дисциплины**: Dota 2, CS2, Valorant, Overwatch, Fortnite и др.
- ❌ **Автоматическое удаление** корпоративных новостей, промо, вакансий
- 🎯 **Приоритизация** по киберспортивной релевантности
- 🔍 **Детекция промо-контента** (рассылки, вебинары, акции)

### 🔄 **Продвинутая дедупликация**
- 🔗 **URL-нормализация** (удаление UTM-меток, www, протоколов)
- 📊 **6 алгоритмов сравнения**:
  - Точное совпадение URL/заголовков
  - Sequence Matcher (difflib)
  - N-gram similarity (биграммы)
  - Jaccard coefficient (пересечение слов)
  - Entity overlap (ключевые компании/модели)
  - Same-domain similarity (статьи с одного сайта)
- 🧬 **Content hashing** (MD5 первых 300 символов)
- 📝 **Word signature** (отсортированные уникальные слова)

### 🎨 **Умная классификация**
- 🏷️ **Topics**: Dota 2, CS2, Valorant, Esports, Patches, General
- 🎯 **Subjects**: Team Spirit, NAVI, Virtus.pro, FaZe, G2, etc.
- 📅 **Freshness control**: макс. 72 часа с публикации
- 🌈 **Diversity engine**:
  - Не более 2 постов на одну тему в последних 7
  - Блок последовательных постов из одного источника
  - Защита от флуда одной компанией (max 8 часов между постами)

### 🤖 **Генерация через Groq**
- 🔄 **Fallback chain**: llama-3.3-70b → llama-3.1-8b
- 📏 **Умная валидация**:
  - Минимум 450 символов
  - Проверка на "воду" (стоп-фразы)
  - Детекция SKIP от модели
- 🎨 **Адаптивные хештеги** по топику
- 🔗 **Автоматические CTA** + реакции

### 💾 **База данных SQLite**
- 📊 **Две таблицы**: `posted_articles` (успешные) + `rejected_urls` (отклонённые)
- 🚀 **WAL mode** для производительности
- 🗂️ **7 индексов** для быстрого поиска
- 🧹 **Auto-cleanup**: 90 дней retention + удаление экономических постов
- 🔒 **Thread-safe** операции

---

## 🚀 Установка

### Требования
```bash
Python 3.10+
SQLite3 (встроен в Python)
```

### Быстрый старт

```bash
# 1. Клонирование
git clone https://github.com/yourusername/esports-news-poster.git
cd esports-news-poster

# 2. Виртуальное окружение
python3 -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 3. Зависимости
pip install -r requirements.txt

# 4. Переменные окружения
cp .env.example .env
nano .env  # Заполните GROQ_API_KEY, TELEGRAM_BOT_TOKEN, CHANNEL_ID

# 5. Запуск
python telegrambot.py
```

### requirements.txt

```txt
requests>=2.33.0
python-dotenv>=1.0.1
aiohttp>=3.14.0
feedparser>=6.0.11
aiogram>=3.17.0
groq
beautifulsoup4>=4.12.3
lxml>=6.1.0

```

---

## ⚙️ Конфигурация

### Переменные окружения

```bash
# .env файл
GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxx
TELEGRAM_BOT_TOKEN=1234567890:ABCdefGHIjklMNOpqrsTUVwxyz
CHANNEL_ID=@your_channel_id

# Опционально
RETENTION_DAYS=90                  # срок хранения в БД
```

### Настройка Config

```python
class Config:
    # Пороги дедупликации
    title_similarity_threshold = 0.55  # SequenceMatcher
    ngram_similarity_threshold = 0.40  # N-gram
    entity_overlap_threshold = 0.45    # Entity overlap
    jaccard_threshold = 0.50           # Jaccard
    same_domain_similarity = 0.40      # Same domain
    
    # Валидация контента
    min_post_length = 450              # минимум символов
    max_article_age_hours = 72         # макс. возраст новости
    min_esports_score = 2              # минимум киберспорт-релевантности
    
    # Diversity
    diversity_window = 7               # последние N постов
    same_topic_limit = 2               # макс. постов одной темы
    same_subject_hours = 8             # защита от флуда компанией
    
    # Groq
    groq_retries_per_model = 2         # повторы на модель
    groq_base_delay = 2.0              # базовая задержка
```

---

## 📊 Архитектура

### Схема работы

```
┌──────────────┐
│ RSS Feeds    │  (15 источников)
└──────┬───────┘
       │
       ▼
┌──────────────────────┐
│ Async Feed Loader    │  (aiohttp + feedparser)
│ • Рандомные задержки │
│ • Timeout 15s        │
└──────┬───────────────┘
       │
       ▼
┌──────────────────────────────┐
│ Relevance Filter             │
│ • Esports keyword scoring    │
│ • Exclude (crypto, sports)   │
│ • Promo/shopping detection   │
│ • Corporate news filter      │
└──────┬───────────────────────┘
       │
       ▼
┌──────────────────────────────┐
│ Batch Deduplication          │
│ • Set[normalized_titles]     │
│ • Set[word_signatures]       │
│ • Set[content_hashes]        │
└──────┬───────────────────────┘
       │
       ▼
┌──────────────────────────────┐
│ Database Deduplication       │
│ • 6 similarity algorithms    │
│ • Entity extraction          │
│ • Subject freshness check    │
└──────┬───────────────────────┘
       │
       ▼
┌──────────────────────────────┐
│ Diversity Control            │
│ • Topic distribution         │
│ • Source rotation            │
│ • Subject cooldown           │
└──────┬───────────────────────┘
       │
       ▼
┌──────────────────────────────┐
│ Scoring & Sorting            │
│ Score = esports*3 + priority*5 │
│   entities + freshness*2 -   │
│   source_penalty -           │
│   subject_penalty            │
└──────┬───────────────────────┘
       │
       ▼
┌──────────────────────────────┐
│ Generation (Groq)             │
│ • llama-3.3-70b-versatile    │
│ • llama-3.1-8b-instant       │
│ • Retry with backoff         │
└──────┬───────────────────────┘
       │
       ▼
┌──────────────────────────────┐
│ Telegram Posting             │
│ • HTML formatting            │
│ • Adaptive hashtags          │
│ • CTA + source link          │
└──────┬───────────────────────┘
       │
       ▼
┌──────────────────────────────┐
│ Database Commit              │
│ • posted_articles table      │
│ • With topic/subject         │
└──────────────────────────────┘
```

---

## 🗃️ База данных

### Схема таблиц

```sql
CREATE TABLE posted_articles (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    url TEXT NOT NULL,
    norm_url TEXT NOT NULL UNIQUE,       -- нормализованный URL
    domain TEXT NOT NULL,                 -- домен источника
    title TEXT NOT NULL,
    title_normalized TEXT NOT NULL,       -- без знаков препинания
    title_words TEXT,                     -- JSON массив слов
    title_word_signature TEXT,            -- отсортированные слова
    summary TEXT,
    content_hash TEXT,                    -- MD5 первых 300 символов
    entities TEXT,                        -- JSON ключевых компаний
    topic TEXT DEFAULT 'general',         -- llm/image_gen/robotics
    subject TEXT DEFAULT 'other',         -- openai/google/anthropic
    source TEXT,                          -- TechCrunch AI
    posted_date TEXT DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE rejected_urls (
    norm_url TEXT PRIMARY KEY,
    title TEXT,
    reason TEXT,                          -- TITLE_SIM / SAME_SUBJECT
    rejected_at TEXT DEFAULT CURRENT_TIMESTAMP
);

-- Индексы
CREATE INDEX idx_norm_url ON posted_articles(norm_url);
CREATE INDEX idx_content_hash ON posted_articles(content_hash);
CREATE INDEX idx_domain ON posted_articles(domain);
CREATE INDEX idx_posted_date ON posted_articles(posted_date);
CREATE INDEX idx_title_normalized ON posted_articles(title_normalized);
CREATE INDEX idx_title_word_signature ON posted_articles(title_word_signature);
CREATE INDEX idx_subject ON posted_articles(subject);
```

### Примеры запросов

```python
# Проверка дубликата
cursor.execute('SELECT title FROM posted_articles WHERE norm_url = ?', (norm_url,))

# Последние 5 постов
cursor.execute('''
    SELECT title, topic, subject FROM posted_articles 
    ORDER BY posted_date DESC LIMIT 5
''')

# Очистка старых записей
cursor.execute("DELETE FROM posted_articles WHERE posted_date < datetime('now', '-90 days')")
```

---

## 🔬 Алгоритмы дедупликации

### 1. **URL Normalization**

```python
def normalize_url(url: str) -> str:
    # https://www.example.com/article?utm_source=twitter
    # → example.com/article
    
    u = url.lower().replace("https://", "").replace("www.", "")
    if "?" in u:
        base, query = u.split("?", 1)
        params = {k: v for k, v in parse_qs(query).items() 
                  if k not in {'utm_source', 'fbclid', 'ref'}}
        u = f"{base}?{urlencode(params)}" if params else base
    return u.rstrip("/")
```

### 2. **Title Similarity (difflib)**

```python
# "Team Spirit wins The International 2025"
# "Team Spirit takes The International 2025"
# → 0.92 (дубликат при threshold=0.55)

sim = difflib.SequenceMatcher(None, title1.lower(), title2.lower()).ratio()
```

### 3. **N-gram Similarity**

```python
# "Team Spirit wins The International"
# "Team Spirit claims TI victory"
# Биграммы: {"meta releases", "releases llama", "llama 3"}
#          vs {"meta unveils", "unveils llama", "llama 3"}
# Jaccard = 1/5 = 0.20 (не дубликат)

ngrams1 = {"meta releases", "releases llama", "llama 3"}
ngrams2 = {"meta unveils", "unveils llama", "llama 31"}
jaccard = len(ngrams1 & ngrams2) / len(ngrams1 | ngrams2)
```

### 4. **Word Signature**

```python
# "Valorant announces new agent"
# "New Valorant agent announced"
# Signature: "agent announced new valorant"
# → Exact match!

words = get_title_words(title)  # без стоп-слов
signature = ' '.join(sorted(words))
```

### 5. **Entity Overlap**

```python
# "Virtus.pro signs new roster"
# "Virtus.pro announces new lineup for 2025"
# Entities: {openai, microsoft, gpt-4} ∩ {microsoft, openai, gpt-4}
# Overlap = 3/3 = 1.0 (дубликат)

entities1 = extract_entities(text1)  # {openai, microsoft, gpt-4}
entities2 = extract_entities(text2)  # {microsoft, openai, gpt-4}
overlap = len(entities1 & entities2) / min(len(entities1), len(entities2))
```

### 6. **Same Domain Similarity**

```python
# techcrunch.com/article1: "ChatGPT hits 100M users"
# techcrunch.com/article2: "ChatGPT reaches 100 million users"
# Same domain → применяем строгий порог 0.40

if domain1 == domain2:
    threshold = 0.40  # вместо обычных 0.55
```

---

## 🎯 Фильтры контента

### **Esports Relevance Score**

```python
def esports_relevance_score(text: str) -> int:
    score = 0
    
    # Сильные ключевые слова (+2 каждое)
    for kw in ESPORTS_KEYWORDS_STRONG:  # "киберспорт", "dota 2", "cs2"
        if kw in text.lower():
            score += 2
    
    # Слабые ключевые слова (+1 каждое)
    for kw in ESPORTS_KEYWORDS_WEAK:  # "игра", "геймер", "шутер"
        if kw in text.lower():
            score += 1
    
    return score

# Примеры:
# "NAVI wins IEM Katowice 2025" → 4 (navi+2, iem+2)
# "Best laptop deals" → 0 (отклоняется)
```

### **Corporate News Detection**

```python
def is_corporate_news(text: str) -> bool:
    corporate_patterns = [
        "steps down", "resigns", "fired", "laid off",
        "new ceo", "restructuring", "lawsuit", "stock price"
    ]
    
    product_markers = [
        "launch", "release", "announce", "new feature",
        "update", "api", "open source"
    ]
    
    corporate_count = sum(1 for p in corporate_patterns if p in text.lower())
    product_count = sum(1 for m in product_markers if m in text.lower())
    
    # Если корпоративных слов >= 2 и больше, чем продуктовых
    return corporate_count >= 2 and corporate_count > product_count

# ❌ "Meta CEO Mark Zuckerberg steps down"
# ✅ "Meta launches Llama 3.1 with 405B parameters"
```

### **Shopping Content Detection**

```python
def is_shopping_content(text: str) -> bool:
    shopping_patterns = [
        "cheapest price", "on sale", "best deal",
        "drops to $", "where to buy"
    ]
    
    # Если есть цена + продуктовые слова (и мало игровых слов)
    if re.search(r'\$\d+', text) and esports_relevance_score(text) < 4:
        product_words = ["phone", "laptop", "earbuds", "specs"]
        if any(w in text.lower() for w in product_words):
            return True
    
    return False

# ❌ "Gaming chair on sale for $199"
# ✅ "NAVI announces $1M roster changes"
```

### **Promo Content Detection**

```python
def is_promo_content(text: str) -> bool:
    promo_patterns = [
        "newsletter", "subscribe", "sign up",
        "webinar", "free trial", "рассылка"
    ]
    
    # Если >= 2 промо-слов или специфичные фразы в заголовке
    promo_count = sum(1 for p in promo_patterns if p in text.lower())
    if promo_count >= 2:
        return True
    
    title_promos = ["launches newsletter", "new podcast", "подпишитесь"]
    return any(p in text.lower() for p in title_promos)

# ❌ "Subscribe to our esports newsletter"
# ✅ "ESL announces new tournament format"
```

---

## 🤖 Генерация (Groq)

### Промпт-инжиниринг

```python
prompt = f"""Ты — редактор Telegram-канала про киберспорт и видеоигры для аудитории из РФ и СНГ.

НОВОСТЬ:
Заголовок: {article.title}
Содержание: {article.summary[:800]}

ЗАДАЧА: Напиши пост для Telegram-канала.

ФОКУС КАНАЛА — только это:
🟢 Киберспортивные турниры (Dota 2, CS2, Valorant)
🟢 Обновления и патчи
🟢 Трансферы и изменения составов

НЕ ПОДХОДИТ — ответь SKIP:
🔴 Кадровые перестановки
🔴 Корпоративные скандалы
🔴 Финансовые отчёты

СТРУКТУРА:
1. 🔥 Цепляющий заголовок
2. Что нового — конкретика
3. Чем полезно
4. Вывод (1-2 предложения)

ТРЕБОВАНИЯ:
✅ 700-1000 символов
✅ Конкретные цифры/даты
✅ Живой стиль

ЗАПРЕЩЕНО:
❌ "стоит отметить", "интересно, что"
❌ Вода без фактов

ПОСТ:"""
```

### Fallback Chain

```python
GROQ_MODELS = [
    "llama-3.3-70b-versatile",  # Сначала мощная модель
    "llama-3.1-8b-instant",     # Затем быстрая
]

for model in GROQ_MODELS:
    for attempt in range(2):  # 2 попытки на модель
        try:
            resp = groq_client.chat.completions.create(
                model=model,
                temperature=0.7,
                max_tokens=1200,
                messages=[{"role": "user", "content": prompt}]
            )
            text = resp.choices[0].message.content.strip()
            
            # Валидация
            if "SKIP" in text.upper()[:10]:
                return None
            if len(text) < 450:
                break  # Следующая модель
            if has_water_phrases(text):
                continue  # Повтор
            
            return format_post(text, article)
            
        except Exception as e:
            await asyncio.sleep(2.0 * (2 ** attempt))  # Exponential backoff
```

### Адаптивные хештеги

```python
class Topic:
    HASHTAGS = {
        "cs": "#CS2 #CSGO #контрстрайк",
        "image_gen": "#Midjourney #StableDiffusion #ИИАрт",
        "robotics": "#роботы #робототехника",
        "hardware": "#NVIDIA #GPU #чипы",
        "messenger": "#Telegram #мессенджеры #боты",
        "general": "#игры #гейминг #видеоигры"
    }
    
    @staticmethod
    def detect(text: str) -> str:
        t = text.lower()
        if "telegram" in t or "durov" in t:
            return "messenger"
        if "gpt" in t or "chatgpt" in t:
            return "llm"
        if "midjourney" in t or "dall-e" in t:
            return "image_gen"
        # ...
        return "general"
```

---

## 📈 Scoring система

### Алгоритм ранжирования

```python
def score(article: Article) -> float:
    text = f"{article.title} {article.summary}"
    
    # 1. Киберспорт-релевантность (x3)
    esports_score = esports_relevance_score(text)
    
    # 2. Приоритет (Telegram-тематика x5)
    priority_score = priority_score(text)
    
    # 3. Ключевые компании (+1 за каждую)
    entities = extract_entities(text)
    
    # 4. Свежесть (0-2 баллов)
    age_hours = (now - article.published).total_seconds() / 3600
    freshness = max(0, 72 - age_hours) / 72 * 2
    
    # 5. Штрафы
    source_penalty = count_same_source(article.source) * 3
    subject_penalty = count_same_subject(detect_subject(text)) * 2
    
    return (esports_score * 3 + priority_score * 5 + len(entities) + 
            freshness - source_penalty - subject_penalty)
```

### Примеры скоринга

```python
# "NAVI wins IEM Katowice 2025"
# es=10 (navi, iem, katowice, турнир, финал), entities=2,
# freshness=1.5, penalties=0
# Score = 10*3 + 0 + 2 + 1.5 = 33.5 ⭐⭐⭐

# "Dota 2 patch 7.38 — balance changes and new hero"
# es=8 (dota 2, патч, баланс, герой), entities=1,
# freshness=2, penalties=0
# Score = 8*3 + 0 + 1 + 2 = 27 ⭐⭐

# "New gaming laptop with RTX 5090"
# es=0, priority=0, entities=1, freshness=0, penalties=0
# Score = 0 + 0 + 1 + 0 = 1 ❌ (отклоняется)
```

---

## 🔧 Расширенные настройки

### Добавление RSS-источника

```python
RSS_FEEDS = [
    # ...существующие...
    
    # Новый источник
    ("https://example.com/esports/feed/", "Example Esports Blog"),
]
```

### Создание нового фильтра

```python
# В списке HARD_EXCLUDE_KEYWORDS
HARD_EXCLUDE_KEYWORDS = [
    # ...
    "quantum computing",  # Исключить неигровое
]

# Или создать функцию
def is_quantum_news(text: str) -> bool:
    quantum_keywords = ["quantum", "qubit", "superposition"]
    return sum(1 for kw in quantum_keywords if kw in text.lower()) >= 2

# В is_relevant():
if is_quantum_news(text):
    logger.info(f"  🔬 QUANTUM: {article.title[:50]}")
    return False
```

### Настройка diversity

```python
class Config:
    # Разрешить больше постов одной темы
    same_topic_limit = 3  # вместо 2
    
    # Уменьшить окно для разнообразия
    diversity_window = 5  # вместо 7
    
    # Увеличить cooldown для компаний
    same_subject_hours = 12  # вместо 8
```

---

## 🚀 Деплой

### GitHub Actions (ежедневная публикация)

```yaml
# .github/workflows/post.yml
name: Esports News Poster

on:
  schedule:
    - cron: '0 */6 * * *'  # Каждые 6 часов
  workflow_dispatch:

jobs:
  post:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      
      - name: Install dependencies
        run: |
          pip install -r requirements.txt
      
      - name: Run poster
        env:
          GROQ_API_KEY: ${{ secrets.GROQ_API_KEY }}
          TELEGRAM_BOT_TOKEN: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          CHANNEL_ID: ${{ secrets.CHANNEL_ID }}
        run: python telegrambot.py
      
      - name: Upload DB
        uses: actions/upload-artifact@v3
        with:
          name: database
          path: posted_articles.db
```

### Docker

```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Зависимости
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Код
COPY telegrambot.py .

# База данных (volume)
VOLUME /app/data

CMD ["python", "telegrambot.py"]
```

```bash
# Сборка
docker build -t esports-poster .

# Запуск
docker run -d \
  --name esports-poster \
  -v $(pwd)/data:/app/data \
  -e GROQ_API_KEY=xxx \
  -e TELEGRAM_BOT_TOKEN=xxx \
  -e CHANNEL_ID=@channel \
  esports-poster
```

### Systemd Service

```ini
# /etc/systemd/system/esports-poster.service
[Unit]
Description=Esports News Poster
After=network.target

[Service]
Type=simple
User=aiposter
WorkingDirectory=/opt/esports-poster
Environment="GROQ_API_KEY=xxx"
Environment="TELEGRAM_BOT_TOKEN=xxx"
Environment="CHANNEL_ID=@channel"
ExecStart=/usr/bin/python3 telegrambot.py
Restart=on-failure
RestartSec=3600

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable esports-poster
sudo systemctl start esports-poster

# Логи
journalctl -u esports-poster -f
```

### Cron (простой способ)

```bash
# crontab -e
0 */6 * * * cd /opt/esports-poster && /usr/bin/python3 telegrambot.py >> /var/log/esports-poster.log 2>&1
```

---

## 🐛 Troubleshooting

### Проблема: "Все статьи отфильтровываются"

```bash
# Проверка киберспорт-скоринга
python3 << EOF
from esports_poster import esports_relevance_score

text = "Your article title and summary"
score = esports_relevance_score(text)
print(f"Esports Score: {score} (min required: 2)")
EOF

# Временно снизить порог
# В Config:
min_esports_score = 1  # вместо 2
```

### Проблема: "Groq API rate limit"

```python
# Увеличить задержки
class Config:
    groq_retries_per_model = 3  # вместо 2
    groq_base_delay = 5.0       # вместо 2.0

# Или использовать только быструю модель
GROQ_MODELS = ["llama-3.1-8b-instant"]
```

### Проблема: "База данных заблокирована"

```bash
# Проверка WAL mode
sqlite3 posted_articles.db "PRAGMA journal_mode"
# Должно быть: wal

# Если нет:
sqlite3 posted_articles.db "PRAGMA journal_mode=WAL"

# Или удалить lock-файлы
rm posted_articles.db-shm posted_articles.db-wal
```

### Проблема: "Дублируются посты"

```python
# Проверка нормализации
from telegrambot import normalize_url, normalize_title

url1 = "https://www.example.com/article?utm_source=twitter"
url2 = "http://example.com/article"
print(normalize_url(url1) == normalize_url(url2))  # True

# Снизить пороги
class Config:
    title_similarity_threshold = 0.40  # вместо 0.55
    ngram_similarity_threshold = 0.30  # вместо 0.40
```

### Проблема: "Telegram flood wait"

```python
# Добавить задержку перед постингом
await bot.send_message(...)
await asyncio.sleep(5)  # 5 секунд между постами
```

---

## 📚 API Reference

### PostedManager

```python
manager = PostedManager("posted_articles.db")

# Проверка дубликата
result = manager.is_duplicate(url, title, summary)
# → DuplicateCheckResult(is_duplicate=bool, reasons=[...], max_similarity=float)

# Проверка свежести темы
ok, reason = manager.check_subject_freshness("navi", title)
# → (False, "SAME_SUBJECT (openai, 4h ago): Previous title...")

# Проверка разнообразия
ok, reason = manager.check_diversity("cs", "HLTV")
# → (False, "SAME_AS_LAST: llm")

# Добавление статьи
success = manager.add(article, topic="dota", subject="team_spirit")
# → True

# Статистика
stats = manager.get_stats()
# → {'total_posted': 245, 'total_rejected': 1523}

# Очистка
manager.cleanup(days=90)

# Закрытие
manager.close()
```

### Utility Functions

```python
# URL
normalize_url("https://www.example.com/article?utm_source=twitter")
# → "example.com/article"

get_domain("https://techcrunch.com/2024/article")
# → "techcrunch.com"

# Title
normalize_title("Team Spirit Wins The International 2025!")
# → "team spirit wins the international 2025"

get_title_words("NAVI wins IEM Katowice 2025")
# → frozenset({'navi', 'wins', 'iem', 'katowice', '2025'})

get_sorted_word_signature("NAVI wins IEM Katowice")
# → "iem katowice navi wins"

# Similarity
calculate_similarity("FaZe wins IEM", "FaZe takes IEM title")
# → 0.85

jaccard_similarity({'dota', 'patch'}, {'dota', 'update'})
# → 0.33

ngram_similarity("Team Spirit wins TI", "Team Spirit claims TI trophy")
# → 0.50

# Entities
extract_entities("Virtus.pro signs new Dota 2 roster")
# → {'virtus.pro', 'dota 2'}

# Scoring
esports_relevance_score("NAVI wins IEM Katowice 2025")
# → 10

priority_score("New CS2 patch notes released")
# → 0

# Topics
Topic.detect("Team Spirit wins The International 2025")
# → "dota"

detect_subject("NAVI announces roster changes")
# → "navi"
```

---

## 📊 Метрики и мониторинг

### Логирование

```python
# block_esports_poster.log
2025-06-20 14:30:00 | INFO | 📥 Загрузка RSS...
2025-06-20 14:30:05 | INFO |   ✅ HLTV: 18
2025-06-20 14:30:10 | INFO | 🔍 Фильтрация...
2025-06-20 14:30:11 | INFO |   ✅ PASS (es=8): NAVI wins IEM Katowice...
2025-06-20 14:30:12 | INFO |   🚫 CORPORATE: New CEO at gaming company...
2025-06-20 14:30:30 | INFO | ✅ ОПУБЛИКОВАНО [cs][HLTV]: FaZe Clan wins IEM...
```

### Статистика из БД

```python
# Топ источников
SELECT source, COUNT(*) as cnt 
FROM posted_articles 
GROUP BY source 
ORDER BY cnt DESC 
LIMIT 5;

# Распределение по топикам
SELECT topic, COUNT(*) as cnt 
FROM posted_articles 
GROUP BY topic;

# Посты за последнюю неделю
SELECT COUNT(*) 
FROM posted_articles 
WHERE posted_date >= datetime('now', '-7 days');

# Средний киберспорт-скор отклонённых
-- Требует добавить колонку esports_score в rejected_urls
```

### Prometheus Metrics (опционально)

```python
from prometheus_client import Counter, Histogram, start_http_server

posts_total = Counter('esports_poster_posts_total', 'Total posts published')
duplicates_total = Counter('esports_poster_duplicates_total', 'Duplicates detected')
generation_time = Histogram('esports_poster_generation_seconds', 'Time to generate post')

# В коде
posts_total.inc()
duplicates_total.labels(reason='TITLE_SIM').inc()

with generation_time.time():
    summary = await generate_summary(article)

# Запуск сервера
start_http_server(8000)
```

---

## 🎨 Примеры постов

### Dota 2 Topic

```
🔥 Team Spirit стала чемпионом The International 2025

Team Spirit обыграла Gaimin Gladiators со счётом 3:1 в гранд-финале The International 2025. Ключевые моменты:

• 🏆 Призовой фонд: $15 млн
• 🌟 Yatoro признан MVP турнира
• 💥 Collapse показал легендарный Timbersaw в решающей карте

Team Spirit забирает второй Aegis в своей истории, повторяя успех 2021 года. Состав показал невероятную стабильность на протяжении всего турнира.

🔥 — огонь  |  🗿 — мимо  |  ⚡ — интересно

#Dota2 #дота2 #киберспорт

🔗 Источник
```

### CS2 Topic

```
🔥 NAVI выигрывает IEM Katowice 2025

NAVI разгромила FaZe Clan со счётом 2:0 в финале IEM Katowice 2025. Ключевые моменты:

• 🏆 Третий титул NAVI в сезоне
• 🌟 jL признан MVP турнира
• 💥 Сенсационная игра b1t на Mirage

Польский зал стоя аплодировал новой команде. NAVI показывает лучшую игру в своей истории.

🔥 — огонь  |  🗿 — мимо  |  ⚡ — интересно

#CS2 #CSGO #контрстрайк

🔗 Источник
```

---

## 🤝 Контрибьютинг

### Как добавить новый фильтр

```python
# 1. Создать функцию в секции FILTERS
def is_hardware_news(text: str) -> bool:
    hardware_keywords = ["gpu", "rtx", "processor", "amd", "intel"]
    count = sum(1 for kw in hardware_keywords if kw in text.lower())
    return count >= 3 and not any(kw in text.lower() for kw in ESPORTS_KEYWORDS_STRONG)

# 2. Добавить в is_relevant()
if is_hardware_news(text):
    logger.info(f"  🚫 HARDWARE: {article.title[:50]}")
    return False

# 3. Протестировать
test_article = Article(
    title="New RTX 5090 benchmark results",
    summary="NVIDIA's latest GPU shows 50% improvement...",
    link="https://example.com/test",
    source="Test"
)
assert not is_relevant(test_article)
```

### Pull Request Guidelines

1. **Форк** репозитория
2. **Создать ветку**: `git checkout -b feature/new-filter`
3. **Добавить тесты** для новой функциональности
4. **Обновить README** с примерами
5. **Commit**: `git commit -m "Add hardware news filter"`
6. **Push**: `git push origin feature/new-filter`
7. **Открыть PR** с описанием изменений

---

## 📜 Лицензия

**MIT License**

```
Copyright (c) 2024

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND.
```

---

## 📞 Поддержка

- **GitHub Issues**: [Создать issue](https://github.com/yourusername/esports-news-poster/issues)
- **Telegram**: [@your_username](https://t.me/your_username)
- **Email**: your@email.com

---

## 🌟 Roadmap

- [ ] **Multi-language support** (EN, RU автодетект)
- [ ] **Image generation** для постов (DALL-E/Midjourney)
- [ ] **Webhook mode** вместо polling RSS
- [ ] **Analytics dashboard** (Grafana + Prometheus)
- [ ] **A/B testing** разных промптов
- [ ] **Thread support** для длинных новостей
- [ ] **Voice messages** для Telegram Stories
- [ ] **Integration с Claude API** как альтернатива Groq

---

## 🎓 Обучающие материалы

### Как работает дедупликация?

```python
# Пример пошагового разбора
article1 = "Team Spirit wins The International"
article2 = "Team Spirit wins The International 2025"

# Шаг 1: Title Similarity
sim = difflib.SequenceMatcher(None, 
    normalize_title(article1), 
    normalize_title(article2)
).ratio()
# → 0.92 > threshold (0.55) → ДУБЛИКАТ

# Шаг 2: N-gram
ngrams1 = {"team spirit wins", "spirit wins the", "wins the international"}
ngrams2 = {"team spirit wins", "spirit wins the", "wins the international", "the international 2025"}
jaccard = len(ngrams1 & ngrams2) / len(ngrams1 | ngrams2)
# → 2/3 = 0.67 > threshold (0.40) → ДУБЛИКАТ

# Шаг 3: Entity Overlap
entities1 = {"team spirit", "the international"}
entities2 = {"team spirit", "the international"}
overlap = len(entities1 & entities2) / min(len(entities1), len(entities2))
# → 2/2 = 1.0 > threshold (0.45) → ДУБЛИКАТ
```

### Как добавить поддержку нового языка?

```python
# 1. Расширить стоп-слова
STOP_WORDS_DE = {'der', 'die', 'das', 'und', 'ist', ...}

# 2. Добавить ключевые слова
ESPORTS_KEYWORDS_STRONG_DE = [
    "e-sport", "turnier", "meisterschaft", ...
]

# 3. Создать функцию детекции языка
def detect_language(text: str) -> str:
    if re.search(r'[а-яА-ЯёЁ]', text):
        return 'ru'
    if any(word in text.lower() for word in ['der', 'die', 'das']):
        return 'de'
    return 'en'

# 4. Использовать в normalize_title()
def normalize_title(title: str, lang: str = None) -> str:
    if lang is None:
        lang = detect_language(title)
    
    stop_words = STOP_WORDS_RU if lang == 'ru' else STOP_WORDS_EN
    # ...
```

---

<div align="center">

**Сделано с ❤️ для автоматизации киберспортивных новостей**

[![Star on GitHub](https://img.shields.io/github/stars/yourusername/esports-news-poster?style=social)](https://github.com/yourusername/esports-news-poster)

</div>

---

## 🎬 ASCII Animation (для README.md)

```
     🎮 ESPORTS NEWS POSTER
    ╔════════════════════════╗
    ║  RSS → Esports → Telegram   ║
    ╚════════════════════════╝
    
    [1] 📥 Загрузка RSS
         ├─ HLTV ✅
         ├─ Dot Esports ✅
         └─ Cybersport.ru ✅
    
    [2] 🔍 Фильтрация
         ├─ Esports Score: 8 ✅
         ├─ Promo: ❌
         └─ Corporate: ❌
    
    [3] 🗄️ Дедупликация
         ├─ URL: OK ✅
         ├─ Title: OK ✅
         └─ Entity: OK ✅
    
    [4] 🤖 Генерация (Groq)
         ├─ llama-3.3-70b
         └─ 876 символов ✅
    
    [5] 📤 Публикация
         └─ Telegram ✅
    
    ✅ ОПУБЛИКОВАНО!
```
