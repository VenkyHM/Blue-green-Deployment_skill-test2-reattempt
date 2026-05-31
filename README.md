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
----
<img width="1690" height="186" alt="image" src="https://github.com/user-attachments/assets/28123152-5786-49a9-a3fa-76500ce8c6ab" />

----

<img width="1699" height="247" alt="image" src="https://github.com/user-attachments/assets/850a30b6-3dd7-4f88-8411-c73562809aa7" />

----

### Local testing

----

<img width="1691" height="611" alt="image" src="https://github.com/user-attachments/assets/97309009-8fdf-43b0-bab4-8d1cd5da951f" />

----

<img width="1694" height="562" alt="image" src="https://github.com/user-attachments/assets/c7eb4a15-a84f-4b32-87b7-d7f694528d1d" />

----

### Containerization using docker
---

<img width="1699" height="950" alt="image" src="https://github.com/user-attachments/assets/69d02c14-7e43-46ff-8e65-c18156173a34" />

---
<img width="1684" height="739" alt="image" src="https://github.com/user-attachments/assets/635e1916-f935-456b-957e-fc1870283066" />

---
### Kubernetes folder structure and yaml file creation for all required deployments

---

<img width="1695" height="589" alt="image" src="https://github.com/user-attachments/assets/90690575-c53c-4970-b377-8a6b27a81cfb" />

---
### Status of all the deployed pods in kubernetes

----

<img width="1697" height="340" alt="image" src="https://github.com/user-attachments/assets/5b2393f4-ca1e-4710-a2da-4e09ac23a0c9" />

---













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

3. Repeat similar steps for Green Frontend (with PORT=3200)

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
```

### 5. Create Kubernetes Manifest Files

#### Required Manifest Files
Create following files in `k8s/` directory:
- `backend-deployment.yaml`
- `frontend-blue-deployment.yaml`
- `frontend-green-deployment.yaml`
- `frontend-service.yaml`
- `ingress.yaml`

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
