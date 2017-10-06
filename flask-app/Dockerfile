# Use a base image
FROM alpine:edge

# Install python and pip
RUN apk add --update py2-pip

# Install python modules needed by the python app
COPY requirements.txt /usr/src/app/
RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt

# Copy files required for the app to run
COPY app.py /usr/src/app/
COPY templates/index.html /usr/src/app/templates/

# Tell the port number the container should expoes
EXPOSE 5000

# Specify the command for running the application
CMD ["python", "usr/src/app/app.py"]
