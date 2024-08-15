# Image of Linux Debian 10 Operating System to host the Application
FROM python:3.9-slim-buster

# Set the working directory inside the Docker container to "/src"
WORKDIR /src

# Copy the requirements.txt file from the local directory to the current working directory inside the Docker container
COPY requirements.txt .

# Install the necessary Python packages and dependencies for the application
RUN pip install --upgrade pip && pip install -r requirements.txt

# Copy the contents of the current directory (where the Dockerfile is located) to the current working directory inside the Docker container
COPY . .

# Run the application
CMD python app.py