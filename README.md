# 📊 Integracja WooCommerce z BigQuery – Google Colab

---

## 🔍 Cel projektu

Ten notebook Jupyter (przystosowany do Google Colab) automatycznie pobiera dane z WooCommerce i zapisuje je w Google BigQuery. Dzięki temu możesz tworzyć interaktywne raporty i dashboardy w Looker Studio.

---

## 🧩 Kluczowe elementy

### Instalacja zależności:
```python
!pip install woocommerce
!pip install google-cloud-bigquery
!pip install simplejson
```

### Autoryzacja WooCommerce:
```python
from woocommerce import API

wcapi = API(
    url="https://twoj-sklep.pl/",
    consumer_key="TWÓJ_KLUCZ_PUBLICZNY",
    consumer_secret="TWÓJ_SEKRETNY_KLUCZ",
    version="wc/v3"
)
```

### Jak uzyskać klucz i sekret WooCommerce:
1. WordPress → WooCommerce → Ustawienia → Zaawansowane → REST API  
2. Kliknij **Dodaj klucz**  
3. Wybierz użytkownika, ustaw uprawnienia **Odczyt/Zapis**  
4. Skopiuj **Consumer Key** i **Consumer Secret**

### Połączenie z BigQuery:
```python
from google.cloud import bigquery

client = bigquery.Client()
```

📚 Dokumentacja WooCommerce API:  
https://woocommerce.github.io/woocommerce-rest-api-docs/

---

## 📈 Looker Studio

Dane przesłane do BigQuery możesz analizować w Looker Studio — umożliwia to budowanie dashboardów, tworzenie wizualizacji, filtrowanie i analizę danych sprzedażowych w czasie rzeczywistym.

---

## 🔄 Kluczowe funkcje

- `format_email(email)` – czyści adresy e-mail  
- `format_phone(phone)` – standaryzuje numery telefonów  
- `hash_sha256(value)` – hashuje dane wrażliwe za pomocą SHA-256, zgodnie z wymogami prywatności i Looker Studio

---

## 🤖 Automatyzacja

Możesz zaplanować automatyczne uruchamianie notebooka raz dziennie:

1. Wejdź na: https://console.cloud.google.com/bigquery  
2. W sekcji **Explorer → Notebooks** wybierz swój notebook  
3. Kliknij **Schedule**  
4. Ustaw:
   - Nazwę harmonogramu  
   - Autoryzację (użytkownik lub konto serwisowe)  
   - Runtime template (lub domyślny)  
   - Cloud Storage bucket (upewnij się, że masz uprawnienia **Storage Admin**)  
   - Częstotliwość (np. codziennie o określonej godzinie)  
5. Kliknij **Create schedule**

📄 Dokumentacja:  
https://cloud.google.com/bigquery/docs/orchestrate-notebooks

---

## 🗂️ Schemat tabeli w BigQuery

Dane są zapisywane w tabeli BigQuery w formacie zagnieżdżonym:

### Główne pola (zamówienie):

- `id`: INTEGER – ID zamówienia  
- `total_netto`: FLOAT – wartość netto  
- `date_created`: TIMESTAMP – data utworzenia  
- `customer_id`: INTEGER – ID klienta  
- `email`: STRING – zhashowany e-mail  
- `phone`: STRING – zhashowany telefon  
- `products`: RECORD (REPEATED) – lista produktów

### Pola w `products` (RECORD):

- `product_id`: INTEGER  
- `product_sku`: STRING  
- `product_name`: STRING  
- `quantity`: INTEGER  
- `price`: FLOAT  
- `subtotal`: FLOAT

🔐 Pola `email` i `phone` są szyfrowane algorytmem SHA-256 (zgodność z RODO i wymaganiami Looker Studio). Dodatkowo są dostosowane do importu jako Customer List dla Google Ads.
