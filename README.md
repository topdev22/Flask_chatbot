# Flask Chatbot

A pattern-based chatbot built with **Python**, **Flask**, and **TensorFlow/Keras**. It classifies user messages against predefined intents and returns contextual responses through a simple web interface.

---

## Features

- **Intent-based classification** — Neural network maps user input to predefined intent tags
- **Customizable responses** — Define patterns and responses in `intents.json`
- **Name handling** — Recognizes “my name is …” and “hi my name is …” and uses the name in replies
- **Web UI** — Chat interface served by Flask with minimal, responsive styling
- **Optional public URL** — Use ngrok to expose the bot for testing or demos

---

## Tech Stack

| Layer        | Technology        |
|-------------|--------------------|
| Backend     | Flask              |
| ML / Model  | TensorFlow, Keras  |
| NLP         | NLTK (tokenize, WordNet lemmatizer) |
| Frontend    | HTML, CSS, jQuery  |

---

## Project Structure

```
Flask_chatbot/
├── app.py              # Flask app, routes, and chat logic
├── train.py            # Trains the intent model from intents.json
├── intents.json        # Intent definitions (patterns + responses)
├── chatbot_model.h5    # Trained Keras model (created by train.py)
├── words.pkl           # Vocabulary (created by train.py)
├── classes.pkl         # Intent class list (created by train.py)
├── requirements.txt    # Python dependencies
├── templates/
│   └── index.html      # Chat page
└── static/
    ├── style.css       # Chat UI styles
    └── css.css         # Additional styles (optional)
```

---

## Prerequisites

- **Python** 3.7+
- **TensorFlow** (includes Keras in 2.x)
- **Flask**, **NLTK**

---

## Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/<your-username>/Flask_chatbot.git
   cd Flask_chatbot
   ```

2. **Create and activate a virtual environment**

   ```bash
   python -m venv venv
   # Windows
   venv\Scripts\activate
   # Linux / macOS
   source venv/bin/activate
   ```

3. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```

   Or manually:

   ```bash
   pip install tensorflow flask nltk
   ```

4. **Download NLTK data** (required for `train.py`)

   Run once:

   ```bash
   python -c "import nltk; nltk.download('punkt'); nltk.download('wordnet'); nltk.download('omw-1.4')"
   ```

   Or run `train.py` once; it will trigger the same downloads.

---

## Usage

### 1. Train the model

Generates `chatbot_model.h5`, `words.pkl`, and `classes.pkl` from `intents.json`:

```bash
python train.py
```

### 2. Start the Flask server

```bash
python app.py
```

### 3. Open the chatbot

In your browser go to: **http://127.0.0.1:5000/**

---

## Configuration

### Adding or editing intents

Edit `intents.json`. Each intent has:

- **tag** — Unique label (e.g. `greetings`, `thanks`)
- **patterns** — Example user phrases
- **responses** — Possible bot replies (one is chosen at random)
- **context** — Optional (can be left as `[""]`)

Example:

```json
{
  "tag": "custom_topic",
  "patterns": ["example phrase one", "another way to say it"],
  "responses": ["Reply A", "Reply B"],
  "context": [""]
}
```

After changing intents, **retrain** the model:

```bash
python train.py
```

Then restart the Flask app.

### Name placeholder

Responses can use `{n}` for the user’s name when they say “my name is …” or “hi my name is …”:

```json
"responses": ["Nice to meet you {n}. How can I help?"]
```

### Exposing with ngrok (optional)

To get a public URL (e.g. for testing or integrations):

1. Install: `pip install flask-ngrok`
2. In `app.py`, uncomment: `run_with_ngrok(app)`
3. Configure ngrok (e.g. auth token) as per [ngrok docs](https://ngrok.com/docs).

---

## Model Details

- **Architecture** — Feedforward network:
  - Dense(128) → Dropout(0.5) → Dense(64) → Dropout(0.5) → Dense(num_classes, softmax)
- **Input** — Bag-of-words vector from lemmatized, tokenized text (NLTK).
- **Training** — SGD with Nesterov momentum; 200 epochs, batch size 5.
- **Inference** — Predictions below a 0.25 probability threshold are ignored; the highest-scoring intent is used to pick a response from `intents.json`.

---

## License

This project is licensed under the terms in the [LICENSE](LICENSE) file.

---

## Contributing

Contributions are welcome. Please read [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) before submitting issues or pull requests.
