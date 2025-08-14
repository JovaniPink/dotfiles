# https://docs.docker.com/reference/dockerfile/
# Use a lightweight Python 3.11 base image for efficiency
FROM python:3.11-slim

# Set the working directory for the application
WORKDIR /app

# Copy the .env file
COPY config.yaml .

# Copy the Service Account JSON file
COPY JSON_SERVICE_ACCOUNT_KEY.json .

# Copy the requirements file and install dependencies
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of your application code
COPY . .

# Set the default command to run your Python application
CMD [ "python", "./main.py" ]
