pipeline {
  agent any

  environment {
    GIT_REPO = "https://github.com/chrisn41/final-destination-12.git"
  }

  stages {
    stage('Clone Repository') {
      steps {
        git url: "${env.GIT_REPO}", branch: 'main', credentialsId: 'github-token'
      }
    }

    stage('Update index.html') {
      steps {
        sh '''
        python3 scripts/update_gallery.py
        '''
      }
    }

    stage('Commit and Push Changes') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'github-token', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
          sh '''
          git config user.name "CI Bot"
          git config user.email "ci-bot@example.com"

          git add webapp/index.html
          git commit -m "CI: Update index.html with new gallery images" || echo "No changes to commit"

          git remote set-url origin https://$GIT_USER:$GIT_TOKEN@github.com/chrisn41/final-destination-12.git
          git push origin main
          '''
        }
      }
    }

    stage('Deploy (Optional)') {
      steps {
        sh '''
        docker compose down || true
        docker compose up -d --build
        '''
      }
    }
  }
}
