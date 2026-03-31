# CST8915 Lab 8: Deploying and Managing the Algonquin Pet Store (On Steroids)
**Student Name**: Xinyi Zhao    
**Student ID**: 040953633    
**Course**: CST8915 Full-stack Cloud-native Development    
**Semester**: Winter 2026    

---

## Demo Video

🎥 https://youtu.be/Uj_6QU3qh9Y

---

## Technical Explanations 

### MongoDB High Availability and Persistent Storage

In the original deployment, MongoDB was running as a single instance with ephemeral storage, so the data would be lost if the pod restarted. To improve this, I changed MongoDB to a StatefulSet with 3 replicas.

I also configured a headless service (`clusterIP: None`), which gives each MongoDB pod a stable DNS name such as `mongodb-0.mongodb`. This is required for communication between the replicas.

To make the data persistent, I added PersistentVolumeClaims (PVCs) using `volumeClaimTemplates`, so each MongoDB pod has its own storage. The data is stored in `/data/db`, which ensures it is not lost even if a pod is deleted and recreated.

Then I initialized a replica set (`rs0`) manually using the MongoDB shell. I verified it using `rs.status()`, which showed that all three nodes were part of the replica set. This setup improves availability because the system can continue working even if one node fails.

---

### RabbitMQ Persistent Storage

In the original setup, RabbitMQ also used ephemeral storage, so messages would be lost after a restart. To fix this, I added a PersistentVolumeClaim to the RabbitMQ StatefulSet.

The volume is mounted to `/var/lib/rabbitmq`, which is the default directory where RabbitMQ stores its data. This ensures that queues and messages are preserved even if the pod restarts.

I also kept the existing ConfigMap for enabling plugins, so the functionality of RabbitMQ remains unchanged while adding persistence.

---

### Azure Managed Service Alternatives

Instead of running these services in Kubernetes, Azure provides managed alternatives:

- **MongoDB → Azure Cosmos DB (MongoDB API)**  
  This service supports MongoDB workloads and provides built-in replication, scaling, and backups. It removes the need to manage replica sets and storage manually.

- **RabbitMQ → Azure Service Bus**  
  This is a fully managed messaging service that provides reliable queues and message persistence. It simplifies the architecture by removing the need to manage RabbitMQ infrastructure.

---

### Summary

In this task, I improved the deployment by adding persistent storage and high availability. MongoDB is now running as a replica set with 3 nodes and persistent volumes, and RabbitMQ stores its data using a persistent volume. These changes make the system more reliable compared to the original setup, where data could be lost easily.

---

## AI Tools Used

I used ChatGPT during this lab to help with debugging and understanding Kubernetes concepts.

It was mainly used to:
- Fix YAML configuration issues  
- Understand errors from `kubectl`  
- Clarify concepts like StatefulSets, replica sets, and persistent volumes  
- Improve the clarity of the README  

All the setup, testing, and final implementation were completed by myself.
