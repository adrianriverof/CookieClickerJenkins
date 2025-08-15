pipeline {
    agent any
    
    environment {
        UNITY_PATH = "/home/adrian/Unity-2022.3.7f1/Editor/Unity" // CAMBIAD ESTO
        REPO_URL = "https://github.com/adrianriverof/CookieClickerJenkins.git"
    }
    
    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                sh "git clone ${REPO_URL} ."
            }
        }
        
		stage('Test') {
			steps {
				sh """
					killall Xvfb 2>/dev/null || true
                    rm -f /tmp/.X1-lock
                    
					Xvfb :1 -screen 0 1024x768x24 &
					export DISPLAY=:1
					
					mkdir -p "${WORKSPACE}/CI" && \
					"${UNITY_PATH}" \
                        -runTests \
                        -projectPath "${WORKSPACE}" \
                        -exit -batchmode \
                        -testResults "${WORKSPACE}/CI/results.xml" \
                        -testPlatform EditMode \
                        -logfile "${WORKSPACE}/CI/unity.log"
                    
                    killall Xvfb
				"""
				archiveArtifacts artifacts: 'CI/*.log', fingerprint: true
			}
		}
        
        stage('Build') {
            steps {
                sh """
                    "${UNITY_PATH}" -executeMethod SimpleBuildScript.Build -projectPath "${WORKSPACE}" -quit -batchmode
                """
                archiveArtifacts artifacts: 'Build/**/*', fingerprint: true
                
            }
        }
    }
}