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
                mkdir -p $HOME/.local/share/unity3d/Unity/
                
                cp /var/lib/jenkins/Unity_lic.ulf $HOME/.local/share/unity3d/Unity/Unity_lic.ulf
                
                ls -l $HOME/.local/share/unity3d/Unity/
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
                    "${UNITY_PATH}" -executeMethod SimpleBuildScript.Build -projectPath "${WORKSPACE}" -quit -batchmode -logfile "${WORKSPACE}/CI/build.log
                """
                archiveArtifacts artifacts: 'CI/build.log', fingerprint: true
                archiveArtifacts artifacts: 'Build/**/*', fingerprint: true
                
            }
        }
    }
}