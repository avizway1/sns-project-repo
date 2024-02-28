# sns-project-repo


**STEP 1: ADD REQUIRED REPO FROM OFFICIAL NODEJS WEBSITE.**

```bash
curl -sL https://rpm.nodesource.com/setup_16.x | sudo bash -
```

**STEP 2: INSTALL NODEJS.**

```bash
sudo yum install -y nodejs
```

**STEP 3: VERIFY INSTALLED VERSION.**

```bash
node -v
npm -v
```

**Initialise Node project**

```bash
npm init -y
```

**Install required dependencies**

```bash
npm install express aws-sdk body-parser
```


**STEP 4: CREATE A server.js FILE AND RUN IT.**

Create a file named `app.js` with the provided the below content.

***Make sure to Replace ACCESSKEY, SECRETKEY, SNS TOPIC ARN and REGION.***


```bash
// with accesskey and secret access key method
const express = require('express');
const bodyParser = require('body-parser');
const AWS = require('aws-sdk');

const app = express();
const port = process.env.PORT || 3000;

app.use(bodyParser.json());

// AWS SNS configuration
AWS.config.update({
  accessKeyId: 'YOUR-ACCESS-KEY',      // Replace with your keys
  secretAccessKey: 'YOUR-SECRET-KEY',  // Replace with your keys
  region: 'YOUR-REGION',              // Replace with your AWS region
});

const sns = new AWS.SNS();

// Route to handle email sending
app.post('/send-email', async (req, res) => {
  try {
    const { email, subject, message } = req.body;

    // AWS SNS Publish
    await sns.publish({
      TopicArn: 'YOUR-SNS-TOPIC-ARN',          // Replace with your SNS TOpic ARN
      Message: message,
      Subject: subject || 'Default Subject',
    }).promise();

    res.json({ success: true, message: 'Email sent successfully!' });
  } catch (error) {
    console.error('Error:', error);
    res.status(500).json({ success: false, message: 'Error sending email.' });
  }
});

// Default route handler serving HTML file
app.get('/', (req, res) => {
  res.sendFile(__dirname + '/index.html');
});

// Start server
app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});

```


***if you are deploying with-in an ec2 instance, Want to Use IAM Roles option, then refer below app.js file***

```bash
const express = require('express');
const bodyParser = require('body-parser');
const AWS = require('aws-sdk');

const app = express();
const port = process.env.PORT || 3000;

app.use(bodyParser.json());

// AWS SNS configuration with IAM role
AWS.config.region = 'ap-south-1'; 			// Replace with your AWS region
const sns = new AWS.SNS();

// Route to handle email sending
app.post('/send-email', async (req, res) => {
  try {
    const { email, subject, message } = req.body;

    // AWS SNS Publish
    await sns.publish({
      TopicArn: 'arn:aws:sns:ap-south-1:1234567890:My-S3-Alerts',	// Replace with your SNS TOpic ARN
      Message: message,
      Subject: subject || 'Default Subject',
    }).promise();

    res.json({ success: true, message: 'Email sent successfully!' });
  } catch (error) {
    console.error('Error:', error);
    res.status(500).json({ success: false, message: 'Error sending email.' });
  }
});

// Default route handler serving HTML file
app.get('/', (req, res) => {
  res.sendFile(__dirname + '/index.html');
});

// Start server
app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});

```


this script creates a basic web server that listens on default port 3000, reads the content of an `index.html` file, and sends it as the response when a request is made to the server. The server logs a message indicating that it is running on `http://localhost:3000`.

**STEP 5: Create Required Index.html file with below content.**

create an index.html file in same location where app.js is placed.

```bash
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>SNS project by AWS Avinash Reddy</title>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      background-color: #f4f4f4;
      margin: 0;
      padding: 0;
    }

    h1 {
      color: #333;
      text-align: center;
      padding: 20px;
      background-color: #4CAF50;
      margin: 0;
    }

    form {
      max-width: 400px;
      margin: 20px auto;
      background-color: #fff;
      padding: 20px;
      border-radius: 8px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }

    label {
      display: block;
      margin-bottom: 8px;
      color: #333;
    }

    input,
    textarea {
      width: 100%;
      padding: 8px;
      margin-bottom: 16px;
      box-sizing: border-box;
      border: 1px solid #ccc;
      border-radius: 4px;
    }

    button {
      background-color: #4CAF50;
      color: #fff;
      padding: 10px 15px;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }

    button:hover {
      background-color: #45a049;
    }

    footer {
      text-align: center;
      padding: 10px;
      background-color: #333;
      color: #fff;
      position: fixed;
      bottom: 0;
      width: 100%;
    }
  </style>
</head>
<body>
  <h1>SNS Project by Avinash</h1>
  <form id="emailForm" action="/send-email" method="post">
    <label for="email">Email:</label>
    <input type="email" id="email" name="email" required>
    <br>
    <label for="subject">Subject:</label>
    <input type="text" id="subject" name="subject">
    <br>
    <label for="message">Message:</label>
    <textarea id="message" name="message" required></textarea>
    <br>
    <button type="button" onclick="sendEmail()">Send Email</button>
  </form>

  <footer>
    A project by Avinash Reddy
  </footer>

  <script>
    async function sendEmail() {
      const email = document.getElementById('email').value;
      const subject = document.getElementById('subject').value;
      const message = document.getElementById('message').value;

      try {
        const response = await fetch('/send-email', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({ email, subject, message }),
        });

        const result = await response.json();
        alert(result.message);
      } catch (error) {
        console.error('Error:', error);
        alert('Error sending email.');
      }
    }
  </script>
</body>
</html>
```


**STEP 6: NODE.JS WILL INTERPRET AND EXECUTE THE CODE IN THE server.js FILE. THIS IS A COMMON PATTERN FOR RUNNING SERVER-SIDE JAVASCRIPT APPLICATIONS.**

```bash
node app.js
```

**After following these steps, your Node.js server should be up and running.**

**Make sure your ec2 instance security group io opened with required port i.e; 80/3000/any other port**



**Below Steps are optional, but recommended**

if you wan to run it as an application use PM2 tool.

**Command to install pm2**

```bash
npm install -g pm2
```

**list all running processes/apps.**

```bash
pm2 list   (or) pm ls
```

**Start app/services with name**

```bash
pm2 start app.js --name "my-app1"
```
**Stop app/services with name**

```bash
pm2 stop app.js --name "my-app1"
```

**Command to list the logs**

```bash
pm2 logs id/name
```

**Command to Monitor the resources**

```bash
pm2 monit
```

**To make service as startup**

```bash
pm2 startup
```

**Command to delete service/app**

```bash
pm2 delete name/id
```
