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
	
		stage('Get License') {
            steps {
                sh """
                if [ -f /home/adrian/.local/share/unity3d/Unity/Unity_lic.ulf ]; then
                    echo "Licencia encontrada ✅"
                else
                    echo "ERROR: No se encuentra el archivo de licencia"
                    exit 1
                fi
        
                chmod 644 /home/adrian/.local/share/unity3d/Unity/Unity_lic.ulf
                ls -l /home/adrian/.local/share/unity3d/Unity/
                """
            }
        }
	
	
		stage('Test') {
			steps {
				sh """
                    
					Xvfb :1000 -screen 0 1024x768x24 &
					export DISPLAY=:1000
					
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