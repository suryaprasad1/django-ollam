# 🚀 Django + Ollama Integration (Step-by-Step Guide)

## 📌 Overview

This guide helps you integrate a **local AI model (Ollama)** with a **Django web application** to build a chatbot.

---

# 🧰 Prerequisites

* Python 3.x
* Django installed
* EC2 / Local machine
* Ollama installed

---

# 🟢 Step 1: Install Ollama

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Start Ollama:

```bash
OLLAMA_HOST=0.0.0.0 ollama serve
```

---

# 🟢 Step 2: Pull AI Model

Use a lightweight model (recommended):

```bash
ollama pull phi3 , llama3.2:1b
```

Check models:

```bash
ollama list
```

---

# 🟢 Step 3: Test Ollama API

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "phi3", "llama3.2:1b",
  "prompt": "Hello",
  "stream": false
}'
```

---

# 🟢 Step 4: Create Django Project

```bash
django-admin startproject chatbot_project
cd chatbot_project
python manage.py startapp chatbot
```

---

# 🟢 Step 5: Add App to settings.py

```python
INSTALLED_APPS = [
    ...
    'chatbot',
]
```

---

# 🟢 Step 6: Create AI Function

📁 `chatbot/utils.py`

```python
import requests

def ask_ai(message):
    response = requests.post(
        "http://YOUR_IP:11434/api/generate",
        json={
            "model": "phi3",
            "prompt": message,
            "stream": False
        },
        timeout=120
    )

    return response.json()["response"]
```

👉 Replace `YOUR_IP` with:

* Local: `localhost`
* EC2: `44.195.62.94`

---

# 🟢 Step 7: Create View

📁 `chatbot/views.py`

```python
from django.http import JsonResponse
from django.shortcuts import render
import json
from .utils import ask_ai

def chatbot_view(request):
    if request.method == "POST":
        data = json.loads(request.body)
        message = data.get("message")

        response = ask_ai(message)

        return JsonResponse({"response": response})

    return render(request, "chat.html")
```

---

# 🟢 Step 8: Configure URLs

📁 `chatbot/urls.py`

```python
from django.urls import path
from .views import chatbot_view

urlpatterns = [
    path('', chatbot_view, name='chatbot'),
]
```

📁 `project/urls.py`

```python
from django.urls import path, include

urlpatterns = [
    path('', include('chatbot.urls')),
]
```

---

# 🟢 Step 9: Create Template

📁 `templates/chat.html`

👉 Use your chatbot UI (already created)

---

# 🟢 Step 10: Run Server

```bash
python manage.py runserver
```

Open:

```
http://127.0.0.1:8000
```

---

# ⚠️ Important Config (EC2 Users)

## 🔓 Open Port

Allow in Security Group:

```
Port: 11434
Source: 0.0.0.0/0
```

---

## 🌐 Bind Ollama

```bash
OLLAMA_HOST=0.0.0.0 ollama serve
```

---

# 🐞 Common Errors & Fixes

## ❌ Connection refused

* Ollama not running
* Port not open

## ❌ Model not found

```bash
ollama pull phi3
```

## ❌ Timeout

* Use smaller model
* Increase timeout

---

# 🚀 Production Tips

* Use **Nginx reverse proxy**
* Add authentication
* Restrict public access
* Use **Gunicorn + Django**

---

# 🎯 Final Architecture

```
User → Django → Ollama API → AI Model → Response → Django → User
```

---

# ✅ Done!

You now have a working:

* Django chatbot
* Local AI (no API cost)
* Production-ready base

---

# 🚀 Next Steps

* Add chat history (DB)
* Streaming responses
* User authentication
* Deploy with HTTPS

---
