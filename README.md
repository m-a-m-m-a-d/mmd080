# Project: Digikala Emergency Task Management System
# Author: Developer Team
# Date: 2026-01-08

import datetime
import logging
from typing import List, Optional
from sqlalchemy import create_engine, Column, Integer, String, Boolean, DateTime, ForeignKey
from sqlalchemy.orm import declarative_base, sessionmaker, relationship
from flask import Flask, request, jsonify

# ------------------------------
# Logging Configuration
# ------------------------------
logging.basicConfig(
    filename="digikala_emergency.log",
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s"
)

# ------------------------------
# Database Setup
# ------------------------------
Base = declarative_base()
engine = create_engine("sqlite:///digikala_tasks.db", echo=False)
SessionLocal = sessionmaker(bind=engine)

# ------------------------------
# User Model
# ------------------------------
class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    username = Column(String, unique=True, nullable=False)
    role = Column(String, default="employee")  # admin, manager, employee
    tasks = relationship("Task", back_populates="assigned_user")

    def __repr__(self):
        return f"<User(username={self.username}, role={self.role})>"

# ------------------------------
# Task Model
# ------------------------------
class Task(Base):
    __tablename__ = "tasks"

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, nullable=False)
    category = Column(String, default="General")
    priority = Column(Integer, default=3)  # 1=High, 2=Medium, 3=Low
    done = Column(Boolean, default=False)
    created_at = Column(DateTime, default=datetime.datetime.now)
    user_id = Column(Integer, ForeignKey("users.id"))

    assigned_user = relationship("User", back_populates="tasks")

    def __repr__(self):
        return f"<Task(title={self.title}, category={self.category}, priority={self.priority}, done={self.done})>"

# ------------------------------
# Database Initialization
# ------------------------------
Base.metadata.create_all(bind=engine)

# ------------------------------
# Task Manager Class
# ------------------------------
class TaskManager:
    def __init__(self):
        self.db = SessionLocal()

    def add_user(self, username: str, role: str = "employee"):
        user = User(username=username, role=role)
        self.db.add(user)
        self.db.commit()
        logging.info(f"User '{username}' added with role '{role}'.")

    def add_task(self, title: str, category: str, priority: int, username: str):
        user = self.db.query(User).filter_by(username=username).first()
        if not user:
            raise ValueError("User not found.")
        task = Task(title=title, category=category, priority=priority, assigned_user=user)
        self.db.add(task)
        self.db.commit()
        logging.info(f"Task '{title}' added for user '{username}'.")

    def complete_task(self, task_id: int):
        task = self.db.query(Task).filter_by(id=task_id).first()
        if task:
            task.done = True
            self.db.commit()
            logging.info(f"Task '{task.title}' marked as completed.")

    def generate_report(self):
        tasks = self.db.query(Task).all()
        total = len(tasks)
        completed = len([t for t in tasks if t.done])
        pending = total - completed
        return {
            "total": total,
            "completed": completed,
            "pending": pending
        }

# ------------------------------
# Flask API
# ------------------------------
app = Flask(__name__)
manager = TaskManager()

@app.route("/users", methods=["POST"])
def create_user():
    data = request.json
    manager.add_user(data["username"], data.get("role", "employee"))
    return jsonify({"message": "User created successfully."})

@app.route("/tasks", methods=["POST"])
def create_task():
    data = request.json
    manager.add_task(data["title"], data.get("category", "General"), data.get("priority", 3), data["username"])
    return jsonify({"message": "Task created successfully."})

@app.route("/tasks/<int:task_id>/complete", methods=["PUT"])
def complete_task(task_id):
    manager.complete_task(task_id)
    return jsonify({"message": "Task marked as completed."})

@app.route("/report", methods=["GET"])
def report():
    return jsonify(manager.generate_report())

if __name__ == "__main__":
    app.run(debug=True)
class Task:
    def __init__(self, title, done=False, created_at=None):
        self.title = title
        self.done = done
        self.created_at = created_at if created_at else datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")

    def mark_done(self):
        self.done = True

    def to_dict(self):
        return {"title": self.title, "done": self.done, "created_at": self.created_at}

    def __str__(self):
        status = "✔️ Completed" if self.done else "❌ Pending"
        return f"{self.title} - {status} (Created: {self.created_at})"


