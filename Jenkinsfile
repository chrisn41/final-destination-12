pipeline {
  agent any

  environment {
    GIT_REPO = "https://github.com/chrisn41/final-destination-12.git"
  }

  stages {
    stage('Clone Repository') {
      steps {
        git url: "${env.GIT_REPO}", branch: 'main', credentialsId: 'ceb0c844-e96d-4c75-a7c0-7e5ef62131d7'
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
        withCredentials([usernamePassword(credentialsId: 'ceb0c844-e96d-4c75-a7c0-7e5ef62131d7', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
          sh '''
          git config user.name "chrisn41"
          git config user.email "tawakalkrisna@gmail.com"

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
        cd ci-final-project

        # Salin prometheus.yml dari root repo ke direktori kerja
        cp ../prometheus.yml .

        docker compose down || true
        docker compose up -d --build
        '''
      }
    }
  }
}
