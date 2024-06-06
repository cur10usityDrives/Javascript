# NodeJS Time-Server Project

## Introduction

This project is a simple Node.js server that provides the current date and time via an API endpoint. 
It demonstrates the setup and configuration of a basic Node.js application using the Express framework.

## Features

- Provides the current date and time through a JSON API.
- Utilizes Node.js and Express for handling HTTP requests.
- Hosted on a Google Cloud Platform (GCP) Ubuntu virtual machine.

## Setup and Installation

### Prerequisites

- Google Cloud Platform (GCP) account.
- Basic knowledge of SSH and terminal commands.
- Node.js and npm installed on the VM.

### Step-by-Step Guide

1. **Create and Access the GCP VM**

   - Provision a new Ubuntu VM instance on Google Cloud Platform.
   - SSH into the VM instance.

2. **Update the Package List and Install Curl**

   ```bash
   sudo apt update
   sudo apt install curl
   ```

3. **Install Node.js and npm**

   The following command downloads and runs a script that configures your package manager to use the NodeSource repository. This repository contains the necessary packages for installing Node.js version 18.x. This script updates your system’s package sources to include the NodeSource repository, which is specifically configured to provide Node.js packages.

   ```bash
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt install nodejs
   ```

4. **Verify Node.js and npm Installation**

   ```bash
   node -v
   npm -v
   ```

5. **Set Up the Project Directory**

   ```bash
   mkdir time-server
   cd time-server
   npm init -y
   ```

6. **Install Express**

   ```bash
   npm install express
   ```

7. **Create the Server Script**

   Create a file named `time_server.js` and add the following content:

   ```javascript
    const http = require('http');
    const url = require('url');
    
    function parsetime(time) {
      return {
        hour: time.getHours(),
        minute: time.getMinutes(),
        second: time.getSeconds()
      };
    }
    
    function unixtime(time) {
      return { unixtime: time.getTime() };
    }
    
    function currenttime() {
      const now = new Date();
      return {
        year: now.getFullYear(),
        month: now.getMonth() + 1, // Months are zero-based
        date: now.getDate(),
        hour: now.getHours(),
        minute: now.getMinutes()
      };
    }
    
    const server = http.createServer((req, res) => {
      const parsedUrl = url.parse(req.url, true);
      const time = new Date(parsedUrl.query.iso);
      let result;
      if (parsedUrl.pathname === '/api/parsetime') {
        result = parsetime(time);
      } else if (parsedUrl.pathname === '/api/unixtime') {
        result = unixtime(time);
      } else if (parsedUrl.pathname === '/api/currenttime') {
        result = currenttime();
      }
    
      if (result) {
        res.writeHead(200, { 'Content-Type': 'application/json' });
        res.end(JSON.stringify(result));
      } else {
        res.writeHead(404);
        res.end();
      }
    });
    
    const port = Number(process.argv[2]) || 8000;

    function startServer(port) {
      server.listen(port, () => {
        console.log('Node server running on http://localhost:' + port);
      }).on('error', (err) => {
        if (err.code === 'EADDRINUSE') {
          console.log(`Port ${port} is in use, trying port ${port + 1}`);
          startServer(port + 1);
        } else {
          console.log(`Server error: ${err}`);
        }
      });
    }

    startServer(port);
   ```

8. **Run the Server**

   ```bash
   node time_server.js 8000
   ```

9. **Access the API**

   Open another cli and enter the following:

   ```bash
   curl http://localhost:8000/api/currenttime
   ```

## Testing

### Functional Testing

- **Endpoint Testing**:
  - **Valid Request**:
   ```bash
    curl http://localhost:8000/api/currenttime
   ```
    - **Expected Response**: JSON object with current date and time.
  - **Invalid Request**:
   ```bash
    curl http://localhost:8000/invalidendpoint
   ```
    - **Expected Response**: 404 Not Found.

### Tools

- **Curl**:
  ```bash
  curl http://localhost:8000/api/currenttime
  ```

## Conclusion

- **Successful Deployment**: The Node.js server was successfully deployed on a GCP Ubuntu VM.
- **Functionality Achieved**: The server effectively provides the current date and time via a simple API.
- **Skills Demonstrated**: Setup, configuration, and deployment of a Node.js application.
- **Foundation for Further Development**: The project can be expanded with additional features and endpoints.

## Author

Natnael Haile
