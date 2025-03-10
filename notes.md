Run all comammands via docker compose

bash
```
docker-compose run --rm app sh -c "python manage.py collectstatic"

docker-compose: Runs a Docker Compose command
run: Will start a specific conatiner defined in config
--rm: removes any existing container
app: The Name of the service
sh -c: passes in a single shell command
"xxxx": command to run inside the container
--------------------------------------------------------------------------------------------------------------------------------------

Great questions! Let’s break them down step by step.

---

## **1️⃣ Difference Between Image and Container**
### **💿 What is a Docker Image?**
- An **image** is like a **blueprint** or a **template** for creating containers.
- It contains everything needed to run an application:
  - OS (e.g., Alpine Linux)
  - Python
  - Your code
  - Dependencies (like Django, djangorestframework, etc.)
- **Images are read-only** and can be reused multiple times.

### **📦 What is a Docker Container?**
- A **container** is a running instance of an image.
- When you **run** an image, it creates a **container** (just like running an `.exe` file creates a process).
- Containers are **isolated** environments.
- They **can be started, stopped, and deleted** without affecting the original image.

### **How They Work Together in Your Project?**
1. **Dockerfile creates the Image.**
2. **Docker Compose runs a Container from the Image.**

---

## **2️⃣ How Do `Dockerfile` and `docker-compose.yaml` Work Together?**
Think of it like:
- **Dockerfile** = "Recipe for making the dish"
- **docker-compose.yaml** = "How and where to serve the dish"

Here’s the complete flow in **your project**:

---

### **🛠 Step 1: Build the Image (from Dockerfile)**
When you run:
```bash
docker-compose build
```
1. **Docker reads the `Dockerfile`** and:
   - Downloads `python:3.9-alpine3.13` as the **base image**.
   - Copies your `requirements.txt` and `requirements.dev.txt` into the image.
   - Installs dependencies inside a **virtual environment**.
   - Copies your Django `app` folder into the image.
   - Creates a lightweight Linux user (`django-user`).
   - Sets environment variables.

2. **Once built, the image is stored on your machine** and can be reused.

---

### **🚀 Step 2: Start a Container (using `docker-compose.yaml`)**
When you run:
```bash
docker-compose up
```
1. **Docker Compose reads `docker-compose.yaml`** and:
   - Uses the built **image**.
   - Passes **build arguments (`DEV=true`)** to enable dev dependencies.
   - Maps **port 8000 (container) → port 8000 (host)** so you can access the app.
   - Syncs your local `app` folder to the container (so changes reflect live).
   - Runs this command:
     ```bash
     python manage.py runserver 0.0.0.0:8000
     ```
     This starts your Django REST API.

2. **Your app is now running inside a container!**
   - Access it at `http://localhost:8000/`
   - If you update your code, Docker sees changes (because of the `volumes` setting).

---

### **🔄 Step 3: Stopping and Restarting**
- To stop the running container:
  ```bash
  docker-compose down
  ```
- To restart it:
  ```bash
  docker-compose up
  ```

---

## **🛠 Full Workflow Summary**
1. **You define an image in `Dockerfile`.**
2. **You use `docker-compose.yaml` to configure how the app should run.**
3. **`docker-compose build` creates an image using the `Dockerfile` .**
4. **`docker-compose up` runs a container using that image.**
5. **You develop inside the container, and changes reflect in real-time (cuz we have `volumes`).**
6. **You can stop, restart, or delete containers without affecting the image.** 

---

## **🎯 Final Analogy**
Think of **Dockerfile** and **Docker Compose** like setting up a new gaming PC:
- **Dockerfile** → Installing Windows, drivers, software (Image = System Backup)
- **docker-compose.yaml** → Configuring monitor, peripherals, and launching a game (Container = Running System)

---

Let me know if you need more explanations. 🚀🔥