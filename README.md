# BWT AquaCloud 🌊

## 📌 Problem Statement
Managing and understanding learning content is difficult due to information overload and lack of personalization.

## 💡 Solution Overview
BWT AquaCloud is an AI-powered learning assistant that helps users understand topics in a simple and personalized way.  
It adapts explanations based on the user’s level and generates summaries and practice questions automatically.

## ⚙️ Key Features
- Personalized topic explanations
- AI-generated summaries
- Quiz generation for self-assessment
- Simple and user-friendly interface

## 🏗️ System Architecture
The system follows a client-server architecture where:
- The frontend collects user input (topic and level)
- The backend processes the request
- The AI model generates the response
- The result is displayed to the user



## 🛠️ Tech Stack
- Frontend: HTML, CSS, JavaScript
- Backend: Python (Flask)
- AI Model: OpenAI API

## 🚀 How to Run
1. Clone the repository  
2. Install required libraries  
3. Run the backend server  
4. Open the frontend in browser  

## 📈 Future Enhancements
- User progress tracking  
- Flashcards support  
- Voice-based learning  
- Mobile app version


🎮 LearnQuest

Every concept is a quest. Every student is a hero.

LearnQuest is a gamified AI-powered learning app.
Enter any concept (like CNN, Recursion, etc.) and the AI generates 3 progressive learning levels with:

Simple explanations

Quizzes

XP rewards

📁 Project Structure
learnquest-backend/
│
├── main.py
├── requirements.txt
├── .env.example
├── .gitignore
├── README.md
│
├── models/
│   ├── __init__.py
│   └── schemas.py
│
├── engine/
│   ├── __init__.py
│   └── level_generator.py
│
├── routers/
│   ├── __init__.py
│   └── quest_router.py
│
└── frontend/
    └── index.html
🚀 Setup Instructions
1️⃣ Clone the repository
git clone https://github.com/YOUR_USERNAME/BWT_AquaCloud.git
cd BWT_AquaCloud
2️⃣ Create virtual environment & install dependencies
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
3️⃣ Set API key

Create a .env file (use .env.example as reference):

GROQ_API_KEY=your_groq_api_key_here

Get your API key from:
https://console.groq.com/keys

4️⃣ Run backend
python main.py

Backend runs at:

http://localhost:8000
5️⃣ Open frontend

Open this file in browser:

frontend/index.html
🛠 Tech Stack

Backend: FastAPI (Python)

AI: Groq (LLaMA 3.3 70B)

Frontend: HTML, CSS, JavaScript

🎯 Features

Game-style learning levels

AI-generated explanations

Quiz after each level

XP-based progress

Adaptive learning flow

----------------------------------------------------------------------------------------------------------------------------
CODE:

📄 main.py
python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from routers import quest_router

app = FastAPI(title="LearnQuest API", version="1.0.0")

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

app.include_router(quest_router.router, prefix="/api/v1")

@app.get("/")
def root():
    return {
        "game": "LearnQuest",
        "tagline": "Every concept is a quest. Every student is a hero. 🏆"
    }

if __name__ == "__main__":
    import uvicorn
    uvicorn.run("main:app", host="0.0.0.0", port=8000, reload=True)
📄 requirements.txt
text
fastapi
uvicorn[standard]
groq
python-dotenv
pydantic
📄 .env.example
text
GROQ_API_KEY=your_groq_api_key_here
📄 .gitignore
text
venv/
__pycache__/
*.pyc
.env
*.egg-info/
dist/
.DS_Store
📄 models/schemas.py
python
from pydantic import BaseModel
from typing import List

class QuestRequest(BaseModel):
    concept: str
    student_name: str = "Hero"

class QuizOption(BaseModel):
    label: str
    text: str

class QuizQuestion(BaseModel):
    question: str
    options: List[QuizOption]
    correct: str
    explanation: str

class Level(BaseModel):
    level_number: int
    title: str
    tagline: str
    content: str
    quiz: QuizQuestion
    xp: int

class QuestResponse(BaseModel):
    concept: str
    student_name: str
    levels: List[Level]
📄 models/__init__.py
python
undefined
📄 engine/level_generator.py
python
import os
import json
from groq import Groq
from dotenv import load_dotenv
from models.schemas import Level, QuizQuestion, QuizOption

