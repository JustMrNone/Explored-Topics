Let me provide a **complete overview** of the steps we discussed to set up your project, including **Gunicorn**, **Postgres**, and all other configurations:

* * * * *

**1\. Initial Setup**
---------------------

### **Postgres Database Configuration**

We used a **Postgres 13** Docker image, and the following configuration was included in the `docker-compose.yml` file:

yaml

Copy code

`db:
  image: postgres:13
  environment:
    POSTGRES_DB: QuantumCo
    POSTGRES_USER: mrnone
    POSTGRES_PASSWORD: V@p9FmZt*2@4t1hx3EiF
  volumes:
    - db_data:/var/lib/postgresql/data`

The above sets up:

-   A **database** named `QuantumCo`.
-   A **user** named `mrnone` with a secure password.
-   Persistent storage for database files using `db_data`.

* * * * *

### **Gunicorn Setup**

The Gunicorn WSGI server was set up for serving the Django application. The `Dockerfile.prod` defined how Gunicorn is installed and used.

#### **Dockerfile.prod**

dockerfile

Copy code

`# Start from Python 3.13 base image
FROM python:3.13-slim

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

# Set the working directory in the container
WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the project files into the container
COPY . .

# Gunicorn command to run the Django app
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "QuantumCo.wsgi:application"]`

* * * * *

### **Gunicorn Command in Docker Compose**

In the `docker-compose.yml`, the **web** service was configured to use Gunicorn:

yaml

Copy code

`web:
  build:
    context: .  # Root of your Django project
    dockerfile: Dockerfile.prod  # Dockerfile for Django (Gunicorn)
  command: gunicorn --bind 0.0.0.0:8000 QuantumCo.wsgi:application
  volumes:
    - .:/app  # Mount the current directory to the /app directory in the container
  expose:
    - "8000"  # Expose port 8000 for Nginx
  depends_on:
    - db  # Ensure this service starts after Postgres`

This connects Django (via Gunicorn) to Nginx, with Nginx reverse-proxying all requests.

* * * * *

**2\. Nginx Configuration**
---------------------------

We set up **Nginx** as a reverse proxy for Gunicorn and for serving static and media files. The full `nginx.conf` is as follows:

nginx

Copy code

`events {
    worker_connections 1024;  # Adjust based on system requirements
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    server {
        listen 80;
        server_name quantum-co.dev www.quantum-co.dev;

        # Redirect HTTP to HTTPS
        return 301 https://$host$request_uri;
    }

    server {
        listen 443 ssl;
        server_name quantum-co.dev www.quantum-co.dev;

        ssl_certificate /etc/letsencrypt/live/quantum-co.dev/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/quantum-co.dev/privkey.pem;
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers HIGH:!aNULL:!MD5;

        root /app;

        # Serve static files
        location /static/ {
            alias /home/none/production/projects/Quantum-Co/staticfiles/;
        }

        # Serve media files
        location /media/ {
            alias /home/none/production/projects/Quantum-Co/media/;
        }

        # Proxy requests to Gunicorn
        location / {
            proxy_pass http://web:8000;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}`

This configuration:

1.  Redirects HTTP traffic to HTTPS.
2.  Proxies requests to Gunicorn.
3.  Serves static and media files.

* * * * *

**3\. SSL Certificates**
------------------------

We used **Certbot** to obtain SSL certificates for the domain. The steps included:

### Certbot Command:

bash

Copy code

`sudo certbot --nginx -d quantum-co.dev -d www.quantum-co.dev`

During setup, we selected option `2` to redirect HTTP traffic to HTTPS:

css

Copy code

`Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 2`

### Checking Certificate Files:

bash

Copy code

`sudo ls -l /etc/letsencrypt/live/quantum-co.dev/`

Ensured Nginx had permission to read these files:

bash

Copy code

`sudo chmod -R 755 /etc/letsencrypt/live/
sudo chmod -R 755 /etc/letsencrypt/archive/`

* * * * *

**4\. .gitignore for Security**
-------------------------------

We excluded sensitive files from Git:

#### **Updated .gitignore**

plaintext

Copy code

`# Sensitive files
.env
*.pyc
__pycache__/
db.sqlite3`

### Removing Cached Files

bash

Copy code

`git rm --cached .env
git commit -m "Remove .env from repository"`

* * * * *

**5\. Troubleshooting**
-----------------------

We handled several issues during the setup process:

1.  **Port Conflicts:** Ensured no process was using ports `80` or `443`:

    bash

    Copy code

    `sudo lsof -i:80
    sudo lsof -i:443
    sudo systemctl stop nginx`

2.  **Permission Errors:** Adjusted permissions for SSL files to ensure Nginx could access them.

* * * * *

**6\. Cloudflare Configuration**
--------------------------------

In Cloudflare, we configured SSL/TLS mode to **Full (strict)**. This ensures:

1.  Encrypted communication between the browser and Cloudflare.
2.  Encrypted communication between Cloudflare and the origin server.

* * * * *

**Final `docker-compose.yml` File**
-----------------------------------

Here is the final `docker-compose.yml`:

yaml

Copy code

`version: '3'

services:
  web:
    build:
      context: .  # Root of your Django project
      dockerfile: Dockerfile.prod
    command: gunicorn --bind 0.0.0.0:8000 QuantumCo.wsgi:application
    volumes:
      - .:/app
    expose:
      - "8000"
    depends_on:
      - db

  db:
    image: postgres:13
    environment:
      POSTGRES_DB: QuantumCo
      POSTGRES_USER: mrnone
      POSTGRES_PASSWORD: V@p9FmZt*2@4t1hx3EiF
    volumes:
      - db_data:/var/lib/postgresql/data

  nginx:
    image: nginx:latest
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./staticfiles:/home/none/production/projects/Quantum-Co/staticfiles/
      - ./media:/app/media
    ports:
      - "80:80"
      - "443:443"
    depends_on:
      - web

volumes:
  db_data:`

* * * * *

**Final Status**
----------------

Your website is:

1.  Deployed with Docker containers (Postgres, Django/Gunicorn, Nginx).
2.  Accessible over HTTPS with proper SSL certificates.
3.  Integrated with Cloudflare for `Full (strict)` encryption.
4.  Configured to serve static and media files efficiently.

* * * * *

Let me know if you need further clarifications or help expanding on any part! 😊