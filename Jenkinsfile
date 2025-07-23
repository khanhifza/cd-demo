pipeline {
    agent any

    environment {
        PROD_PATH = '/var/www/html/cd-demo' // Change this if your NGINX root differs
    }

    stages {

        stage('Create HTML Page') {
            steps {
                echo "[Init] Creating custom Vidhyarthi HTML page..."
                sh '''
                mkdir -p dev-stage test-stage prod-stage artifacts

                cat > dev-stage/index.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Opstree Welcome Page</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f5f5f5;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
        }
        header {
            background-color: #2c3e50;
            color: white;
            width: 100%;
            padding: 20px 0;
            text-align: center;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        .logo {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            margin-bottom: 15px;
        }
        .container {
            max-width: 800px;
            margin: 30px auto;
            padding: 20px;
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
            text-align: center;
            flex-grow: 1;
        }
        h1 {
            color: #e74c3c;
        }
        .ninja-welcome {
            font-size: 2em;
            font-weight: bold;
            color: #e74c3c;
            margin: 30px 0;
            text-transform: uppercase;
            letter-spacing: 1px;
            text-shadow: 1px 1px 2px rgba(0,0,0,0.1);
            animation: pulse 2s infinite;
        }
        .welcome-message {
            font-size: 1.2em;
            margin: 20px 0;
            color: #34495e;
        }
        .phase-info {
            background-color: #f8f9fa;
            padding: 15px;
            border-radius: 5px;
            margin: 20px 0;
            border-left: 4px solid #3498db;
        }
        button {
            background-color: #3498db;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 1em;
            transition: background-color 0.3s;
            margin-top: 20px;
        }
        button:hover {
            background-color: #2980b9;
        }
        footer {
            background-color: #2c3e50;
            color: white;
            width: 100%;
            padding: 15px 0;
            text-align: center;
        }
        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }
        .ninja-icon {
            font-size: 1.5em;
            margin: 0 5px;
        }
    </style>
</head>
<body>
    <header>
        <img src="https://avatars.githubusercontent.com/u/48691121?s=200&v=4" alt="Opstree Logo" class="logo">
        <h1>Opstree</h1>
    </header>
    <div class="container">
        <div class="ninja-welcome">
            <span class="ninja-icon">🥷</span>
            HI NINJAS! WELCOME TO VIDHYARTHI PHASE
            <span class="ninja-icon">🥷</span>
        </div>
        <div class="welcome-message">
            <p>We're thrilled to have you join our learning community.</p>
            <p>The Vidhyarthi phase is where your journey to mastery begins!</p>
        </div>
        <div class="phase-info">
            <h3>About Vidhyarthi Phase</h3>
            <p>This is your foundational training period where you'll:</p>
            <ul style="list-style-type: none; padding: 0;">
                <li>• Learn essential skills from industry experts</li>
                <li>• Work on real-world projects</li>
                <li>• Collaborate with fellow ninjas</li>
                <li>• Build a strong professional foundation</li>
            </ul>
        </div>
        <button id="actionButton">Begin Your Journey</button>
    </div>
    <footer>
        &copy; 2023 Opstree. All rights reserved.
    </footer>
    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const button = document.getElementById('actionButton');
            const welcomeMessage = document.querySelector('.ninja-welcome');
            button.addEventListener('click', function() {
                const originalMessage = welcomeMessage.innerHTML;
                welcomeMessage.innerHTML = '🥷 LET THE TRAINING BEGIN! 🥷';
                welcomeMessage.style.color = '#27ae60';
                button.textContent = 'Continue Learning';
                button.style.backgroundColor = '#27ae60';
                setTimeout(() => {
                    welcomeMessage.innerHTML = originalMessage;
                    welcomeMessage.style.color = '#e74c3c';
                    button.textContent = 'Continue Your Journey';
                    button.style.backgroundColor = '#3498db';
                }, 3000);
            });
        });
    </script>
</body>
</html>
EOF
                '''
            }
        }

        stage('Build') {
            steps {
                echo "[Build] Copying to test-stage..."
                sh 'cp dev-stage/index.html test-stage/'
            }
        }

        stage('Test') {
            steps {
                echo "[Package] Creating tarball..."
                sh '''
                    cd test-stage
                    tar -czf ../artifacts/site.tar.gz index.html
                '''
            }
        }

        stage('Manual Approval') {
            steps {
                input message: 'Proceed with production deployment?', ok: 'Deploy'
            }
        }

        stage('Deploy to Prod') {
            steps {
                echo "[Deploy] Copying to NGINX path..."
                sh '''
                    sudo mkdir -p ${PROD_PATH}
                    sudo tar -xzf artifacts/site.tar.gz -C ${PROD_PATH}
                '''
            }
        }

        stage('Verify') {
            steps {
                echo "[Verify] Checking if deployed correctly..."
                sh 'curl -s http://localhost/cd-demo/index.html | grep "Opstree Welcome Page"'
                echo "✅ Deployed: http://<your-server-ip>/cd-demo/"
            }
        }
    }
}
