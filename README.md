# Korean PII Safe Annotator

A simple and powerful web application designed to anonymize Personally Identifiable Information (PII) in Korean text. This tool replaces detected PII with placeholder tags (e.g., `<PERSON>`, `<KR_PHONE_NUMBER>`), making the text safe for use in data annotation, logging, or analysis without exposing sensitive user data.

The application is built with Python, Flask, and the Microsoft Presidio framework, leveraging a sophisticated hybrid model for PII detection.

![App Screenshot](https://i.imgur.com/kP12a9G.png)

## Key Features

-   **Simple Web Interface:** A clean, two-panel UI to input raw text and instantly see the anonymized output.
-   **High-Accuracy PII Detection:** Utilizes a hybrid approach combining rule-based logic and a state-of-the-art AI model for comprehensive detection.
-   **Real-time Processing:** Anonymizes text on the fly with an efficient backend.
-   **Efficient Model Handling:** The large AI models are loaded only once on server startup, ensuring fast processing for all subsequent requests.
-   **Ready for Deployment:** Includes a production-ready setup using Gunicorn.

---

## The PII Detection & Replacement Process

This application's strength lies in its **hybrid detection model**, which uses two complementary strategies to identify PII with high accuracy.

### 1. Rule-Based Recognizers (For Structured PII)

We use custom-built, high-precision recognizers for structured data formats that follow predictable patterns. This method is fast, accurate, and essential for data types that an AI model might miss.

| PII Entity | Detection Method | Description |
| :--- | :--- | :--- |
| **KR_RESIDENT_REGISTRATION_NUMBER** | Regex + Checksum Algorithm | Identifies Korean RRNs and validates them using the official checksum algorithm to eliminate false positives. |
| **KR_PHONE_NUMBER** | Enhanced Regex | Detects various common mobile and landline phone number formats, with or without hyphens. |
| **KR_BANK_ACCOUNT_NUMBER** | Generic Regex | Identifies common bank account number formats. Can be extended for specific banks. |
| **EMAIL_ADDRESS** | Presidio's Built-in Regex | Uses a robust, pre-built recognizer for finding email addresses. |

### 2. AI-Based NER Model (For Contextual PII)

For PII that doesn't follow strict patterns (like names), we leverage a pre-trained Natural Language Processing (NLP) model fine-tuned for Korean Named Entity Recognition (NER).

-   **Default Model:** `Leo97/KoELECTRA-small-v3-modu-ner`
-   **Function:** This model reads the text and identifies entities based on context, allowing it to find PII that rule-based methods cannot, such as:
    -   **PERSON (`<PERSON>`)**: Names of people.
    -   **LOCATION (`<LOCATION>`)**: Addresses, cities, countries.
    -   **ORGANIZATION (`<ORGANIZATION>`)**: Company names, government bodies.
    -   And other entities supported by the specific model.

### 3. The Replacement Strategy

After the AnalyzerEngine detects all PII using both methods, the AnonymizerEngine processes the text. The default strategy is **`replace`**, which substitutes the identified PII string with its entity type enclosed in angle brackets (e.g., `홍길동` becomes `<PERSON>`). This preserves the context that a piece of PII was present while completely removing the sensitive data, making it perfect for safe data annotation.

---

## Technology Stack

-   **Backend:** Python 3.9+, Flask
-   **PII Engine:** Microsoft Presidio (Analyzer & Anonymizer)
-   **NLP Models:**
    -   Hugging Face Transformers (`Leo97/KoELECTRA-small-v3-modu-ner`)
    -   spaCy (`ko_core_news_sm`) for Korean tokenization
-   **Frontend:** HTML5, CSS3, Vanilla JavaScript
-   **Deployment:** Gunicorn

---

## Setup and Installation

Follow these steps to get the application running on your local machine.

### 1. Prerequisites

-   Python 3.9 or higher.
-   An internet connection to download the required models.

### 2. Clone the Repository

```bash
git clone <your-repository-url>
cd pii_annotator_app