## **Number Classification API Development. HNG task 1**

## SYNOPSIS

This project addresses the need for a simple and readily available way to access information about the mathematical properties of numbers.  The Number Classification API provides this functionality, taking a number as input and returning a structured JSON response with key properties and a fun fact. This project allows you to touch upon all stages of the SDLC, from development and testing to deployment and monitoring.  This end-to-end experience is invaluable for understanding how different DevOps practices integrate.

## FEATURES

- **Number input:** Accepts integer numbers as input

- **Mathematical Property Calculation:** Determines whether a number is prime, odd, armstrong, perfect, digit sum etc.

- **Fun Fact Retrieval:** Fetches a fun fact about the number from an external API

- **JSON Output:** Returns all the calculated properties and the fun fact in a structured JSON format.

- **CORS Handling:** Implements Cross-Origin Resource Sharing (CORS) to allow access from different web domains

- **Deployment:**  Must be deployed to a publicly accessible endpoint.

**Development tools**

- VS code ( open source editor)

- Python (Lambda function)

- AWS Lambda (Serverless execution)

- API Gateway (Public API exposure)

- GitHub (Version control and CI/CD)


**Design Diagram**

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7sxpz7orydzj4vbqtej4.jpg)

**Project Structure**

```
Number-Classification-API/
│-- lambda_function.py  # Main Lambda function
│-- requirements.txt    # Python dependencies
│-- README.md           # Project documentation
│-- .gitignore          # Git ignore file
└── app.py              # Python scripts
```


**Setting up**

- Create a folder for your project.

- Open the folder on your VScode editor.

- create app.py and lambda-function.py files.

- Open your github account and create a repository (Number-Classifier-API).

**Clone the repository**
```
git clone https://github.com/Victor-divine/Number-Classification-API.git
```

**- Change your directory to the project folder and then to your repository on your terminal.**
```
cd HNG-Task
cd Number-Classification-API
```


**Create and Activate Virtual Environment**

```
python -m venv venv
Source venv\Scripts\activate
```

**Dependencies Installation**

```
pip install flask flask-cors
```

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0za8n3uwpjizldn7ngdq.PNG)


**Create the Flask App**

Put the app code to the app.py file.

```
from flask import Flask, request, jsonify
from flask_cors import CORS

app = Flask(__name__)
CORS(app)  # Enable CORS

# Function to check if a number is prime
def is_prime(n):
    if n < 2:
        return False
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            return False
    return True

# Function to check if a number is a perfect number
def is_perfect(n):
    return n == sum(i for i in range(1, n) if n % i == 0)

# Function to check if a number is an Armstrong number
def is_armstrong(n):
    digits = [int(d) for d in str(n)]
    power = len(digits)
    return n == sum(d**power for d in digits)

# Function to generate fun facts
def get_fun_fact(n):
    if is_armstrong(n):
        return f"{n} is an Armstrong number because {' + '.join([f'{d}^{len(str(n))}' for d in str(n)])} = {n}"
    elif is_prime(n):
        return f"{n} is a prime number because it has only two divisors: 1 and itself."
    elif is_perfect(n):
        return f"{n} is a perfect number because the sum of its proper divisors equals the number."
    else:
        return f"{n} is just an interesting number!"

@app.route('/api/classify-number', methods=['GET'])
def classify_number():
    num = request.args.get('number')

    if not num or not num.isdigit():
        return jsonify({"number": num, "error": True}), 400

    num = int(num)
    
    response = {
        "number": num,
        "is_prime": is_prime(num),
        "is_perfect": is_perfect(num),
        "properties": ["odd" if num % 2 else "even"],
        "digit_sum": sum(int(d) for d in str(num)),
        "fun_fact": get_fun_fact(num)
    }

    if is_armstrong(num):
        response["properties"].append("armstrong")

    return jsonify(response)

if __name__ == '__main__':
    app.run(debug=True)

```

- **Run the API Locally**

```
python app.py
```

- **Open your browser to test the API:**

```
http://127.0.0.1:5000/api/classify-number?number=153
```

**JSON Response**

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1fsb1hqaorwtcdp9r5oo.PNG)

**Deploy the API**

```
import json

def is_prime(n):
    if n < 2:
        return False
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            return False
    return True

def is_perfect(n):
    return n == sum(i for i in range(1, n) if n % i == 0)

def is_armstrong(n):
    digits = [int(d) for d in str(n)]
    power = len(digits)
    return n == sum(d**power for d in digits)

def get_fun_fact(n):
    if is_armstrong(n):
        return f"{n} is an Armstrong number because {' + '.join([f'{d}^{len(str(n))}' for d in str(n)])} = {n}"
    elif is_prime(n):
        return f"{n} is a prime number because it has only two divisors: 1 and itself."
    elif is_perfect(n):
        return f"{n} is a perfect number because the sum of its proper divisors equals the number."
    else:
        return f"{n} is just an interesting number!"

def lambda_handler(event, context):
    try:
        query_params = event.get("queryStringParameters", {})
        num = query_params.get("number", "")

        if not num.isdigit():
            return {
                "statusCode": 400,
                "headers": {
                    "Content-Type": "application/json",
                    "Access-Control-Allow-Origin": "*"
                },
                "body": json.dumps({"number": num, "error": True})
            }

        num = int(num)

        response = {
            "number": num,
            "is_prime": is_prime(num),
            "is_perfect": is_perfect(num),
            "properties": ["odd" if num % 2 else "even"],
            "digit_sum": sum(int(d) for d in str(num)),
            "fun_fact": get_fun_fact(num)
        }

        if is_armstrong(num):
            response["properties"].append("armstrong")

        return {
            "statusCode": 200,
            "headers": {
                "Content-Type": "application/json",
                "Access-Control-Allow-Origin": "*"
            },
            "body": json.dumps(response)
        }

    except Exception as e:
        return {
            "statusCode": 500,
            "headers": {
                "Content-Type": "application/json",
                "Access-Control-Allow-Origin": "*"
            },
            "body": json.dumps({"error": str(e)})
        }

```


**Package and Deploy to AWS Lambda**

```
zip function.zip lambda_function.py
```

**Create an API gateway**

- Choose HTTP API.

- Add Integration → Choose Lambda Function.

- Select your Lambda function (NumberClassifierAPI).

- Enter /api/classify-number as the route.

- Choose GET as the method.

- Then Deploy.


![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cgquzey9795wet1j4u8p.PNG)

**Test your API**
Once deployed, copy the API Gateway URL and test it in your browser

```
https://qn5p0npw11.execute-api.us-east-1.amazonaws.com/Test/api/classify-number?number=371
```
**JSON Output.**

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/n0o7ayzas5ntj6e14qj0.PNG)


**Enable CORS in API Gateway.**


![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/gfl2zdjaz1rcd4kxcewz.PNG)


##<u>Significance of the project.</u>
The project, though simple, offers a sandbox for practicing and demonstrating a wide range of DevOps skills. Building, deploying, and managing a real (small) application provides practical experience highly valued in the field.  Incorporating advanced practices makes the project even more impressive and valuable.


Editor Basics
Use Markdown to write and format posts.
Commonly used syntax
Embed rich content such as Tweets, YouTube videos, etc. Use the complete URL: {% embed https://... %}. See a list of supported embeds.
In addition to images for the post's content, you can also drag and drop a cover image.
