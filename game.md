# 📓 Innovator Journal: Code Autopsy
**Project:** HTML5 Canvas Game Autopsy  
**Game Name:** *Flappy Webby*  
**File:** `flappy_webby.html`  

---

## 1. Project Overview & Setup

For this activity, I generated a single-file HTML5/JavaScript game called **Flappy Webby** using the HTML5 `<canvas>` element. The game features a simple physics-based bird maneuvering through obstacles set against a sunset backdrop.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Flappy Webby</title>
  <style>
    body {
      background-color: #2b1f3d;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="400" height="600"></canvas>
</body>
</html>
