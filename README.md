# Capitalizing Equality — Gender Representation in Business Journalism (NLP Analysis)

A web-scraping and natural-language-processing pipeline that investigates whether male and female business figures are portrayed differently in journalism, by scraping full-text articles, engineering linguistic/sentiment features, and visualizing patterns across gender. Built as a research project proposal and analysis combining ethical web scraping, NLTK/TextBlob-based text processing, and exploratory data analysis to test for evidence of gendered bias in tone, language, and representation.

## Contents

| File | Description |
|---|---|
| `project_.ipynb` | Full pipeline — project proposal/background, web scraping, text cleaning, feature engineering, exploratory data analysis, and conclusion. |
| `URLs.csv` | Input list of four source article URLs (two profiling men, two profiling women) from Forbes, Channel NewsAsia, Britannica, and The Guardian. |

## How It Works

### 1. Aims, Objectives & Background

The project examines whether gender influences how business figures are portrayed in journalism, on the premise that media framing of gender can reveal or reinforce societal biases. It targets speaking/tone measurements (story framing, gendered language use) and content analysis (how each figure is represented), aiming to surface relationships between these measurements and any evidence of gendered bias.

### 2. Data Requirements & Sourcing

Four articles were selected — two profiling men, two profiling women — all well-known business figures, to keep the comparison even and interesting. Sources were deliberately varied for reach and editorial perspective: **The Guardian** (UK's most-read quality news publisher), **Forbes** (leading business/financial news source), **Encyclopaedia Britannica** (trusted reference publisher since 1768), and **CNA/Channel NewsAsia** (Singapore's most trusted news brand). URLs are listed in `URLs.csv`.

### 3. Ethical Considerations

Each source's terms and conditions were reviewed prior to scraping to confirm none prohibit it; all permit personal, non-commercial use of content, which this academic project respects. Risks of the analysis were also considered upfront — the project does not attempt a complete authorial style study of any individual writer, and its aim is to analyze objective textual properties, not to pass judgment on the writers themselves.

### 4. Web Scraping

1. **`getWebpage(url)`** — fetches a page with `requests` and parses it with BeautifulSoup, returning `'error'` if the page isn't accessible (non-200 status).
2. **Per-site extraction functions** — because each site's HTML structure differs, dedicated functions (`getDataFromGurdian`, `getDataFromForbes`, `getDataFromChannelNewsAsia`, `getDataFromBritannica`) locate the title (`<h1>`), byline/author element, and article body paragraphs using site-specific selectors, then concatenate paragraph text into a single string.
3. **URL processing** — `URLs.csv` is read into a DataFrame, and each URL's source website is extracted from its domain (via `urlparse`) to route it to the correct extraction function.
4. **Scraping loop** — iterates over all URLs, calls the appropriate extraction function based on detected website, and assembles the results (URL ID, title, author, text) into a `scrapData` DataFrame.
5. **Sanity checks** — word counts for article text and titles are computed and summarized (`describe()`) to confirm the scrape produced reasonable, non-empty content.

### 5. Data Cleaning & Processing

1. **Editors DataFrame** — a per-author row is created to store each editor's combined text across their article(s), enabling author-level comparison (total words, average words per article, average title length).
2. **Stop word removal** — punctuation is normalized via regex, text is tokenized with NLTK, and English stop words (plus stray punctuation/suffix tokens) are filtered out, both for word-frequency counting and for producing cleaned "Filtered words" columns on both the editors and articles DataFrames.
3. **Lemmatization** — `WordNetLemmatizer` reduces filtered tokens to their base dictionary form (e.g. "building" → "build"), added as "Lemmatized words"/"Lemmatized title words" columns for both editors and articles.

### 6. Feature Engineering

Per-article features computed from the (unfiltered) article text to capture style, readability, and gender-related signals:

- **Average sentence length** — total words ÷ total sentences (NLTK tokenization), as a proxy for text complexity/writing style.
- **Unique word ratio** — unique words ÷ total words, indicating vocabulary diversity.
- **Flesch Reading Ease** — computed via the Textatistic library to quantify readability.
- **Sentiment bias ratio** — ratio of positive to negative sentiment words using NLTK's opinion lexicon; a ratio above 1 indicates more positive than negative sentiment words.
- **Gender-quoted sources ratio** — ratio of male-referencing pronouns (he/his/him) to total gendered pronoun mentions (he/his/him + she/her); a ratio below 0.5 indicates more female-referencing pronoun usage.
- **Subjectivity score** — TextBlob-derived score from 0 (fully objective/factual) to 1 (fully subjective/opinionated).

### 7. Exploratory Data Analysis

A series of visualizations explore the engineered features and their relationships:

- **Word clouds** — per-article and combined, sized by word frequency (using lemmatized text).
- **Word count bar chart** — text length distribution across the four articles.
- **Sentiment distribution bar chart** — counts of articles by sentiment label.
- **Scatter plots** — text length vs. average sentence length; average sentence length vs. Flesch Reading Ease.
- **Correlation heatmap** — pairwise correlations among all numeric features.
- **Pairplot** — relationships among average sentence length, unique word ratio, Flesch Reading Ease, and sentiment bias ratio.
- **Violin plot** — distribution of subjectivity scores across articles.

## Results

No clear or systematic bias was found across the four articles analyzed — coverage of both male and female figures was broadly supportive in tone. Given the small sample size (four articles across four sources), this is a preliminary, exploratory finding rather than a statistically representative conclusion, but it demonstrates a full, ethically-grounded pipeline for scraping and quantitatively comparing gendered media coverage that could be scaled to a larger article set.

## Usage

1. Ensure `URLs.csv` is present in the working directory (or replace with your own list of article URLs from supported sources).
2. Open `project_.ipynb` and run all cells in order.
3. Required Python packages (installed within the notebook): `pandas`, `beautifulsoup4`, `requests`, `nltk`, `textatistic`, `textblob`, `wordcloud`, `matplotlib`, `seaborn`, `plotly`. NLTK corpora (`stopwords`, `punkt`, `wordnet`, `vader_lexicon`, `opinion_lexicon`) are downloaded automatically in the first code cell.
4. To analyze different articles, extend `URLs.csv` with more URLs from a supported source (Guardian, Forbes, CNA, Britannica), or add a new extraction function for an additional source following the existing pattern.
5. Review the printed word-frequency/lemmatization output and generated plots inline in the notebook, and the final `scrapData`/`editors` DataFrames for the full feature set.

## Skills Demonstrated

- Ethical web scraping with `requests` and BeautifulSoup, including terms-of-service review and site-specific HTML parsing
- Text preprocessing: stop word removal, punctuation normalization, tokenization, lemmatization with NLTK
- NLP feature engineering: readability scoring (Textatistic), sentiment/opinion lexicon analysis, subjectivity scoring (TextBlob), custom gendered-pronoun ratio metrics
- Exploratory data analysis and visualization: word clouds, bar charts, scatter plots, correlation heatmaps, pairplots, violin plots (Matplotlib, Seaborn, WordCloud)
- Structured research design: aims/objectives framing, source justification, and explicit ethical risk assessment for a media-bias study
