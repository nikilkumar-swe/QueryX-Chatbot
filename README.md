# QueryX: AI-Powered Customer Query Summarizer

QueryX is a Spring Boot backend that uses an LLM (via **Spring AI**) to turn long, unstructured customer complaints from food delivery apps into short, easy-to-read summaries of 2–3 lines. Support teams can scan a complaint in seconds instead of reading a full paragraph.

**Live Demo:** [https://queryx-zomato.onrender.com/](https://queryx-zomato.onrender.com/)

> The app is hosted on Render's free tier, so the first request after a period of inactivity may take a few seconds to wake up.

---

## Problem

Customers often write long, emotional, and messy messages when something goes wrong with an order: a missing item, a wrong order, a late delivery. Agents have to read the whole message just to find out what actually happened.

## Solution

QueryX takes the raw complaint, sends it to an LLM with a carefully restricted prompt, and returns a concise 2–3 line summary. Every message and its summary is stored in PostgreSQL, so the full query history can be retrieved later.

**Example**

| Input | Summary |
|---|---|
| "I ordered a paneer wrap and fries last night from the restaurant near my place, the delivery guy was very late and when the order finally came the chutney was missing and the fries were cold. I've ordered from here many times and this never happened before..." | Order arrived late with cold fries and a missing chutney. Customer is a repeat buyer and is unhappy with the experience. |

---

## Features

- **AI summarization:** condenses long customer complaints into 2–3 lines using an LLM through Spring AI.
- **Domain-restricted prompting:** the prompt context limits the model to food-delivery queries. Irrelevant or off-topic input is rejected, which keeps responses reliable and on-topic.
- **Query history:** all customer messages and generated summaries are persisted in PostgreSQL and can be retrieved through the API.
- **Lightweight frontend:** a simple HTML/CSS/JavaScript UI served directly from Spring Boot static resources, so there is no separate frontend deployment.
- **Containerized:** ships with a Dockerfile and is deployed live on Render.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Java |
| Framework | Spring Boot |
| AI Integration | Spring AI (OpenAI) |
| Persistence | Spring Data JPA, PostgreSQL (Neon DB) |
| Frontend | HTML, CSS, JavaScript |
| Containerization | Docker |
| Deployment | Render |

---

## How It Works

```
User (Browser UI)
      │  complaint text
      ▼
Spring Boot REST API
      │
      ├──► Domain-restricted prompt + complaint ──► LLM (Spring AI / OpenAI)
      │                                                   │
      │◄───────────────── 2–3 line summary ◄──────────────┘
      │
      ├──► Save message + summary ──► PostgreSQL (Spring Data JPA)
      ▼
Summary returned to the UI
```

---

## Getting Started

### Prerequisites

- Java 17 or later
- Maven
- A PostgreSQL database (local, or a hosted one such as Neon)
- An OpenAI API key
- Docker (optional, for running the container)

### 1. Clone the repository

```bash
git clone https://github.com/nikilkumar-swe/QueryX-Chatbot.git
cd QueryX-Chatbot
```

### 2. Configure environment variables

Set the following before running the app. Adjust the names if your `application.properties` uses different ones.

| Variable | Description |
|---|---|
| `SPRING_AI_OPENAI_API_KEY` | Your OpenAI API key |
| `SPRING_DATASOURCE_URL` | JDBC URL, e.g. `jdbc:postgresql://<host>:5432/<db>` |
| `SPRING_DATASOURCE_USERNAME` | Database username |
| `SPRING_DATASOURCE_PASSWORD` | Database password |

```bash
# Linux / macOS
export SPRING_AI_OPENAI_API_KEY=your_key_here
export SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/queryx
export SPRING_DATASOURCE_USERNAME=postgres
export SPRING_DATASOURCE_PASSWORD=your_password
```

> Never commit API keys or database credentials to GitHub. Keep them in environment variables.

### 3. Run locally

```bash
mvn spring-boot:run
```

The app starts at `http://localhost:8080`.

### 4. Run with Docker

```bash
docker build -t queryx .
docker run -p 8080:8080 \
  -e SPRING_AI_OPENAI_API_KEY=your_key_here \
  -e SPRING_DATASOURCE_URL=jdbc:postgresql://<host>:5432/<db> \
  -e SPRING_DATASOURCE_USERNAME=<user> \
  -e SPRING_DATASOURCE_PASSWORD=<password> \
  queryx
```

---

## API Endpoints

<!-- Update the paths below to match your controller. -->

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/summarize` | Accepts a customer complaint and returns a 2–3 line summary |
| `GET` | `/api/history` | Returns all stored queries and their summaries |

**Sample request**

```json
{
  "message": "I ordered a paneer wrap and fries, the delivery was late and the chutney was missing."
}
```

**Sample response**

```json
{
  "summary": "Order arrived late and the chutney was missing."
}
```

---

## Project Structure

```
QueryX-Chatbot/
├── src/
│   └── main/
│       ├── java/                 # Controllers, services, repositories, entities
│       └── resources/
│           ├── static/           # HTML, CSS, JavaScript frontend
│           └── application.properties
├── Dockerfile
├── pom.xml
└── README.md
```

---

## Deployment

The application is containerized with Docker and deployed on **Render**. The PostgreSQL database is hosted on **Neon**. Secrets (OpenAI key and database credentials) are configured as environment variables on Render.

---


## Future Improvements

- Sentiment and urgency tagging for each complaint
- Complaint category detection (missing item, wrong order, late delivery)
- Pagination and search for query history
- Rate limiting and basic authentication

---

## Author

**Nikil Kumar**, Java Backend Developer

[GitHub](https://github.com/nikilkumar-swe) · [LinkedIn](https://www.linkedin.com/in/nikilkumar07/) · [Portfolio](https://nikilkumar07.vercel.app/)
