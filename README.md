# Note : Troubleshooting is still in progress / i used EC2 instance for the deployment now AWS account is closed and i am creating new one by my own.

# Blue-Green Deployment Project

## Prerequisites
- Docker Desktop
- Minikube
- kubectl
- Helm
- Node.js
- Git

## Project Setup

### 1. Clone the Repository
```bash
git clone <your-repository-url>
cd blue-green-project
```
---
<img width="1919" height="172" alt="image" src="https://github.com/user-attachments/assets/d12d86fc-946d-4628-bd78-7d6c3c66f0c0" />

----




### 2. Local Development

#### Backend Setup
1. Navigate to backend directory
2. Install dependencies
```bash
cd backend
npm install
```
3. Create `.env` file with:
```
PORT=5000
MONGO_URI=your-mongodb-connection-string
```
4. Start backend server
```bash
npm start
```
----
<img width="1918" height="318" alt="image" src="https://github.com/user-attachments/assets/e14dfbfd-4f3a-4590-9ae7-5bbf3ab5f977" />

----

<img width="1914" height="673" alt="image" src="https://github.com/user-attachments/assets/a9938ec4-b6e1-47ee-8c94-cb8be75bf8bb" />

----
## After entering the details form is not getting registered.

<img width="1917" height="194" alt="image" src="https://github.com/user-attachments/assets/c2c65bfc-2aeb-44e2-8dc9-d3c7f7306b2e" />

---




#### Frontend Setup
1. Setup Blue Frontend
```bash
cd frontend-blue
npm install
```
2. Create `.env` file:
```
PORT=3100
```
3. Start blue frontend
```bash
npm start
```
---
<img width="1917" height="211" alt="image" src="https://github.com/user-attachments/assets/5d7814d6-a7ce-4da3-b45b-5134feaf4f19" />

----

<img width="1907" height="1032" alt="image" src="https://github.com/user-attachments/assets/503ecb11-016d-4399-a46d-1d9b0e569417" />

----


3. Repeat similar steps for Green Frontend (with PORT=3200)
   1. Setup Green Frontend
```bash
cd frontend-green
npm install
```
2. Create `.env` file:
```
PORT=3100
```
3. Start green frontend
```bash
npm start
```
---

<img width="1918" height="363" alt="image" src="https://github.com/user-attachments/assets/070b903e-365e-4f4e-941f-083097cc45b5" />

----

<img width="1911" height="1038" alt="image" src="https://github.com/user-attachments/assets/a0d29334-8955-4baa-86b9-d72cf323fa49" />

---
## Unable to submit the file due to getting some error as mentioned in the below screenshot

---
<img width="1899" height="1023" alt="image" src="https://github.com/user-attachments/assets/d7984ca0-d26a-42b4-b160-fc630493f718" />

---




### 3. Dockerization

#### Build Docker Images
```bash
# Build Backend Image
docker build -t your-username/backend:v1 ./backend

# Build Blue Frontend Image
docker build -t your-username/frontend-blue:v1 ./frontend-blue

# Build Green Frontend Image
docker build -t your-username/frontend-green:v1 ./frontend-green
```
----
<img width="1914" height="631" alt="image" src="https://github.com/user-attachments/assets/37b61450-5170-4850-94b6-2202a9c38439" />

----
<img width="1919" height="301" alt="image" src="https://github.com/user-attachments/assets/92997508-a124-4dc0-9f64-34b26d21da25" />

----
<img width="1919" height="231" alt="image" src="https://github.com/user-attachments/assets/d04c248c-d929-448d-8db7-0ae66f3d6221" />

----






### 4. Kubernetes Deployment

#### Minikube Setup
1. Start Minikube
```bash
minikube start
```

2. Enable Required Addons
```bash
minikube addons enable metrics-server
minikube addons enable ingress

3.
eval $(minikube docker-env)

docker build -t backend-image:latest ./backend
docker build -t frontend-blue-image:latest ./frontend-blue
docker build -t frontend-green-image:latest ./frontend-green

kubectl rollout restart deployment backend
kubectl rollout restart deployment frontend-blue
kubectl rollout restart deployment frontend-green

docker build -t backend-image:latest ./backend
docker build -t frontend-blue-image:latest ./frontend-blue
docker build -t frontend-green-image:latest ./frontend-green

kubectl rollout restart deployment backend
kubectl rollout restart deployment frontend-blue
kubectl rollout restart deployment frontend-green

```
----
<img width="1918" height="361" alt="image" src="https://github.com/user-attachments/assets/5ac9fdce-f64b-4d09-845c-9f7ff6f69cef" />


---
<img width="1912" height="1055" alt="image" src="https://github.com/user-attachments/assets/e303b294-4168-43ff-a4dc-af1b29160e4e" />

---

## End point validation 

---
<img width="1370" height="65" alt="image" src="https://github.com/user-attachments/assets/0c52304d-654e-4657-b40a-961b1f8a7042" />

---

<img width="1699" height="605" alt="image" src="https://github.com/user-attachments/assets/1383a5ca-2e31-4060-9a96-fd28d3f50e69" />

---






### 5. Create Kubernetes Manifest Files

#### Required Manifest Files
Create following files in `k8s/` directory:
- `backend-deployment.yaml`
- `frontend-blue-deployment.yaml`
- `frontend-green-deployment.yaml`
- `frontend-service.yaml`
- `ingress.yaml`

---
<img width="1914" height="1054" alt="image" src="https://github.com/user-attachments/assets/49e32c73-ef8a-4f02-9f44-c14a9d095d8b" />

----


#### Service File Key Concepts
Your `frontend-service.yaml` should:
- Use selector to route traffic
- Define version (blue/green)
- Map ports correctly

### 6. Deploy to Minikube
```bash
# Apply all manifests
kubectl apply -f k8s/

# Verify deployments
kubectl get deployments
kubectl get services
kubectl get pods
```

### 7. Blue-Green Switching

#### Switch Traffic Methods

1. Basic Patch Command
```bash
# Switch to Green
kubectl patch service frontend-service -p '{"spec":{"selector":{"version":"green"}}}'

# Switch back to Blue
kubectl patch service frontend-service -p '{"spec":{"selector":{"version":"blue"}}}'
```

2. Detailed Patch Command
```bash
kubectl patch service frontend-service --type='merge' -p '{
  "spec":{
    "selector":{
      "app":"frontend",
      "version":"green"
    }
  }
}'
```

### 8. Verification
- Check service endpoints
- Verify traffic routing
- Monitor application logs

### Troubleshooting
- `kubectl get pods` - Check pod status
- `kubectl logs <pod-name>` - View logs
- `kubectl describe service frontend-service` - Service details

### Cleanup
```bash
# Remove deployments
kubectl delete -f k8s/

# Stop Minikube
minikube stop
```

## Blue-Green Deployment Flow Chart

```mermaid
graph TD
    A[Blue Environment Running] -->|Deploy Green| B[Green Environment Prepared]
    B -->|Validate Green| C{Green Ready?}
    C -->|Yes| D[Update Service Selector]
    C -->|No| B
    D -->|Redirect Traffic| E[Green Now Active]
    E -->|Rollback Option| A
```

### Flow Explanation
1. Blue environment is initial production
2. Green environment deployed alongside
3. Validate green environment 
4. Update service selector
5. Redirect traffic to green
6. Blue remains as rollback option

## Best Practices
- Implement health checks
- Use resource limits
- Configure monitoring
- Validate before switching
- Maintain rollback strategy


## License
This project is licensed under the MIT License
