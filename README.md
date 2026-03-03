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

┌─────────────────────────────────────────────────────┐
│                     USER BROWSER                    │
│         Frontend (HTML + CSS + JavaScript)          │
│  ┌─────────────────┐      ┌─────────────────────┐  │
│  │  Topic Input UI  │      │  Level Selector UI  │  │
│  └────────┬─────────┘      └──────────┬──────────┘  │
└───────────┼─────────────────────────  ┼─────────────┘
            │         HTTP Request       │
            └──────────────┬────────────┘
                           ▼
┌─────────────────────────────────────────────────────┐
│              Backend Server (Python Flask)          │
│  ┌──────────────────────────────────────────────┐   │
│  │           Request Handler / Router            │   │
│  └──────────────────┬───────────────────────────┘   │
│                     │                               │
│  ┌──────────────────▼───────────────────────────┐   │
│  │        Prompt Builder / Context Manager       │   │
│  └──────────────────┬───────────────────────────┘   │
└─────────────────────┼───────────────────────────────┘
                      │         API Call
                      ▼
┌─────────────────────────────────────────────────────┐
│                  OpenAI API Layer                   │
│  ┌──────────────┐  ┌─────────────┐  ┌───────────┐  │
│  │  Explanation │  │   Summary   │  │   Quiz    │  │
│  │  Generator   │  │  Generator  │  │ Generator │  │
│  └──────────────┘  └─────────────┘  └───────────┘  │
└─────────────────────────────────────────────────────┘
                      │
                      │    JSON Response
                      ▼
┌─────────────────────────────────────────────────────┐
│                  Output Display UI                  │
│   📄 Summary  |  💡 Explanation  |  📝 Quiz Cards  │
└─────────────────────────────────────────────────────┘





