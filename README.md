# Categorizing-Fake-News


Projekt zaczął się jako analiza tekstu artykułów z podziałem na fake news i prawdziwe wiadomości. Notebook przechodzi przez cały proces: od eksploracji danych, przez analizę językową, aż po wytrenowanie modeli klasyfikujących.

Na końcu dodałem funkcję, dzięki której można wkleić link do dowolnego artykułu i sprawdzić, co model o nim sądzi.

## Co znajdziesz w notebooku

Notebook (`CFN.ipynb`) podzielony jest na kilka etapów.

**Analiza danych i języka**

Dane wczytywane są z pliku `fake_news_data.csv`. Na start sprawdzam ile jest artykułów fake, a ile prawdziwych, i robię tagowanie tekstu przez spaCy (części mowy, encje nazwane). Dzięki temu widać jakie słowa i typy encji dominują w jednej i drugiej grupie.

**Czyszczenie tekstu**

Standardowy pipeline: usuwanie nagłówków typu "Miasto - treść", zamiana na małe litery, usuwanie znaków interpunkcyjnych, usuwanie stopwords, tokenizacja i lematyzacja. Wynik trzymany jest w kolumnie `text_clean`.

**Analiza sentymentu**

Do artykułów doklejam wynik z VADER i sprawdzam czy sentyment różni się między fake newsami a prawdziwymi wiadomościami.

**Modelowanie tematów**

LDA i LSA na artykułach z etykietą fake news, żeby zobaczyć jakie tematy się powtarzają.

**Klasyfikacja**

Tekst zamieniany jest na bag of words przez `CountVectorizer`, a potem trenowane są dwa modele: regresja logistyczna i SGD Classifier. Oba testowane są na osobnym zbiorze testowym i sprawdzana jest ich dokładność.

**Sprawdzanie artykułu z linku**

To ostatnia część notebooka. Wklejasz adres URL, kod pobiera treść strony, wyciąga tekst z akapitów, przepuszcza go przez ten sam proces czyszczenia co dane treningowe, a na końcu oba wytrenowane modele dają swoją predykcję: fake news czy prawdziwa wiadomość.

**Douczanie modelu**

Po sprawdzeniu artykułu możesz podać jaka jest jego prawdziwa etykieta. Ten artykuł zostaje wtedy dopisany do `fake_news_data.csv`, a cały zbiór danych przechodzi jeszcze raz przez czyszczenie tekstu. Na tej podstawie budowany jest nowy `CountVectorizer` i od nowa trenowane są oba modele, `lr` i `svm`.

Dzięki temu każdy sprawdzony i potwierdzony artykuł zostaje na stałe w danych treningowych, a model przy kolejnym uruchomieniu korzysta już z powiększonego zbioru. Warto pamiętać, że to retrenowanie nadpisuje istniejące modele i vectorizer, więc kolejne sprawdzenie artykułu korzysta już ze świeżej wersji.

## Wymagania

Do uruchomienia notebooka potrzebujesz:

```
pandas
matplotlib
seaborn
spacy
nltk
vaderSentiment
gensim
scikit-learn
requests
beautifulsoup4
```

Dodatkowo trzeba pobrać model językowy spaCy:

```
python -m spacy download en_core_web_sm
```

oraz zasoby NLTK (stopwords, punkt, wordnet):

```python
import nltk
nltk.download('stopwords')
nltk.download('punkt')
nltk.download('wordnet')
```

## Jak uruchomić

1. Sklonuj repozytorium i zainstaluj zależności z listy powyżej.
2. Upewnij się, że plik `fake_news_data.csv` znajduje się w tym samym folderze co notebook.
3. Otwórz `CFN.ipynb` i uruchom komórki po kolei, od góry do dołu. To ważne, bo dalsze etapy korzystają ze zmiennych i modeli utworzonych wcześniej.
4. Gdy dojdziesz do komórki z `input("Podaj link do artykulu: ")`, wklej adres artykułu, który chcesz sprawdzić.
5. Kilka komórek dalej zobaczysz wynik klasyfikacji z obu modeli.

## Ograniczenia

Model trenowany jest na stosunkowo niewielkim i konkretnym zbiorze danych, więc jego skuteczność na artykułach spoza tego zbioru może się różnić. Funkcja pobierająca treść ze strony bazuje na tagach `<p>`, więc na niektórych stronach może złapać też fragmenty niezwiązane z treścią artykułu, na przykład reklamy czy elementy nawigacji. Traktuj wynik jako wskazówkę, nie jako pewnik.

Niektóre strony blokują automatyczne pobieranie treści i wtedy funkcja może zwrócić pusty tekst albo błąd.
