Emergency
# Project: emegerncy solution

import json
import datetime

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


if __name__ == "__main__":
    main()
