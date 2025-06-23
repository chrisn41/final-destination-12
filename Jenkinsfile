pipeline {
  agent any

  environment {
    GIT_REPO = "https://github.com/chrisn41/final-destination-12.git"
  }

  options {
    timestamps()
  }

  stages {
    stage('Clean Workspace') {
      steps {
        echo "🧹 Membersihkan workspace lama..."
        cleanWs()
      }
    }

    stage('Clone Repository') {
      steps {
        echo "📥 Cloning dari GitHub..."
        git url: "${env.GIT_REPO}", branch: 'main', credentialsId: 'ceb0c844-e96d-4c75-a7c0-7e5ef62131d7'
      }
    }

    stage('Update Gallery HTML') {
      steps {
        echo "🖼️ Menjalankan script update_gallery.py..."
        sh '''
          if [ -f scripts/update_gallery.py ]; then
            python3 scripts/update_gallery.py
          else
            echo "❗ File update_gallery.py tidak ditemukan."
            exit 1
          fi
        '''
      }
    }

    stage('Commit & Push Perubahan') {
      steps {
        echo "🔄 Commit & Push jika ada perubahan..."
        withCredentials([usernamePassword(credentialsId: 'ceb0c844-e96d-4c75-a7c0-7e5ef62131d7', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
          sh '''
            git config user.name "chrisn41"
            git config user.email "tawakalkrisna@gmail.com"

            git add webapp/index.html
            git diff --cached --quiet && echo "✅ Tidak ada perubahan." || (
              git commit -m "CI: Update index.html with new gallery images"
              git remote set-url origin https://$GIT_USER:$GIT_TOKEN@github.com/chrisn41/final-destination-12.git
              git push origin main
            )
          '''
        }
      }
    }

    stage('Deploy Docker Containers') {
      steps {
        echo "🚀 Deploy aplikasi dan monitoring..."
        sh '''
          cd $WORKSPACE

          echo "🛑 Menghentikan container lama..."
          docker compose down -v || true
          docker rm -f node-exporter grafana prometheus final-web || true
          docker volume prune -f --filter "label!=keep" || true

          echo "⚙️ Membuild dan menjalankan container..."
          docker compose up -d --build

          echo "✅ Semua service berhasil dijalankan."
          docker ps
        '''
      }
    }
  }

  post {
    failure {
      echo "❌ Pipeline gagal. Periksa log error di atas."
    }
    success {
      echo "🎉 Pipeline berhasil selesai!"
    }
  }
}
