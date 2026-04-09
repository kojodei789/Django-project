name: task1

on:
  push:
    branches:
      - deploy-to-ec2

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Source
        uses: actions/checkout@v4

      - name: Login to Docker Hub
        run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

      - name: Build Docker Image
        run: docker build -t kojodei789/django-cicd-app:latest .

      - name: Publish image to Docker Hub
        run: docker push kojodei789/django-cicd-app:latest

  deploy:
    needs: build
    runs-on: ubuntu-latest

    steps:
      - name: Deploy to EC2
        run: |
          mkdir -p ~/.ssh
          echo "${{ secrets.EC2_SSH_KEY }}" > ~/.ssh/KAD.pem
          chmod 600 ~/.ssh/KAD.pem
          sed -i 's/\r$//' ~/.ssh/KAD.pem

          ssh -o StrictHostKeyChecking=no -i ~/.ssh/KAD.pem ubuntu@${{ secrets.EC2_HOST }} << EOF
            docker pull kojodei789/django-cicd-app:latest
            docker rm -f djangoContainer || true
            docker run -d -p 8000:8000 --name djangoContainer kojodei789/django-cicd-app:latest
          EOF
