Here's a complete working **Node.js + CodePipeline + CodeBuild** project using a GitHub repository named `nodejs-codepipeline-demo`.

---

## 📁 Project Repo: `nodejs-codepipeline-demo`

### 📂 Folder Structure:

```
nodejs-codepipeline-demo/
├── app.js
├── package.json
├── buildspec.yml
├── appspec.yml               # Only for EC2/CodeDeploy
└── scripts/                  # Only for EC2/CodeDeploy
    ├── install_dependencies.sh
    └── start_server.sh
```

---

### 📄 1. `app.js`

```js
const http = require('http');
const port = 3000;

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('🚀 Hello from CodePipeline + CodeBuild + Node.js!\n');
});

server.listen(port, () => {
  console.log(`App running at http://localhost:${port}`);
});
```

---

### 📄 2. `package.json`

```json
{
  "name": "nodejs-codepipeline-demo",
  "version": "1.0.0",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {}
}
```

---

### 📄 3. `buildspec.yml` (For S3/Static or EC2)

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 14
    commands:
      - echo Installing dependencies...
      - npm install
  build:
    commands:
      - echo Build phase completed

artifacts:
  files:
    - '**/*'
    - appspec.yml
    - scripts/*
  discard-paths: no
```

---

### 📄 4. `appspec.yml` (Required for EC2 deployments)

```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /home/ec2-user/nodejs-app
hooks:
  AfterInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
      runas: ec2-user
  ApplicationStart:
    - location: scripts/start_server.sh
      timeout: 300
      runas: ec2-user
```

---

### 📄 5. `scripts/install_dependencies.sh`

```bash
#!/bin/bash
cd /home/ec2-user/nodejs-app
npm install
```

---

### 📄 6. `scripts/start_server.sh`

```bash
#!/bin/bash
cd /home/ec2-user/nodejs-app
nohup node app.js > app.log 2>&1 &
```

---

## ✅ GitHub Setup

1. Create a repo: `nodejs-codepipeline-demo`
2. Push the code:

```bash
git init
git remote add origin https://github.com/your-user/nodejs-codepipeline-demo.git
git add .
git commit -m "Initial commit"
git push -u origin main
```

---

Would you like:

- The GitHub Action version instead of CodeBuild?
- CloudFormation or Terraform automation for EC2 setup too?

Let me know, and I’ll build it for you.
