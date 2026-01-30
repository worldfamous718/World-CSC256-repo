# Sprint 1 Reflection – Foundations, TDD, and API Design
![Eureka Moment](../images/Eureka.png)
## Overview

This reflection summarizes my experience completing Sprint 1, including what I learned, where I struggled, and how I worked through the challenges to successfully build and test a Flask-based API using Test-Driven Development (TDD).

---

## Initial Challenges and Mindset

I want to be honest about how this sprint went. Due to a heavy workload from other classes and multiple back-to-back due dates, my time management suffered this week. I waited until closer to the deadline to really dig into the lab portion of the assignment, which made the initial experience more stressful than it probably needed to be.

At the onset of this lab, I was honestly very confused and overwhelmed. This was not a traditional step-by-step lab where you follow instructions to learn a single concept in isolation. Instead, this sprint required actually *building a working system* from given instructions and a basic framework. That shift alone took some adjustment.

---

## Understanding the Project Structure

One of the first things I had to do was slow down and read. I spent time going through the various README documents throughout the repository to piece together how the application was expected to work. In particular, I had to understand the idea of a **Flask app factory**, which was not explicitly walked through in the instructions.

Seeing an almost empty `__init__.py` file was confusing at first, but it eventually made sense once I realized that I was expected to create the mechanism that builds the application myself.

The **task tracker user stories (US000–US003)** were what really helped everything click. Once I reframed the assignment around those user stories, the work became much clearer. Instead of asking “what file do I edit?”, I started asking “what behavior does this user story require?” That mindset shift cleared up most of my confusion.

---

## Flask App Factory (US000)

The first major concept I learned was how and *why* to use a Flask app factory. Instead of creating the Flask app at import time, I wrote a `create_app()` function that builds and returns the app when needed.

```python
from flask import Flask
from .routes import register_routes

def create_app():
    app = Flask(__name__)
    register_routes(app)
    return app
```
This taught me that:

- The application should not run automatically when imported

- Tests and CI can create a fresh app instance on demand

- This structure avoids side effects and supports scalability

This was my first real exposure to how professional Flask projects are structured.

---
## Health Check Endpoint (US001)

The /api/health endpoint was my first API route in this project. Its purpose is to confirm that the system is running and responding.

``` python
@app.route("/api/health")
def health():
    return {"status": "ok"}
```

Although simple, this endpoint helped me understand:

- Flask routing fundamentals

- JSON response formatting

- The importance of health checks in CI/CD pipelines
  
---
## Learning Test-Driven Development (TDD)

This sprint was my first time truly using Test-Driven Development, and it ended up being one of the most valuable lessons.

Example health check test:
``` python
def test_health_endpoint_returns_ok():
    app = create_app()
    client = app.test_client()

    response = client.get("/api/health")

    assert response.status_code == 200
    assert response.get_json() == {"status": "ok"}
```
Through TDD, I learned that:

- Tests define expected behavior before code exists

- A failing test confirms the test is meaningful

- Passing tests provide confidence that functionality works

I now realize that using TDD earlier in my Python learning journey would have helped me grasp unit testing much sooner.

---
## Task Management Endpoints (US002 & US003)

To support task tracking, I implemented both POST and GET endpoints using an in-memory list.

## Add Task (POST)
``` python
tasks = []

@app.route("/api/tasks", methods=["POST"])
def add_task():
    data = request.get_json() or {}
    title = data.get("title")

    task = {"id": len(tasks) + 1, "title": title}
    tasks.append(task)

    return task, 201
```
## View Tasks (GET)
``` python
@app.route("/api/tasks", methods=["GET"])
def view_tasks():
    return tasks, 200
```
This reinforced several important concepts:

- HTTP methods define behavior even when paths are the same

- APIs act as the source of truth independent of the UI

- Server-side state must be handled carefully during testing

To keep tests isolated, I learned to clear shared state:
``` python
routes.tasks.clear()
```
This helped me understand why test isolation is critical in real-world applications.

---
## Final Takeaways

Despite a rough start, Sprint 1 turned out to be one of the most educational assignments I’ve completed. It forced me to:

- Interpret documentation instead of following scripted steps

- Build functionality directly from user stories

- Apply GitHub Flow with branches, commits, and CI

- Use TDD in a real project context

Once I got into the groove, the confusion gave way to confidence. I now feel significantly more comfortable with Flask project structure, API design, and pytest-based testing.

Most importantly, this sprint reinforced that early confusion is often a sign of meaningful learning. 
