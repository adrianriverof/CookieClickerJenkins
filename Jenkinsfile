pipeline {
    agent any
    
    environment {
        UNITY_PATH = "/home/adrian/Unity-2022.3.7f1/Editor/Unity" // CAMBIAD ESTO
        REPO_URL = "https://github.com/adrianriverof/CookieClickerJenkins.git"
        UNITY_CACHE_DIR = "/var/lib/jenkins/UnityCache"
    }
    
    stages {
        stage('Checkout') {
            steps {
            	discordSend description: "Adrian", footer: "Pipeline start", link: env.BUILD_URL, result: currentBuild.currentResult, title: env.JOB_NAME, webhookURL: "https://discord.com/api/webhooks/1403692153439391754/jQaX79xZrL0QqQ4PlwgmUwclwU4Fpriv1yxOowDFKiFPI8wmjoVsjeULtlC7QKFknd9a"
                sh "git pull ${REPO_URL}"
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
					export UNITY_CACHE_DIR=/var/lib/jenkins/UnityCache
					export UNITY_GI_CACHE_PATH=/var/lib/jenkins/UnityCache/GiCache
					mkdir -p "\$UNITY_GI_CACHE_PATH"
					
					export DISPLAY=:1000
		
					Xvfb :1000 -screen 0 1024x768x24 &
					sleep 3
		
					"${UNITY_PATH}" -executeMethod SimpleBuildScript.Build -projectPath "${WORKSPACE}" -quit -batchmode -logfile /dev/stdout
		
					killall Xvfb
				"""
				archiveArtifacts artifacts: 'Build/**/*', fingerprint: true

            }
        }
        stage('Deploy'){
        	steps{
        		sh """
        			butler push "${pwd()}/Build" adrianriverof/testing-deploying-with-jenkins:linux
        		"""
        	}
        }
    }
}