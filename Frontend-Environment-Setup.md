# Frontend Environment Setup

## Required Software

### 1. Node.js

Node.js is a JavaScript runtime environment used to run JavaScript applications outside the browser.

Purpose:

* Install packages using NPM
* Run React applications
* Execute development tools like Vite

Official Website:
[Node.js Official Website](https://nodejs.org?utm_source=chatgpt.com)

Recommended Version:

* LTS (Long Term Support)

---

### 2. Visual Studio Code (VS Code)

VS Code is a lightweight and powerful source code editor.

Purpose:

* Write React code
* Install extensions
* Run terminal commands
* Debug applications

Official Website:
[VS Code Official Website](https://code.visualstudio.com?utm_source=chatgpt.com)

---

# Open Command Prompt / Terminal

You can use:

* Command Prompt
* PowerShell
* VS Code Terminal

Open terminal using:

```text
Ctrl + `
```

---

# Install Vite

Vite is a modern frontend build tool used to create fast React applications.

Command:

```bash
npm create vite@latest
```

---

# Create React JS Application with TypeScript

Command:

```bash
npm create vite@latest my-react-app -- --template react-ts
```

Explanation:

| Part                | Meaning                     |
| ------------------- | --------------------------- |
| npm                 | Node Package Manager        |
| create vite@latest  | Create latest Vite project  |
| my-react-app        | Project name                |
| --template react-ts | React + TypeScript template |

---

# Move into Project Folder

Command:

```bash
cd my-react-app
```

Explanation:

* `cd` means Change Directory
* Moves terminal into project folder

---

# Install Project Dependencies

Command:

```bash
npm install
```

Purpose:

* Downloads all required packages
* Creates `node_modules` folder

Important Files Installed:

* React
* React DOM
* TypeScript
* Vite Packages

---

# Run the React Application

Command:

```bash
npm run dev
```

Purpose:

* Starts development server
* Runs React application locally

Example Output:

```text
Local: http://localhost:5173/
```

Open the URL in browser.

---

# Stop the React Application

Shortcut:

```text
Ctrl + C
```

Purpose:

* Stops development server

---

# Complete Workflow

```text
Install Node.js
        ↓
Install VS Code
        ↓
Open Terminal
        ↓
Create React App using Vite
        ↓
Move into Project Folder
        ↓
Install Packages
        ↓
Run Application
        ↓
Open Browser
```

---

# Important Notes

## node_modules Folder

* Automatically created after `npm install`
* Contains all project packages
* Do not edit manually

---

## package.json

Main project configuration file.

Contains:

* Project name
* Dependencies
* Scripts

---

## npm

NPM stands for:

```text
Node Package Manager
```

Used to:

* Install packages
* Manage dependencies
* Run scripts

---

# Recommended VS Code Extensions

| Extension           | Purpose                        |
| ------------------- | ------------------------------ |
| ES7+ React Snippets | React shortcuts                |
| Prettier            | Code formatting                |
| ESLint              | Code validation                |
| Auto Rename Tag     | Rename HTML tags automatically |
| Material Icon Theme | Better file icons              |

---

# Folder Structure

```text
my-react-app
│
├── node_modules
├── public
├── src
│   ├── App.tsx
│   ├── main.tsx
│
├── package.json
├── tsconfig.json
├── vite.config.ts
```

---

# Quick Commands Summary

| Purpose             | Command                                                    |
| ------------------- | ---------------------------------------------------------- |
| Create Vite Project | npm create vite@latest                                     |
| Create React TS App | npm create vite@latest my-react-app -- --template react-ts |
| Open Project Folder | cd my-react-app                                            |
| Install Packages    | npm install                                                |
| Run Project         | npm run dev                                                |
| Stop Project        | Ctrl + C                                                   |

---

# Expected Result

After successful setup:

* React application runs locally
* Browser opens React page
* Ready to start frontend development