load_dotenv()

client = Groq(api_key=os.environ.get("GROQ_API_KEY"))

LEVEL_CONFIGS = {
    1: {"name": "Novice",    "xp": 100, "depth": "beginner-friendly, simple analogies"},
    2: {"name": "Explorer",  "xp": 200, "depth": "intermediate, real-world examples"},
    3: {"name": "Master",    "xp": 300, "depth": "advanced, deep technical detail"},
}

def generate_level(concept: str, level_number: int) -> Level:
    config = LEVEL_CONFIGS[level_number]

    prompt = f"""
You are LearnQuest AI — a gamified learning engine.
Generate a learning level for the concept: "{concept}"
Level: {level_number} ({config['name']}) — {config['depth']}

Respond ONLY in this exact JSON format:
{{
  "title": "Level {level_number}: <creative title>",
  "tagline": "<motivational one-liner>",
  "content": "<3-4 sentence explanation at this level's depth>",
  "quiz": {{
    "question": "<quiz question about {concept}>",
    "options": [
      {{"label": "A", "text": "<option A>"}},
      {{"label": "B", "text": "<option B>"}},
      {{"label": "C", "text": "<option C>"}},
      {{"label": "D", "text": "<option D>"}}
    ],
    "correct": "A",
    "explanation": "<why correct answer is right>"
  }}
}}
"""

    response = client.chat.completions.create(
        model="llama-3.3-70b-versatile",
        messages=[{"role": "user", "content": prompt}],
        temperature=0.7,
    )

    raw = response.choices[0].message.content.strip()

    # Strip markdown code blocks if present
    if raw.startswith("```"):
        raw = raw.split("```")[1]
        if raw.startswith("json"):
            raw = raw[4:]
    raw = raw.strip()

    data = json.loads(raw)

    quiz_data = data["quiz"]
    quiz = QuizQuestion(
        question=quiz_data["question"],
        options=[QuizOption(**opt) for opt in quiz_data["options"]],
        correct=quiz_data["correct"],
        explanation=quiz_data["explanation"]
    )

    return Level(
        level_number=level_number,
        title=data["title"],
        tagline=data["tagline"],
        content=data["content"],
        quiz=quiz,
        xp=config["xp"]
    )
📄 engine/__init__.py
python
undefined
📄 routers/quest_router.py
python
from fastapi import APIRouter
from models.schemas import QuestRequest, QuestResponse
from engine.level_generator import generate_level

router = APIRouter()

@router.post("/quest/start", response_model=QuestResponse)
def start_quest(req: QuestRequest):
    levels = [generate_level(req.concept, i) for i in range(1, 4)]
    return QuestResponse(
        concept=req.concept,
        student_name=req.student_name,
        levels=levels
    )
