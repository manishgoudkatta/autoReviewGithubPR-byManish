# CodeReview by Manish (AI PR Guardian)

[![AI PR Guardian](https://img.shields.io/badge/AI%20PR%20Guardian-Active-success)](https://github.com/your-username/your-repo)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.100.0+-009688?style=flat&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![Python 3.11](https://img.shields.io/badge/Python-3.11-3776AB?style=flat&logo=python&logoColor=white)](https://www.python.org/)

**CodeReview by Manish (AI PR Guardian)** is like having an automated, senior developer reviewing your code 24/7. 

**How it works:**
1. **The Trigger:** Whenever you or someone else opens a "Pull Request" on GitHub, GitHub sends a signal to this application.
2. **The Analysis:** Your application reads the new code changes and hands them over to a highly intelligent AI (using Groq or OpenAI).
3. **The Review:** The AI reads the code, looks for bugs, checks for best practices, and figures out how the code can be improved.
4. **The Feedback:** The application then posts the AI's suggestions and feedback directly back onto the GitHub Pull Request as a comment, exactly like a human teammate would.
5. **The Dashboard:** Finally, it provides a web interface (dashboard) where you can log in and see a history of all the reviews it has done, along with statistics on code quality.

---

## 🚀 Features

- **Automated AI Code Review:** Connect your GitHub repository to have PRs automatically reviewed upon creation, synchronization, or when marked "ready for review".
- **Intelligent Feedback:** Uses advanced large language models (e.g., Llama 3 via Groq ) to provide in-depth code quality checks, identify issues, and suggest improvements.
- **Easy CI/CD Integration:** Simply add the provided GitHub Action workflow to have your repository automatically trigger the AI review endpoint.
- **User Dashboard:** A simple API dashboard serving global code review statistics (total reviews, average score, token usage, issues tracked).
- **Multi-user Support:** Integrate with GitHub OAuth for multi-user login and tracking individualized code review history.
- **Docker Ready:** Comes with a ready-to-use Dockerfile that makes it perfectly optimized for deployments to platforms like Railway, Render, or ECS.

---

## 🛠 Tech Stack

- **Backend Framework:** FastAPI (Python 3.11)
- **Deployment & Containerization:** Docker
- **AI / LLM Integration:** Groqa
- **CI / CD Tooling:** GitHub Actions, Webhooks
- **Authentication:** GitHub OAuth App

---

## 📦 Project Structure

```text
.
├── .env.example             # Example environment variables required
├── .github/
│   └── workflows/
│       └── pr-review.yml    # GitHub Actions Workflow to trigger reviews
├── backend/
│   ├── app/                 # FastAPI Endpoints, Webhooks & Core Engine
│   ├── tests/               # Unit, integration, and webhook testing logic
│   └── requirements.txt     # Python dependencies
├── Dockerfile               # Container setup
└── .gitignore
```

---

## ⚙️ Setup and Installation

### Prerequisites

- Python 3.11+
- A GitHub Personal Access Token (PAT) with `repo` scopes.
- Groq or OpenAI API Key.
- Optional: A registered [GitHub OAuth App](https://github.com/settings/developers) for multi-user authentication.

### Local Initialization

1. **Clone the repository:**
   ```bash
   git clone https://github.com/your-username/your-repo.git
   cd your-repo
   ```

2. **Create a virtual environment and install dependencies:**
   ```bash
   cd backend
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```

3. **Configure Environment Variables:**
   Copy `.env.example` to `.env` in the `backend/` directory and configure your keys.
   ```bash
   cp ../.env.example .env
   ```
   **Important Settings in `.env`:**
   ```env
   GITHUB_TOKEN=ghp_your_personal_access_token
   LLM_PROVIDER=groq # or openai
   GROQ_API_KEY=gsk_your_groq_api_key
   APP_URL=http://localhost:8000
   ```

4. **Run the API server:**
   ```bash
   uvicorn app.main:app --reload
   ```
   The dashboard and API will be available at [http://localhost:8000](http://localhost:8000). To view API documentation, navigate to `http://localhost:8000/docs`.

---

## 🐳 Docker Deployment

The application is fully containerized. You can run it effortlessly using Docker.

```bash
docker build -t ai-pr-guardian .
docker run -p 8000:8000 --env-file backend/.env ai-pr-guardian
```

---

## 🔗 GitHub Actions Integration

You can easily set up GitHub Actions in any child repository to automatically trigger code reviews on Pull Requests. Just drop the `pr-review.yml` from our project into the `.github/workflows/` directory of the target repository.

Make sure to configure the standard GitHub repository secrets on the target repository:
- `REVIEW_API_URL`: The deployed URL (or valid ngrok tunnel) for this application.
- `REVIEW_API_KEY`: Must match the `REVIEW_API_KEY` defined in this server's `.env`.

### Example Target Repository Workflow File
```yaml
name: AI PR Guardian
on:
  pull_request:
    types: [opened, synchronize, ready_for_review]

jobs:
  ai-review:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
      contents: read
    steps:
    - name: Trigger AI Review
      run: |
        curl -X POST '${{ secrets.REVIEW_API_URL }}/webhooks/github/action' \
             -H 'Content-Type: application/json' \
             -H 'X-Api-Key: ${{ secrets.REVIEW_API_KEY }}' \
             -d '{
               "repo": "${{ github.repository }}",
               "pr_number": ${{ github.event.pull_request.number }},
               "head_sha": "${{ github.event.pull_request.head.sha }}"
             }'
```


