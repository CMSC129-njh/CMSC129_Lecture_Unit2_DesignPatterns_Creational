# 🏗️ Creational Design Patterns

## Understanding Patterns Through a To-Do List Application

## Table of Contents

- [Introduction](#introduction)
- [What are Creational Patterns?](#what-are-creational-patterns)
- [Our Example: To-Do List Application](#our-example-to-do-list-application)
- [Pattern 1: Singleton Pattern](#pattern-1-singleton-pattern)
- [Pattern 2: Factory Pattern](#pattern-2-factory-pattern)
- [Pattern 3: Builder Pattern](#pattern-3-builder-pattern)
- [Best Practices](#best-practices)
- [Summary](#summary)

---

## Introduction

Design patterns are reusable solutions to commonly occurring problems in software design. They're like recipes that you can follow to solve common coding challenges.

> **"Each pattern describes a problem which occurs over and over again in our environment, and then describes the core of the solution to that problem."** - Christopher Alexander

### Why Learn Patterns?

- ✅ **Write better code** - Proven solutions to common problems
- ✅ **Communicate clearly** - Shared vocabulary with other developers
- ✅ **Avoid mistakes** - Learn from others' experiences
- ✅ **Build maintainable software** - Code that's easy to change

---

## What are Creational Patterns?

**Creational patterns** help you create objects in smart ways.

When you're new to programming, you might create objects the simple way - just calling `Task()` or `EmailNotification()` directly in your code. But as your application grows, this approach can lead to problems:

- What if you need different types of notifications (email, SMS, push)?
- What if creating a task requires complex setup with many optional fields?
- What if you need to ensure only one instance of something exists (like a task manager)?

Creational patterns solve these problems by providing structured, maintainable ways to create objects based on different situations.

### The Three Patterns We'll Learn:

| Pattern       | Purpose                            | Problem It Solves                                           | Real-World Analogy                                               |
| ------------- | ---------------------------------- | ----------------------------------------------------------- | ---------------------------------------------------------------- |
| **Singleton** | Only one instance exists           | Multiple instances waste resources and cause data conflicts | There's only ONE task manager in your app                        |
| **Factory**   | Create different types of objects  | Hard-coded object creation scattered throughout code        | A notification factory that creates different notification types |
| **Builder**   | Build complex objects step-by-step | Constructors with too many parameters are hard to use       | Assembling a task piece by piece                                 |

---

## Our Example: To-Do List Application

Throughout this guide, we'll build a **To-Do List Application** that manages tasks. This will help you see how patterns work in a real application you can relate to!

```
📱 To-Do List App
├── 📋 Task Manager (Singleton)
├── 🏭 Notification Factory (Factory Pattern)
└── 👷 Task Builder (Builder Pattern)
```

### What We're Building:

- **Tasks** with different types (School, Personal, Others)
- **Task Manager** that coordinates everything
- **Different ways** to create tasks based on needs
- **Notifications** when tasks are due, with different methods (email, SMS, push)

---

## Pattern 1: Singleton Pattern

### 🎯 **Purpose**

Ensure there's only ONE instance of a class in your entire application. Like having one single task manager that everyone uses.

### 📖 **Concept in Plain English**

Imagine your to-do app has a TaskManager. You don't want 3 different task managers each with their own task lists! You want ONE central manager that keeps track of everything.

### 🎨 **Visual Diagram**

```
WITHOUT Singleton:
══════════════════
┌────────┐         ┌─────────────────┐
│ User 1 │────────>│ TaskManager #1  │
└────────┘         │   (5 tasks)     │
                   └─────────────────┘
┌────────┐         ┌─────────────────┐
│ User 2 │────────>│ TaskManager #2  │  ❌ Different instances!
└────────┘         │   (3 tasks)     │  ❌ Data not synced!
                   └─────────────────┘
┌────────┐         ┌─────────────────┐
│ User 3 │────────>│ TaskManager #3  │
└────────┘         │   (7 tasks)     │
                   └─────────────────┘


WITH Singleton:
═══════════════
┌────────┐
│ User 1 │─────┐
└────────┘     │
               │    ┌──────────────────────┐
┌────────┐     ├───>│   TaskManager        │
│ User 2 │─────┤    │   (THE ONE INSTANCE) │  ✅ One shared instance!
└────────┘     │    │   (15 tasks)         │  ✅ All data synced!
               │    └──────────────────────┘
┌────────┐     │
│ User 3 │─────┘
└────────┘
```

### 📝 **Pseudocode**

```
PSEUDOCODE:
-----------
WHEN someone asks for TaskManager:
    IF TaskManager instance already exists:
        RETURN the existing instance
    ELSE:
        CREATE new TaskManager instance
        SAVE it as THE instance
        RETURN it
```

### 🚫 **Anti-Example: Without Singleton Pattern**

```python
# ❌ PROBLEMATIC: Multiple task managers can be created

class TaskManager:
    def __init__(self):
        self.tasks = []
        print(f"📋 Creating a new Task Manager")

    def add_task(self, task_name):
        self.tasks.append(task_name)
        print(f"✅ Added: {task_name}")

    def show_tasks(self):
        print(f"📝 Tasks ({len(self.tasks)}):")
        for i, task in enumerate(self.tasks, 1):
            print(f"   {i}. {task}")

# ❌ PROBLEM: Each person creates their own manager!
print("=== Without Singleton ===\n")

# Keith creates a task manager
keith_manager = TaskManager()
keith_manager.add_task("Buy groceries")
keith_manager.add_task("Call dentist")

# Jave creates his own task manager
jave_manager = TaskManager()
jave_manager.add_task("Finish homework")

# Are they the same manager?
print(f"\nAre they the same? {keith_manager is jave_manager}")  # False!

print("\nKeith's tasks:")
keith_manager.show_tasks()

print("\nJave's tasks:")
jave_manager.show_tasks()

# ❌ The tasks are split across different managers!
```

**Output:**

```
=== Without Singleton ===

📋 Creating a new Task Manager
✅ Added: Buy groceries
✅ Added: Call dentist
📋 Creating a new Task Manager
✅ Added: Finish homework

Are they the same? False

Keith's tasks:
📝 Tasks (2):
   1. Buy groceries
   2. Call dentist

Jave's tasks:
📝 Tasks (1):
   1. Finish homework
```

**Problems:**

- 🔴 Multiple managers created (memory waste)
- 🔴 Tasks are not shared/synced
- 🔴 Confusing - which manager has which tasks?
- 🔴 Hard to get a complete view of all tasks

### ✅ **Solution: With Singleton Pattern**

```python
# ✅ SOLUTION: Only ONE task manager can exist

class TaskManager:
    _instance = None
    _initialized = False

    def __new__(cls):
        # This runs BEFORE __init__
        if cls._instance is None:
            print("🎉 Creating the ONE and ONLY Task Manager")
            cls._instance = super().__new__(cls)
        else:
            print("♻️ Returning the existing Task Manager")
        return cls._instance

    def __init__(self):
        # Only initialize once
        if not TaskManager._initialized:
            self.tasks = []
            TaskManager._initialized = True

    def add_task(self, task_name):
        self.tasks.append(task_name)
        print(f"✅ Added: {task_name}")

    def show_tasks(self):
        print(f"\n📝 All Tasks ({len(self.tasks)}):")
        if not self.tasks:
            print("   (No tasks yet)")
        for i, task in enumerate(self.tasks, 1):
            print(f"   {i}. {task}")

    def get_task_count(self):
        return len(self.tasks)

# ✅ USAGE: Everyone gets the same manager!
print("=== With Singleton Pattern ===\n")

# Keith gets a task manager
keith_manager = TaskManager()
keith_manager.add_task("Buy groceries")
keith_manager.add_task("Call dentist")

# Jave gets a task manager (but it's the SAME one!)
jave_manager = TaskManager()
jave_manager.add_task("Finish homework")

# Nero also gets it
nero_manager = TaskManager()
nero_manager.add_task("Walk the dog")

# Check if they're all the same
print(f"\nAre Keith and Jave's managers the same? {keith_manager is jave_manager}")  # True!
print(f"Are Jave and Nero's managers the same? {jave_manager is nero_manager}")  # True!

# Everyone sees ALL tasks!
print("\nKeith's view:")
keith_manager.show_tasks()

print("\nJave's view:")
jave_manager.show_tasks()

print("\nNero's view:")
nero_manager.show_tasks()

print(f"\n✅ Total tasks in system: {keith_manager.get_task_count()}")
```

**Output:**

```
=== With Singleton Pattern ===

🎉 Creating the ONE and ONLY Task Manager
✅ Added: Buy groceries
✅ Added: Call dentist
♻️ Returning the existing Task Manager
✅ Added: Finish homework
♻️ Returning the existing Task Manager
✅ Added: Walk the dog

Are Keith and Jave's managers the same? True
Are Jave and Nero's managers the same? True

Keith's view:

📝 All Tasks (4):
   1. Buy groceries
   2. Call dentist
   3. Finish homework
   4. Walk the dog

Jave's view:

📝 All Tasks (4):
   1. Buy groceries
   2. Call dentist
   3. Finish homework
   4. Walk the dog

Nero's view:

📝 All Tasks (4):
   1. Buy groceries
   2. Call dentist
   3. Finish homework
   4. Walk the dog

✅ Total tasks in system: 4
```

### � **TL;DR: How to Implement Singleton Pattern**

**In 3 Simple Steps:**

1. **Add a class variable** to store the single instance:

   ```python
   _instance = None
   ```

2. **Override `__new__()`** to control instance creation:

   ```python
   def __new__(cls):
       if cls._instance is None:
           cls._instance = super().__new__(cls)
       return cls._instance
   ```

3. **Protect `__init__()`** from re-initializing:
   ```python
   _initialized = False
   def __init__(self):
       if not MyClass._initialized:
           # Initialize once
           MyClass._initialized = True
   ```

**That's it!** Now every time someone calls `MyClass()`, they get the same instance.

### 🔑 **Key Takeaways**

- ✅ **One Instance**: Only one TaskManager exists
- ✅ **Shared State**: Everyone sees the same tasks
- ✅ **Memory Efficient**: Don't create duplicate managers
- ✅ **Easy Access**: Get the manager from anywhere in your code

---

## Pattern 2: Factory Pattern

##### _Author Note: If you want to add emojis to your markdown file in VSCode, use "Windows + . ". Don't ask me how to do it in Mac. I have no idea._

### 🎯 **Purpose**

Centralize object creation logic in ONE place instead of scattering it throughout your code. A factory is responsible for creating different types of objects based on input.

### 📖 **Concept in Plain English**

Let's say your to-do app needs to send notifications when tasks are due. Different users prefer different notification methods: email, SMS, or push notifications. Various parts of your app need to send these notifications - when a task is created, when it's due soon, when it's overdue, etc.

Without a factory, every place that sends notifications has to know how to create each notification type. With a factory, you have ONE place that handles all notification creation!

**Think of it like a real factory:** Instead of building a car yourself every time, you go to a car factory and say _"I want a Jimny, yung 4 doors"_ - the factory handles all the complex assembly.

### 🎨 **Visual Diagram**

#### _Author Note: I use AsciiFlow for text-based diagrams (don't do it manually, please lang.😿 )_

```
WITHOUT Factory:
════════════════
┌──────────────┐     ┌──────────────────────────────┐
│ UserSignup   │     │ if method == "email":        │
│              │────>│   create EmailNotification() │  ❌ Duplicated
└──────────────┘     │ elif method == "sms":        │     creation
                     │   create SMSNotification()   │     logic!
                     └──────────────────────────────┘

┌──────────────┐     ┌──────────────────────────────┐
│ TaskReminder │     │ if method == "email":        │
│              │────>│   create EmailNotification() │  ❌ Same logic
└──────────────┘     │ elif method == "sms":        │     REPEATED!
                     │   create SMSNotification()   │
                     └──────────────────────────────┘

┌──────────────┐     ┌──────────────────────────────┐
│ PasswordReset│     │ if method == "email":        │
│              │────>│   create EmailNotification() │  ❌ Duplicated
└──────────────┘     │ elif method == "sms":        │     AGAIN!
                     │   create SMSNotification()   │
                     └──────────────────────────────┘

👉 Problem: Change how SMS works? Update EVERY file!


WITH Factory:
═════════════
┌──────────────┐
│ UserSignup   │────┐
└──────────────┘    │
                    │
┌──────────────┐    │     ┌─────────────────────────┐
│ TaskReminder │────┼────>│  NotificationFactory    │
└──────────────┘    │     │  .create(method, msg)   │
                    │     └───────────┬─────────────┘
┌──────────────┐    │                 │
│ PasswordReset│────┘                 │
└──────────────┘                      ▼
                              ┌───────────────┐
                              │  Creates:     │
                              │  • Email      │  ✅ ONE place!
                              │  • SMS        │  ✅ Centralized!
                              │  • Push       │  ✅ Easy to extend!
                              └───────────────┘

👉 Benefit: Change SMS? Update ONLY the factory!
```

### 📝 **Pseudocode**

```
PSEUDOCODE (What happens INSIDE the factory):
----------------------------------------------
CLASS NotificationFactory:
    MAP notification_types = {
        "email" → EmailNotification,
        "sms" → SMSNotification,
        "push" → PushNotification
    }

    FUNCTION create(method, message):
        notification_class = LOOKUP method in notification_types map
        IF notification_class exists:
            RETURN NEW instance of notification_class
        ELSE:
            RAISE error
```

### 🚫 **Anti-Example: Without Factory Pattern**

```python
# ❌ PROBLEMATIC: Creation logic scattered everywhere

# Let's define notification classes
class EmailNotification:
    def __init__(self, message, recipient):
        self.message = message
        self.recipient = recipient
        self.method = "Email"

    def send(self):
        return f"📧 Sending email to {self.recipient}: {self.message}"


class SMSNotification:
    def __init__(self, message, phone):
        self.message = message
        self.phone = phone
        self.method = "SMS"

    def send(self):
        return f"📱 Sending SMS to {self.phone}: {self.message}"


class PushNotification:
    def __init__(self, message, device_id):
        self.message = message
        self.device_id = device_id
        self.method = "Push"

    def send(self):
        return f"🔔 Sending push to device {self.device_id}: {self.message}"


# 😿 PROBLEM: Every module duplicates notification creation logic!

# In UserSignup.js:
def send_welcome_notification(method, contact):
    """User signup needs to send welcome message"""
    message = "Welcome to our To-Do App!"

    # ❌ Duplication starts here
    if method == "email":
        notification = EmailNotification(message, contact)
    elif method == "sms":
        notification = SMSNotification(message, contact)
    elif method == "push":
        notification = PushNotification(message, contact)
    else:
        return "Unknown method"

    return notification.send()


# In TaskReminder.js:
def send_task_due_notification(method, contact, task_name):
    """Task reminders need the same logic!"""
    message = f"Reminder: '{task_name}' is due soon!"

    # ❌ Duplicated again!
    if method == "email":
        notification = EmailNotification(message, contact)
    elif method == "sms":
        notification = SMSNotification(message, contact)
    elif method == "push":
        notification = PushNotification(message, contact)
    else:
        return "Unknown method"

    return notification.send()


# In PasswordReset.js:
def send_password_reset_notification(method, contact, reset_code):
    """Password reset duplicates it AGAIN!"""
    message = f"Your reset code is: {reset_code}"

    # ❌ Duplicated yet again!
    if method == "email":
        notification = EmailNotification(message, contact)
    elif method == "sms":
        notification = SMSNotification(message, contact)
    elif method == "push":
        notification = PushNotification(message, contact)
    else:
        return "Unknown method"

    return notification.send()


print("=== Without Factory Pattern ===\n")

# Different parts of the app sending notifications
result1 = send_welcome_notification("email", "user@example.com")
print(f"Signup: {result1}")

result2 = send_task_due_notification("sms", "+1234567890", "Buy groceries")
print(f"Reminder: {result2}")

result3 = send_password_reset_notification("push", "device_abc123", "789456")
print(f"Password Reset: {result3}")

print("\n❌ Problems:")
print("  • Notification creation logic DUPLICATED in 3+ places")
print("  • Want to add WhatsApp notifications? Update ALL functions!")
print("  • Want to change how email works? Update ALL functions!")
print("  • Easy to make mistakes and forget to update one place")
```

**Output:**

```
=== Without Factory Pattern ===

Signup: 📧 Sending email to user@example.com: Welcome to our To-Do App!
Reminder: 📱 Sending SMS to +1234567890: Reminder: 'Buy groceries' is due soon!
Password Reset: 🔔 Sending push to device_abc123: Your reset code is: 789456

❌ Problems:
  • Notification creation logic DUPLICATED in 3+ places
  • Want to add WhatsApp notifications? Update ALL functions!
  • Want to change how email works? Update ALL functions!
  • Easy to make mistakes and forget to update one place
```

**Problems:**

- 🔴 **Duplicated Code**: Same if-else logic repeated everywhere
- 🔴 **Hard to Extend**: Add WhatsApp? Update 10+ functions!
- 🔴 **Hard to Maintain**: Change email setup? Update all files!
- 🔴 **Error Prone**: Easy to update one place and forget others

### ✅ **Solution: With Factory Pattern**

```python
# ✅ SOLUTION: ONE factory, everyone uses it!

# Step 1: Define notification classes (same as before - that's fine!)
class EmailNotification:
    def __init__(self, message, recipient):
        self.message = message
        self.recipient = recipient
        self.method = "Email"

    def send(self):
        # for the actual implementation, you would have code here to send the email using an email service (so in python, there's the smtplib library, or you could use a "free" service like SendGrid)
        return f"📧 Sending email to {self.recipient}: {self.message}"


class SMSNotification:
    def __init__(self, message, phone):
        self.message = message
        self.phone = phone
        self.method = "SMS"

    def send(self):
        # again, the SMS-sending logic would be here, but for now, it's just a print-check
        return f"📱 Sending SMS to {self.phone}: {self.message}"


class PushNotification:
    def __init__(self, message, device_id):
        self.message = message
        self.device_id = device_id
        self.method = "Push"

    def send(self):
        # and AGAIN again, the push notification logic would be here, but for now, it's just another print-check
        return f"🔔 Sending push to device {self.device_id}: {self.message}"


# Step 2: ✅ Create ONE factory with ALL creation logic
class NotificationFactory:
    """
    The ONLY place that knows how to create notifications!
    Everyone in your app uses this factory.
    """

    # Map of notification method → notification class
    _notification_types = {
        "email": EmailNotification,
        "sms": SMSNotification,
        "push": PushNotification
    }

    @staticmethod
    def create(method, message, contact_info):
        """
        This is the ONLY function that creates notifications.
        All parts of your app use this!
        """
        method = method.lower()

        # Look up the notification class
        notification_class = NotificationFactory._notification_types.get(method)

        if notification_class:
            return notification_class(message, contact_info)
        else:
            raise ValueError(f"Unknown notification method: {method}")

    @staticmethod
    def add_notification_type(method, notification_class):
        """Easy to add new notification types!"""
        NotificationFactory._notification_types[method] = notification_class
        print(f"✅ Registered new notification type: {method}")


# Step 3: ✅ Now everyone just uses the factory!

# In UserSignup.js:
# this is basically us saying "I need a welcome notification, I don't care how it's created, I just know that it's a push notification and I want it to say `Welcome to our To-Do App!`"
def send_welcome_notification(method, contact):
    # this new function will call the NotificationFactory to create the notification for us, and we just input the message, contact, and method (email/sms/push)
    """Simple! Just call the factory"""
    message = "Welcome to our To-Do App!"
    notification = NotificationFactory.create(method, message, contact)
    return notification.send()


# In TaskReminder.js:
# in this one, we're saying "I need a reminder notification, I don't care how it's created --- I just want a notification that tells the user their task is due soon"
def send_task_due_notification(method, contact, task_name):
    """Also simple! Same factory call"""
    message = f"Reminder: '{task_name}' is due soon!"
    notification = NotificationFactory.create(method, message, contact)
    return notification.send()


# In PasswordReset.js:
# same here, it's just "I want a password reset notif with a reset code"
def send_password_reset_notification(method, contact, reset_code):
    """Clean and simple!"""
    message = f"Your reset code is: {reset_code}"
    notification = NotificationFactory.create(method, message, contact)
    return notification.send()

# So, when your To-do list app grows (and more features are added), and more notification types are needed, we can just use the NotificationFactory to create them (and just input the message, contact, and method) without worrying about the details of how each notification is created!

# So, we could add, say a "Custom User Notification" that users set on their own, or even a "Past Deadline Notification".


print("=== With Factory Pattern ===\n")

# Different parts of the app sending notifications
result1 = send_welcome_notification("email", "user@example.com")
print(f"Signup: {result1}")

result2 = send_task_due_notification("sms", "+1234567890", "Buy groceries")
print(f"Reminder: {result2}")

result3 = send_password_reset_notification("push", "device_abc123", "789456")
print(f"Password Reset: {result3}")

print("\n✅ Benefits:")
print("  • Creation logic in ONE place (the factory)")
print("  • Add WhatsApp? Just update the factory!")
print("  • Change email? Just update the factory!")
print("  • All features automatically get the updates")

# ✅ Easy to extend!
print("\n--- Adding WhatsApp Notifications ---")

class WhatsAppNotification:
    def __init__(self, message, phone):
        self.message = message
        self.phone = phone
        self.method = "WhatsApp"

    def send(self):
        return f"💬 Sending WhatsApp to {self.phone}: {self.message}"

# Register with factory
NotificationFactory.add_notification_type("whatsapp", WhatsAppNotification)

# Everyone can now use WhatsApp!
result4 = send_welcome_notification("whatsapp", "+63987654321")
print(f"New user: {result4}")
```

**Output:**

```
=== With Factory Pattern ===

Signup: 📧 Sending email to user@example.com: Welcome to our To-Do App!
Reminder: 📱 Sending SMS to +1234567890: Reminder: 'Buy groceries' is due soon!
Password Reset: 🔔 Sending push to device_abc123: Your reset code is: 789456

✅ Benefits:
  • Creation logic in ONE place (the factory)
  • Add WhatsApp? Just update the factory!
  • Change email? Just update the factory!
  • All features automatically get the updates

--- Adding WhatsApp Notifications ---
✅ Registered new notification type: whatsapp
New user: 💬 Sending WhatsApp to +639235685111: Welcome to our To-Do App!
```

### 💡 **Important Clarification**

**"Wait, we still have EmailNotification, SMSNotification, etc. What's the difference?"**

Great question! The factory pattern doesn't eliminate classes. Here's what it DOES:

| Without Factory                                 | With Factory                          |
| ----------------------------------------------- | ------------------------------------- |
| ❌ Creation logic scattered in 10+ functions    | ✅ Creation logic in ONE factory      |
| ❌ Update 10+ functions to add WhatsApp         | ✅ Update ONE factory to add WhatsApp |
| ❌ Easy to forget updating some functions       | ✅ Impossible to forget - one place   |
| ❌ Different functions might create differently | ✅ Consistent creation everywhere     |

**The key benefit:** Centralized creation logic, not elimination of classes!

**Real-world benefit:** When you add WhatsApp notifications, you update ONE file (the factory), and suddenly user signup, task reminders, password resets - everything can send WhatsApp messages automatically!

### � **TL;DR: How to Implement Factory Pattern**

**In 3 Simple Steps:**

1. **Create a dictionary/map** that links types to classes:

   ```python
   _notification_types = {
       "email": EmailNotification,
       "sms": SMSNotification,
       "push": PushNotification
   }
   ```

   Note: these should be similar classes that implement the same interface (e.g. they all have a `send()` method) so that the factory can return any of them and the rest of your code can use them interchangeably.

2. **Create a static method** that looks up and creates objects:

   ```python
   @staticmethod
   def create(method, message, contact):
       notification_class = Factory._notification_types.get(method)
       if notification_class:
           return notification_class(message, contact)
       else:
           raise ValueError(f"Unknown type: {method}")
   ```

3. **Use the factory everywhere** instead of creating objects directly:
   ```python
   # Don't do: EmailNotification(msg, addr)
   # Do this: NotificationFactory.create("email", msg, addr)
   ```

**That's it!** Now all object creation is centralized in one place.

### 🔑 **Key Takeaways**

- ✅ **Centralized Logic**: One place to create all notification types
- ✅ **Easy to Extend**: Add WhatsApp? Update one factory, all features get it
- ✅ **Consistent**: Everyone creates notifications the same way
- ✅ **Maintainable**: Change email setup once, everyone benefits

###### _*ECQ1: Your go-to Food Place in Miagao*_

---

## Pattern 3: Builder Pattern

### 🎯 **Purpose**

Build complex objects step-by-step with a clean, readable API. Instead of passing 10 parameters to a constructor (where you might forget what each parameter means), you build the object one piece at a time with clearly-named methods.

### 📖 **Concept in Plain English**

Sometimes you need to create objects with lots of optional details. For example, a task might need: description, priority, due date, tags, assignee, etc. But not all tasks need all fields!

Without Builder Pattern, you'd have constructors with 8+ parameters (most of them optional), and it's confusing to remember which parameter is which.

With Builder Pattern, you build the object step-by-step using clearly-named methods, only setting the fields you actually need!

**Think of it like ordering a custom sandwich:** Instead of saying "I want option 1, option 2, no, yes, no, extra, no" - you say "I want turkey, add lettuce, add tomato, extra cheese, toasted".

### 🎨 **Visual Diagram**

![SKELINGTON](whats.jpg)

```
WITHOUT Builder:
════════════════
Constructor with 8 parameters:
┌──────────────────────────────────────────────────────────┐
│ Task("Write report", "school", "high", "2026-02-25",     │
│      ["urgent"], "john@mail.com", True, "metrics")       │
└──────────────────────────────────────────────────────────┘
         ▲           ▲        ▲          ▲
         │           │        │          │
      What's       What's   What's    What's
       this?       this?    this?     this?


❌ Confusing! Which parameter is which?
❌ Easy to swap parameters accidentally
❌ Simple tasks still need: Task("Buy milk", None, None, None, [], None, False, "")


WITH Builder:
═════════════
┌──────────────────────────────────────────────────────────┐
│  TaskBuilder()                        ┌────────────────┐ │
│    │                                  │ Empty Task     │ │
│    ├─.set_description("Write report")─┤ + description  │ │
│    │                                  └────────────────┘ │
│    ├─.set_category("school")──────────┤ + category     │ │
│    │                                  └────────────────┘ │
│    ├─.set_priority("high")────────────┤ + priority     │ │
│    │                                  └────────────────┘ │
│    ├─.set_due_date("2026-02-25")──────┤ + due_date     │ │
│    │                                  └────────────────┘ │
│    ├─.add_tags("urgent", "docs")──────┤ + tags         │ │
│    │                                  └────────────────┘ │
│    ├─.assign_to("john@mail.com")──────┤ + assignee     │ │
│    │                                  └────────────────┘ │
│    ├─.with_reminder()─────────────────┤ + reminder     │ │
│    │                                  └────────────────┘ │
│    └─.build()─────────────────────────> Complete Task!  │
└──────────────────────────────────────────────────────────┘

✅ Crystal clear what each method does!
✅ Only set the fields you need!
✅ Simple task:  TaskBuilder().set_description("Buy milk").build()
```

### 📝 **Pseudocode**

```
PSEUDOCODE (What happens INSIDE the builder):
----------------------------------------------
CLASS TaskBuilder:
    FUNCTION __init__():
        CREATE empty task object

    FUNCTION set_description(desc):
        SET task.description = desc
        RETURN self  # For method chaining

    FUNCTION set_priority(priority):
        VALIDATE priority is valid
        SET task.priority = priority
        RETURN self

    FUNCTION add_tags(*tags):
        ADD tags to task.tags list
        RETURN self

    FUNCTION build():
        VALIDATE task is complete // "complete" as in it has the required fields (e.g. description), not "completed" as in "done"
        RETURN finished task
```

### 🚫 **Anti-Example: Without Builder Pattern**

```python
# ❌ PROBLEMATIC: Constructor with too many parameters

from datetime import datetime

class Task:
    def __init__(self,
                 description,
                 category="personal",
                 priority="medium",
                 due_date=None,
                 tags=None,
                 assignee=None,
                 send_reminder=False,
                 notes=""):
        # ❌ Too many parameters! I saw you all do this in your 126 projects (creating JS objects with 10+ fields in the constructor)
        self.description = description
        self.category = category
        self.priority = priority
        self.due_date = due_date
        self.tags = tags or []
        self.assignee = assignee
        self.send_reminder = send_reminder
        self.notes = notes

    #  Just a helper function to show the task details
    def display(self):
        info = [f"📋 Task: {self.description}"]
        info.append(f"   Category: {self.category}")
        info.append(f"   Priority: {self.priority}")
        if self.due_date:
            info.append(f"   Due: {self.due_date}")
        if self.tags:
            info.append(f"   Tags: {', '.join(self.tags)}")
        if self.assignee:
            info.append(f"   Assigned to: {self.assignee}")
        if self.send_reminder:
            info.append(f"   🔔 Reminder ON")
        if self.notes:
            info.append(f"   Notes: {self.notes}")
        return "\n".join(info)


print("=== Without Builder Pattern ===\n")

# ❌ Problem 1: Hard to remember parameter order
task1 = Task(
    "Write project report",
    "school",
    "high", #High? What's this? Priority? State of being?
    "2026-02-25",
    ["urgent", "documentation"],
    "john@example.com",
    True, #True what? What's this?
    "Include Q1 metrics"
)

print("Task 1:")
print(task1.display())
print()

# ❌ Problem 2: Many None values for unused parameters
task2 = Task(
    "Buy groceries",
    category="personal",
    priority="low",
    due_date=None,        # Not needed
    tags=None,            # Not needed
    assignee=None,        # Not needed
    send_reminder=False,  # Not needed
    notes=""              # Not needed
)

print("Task 2:")
print(task2.display())
print()

# ❌ Problem 3: Easy to mix up parameters
# What does each parameter mean again?
task3 = Task("Call dentist", "personal", "medium") #what's medium??
print("Task 3:")
print(task3.display())
```

**Output:**

```
=== Without Builder Pattern ===

Task 1:
📋 Task: Write project report
   Category: school
   Priority: high
   Due: 2026-02-25
   Tags: urgent, documentation
   Assigned to: john@example.com
   🔔 Reminder ON
   Notes: Include Q1 metrics

Task 2:
📋 Task: Buy groceries
   Category: personal
   Priority: low

Task 3:
📋 Task: Call dentist
   Category: personal
   Priority: medium
```

**Problems:**

- 🔴 **Too Many Parameters**: Hard to remember what position each parameter is in
- 🔴 **Poor Readability**: What does `True` mean? Or `"medium"`?
- 🔴 **Lots of Defaults**: Simple tasks still need to pass many None/empty values
- 🔴 **Error Prone**: Easy to swap parameters (`due_date` and `assignee` - which is which?)
- 🔴 **Hard to Extend**: Adding a new field means updating ALL constructor calls

### ✅ **Solution: With Builder Pattern**

```python
# ✅ SOLUTION: Builder creates tasks step-by-step

from datetime import datetime

class Task:
    def __init__(self):
        # Start with empty (or default) values
        self.description = ""
        self.category = "personal"
        self.priority = "medium"
        self.due_date = None
        self.tags = []
        self.assignee = None
        self.send_reminder = False
        self.notes = ""

    # same helper function to show task details
    def display(self):
        info = [f"📋 Task: {self.description}"]
        info.append(f"   Category: {self.category}")
        info.append(f"   Priority: {self.priority}")
        if self.due_date:
            info.append(f"   Due: {self.due_date}")
        if self.tags:
            info.append(f"   Tags: {', '.join(self.tags)}")
        if self.assignee:
            info.append(f"   Assigned to: {self.assignee}")
        if self.send_reminder:
            info.append(f"   🔔 Reminder ON")
        if self.notes:
            info.append(f"   Notes: {self.notes}")
        return "\n".join(info)


# ✅ BUILDER: Build tasks step-by-step
class TaskBuilder:
    # create empty Task object to build on
    def __init__(self):
        self.task = Task()

    #### SETTER: sets each field with validation and returns self for chaining ####
    def set_description(self, description):
        """Set the task description"""
        if not description or not description.strip():
            raise ValueError("Description cannot be empty")
        self.task.description = description.strip()
        return self  # Return self for method chaining

    def set_category(self, category):
        """Set the task category"""
        valid_categories = ["school", "personal", "others", "urgent"]
        if category not in valid_categories:
            raise ValueError(f"Category must be one of: {valid_categories}")
        self.task.category = category
        return self

    def set_priority(self, priority):
        """Set the task priority"""
        valid_priorities = ["low", "medium", "high", "critical"]
        if priority not in valid_priorities:
            raise ValueError(f"Priority must be one of: {valid_priorities}")
        self.task.priority = priority
        return self

    def set_due_date(self, due_date):
        """Set the due date"""
        self.task.due_date = due_date
        return self

    def add_tag(self, tag):
        """Add a single tag"""
        if tag and tag.strip():
            self.task.tags.append(tag.strip())
        return self

    def add_tags(self, *tags):
        """Add multiple tags at once"""
        for tag in tags:
            if tag and tag.strip():
                self.task.tags.append(tag.strip())
        return self

    def assign_to(self, assignee):
        """Assign the task to someone"""
        self.task.assignee = assignee
        return self

    def with_reminder(self):
        """Enable reminder for this task"""
        self.task.send_reminder = True
        return self

    def add_notes(self, notes):
        """Add notes to the task"""
        self.task.notes = notes
        return self

    #### BUILDER: Finalize and return the built task ####
    def build(self):
        """Build and return the final task"""
        if not self.task.description:
            raise ValueError("Task must have a description")
        return self.task


# ✅ USAGE: Clean, readable task creation
print("=== With Builder Pattern ===\n")

# ✅ Example 1: Complex task with many details
print("--- Example 1: Complex School Task ---")
task1 = (TaskBuilder()
    .set_description("Write project report")
    .set_category("school")
    .set_priority("high")
    .set_due_date("2026-02-25")
    .add_tags("urgent", "documentation", "Q1")
    .assign_to("john@example.com")
    .with_reminder()
    .add_notes("Include Q1 metrics and team feedback")
    .build())

print(task1.display())
print()

# ✅ Example 2: Simple task with minimal details
print("--- Example 2: Simple Personal Task ---")
task2 = (TaskBuilder()
    .set_description("Buy groceries")
    .set_category("personal")
    .set_priority("low")
    .build())

print(task2.display())
print()

# ✅ Example 3: Only set what you need (just 3 fields instead of 8)
print("--- Example 3: Task with Tags Only ---")
task3 = (TaskBuilder()
    .set_description("Call dentist")
    .add_tag("health")
    .add_tag("phone-call")
    .build())

print(task3.display())
print()

# ✅ Example 4: Validation catches errors early
print("--- Example 4: Error Handling ---")
try:
    invalid_task = (TaskBuilder()
        .set_category("school")
        .set_priority("high")
        .build())  # ❌ No description!
except ValueError as e:
    print(f"❌ Caught error: {e}")

try:
    invalid_task2 = (TaskBuilder()
        .set_description("Some task")
        .set_priority("super-high")  # ❌ Invalid priority!
        .build())
except ValueError as e:
    print(f"❌ Caught error: {e}")

# ✅ Example 5: Build multiple similar tasks
print("\n--- Example 5: Building Multiple Similar Tasks ---")

# Create a base builder configuration
def create_school_task_builder():
    """Helper function to create builder with school defaults"""
    return (TaskBuilder()
        .set_category("school")
        .set_priority("high")
        .with_reminder())

# Now create variations easily
task4 = (create_school_task_builder()
    .set_description("Review code")
    .add_tag("code-review")
    .build())

task5 = (create_school_task_builder()
    .set_description("Update documentation")
    .add_tag("docs")
    .build())

task6 = (create_school_task_builder()
    .set_description("Team standup")
    .set_due_date("2026-02-21")
    .build())

print(f"Created {task4.description} - Priority: {task4.priority}")
print(f"Created {task5.description} - Priority: {task5.priority}")
print(f"Created {task6.description} - Due: {task6.due_date}")
```

**Output:**

```
=== With Builder Pattern ===

--- Example 1: Complex School Task ---
📋 Task: Write project report
   Category: school
   Priority: high
   Due: 2026-02-25
   Tags: urgent, documentation, Q1
   Assigned to: john@example.com
   🔔 Reminder ON
   Notes: Include Q1 metrics and team feedback

--- Example 2: Simple Personal Task ---
📋 Task: Buy groceries
   Category: personal
   Priority: low

--- Example 3: Task with Tags Only ---
📋 Task: Call dentist
   Category: personal
   Priority: medium
   Tags: health, phone-call

--- Example 4: Error Handling ---
❌ Caught error: Task must have a description
❌ Caught error: Priority must be one of: ['low', 'medium', 'high', 'critical']

--- Example 5: Building Multiple Similar Tasks ---
Created Review code - Priority: high
Created Update documentation - Priority: high
Created Team standup - Due: 2026-02-21
```

### � **TL;DR: How to Implement Builder Pattern**

**In 4 Simple Steps:**

1. **Create a builder class** that holds the object being built:

   ```python
   class TaskBuilder:
       def __init__(self):
           self.task = Task()  # Create empty object
   ```

2. **Add setter methods** that return `self` for chaining:

   ```python
   def set_description(self, desc):
       self.task.description = desc
       return self  # Important!
   ```

3. **Add a `build()` method** that validates and returns the object:

   ```python
   def build(self):
       if not self.task.description:
           raise ValueError("Missing required field")
       return self.task
   ```

4. **Use it with method chaining:**
   ```python
   task = (TaskBuilder()
       .set_description("My task")
       .set_priority("high")
       .build())
   ```

**That's it!** Now you can build complex objects with a readable, fluent API.

### 🔑 **Key Takeaways**

- ✅ **Readable**: Each method name clearly shows what it does
- ✅ **Flexible**: Only set the fields you need
- ✅ **Validated**: Errors caught during building, not later
- ✅ **Method Chaining**: Build entire object in one fluent expression
- ✅ **Reusable**: Create helper functions for common configurations
- ✅ **Extendable**: easy to add new parameters

### 🆚 **Builder vs Factory: What's the Difference?**

**Both patterns create objects, but they solve different problems!**

| Aspect                | Factory Pattern                                  | Builder Pattern                                |
| --------------------- | ------------------------------------------------ | ---------------------------------------------- |
| **Main Purpose**      | Create different TYPES of objects                | Create ONE type with many optional fields      |
| **Problem It Solves** | "I need to create email/SMS/push notifications"  | "This constructor has 10 parameters!"          |
| **What It Creates**   | Different classes (EmailNotification, SMS, etc.) | One class with different configurations (Task) |
| **Creation Logic**    | Decides WHICH class to instantiate               | Assembles ONE object piece-by-piece            |
| **Input**             | Type identifier ("email", "sms", "push")         | Step-by-step method calls                      |
| **Output**            | Different object types                           | Same object type, different configurations     |
| **Method Chaining**   | Usually not                                      | Yes! That's the point!                         |
| **Use When**          | You have multiple related classes to create      | You have one class with many optional fields   |

**Example to Clarify:**

```python
# FACTORY: Creates DIFFERENT types of notifications
notification1 = NotificationFactory.create("email", "Hello", "user@example.com")
# → Returns EmailNotification object

notification2 = NotificationFactory.create("sms", "Hello", "+123456789")
# → Returns SMSNotification object (different class!)


# BUILDER: Creates ONE type (Task) with different configurations
task1 = (TaskBuilder()
    .set_description("Simple task")
    .build())
# → Returns Task object with minimal fields

task2 = (TaskBuilder()
    .set_description("Complex task")
    .set_priority("high")
    .set_due_date("2026-02-25")
    .add_tags("urgent")
    .with_reminder()
    .build())
# → Returns Task object with many fields (same class, just more configured!)
```

**Quick Decision Guide:**

- 📌 **Use Factory** when you have code like: `if type == "A": create ClassA() elif type == "B": create ClassB()`
- 📌 **Use Builder** when you have code like: `MyClass(param1, param2, param3, param4, param5, param6, param7, param8)` and most are optional

**Can You Use Both Together?** YES! You could have a Factory that creates different types of Builders, or a Builder that uses a Factory internally. But start simple - use them separately first!

---

## Best Practices

### ✅ **When to Use Each Pattern**

| Pattern       | Use When                                    | Example                                   |
| ------------- | ------------------------------------------- | ----------------------------------------- |
| **Singleton** | Need exactly one instance                   | Task manager, database connection, logger |
| **Factory**   | Creating different types of related objects | Different task types, notification types  |
| **Builder**   | Objects with many optional parameters       | Complex tasks, configuration objects      |

### 💡 **General Tips**

1. **Start Simple**: Don't use patterns until you need them
2. **One Pattern at a Time**: Master each pattern before combining them
3. **Think About Testing**: Patterns should make testing easier, not harder
4. **Document Your Choice**: Explain why you used a pattern in comments

### ⚠️ **Common Mistakes to Avoid**

- ❌ Using Singleton for everything (makes testing hard)
- ❌ Creating factories for simple objects
- ❌ Over-complicating builders with too many methods
- ❌ Forgetting to validate inputs in builders
- ❌ Using patterns just because they exist

---

## Summary

### 🎯 **What We Learned**

We explored three creational patterns through a to-do list application:

1. **🏗️ Singleton Pattern**
   - Ensures only ONE instance exists
   - Perfect for: Task managers, database connections, configuration
   - Key method: `__new__()` to control instance creation

2. **🏭 Factory Pattern**
   - Creates different TYPES of objects (email, SMS, push notifications)
   - Perfect for: Notification systems, document creators, payment processors
   - Key benefit: Centralized creation logic in one place

3. **👷 Builder Pattern**
   - Builds ONE complex object step-by-step
   - Perfect for: Tasks with many optional fields, configuration objects
   - Key benefit: Readable, flexible object construction

### 🚀 **Quick Reference**

```python
# Singleton: Get the one and only instance
manager = TaskManager()
another_ref = TaskManager()  # Same instance!

# Factory: Create different types of notifications
email = NotificationFactory.create("email", "Hello!", "user@example.com")
sms = NotificationFactory.create("sms", "Hello!", "+1234567890")

# Builder: Build complex tasks step-by-step
task = (TaskBuilder()
    .set_description("Call client")
    .set_priority("high")
    .set_due_date("2026-02-25")
    .with_reminder()
    .build())
```

### 📚 **Next Steps**

- Practice implementing these patterns in your projects
- Try combining patterns (e.g., Singleton TaskManager that uses a NotificationFactory)
- Explore other design patterns (Structural, Behavioral)
- Remember: Patterns are tools, use them when they solve real problems!

**Key Reminder:** Don't use patterns just to use them. Use them when:

- You see yourself repeating the same code pattern
- Your constructors are getting too complex
- You need to centralize object creation logic
- You need to ensure only one instance exists

---

> **"Design patterns are not about creating perfect code. They're about creating code that others (including future you) can understand and maintain."**

---

_Happy coding! 🎉_
