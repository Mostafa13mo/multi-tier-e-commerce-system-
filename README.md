
Requirements for Each Component

### 1. Database
- Use `mysql:5.7` or `mongo:latest` image.  
- Set environment variables (`MYSQL_ROOT_PASSWORD`, `MYSQL_DATABASE`, or `MONGO_INITDB_DATABASE`).  
- Expose it internally using a **ClusterIP** service.  
- Should not be directly reachable from the outside. 
- Resource limits: **1 CPU and 1Gi memory**.  

---

### 2. Product Service
- Use a lightweight backend container such as:  
  - `hashicorp/http-echo`  
  - or a small custom API image if available.  
- Add `args` to display a custom message like:  
  - `"-text=Product Service Connected to DB"`  
- Expose it internally using a **ClusterIP** service.  
- Must connect to the Database via `db-service`.  
- Resource limits: **0.5 CPU and 500Mi memory**.  
---

### 3. Order Service
- Use `hashicorp/http-echo` or another small API image.  
- Add `args` to display:  
  - `"-text=Order Service Connected to Product + DB"`  
- Expose it internally using a **ClusterIP** service.  
- Must connect to both `db-service` and `product-service`.  
- Resource limits: **0.5 CPU and 500Mi memory**.  
---

### 4. Frontend
- Use `nginx:latest` or another frontend image.  
- Optionally mount a ConfigMap with a custom `index.html` that calls Product and Order services.  
- Expose it externally using a **NodePort** service.  
- Resource limits: **0.5 CPU and 500Mi memory**.  
---

### 5. NetworkPolicies (Optional Challenge)
- Allow only Product and Order services to talk to the Database.  
- Deny direct DB access from the Frontend.  
- Allow the Frontend to reach only Product and Order services.  
