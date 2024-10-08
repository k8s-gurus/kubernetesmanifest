node {
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Update GIT') {
        script {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    // Git configuration
                    sh "git config user.email k8s.gurus@gmail.com"
                    sh "git config user.name k8s-gurus"

                    // Apply changes
                    sh "cat deployment.yaml"
                    sh "sed -i 's+k8sgurus1/test.*+k8sgurus1/test:${DOCKERTAG}+g' deployment.yaml"
                    sh "cat deployment.yaml"

                    // Check for changes
                    def changes = sh(script: "git diff --name-only", returnStdout: true).trim()

                    // Commit and push only if changes exist
                    if (changes) {
                        echo "Changes detected, committing and pushing to repository."
                        sh "git add ."
                        sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/kubernetesmanifest.git HEAD:main"
                    } else {
                        echo "No changes detected, skipping commit and push."
                    }
                }
            }
        }
    }
}