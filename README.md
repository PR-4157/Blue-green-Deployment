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
git clone https://github.com/mohanDevOps-arch/Blue-green-Deployment.git
cd blue-green-project
```

### 2. Local Development

#### Backend Setup
1. Navigate to backend directory
2. Install dependencies
```bash
cd backend
npm install
```
<img width="684" height="199" alt="Screenshot 2026-05-31 at 3 21 39 PM" src="https://github.com/user-attachments/assets/8de2e464-75fa-47e8-a869-72fe84b563d4" />

3. Create `.env` file with:
```
PORT=5000
MONGO_URI=your-mongodb-connection-string
```
<img width="788" height="255" alt="Screenshot 2026-05-31 at 3 52 29 PM" src="https://github.com/user-attachments/assets/817ed580-4c3e-46a1-8af1-3968764cd69c" />

4. Start backend server
```bash
npm start
```
<img width="708" height="172" alt="Screenshot 2026-05-31 at 11 09 53 PM" src="https://github.com/user-attachments/assets/8d9fabe1-c4af-4013-b920-e3c15520981c" />

#### Frontend Setup
1. Setup Blue Frontend
```bash
cd frontend-blue
npm install
```
<img width="695" height="142" alt="Screenshot 2026-05-31 at 3 23 44 PM copy" src="https://github.com/user-attachments/assets/d7a12c0f-ae23-4fb6-88ee-11db66c1dabc" />

2. Create `.env` file:
```
PORT=3100
```
3. Start blue frontend
```bash
npm start
```
<img width="710" height="141" alt="Screenshot 2026-05-31 at 4 19 26 PM" src="https://github.com/user-attachments/assets/500cff9e-3d42-45bd-a945-db6fffbdcfdf" />

3. Repeat similar steps for Green Frontend (with PORT=3200)
<img width="713" height="124" alt="Screenshot 2026-05-31 at 4 19 27 PM" src="https://github.com/user-attachments/assets/f0d61d4b-f9de-4ae5-a6d4-6e311dc93f14" />

### 3. Dockerization

#### Build Docker Images
# Build Backend Image
docker build -t your-username/backend:v1 ./backend

<img width="705" height="352" alt="Screenshot 2026-06-01 at 7 00 35 PM" src="https://github.com/user-attachments/assets/2b31c3c8-cade-481b-afce-2770e23c0d32" />

# Build Blue Frontend Image
docker build -t your-username/frontend-blue:v1 ./frontend-blue
<img width="908" height="339" alt="Screenshot 2026-06-01 at 7 56 08 PM" src="https://github.com/user-attachments/assets/452a938c-fec9-439c-9b7e-be4884a58218" />

# Build Green Frontend Image
docker build -t your-username/frontend-green:v1 ./frontend-green
<img width="906" height="340" alt="Screenshot 2026-06-01 at 7 56 53 PM" src="https://github.com/user-attachments/assets/04502aa5-4d18-4d1c-b9a0-99708b96686b" />

### 4. Kubernetes Deployment

#### Minikube Setup
1. Start Minikube
```bash
minikube start
```
<img width="701" height="339" alt="Screenshot 2026-05-31 at 11 21 35 PM" src="https://github.com/user-attachments/assets/a827f64a-8d6f-4923-bb25-aa71c7542d58" />

2. Enable Required Addons
```bash
minikube addons enable metrics-server
minikube addons enable ingress
```
<img width="905" height="84" alt="Screenshot 2026-06-01 at 8 05 10 PM" src="https://github.com/user-attachments/assets/51809920-a745-4406-bb40-42df5fc60350" />
<img width="911" height="145" alt="Screenshot 2026-06-01 at 8 05 49 PM" src="https://github.com/user-attachments/assets/bf68ba12-59bb-4e85-a5a7-ddfb2b642e87" />

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
# Apply all manifests
kubectl apply -f k8s/
<img width="910" height="100" alt="Screenshot 2026-06-01 at 8 08 01 PM" src="https://github.com/user-attachments/assets/c2c520a9-5c2d-4be6-aa82-cf59426a66c1" />

# Verify deployments
kubectl get deployments
kubectl get services
kubectl get pods
<img width="899" height="223" alt="Screenshot 2026-06-01 at 8 05 23 PM" src="https://github.com/user-attachments/assets/2ec25e06-c596-41d4-8d34-a08278bebfda" />

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
<img width="906" height="285" alt="Screenshot 2026-06-01 at 8 12 22 PM" src="https://github.com/user-attachments/assets/05ea085e-f847-4276-90ea-a1c0297f4907" />

### 8. Verification
- Check service endpoints
- Verify traffic routing
- Monitor application logs
<img width="907" height="212" alt="Screenshot 2026-06-01 at 8 15 45 PM" src="https://github.com/user-attachments/assets/0d4ea7bb-29f8-4b46-aa1e-07eef850a03b" />
<img width="968" height="827" alt="Screenshot 2026-06-01 at 8 15 54 PM" src="https://github.com/user-attachments/assets/dbf85d8c-b872-46ea-b9d3-995844c9408e" />

### Troubleshooting
- `kubectl get pods` - Check pod status
- `kubectl logs <pod-name>` - View logs
- `kubectl describe service frontend-service` - Service details
<img width="909" height="819" alt="Screenshot 2026-06-01 at 8 21 02 PM" src="https://github.com/user-attachments/assets/45eaca4e-c8f0-491b-8ffa-8f96e6203030" />

### Cleanup
# Remove deployments
kubectl delete -f k8s/
<img width="908" height="98" alt="Screenshot 2026-06-01 at 8 20 05 PM" src="https://github.com/user-attachments/assets/7e4f4aea-f167-4ae6-8f47-6dc24d3ac706" />

# Stop Minikube
minikube stop
<img width="906" height="59" alt="Screenshot 2026-06-01 at 8 19 38 PM" src="https://github.com/user-attachments/assets/0c7eabc5-25fc-4bcb-834e-969951907746" />

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

# Conclusion

This project successfully demonstrated containerization, orchestration, and Blue-Green deployment using Docker and Kubernetes. The backend, frontend applications, and MongoDB database were deployed and managed within a Minikube cluster.

The Blue-Green deployment strategy enabled a seamless transition between application versions by switching traffic through a Kubernetes Service selector. This approach provided zero-downtime deployment, simplified rollback, and improved application availability.

The implementation demonstrated practical knowledge of Docker, Kubernetes Deployments, Services, health checks, and modern DevOps deployment practices.
