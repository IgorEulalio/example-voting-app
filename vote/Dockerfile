# Multi-stage build: build stage
FROM python:3.9-slim as build

WORKDIR /app

COPY requirements.txt /app/requirements.txt

# This will install the dependencies in /root/.local/bin which we will copy over later
RUN pip install --user -r requirements.txt

# Copy our code from the current folder to /app inside the container
COPY . .

# Multi-stage build: final stage
# Use an alpine image for reduced size and vulnerability surface
FROM python:3.9-alpine

# Create a non-root user
RUN adduser -D 1000

# Set the application directory
WORKDIR /app

# Copy only the dependencies installation from the 1st stage image
COPY --from=build /root/.local /root/.local
COPY --from=build /app .

# Make sure scripts in .local are usable:
ENV PATH=/root/.local/bin:$PATH

# Make port 80 available for links and/or publish
EXPOSE 80

# Change to a non-root user
USER 1000

# Define our command to be run when launching the container
CMD ["gunicorn", "app:app", "-b", "0.0.0.0:80", "--log-file", "-", "--access-logfile", "-", "--workers", "4", "--keep-alive", "0"]
