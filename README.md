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