class TaskManager:
    def __init__(self, filename="tasks.json"):
        self.tasks = []
        self.filename = filename
        self.load_tasks()

    def add_task(self, title):
        task = Task(title)
        self.tasks.append(task)
        self.save_tasks()

    def complete_task(self, index):
        if 0 <= index < len(self.tasks):
            self.tasks[index].mark_done()
            self.save_tasks()

    def search_task(self, keyword):
        results = [task for task in self.tasks if keyword.lower() in task.title.lower()]
        print(f"\n--- Search results for '{keyword}' ---")
        for task in results:
            print(task)

    def show_tasks(self):
        print("\n--- emegerncy solution Task List ---")
        for i, task in enumerate(self.tasks):
            print(f"{i+1}. {task}")

    def save_tasks(self):
        with open(self.filename, "w") as f:
            json.dump([task.to_dict() for task in self.tasks], f, indent=4)

    def load_tasks(self):
        try:
            with open(self.filename, "r") as f:
                data = json.load(f)
                self.tasks = [Task(**task) for task in data]
        except FileNotFoundError:
            self.tasks = []


# Demo run
manager = TaskManager()
manager.add_task("Prepare emergency report")
manager.add_task("Check system logs")
manager.add_task("Send update to Eli")
manager.add_task("Backup database")

manager.show_tasks()

# Mark one task as completed
manager.complete_task(1)

manager.show_tasks()

# Search for a task
class Task:
    def __init__(self, title, done=False, created_at=None):
        self.title = title
        self.done = done
        self.created_at = created_at if created_at else datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")

    def mark_done(self):
        self.done = True

    def to_dict(self):
        return {"title": self.title, "done": self.done, "created_at": self.created_at}

    def __str__(self):
        status = "✔️ Completed" if self.done else "❌ Pending"
        return f"{self.title} - {status} (Created: {self.created_at})"


class TaskManager:
    def __init__(self, filename="tasks.json"):
        self.tasks = []
        self.filename = filename
        self.load_tasks()

    def add_task(self, title):
        task = Task(title)
        self.tasks.append(task)
        self.save_tasks()

    def complete_task(self, index):
        if 0 <= index < len(self.tasks):
            self.tasks[index].mark_done()
            self.save_tasks()

    def search_task(self, keyword):
        results = [task for task in self.tasks if keyword.lower() in task.title.lower()]
        print(f"\n--- Search results for '{keyword}' ---")
        for task in results:
            print(task)

    def show_tasks(self):
        print("\n--- emegerncy solution Task List ---")
        if not self.tasks:
            print("No tasks available.")
        for i, task in enumerate(self.tasks):
            print(f"{i+1}. {task}")

    def save_tasks(self):
        with open(self.filename, "w") as f:
            json.dump([task.to_dict() for task in self.tasks], f, indent=4)

    def load_tasks(self):
        try:
            with open(self.filename, "r") as f:
                data = json.load(f)
                self.tasks = [Task(**task) for task in data]
        except FileNotFoundError:
            self.tasks = []


def main():
    manager = TaskManager()

    while True:
        print("\n--- emegerncy solution Menu ---")
        print("1. Add new task")
        print("2. Show all tasks")
        print("3. Complete a task")
        print("4. Search tasks")
        print("5. Exit")

        choice = input("Choose an option: ")

        if choice == "1":
            title = input("Enter task title: ")
            manager.add_task(title)
            print("Task added successfully.")
        elif choice == "2":
            manager.show_tasks()
        elif choice == "3":
            manager.show_tasks()
            index = int(input("Enter task number to complete: ")) - 1
            manager.complete_task(index)
            print("Task marked as completed.")
        elif choice == "4":
            keyword = input("Enter keyword to search: ")
            manager.search_task(keyword)
        elif choice == "5":
            print("Exiting emegerncy solution...")
            break
        else:
            print("Invalid choice, try again.")
