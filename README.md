# Simple ML Application with Docker

## Overview

This project demonstrates the development, containerization, and deployment of a simple machine learning application using Docker. The application uses a decision tree classifier to predict the class of Iris flowers based on the Iris dataset.

## Prerequisites

- Basic understanding of Python programming
- Basic understanding of machine learning concepts
- Familiarity with Git and GitHub
- Basic knowledge of Docker

## Project Structure

ml-app/

├── app.py

├── Dockerfile

├── model.pkl

├── requirements.txt

└── train_model.py


## Instructions

### Step 1: Set Up the VM

1. **Update the System**
    ```sh
    sudo apt update
    sudo apt upgrade -y
    ```

2. **Install Necessary Packages**
    ```sh
    sudo apt install -y curl git
    ```

### Step 2: Install Docker

1. **Remove Old Versions**
    ```sh
    sudo apt remove docker docker-engine docker.io containerd runc
    ```

2. **Set Up the Docker Repository**
    ```sh
    sudo apt update
    sudo apt install -y apt-transport-https ca-certificates curl gnupg lsb-release
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```

3. **Install Docker Engine**
    ```sh
    sudo apt update
    sudo apt install -y docker-ce docker-ce-cli containerd.io
    ```

4. **Verify Docker Installation**
    ```sh
    sudo docker run hello-world
    ```

### Step 3: Create a Dockerfile for the ML Application

1. **Create Project Directory**
    ```sh
    mkdir ml-app
    cd ml-app
    ```

2. **Create a Dockerfile**
    ```Dockerfile
    # Use an official Python runtime as a parent image
    FROM python:3.9-slim
    
    # Set the working directory
    WORKDIR /usr/src/app
    
    # Copy the current directory contents into the container at /usr/src/app
    COPY . .
    
    # Install any needed packages specified in requirements.txt
    RUN pip install --no-cache-dir -r requirements.txt
    
    # Make port 80 available to the world outside this container
    EXPOSE 80
    
    # Run app.py when the container launches
    CMD ["python", "app.py"]
    ```

3. **Create requirements.txt File**
    ```plaintext
    Flask
    numpy
    pandas
    scikit-learn
    ```

### Step 4: Develop the Machine Learning Application

1. **Create a Simple ML Model**
    ```python
    # train_model.py
    from sklearn.datasets import load_iris
    from sklearn.tree import DecisionTreeClassifier
    import pickle
    
    # Load the Iris dataset
    iris = load_iris()
    X, y = iris.data, iris.target
    
    # Train a decision tree classifier
    clf = DecisionTreeClassifier()
    clf.fit(X, y)
    
    # Save the model to a file
    with open('model.pkl', 'wb') as f:
        pickle.dump(clf, f)
    ```

2. **Run the Model Training Script**
    ```sh
    python train_model.py
    ```

3. **Integrate the Model into the Flask App**
    ```python
    # app.py
    from flask import Flask, request, jsonify
    import pickle
    import numpy as np
    
    app = Flask(__name__)
    
    # Load the trained model
    with open('model.pkl', 'rb') as f:
        model = pickle.load(f)
    
    @app.route('/')
    def hello_world():
        return 'Hello, Docker!'
    
    @app.route('/predict', methods=['POST'])
    def predict():
        data = request.get_json(force=True)
        prediction = model.predict(np.array(data['input']).reshape(1, -1))
        return jsonify({'prediction': int(prediction[0])})
    
    if __name__ == '__main__':
        app.run(host='0.0.0.0', port=80)
    ```

4. **Update the Project Directory**

   Ensure your project directory contains the following files:
   - `Dockerfile`
   - `requirements.txt`
   - `train_model.py`
   - `app.py`
   - `model.pkl` (generated after running `train_model.py`)

### Step 5: Build and Run the Docker Container

1. **Build the Docker Image**
    ```sh
    sudo docker build -t ml-app .
    ```

2. **Run the Docker Container**
    ```sh
    sudo docker run -p 4000:80 ml-app
    ```

3. **Access the Application**

   Open your browser and navigate to [http://localhost:4000](http://localhost:4000) to see the running application.

4. **Test the ML Endpoint**

   Test the `/predict` endpoint using curl or Postman by sending a POST request with JSON data:
    ```sh
    curl -X POST http://localhost:4000/predict -H "Content-Type: application/json" -d '{"input": [5.1, 3.5, 1.4, 0.2]}'
    ```

### Step 6: Deploy the Application to GitHub

1. **Initialize a Git Repository**
    ```sh
    git init
    ```

2. **Add All Files and Commit**
    ```sh
    git add .
    git commit -m "Initial commit"
    ```

3. **Create a New Repository on GitHub**

   Create a new repository on GitHub and follow the instructions to push your local repository to GitHub:
    ```sh
    git remote add origin https://github.com/yourusername/your-repository.git
    git branch -M main
    git push -u origin main
    ```

## Additional Information

- **Updating Docker:** If you need to update Docker, run:
    ```sh
    sudo apt update
    sudo apt upgrade docker-ce
    ```

- **Common Issues:**
  - Ensure Docker daemon is running: `sudo systemctl start docker`
  - Check Docker status: `sudo systemctl status docker`
  - Use `docker logs <container_id>` to view container logs for debugging.

By following the steps above, you should be able to build, run, and deploy a simple machine learning application using Docker. If you encounter any issues, consult the Docker documentation or seek help from online communities.
