How Exactly Does Optimistic Concurrency in Kubernetes Work? A solution that addresses deadlock scenarios associated with pessimistic locking and enhances scalability for distributed systems with increased throughput.



Essentially, there is no deadlock for state changes between different controllers. This is achieved with the help of the metadata field called resourceVersion, which exists in every Kubernetes resource. This field acts as a version identifier that changes whenever a resource is modified.



→ Read Phase: A client reads a resource by fetching a copy from the database, which does not acquire locks. 



→ Modify Phase: The client modifies the fetched copy and sends the updates back. 



→ Update Phase: The database checks for changes since the last read by comparing versions or timestamps. If they match, it applies the update; if not, it detects a conflict.



→  Conflict Resolution: For conflicts, optimism concurrency typically resolves them by rolling back changes, retrying, or applying specific logic.



Let's understand with an example:



→ Both Controller A and Controller B retrieve the pod manifest with resourceVersion: 1091



→ Controller A updates the manifest and submits it with resourceVersion: 1091



→ The API server accepts A's update and increments the resource to resourceVersion: 2075



→ Controller B tries to submit its update with resourceVersion: 1091



→ The API server rejects B's update because the current version is now 2075, not 1091



→ Controller B must fetch the latest version (now 2075), reapply its changes, and submit again. 



With that, your #Kubernetes cluster can ensure scalability and reliability in handling multiple controllers operating simultaneously. 

![carbon](https://github.com/user-attachments/assets/c87b39d1-6fe0-4947-ad5b-a49bd1d6f70f)
