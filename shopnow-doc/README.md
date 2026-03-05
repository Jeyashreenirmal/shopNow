First I made the necessary changes in the yaml scripts and instructed
Second I made sure that all necessary tools are installed and configured, like aws cli, eksctl, kubectl and helm. 
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

the same method was followed for other two services (backend and admin)
![ECR repo](screenshots/ecr-repo.png)

next step is to create an EKS cluster, command used
eksctl create cluster --name jsree-shopnow --region us-east-1 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 1 --nodes-max 4

next is to update the kube config, command used aws eks --region us-west-1 update-kubeconfig --name jsree-shopnow
![eks kubeconfig](screenshots/eks_update.png)

![eks UI](screenshots/eks-ui.png)

Then, I decided to follow the first method (i.e) to deploy using Raw Kubernetes Manifest, to get better undertand. 

next step into install the pre-request, storage (EBS-CSI), ingress controller and metrics-server
 to attach the EBS-CSI, I first created a role in IAM and used AmazonEBSCSIDriverpolicy as I faced IAM permission issue
 ![ebs csi role](screenshots/ebs-csi-role.png)

 deployed the storage class yaml file, command kubectl apply -f <file name>
  ![applying the storage yaml ](screenshots/storageclass-yml-apply.png)

verify the storageclass, command used kubectl get storageclass
  ![verify the storageclass ](screenshots/get-storage.png)

installed ingress controller
  ![ingress controller  ](screenshots/ingress-controller.png)

deployed metric-server yaml file
  ![metric-server creation ](screenshots\metric-server.png)

deployed namespace 
  ![namespace ](screenshots\namespace.png)

next step is to create the mongoDB pod, applied deployment and service yaml file
  ![applying mongo pod file ](screenshots\mongo-pod-file.png)
  ![mongo pod file ](screenshots\mongopod.png)

next step isto deploy the backend application, to acheive this I first deployed the configmap, secrets, deployment, service and hpa respectively. , I faced CrashLoopbackoff error, while I checked the logs, the error was host not found in upstream "backend-service", that is backed container is trying to connect to backend-service but kubernetes cannot resolve this service name, I opened the service.yml file of backend and name the service to backend-service, then re-applied the service.yml file and the pod came to runnign state. 
  ![backend pod ](screenshots\backendpod.png)
  ![backend hpa ](screenshots\backend-hpa.png)

next, I deployed the frontend application, in the order of configmap, secrets, deployment, service, and hpa respectively. faced issue on CrashLoopBackoff, reason host not found in upstream "backend". so I created a new service.yml for frontend application naming service name as backend and then the issue was resolved. 
  ![frontend pod ](screenshots\frontendpod.png)
  ![frontend pod hpa ](screenshots\frontend-hpa.png)

deployed admin application
![admin pod ](screenshots\adminpod.png)
![admin pod hpa ](screenshots\admin-hpa.png)

deploying ingress, after deploying ingress, ingress acts as a smart routing, one entry point, it routes multiple service. 
![ingess](screenshots\ingress.png)
![ingess path 1](screenshots\ingress-path1.png)
![ingess path 2](screenshots\ingress-path2.png)

deployed daemonset
![daemonset](screenshots\daemonset.png)

user creation for backend, 
![user creation for mongo pod](screenshots\usercreation.png)


To automate the build and deployment process, I followed the follwing way

Push code ---> GitHub ---> Jenkins Pipeline ---> Build Docker image ---> Push image to ECR ---> update YAMl in Git ---> Argocd detects change ---> Deploy to Kubernetes. 

![jenkins pipeline](screenshots\jenkins.png)
![argocd](screenshots\argocd.png)