📄 routers/__init__.py
python
undefined
📄 frontend/index.html
xml
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>LearnQuest</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      font-family: 'Segoe UI', sans-serif;
      background: #0d0d1a;
      color: #e0e0ff;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 20px;
    }
    #home-screen, #game-screen { width: 100%; max-width: 700px; }
    .logo { text-align: center; margin-bottom: 40px; }
    .logo h1 { font-size: 3rem; color: #b39ddb; }
    .logo p { color: #7986cb; font-size: 1rem; margin-top: 8px; }
    .logo span { font-size: 2rem; margin-right: 10px; }
    input {
      width: 100%;
      padding: 16px 20px;
      margin-bottom: 16px;
      border-radius: 12px;
      border: none;
      background: #1e1e3a;
      color: #e0e0ff;
      font-size: 1rem;
      outline: none;
    }
    input::placeholder { color: #555577; }
    button {
      width: 100%;
      padding: 16px;
      border-radius: 12px;
      border: none;
      background: linear-gradient(135deg, #7c4dff, #448aff);
      color: white;
      font-size: 1.1rem;
      font-weight: bold;
      cursor: pointer;
      transition: opacity 0.2s;
    }
    button:hover { opacity: 0.9; }
    button:disabled { opacity: 0.6; cursor: not-allowed; }

    /* XP Bar */
    #xp-bar-wrap {
      background: #1e1e3a;
      border-radius: 20px;
      height: 14px;
      margin-bottom: 24px;
      overflow: hidden;
    }
    #xp-bar {
      height: 100%;
      background: linear-gradient(90deg, #7c4dff, #448aff);
      border-radius: 20px;
      transition: width 0.6s ease;
    }
    #xp-label { text-align: right; font-size: 0.8rem; color: #7986cb; margin-bottom: 6px; }

    /* Level Card */
    .level-card {
      background: #1e1e3a;
      border-radius: 16px;
      padding: 28px;
      margin-bottom: 24px;
    }
    .level-badge {
      display: inline-block;
      background: linear-gradient(135deg, #7c4dff, #448aff);
      color: white;
      padding: 4px 14px;
      border-radius: 20px;
      font-size: 0.85rem;
      font-weight: bold;
      margin-bottom: 12px;
    }
    .level-card h2 { font-size: 1.5rem; color: #b39ddb; margin-bottom: 6px; }
    .level-card .tagline { color: #7986cb; font-style: italic; margin-bottom: 16px; }
    .level-card p { line-height: 1.7; color: #c0c0e0; }

    /* Quiz */
    .quiz-box {
      background: #13132a;
      border-radius: 16px;
      padding: 24px;
      margin-bottom: 16px;
    }
    .quiz-box h3 { color: #e0e0ff; margin-bottom: 16px; font-size: 1rem; }
    .quiz-options { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
    .opt-btn {
      background: #1e1e3a;
      border: 2px solid #2a2a4a;
      border-radius: 12px;
      padding: 12px 16px;
      color: #c0c0e0;
      font-size: 0.9rem;
      cursor: pointer;
      text-align: left;
      transition: all 0.2s;
      width: auto;
    }
    .opt-btn:hover { border-color: #7c4dff; color: white; }
    .opt-btn.correct { background: #1b4332; border-color: #40c070; color: #40c070; }
    .opt-btn.wrong   { background: #3b1515; border-color: #e05555; color: #e05555; }
    .explanation {
      margin-top: 14px;
      padding: 14px;
      background: #0d0d1a;
      border-radius: 10px;
      color: #a0a0cc;
      font-size: 0.9rem;
      display: none;
    }
    #next-btn { display: none; margin-top: 16px; }
    #finish-screen { text-align: center; display: none; }
    #finish-screen h1 { font-size: 3rem; color: #b39ddb; margin-bottom: 16px; }
    #finish-screen p { color: #7986cb; margin-bottom: 24px; font-size: 1.1rem; }
    #restart-btn { max-width: 300px; margin: 0 auto; display: block; }
  </style>
</head>
<body>

<!-- HOME SCREEN -->
<div id="home-screen">
  <div class="logo">
    <h1><span>🎮</span>LearnQuest</h1>
    <p>Every concept is a quest. Every student is a hero.</p>
  </div>
  <input type="text" id="concept-input" placeholder="Enter a concept (e.g. CNN, Recursion...)" />
  <input type="text" id="name-input" placeholder="Your name (optional)" />
  <button id="start-btn" onclick="startQuest()">⚔️ Start Quest</button>
</div>

<!-- GAME SCREEN -->
<div id="game-screen" style="display:none;">
  <div id="xp-label">0 / 600 XP</div>
  <div id="xp-bar-wrap"><div id="xp-bar" style="width:0%"></div></div>

  <div id="level-content"></div>

  <div class="quiz-box">
    <h3 id="quiz-question"></h3>
    <div class="quiz-options" id="quiz-options"></div>
    <div class="explanation" id="explanation"></div>
  </div>

  <button id="next-btn" onclick="nextLevel()">Next Level ➡️</button>
</div>

<!-- FINISH SCREEN -->
<div id="finish-screen">
  <h1>🏆 Quest Complete!</h1>
  <p id="finish-msg"></p>
  <button id="restart-btn" onclick="location.reload()">🔄 New Quest</button>
</div>

<script>
  const API = "http://localhost:8000/api/v1";
  let levels = [], currentLevel = 0, totalXP = 0, studentName = "Hero";

  async function startQuest() {
    const concept = document.getElementById("concept-input").value.trim();
    studentName = document.getElementById("name-input").value.trim() || "Hero";
    if (!concept) return alert("Enter a concept!");

    const btn = document.getElementById("start-btn");
    btn.disabled = true;
    btn.textContent = "⏳ Loading Quest...";

    try {
      const res = await fetch(`${API}/quest/start`, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ concept, student_name: studentName })
      });
      if (!res.ok) throw new Error("Server error");
      const data = await res.json();
      levels = data.levels;
      document.getElementById("home-screen").style.display = "none";
      document.getElementById("game-screen").style.display = "block";
      loadLevel(0);
    } catch (e) {
      alert("❌ Could not connect to backend. Is it running?");
      btn.disabled = false;
      btn.textContent = "⚔️ Start Quest";
    }
  }

  function loadLevel(index) {
    currentLevel = index;
    const lv = levels[index];
    document.getElementById("next-btn").style.display = "none";
    document.getElementById("explanation").style.display = "none";

    document.getElementById("level-content").innerHTML = `
      <div class="level-card">
        <span class="level-badge">Level ${lv.level_number} • ${lv.xp} XP</span>
        <h2>${lv.title}</h2>
        <p class="tagline">${lv.tagline}</p>
        <p>${lv.content}</p>
      </div>
    `;

    document.getElementById("quiz-question").textContent = "❓ " + lv.quiz.question;
    const opts = document.getElementById("quiz-options");
    opts.innerHTML = "";
    lv.quiz.options.forEach(opt => {
      const b = document.createElement("button");
      b.className = "opt-btn";
      b.textContent = `${opt.label}. ${opt.text}`;
      b.onclick = () => checkAnswer(opt.label, lv.quiz.correct, lv.quiz.explanation, lv.xp);
      opts.appendChild(b);
    });
  }

  function checkAnswer(chosen, correct, explanation, xp) {
    document.querySelectorAll(".opt-btn").forEach(b => {
      b.disabled = true;
      const label = b.textContent.split(".")[0].trim();
      if (label === correct) b.classList.add("correct");
      else if (label === chosen) b.classList.add("wrong");
    });

    if (chosen === correct) {
      totalXP += xp;
      updateXP();
    }

    const exp = document.getElementById("explanation");
    exp.textContent = (chosen === correct ? "✅ " : "❌ ") + explanation;
    exp.style.display = "block";

    const nextBtn = document.getElementById("next-btn");
    nextBtn.style.display = "block";
    nextBtn.textContent = currentLevel < levels.length - 1 ? "Next Level ➡️" : "🏆 Finish Quest";
  }

  function updateXP() {
    const maxXP = 600;
    document.getElementById("xp-bar").style.width = (totalXP / maxXP * 100) + "%";
    document.getElementById("xp-label").textContent = `${totalXP} / ${maxXP} XP`;
  }

  function nextLevel() {
    if (currentLevel < levels.length - 1) {
      loadLevel(currentLevel + 1);
    } else {
      document.getElementById("game-screen").style.display = "none";
      document.getElementById("finish-screen").style.display = "block";
      document.getElementById("finish-msg").textContent =
        `${studentName} earned ${totalXP} / 600 XP! You're a LearnQuest Hero! 🌟`;
    }
  }
</script>
</body>
</html>
📄 README.md
text
# 🎮 LearnQuest

> Every concept is a quest. Every student is a hero.

LearnQuest is a gamified AI-powered learning app. Enter any concept, and the AI generates 3 progressive levels of learning with quizzes and XP rewards.

## 🚀 Setup

### 1. Clone & Install
```bash
git clone https://github.com/YOUR_USERNAME/learnquest-backend.git
cd learnquest-backend
python -m venv venv
venv\Scripts\activate.bat      # Windows
pip install -r requirements.txt
2. Set API Key
Create a .env file:

text
GROQ_API_KEY=your_groq_api_key_here
Get your free key at: https://console.groq.com/keys

3. Run
bash
python main.py
4. Open Frontend
Open frontend/index.html in your browser.

🛠 Tech Stack
Backend: FastAPI + Python

AI: Groq (LLaMA 3.3 70B)

Frontend: Vanilla HTML/CSS/JS

text

***

## ✅ Steps to Upload to GitHub

1. **Create a new repo** at github.com → name it `learnquest-backend`
2. Copy all files into the folder structure shown above
3. Run in terminal:
```bash
cd C:\Users\Vinayaka\learnquest-backend
git init
git add .
git commit -m "🎮 Initial LearnQuest release"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/learnquest-backend.git
git push -u origin main


make it github pastable








