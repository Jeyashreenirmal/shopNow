first I made sure that all necessary tools are installed and configured, like aws cli, eksctl, kubectl and helm. 
and then decide to build docker images for frontend, backend and admin services. 

created a directoy in aws ECR for frontend using the command 
aws ecr create-repository --repository-name jsree-shopnow-f --region us-east-1

building the images
docker build -t jsree-shopnow-f:frontend .

tag
docker tag jsree-shopnow-f:frontend 975050024946.dkr.ecr.us-east-1.amazonaws.com/jsree-shopnow-f:frontend

push the image into ECR
docker push 975050024946.dkr.ecr.us-east-1.amazonaws.com/jsree-shopnow-f:frontend

![Docker Push](screenshots/frontend_docker.png)

