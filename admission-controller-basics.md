In my last post, you saw how the Pod creation workflow works. But have you noticed the Admission controllers, a powerful feature in Kubernetes that intercepts requests to the Kubernetes API server before the persistence of resources? They act as gatekeepers, ensuring that what enters your cluster meets specific criteria and policies 🤔 
🛡️ 𝗪𝗵𝗮𝘁 𝗮𝗿𝗲 𝗔𝗱𝗺𝗶𝘀𝘀𝗶𝗼𝗻 𝗖𝗼𝗻𝘁𝗿𝗼𝗹𝗹𝗲𝗿𝘀?

→ They are code components within the Kubernetes API server that check and potentially modify incoming requests

→ They apply to operations that create, modify, or delete resources (but not read operations like get, watch, list)

→ Think of them as security guards at the entrance of your cluster, inspecting every request before letting it in! 🚪

🔄 𝗧𝘆𝗽𝗲𝘀 𝗼𝗳 𝗔𝗱𝗺𝗶𝘀𝘀𝗶𝗼𝗻 𝗖𝗼𝗻𝘁𝗿𝗼𝗹𝗹𝗲𝗿𝘀

→ 𝗠𝘂𝘁𝗮𝘁𝗶𝗻𝗴 𝗖𝗼𝗻𝘁𝗿𝗼𝗹𝗹𝗲𝗿𝘀 : These can modify objects in the request (like adding default values or injecting sidecars) ✏️

→ 𝗩𝗮𝗹𝗶𝗱𝗮𝘁𝗶𝗻𝗴 𝗖𝗼𝗻𝘁𝗿𝗼𝗹𝗹𝗲𝗿𝘀: These only validate objects against policies but cannot modify them ✅

→ Some controllers can perform both functions, giving you complete control over what enters your cluster

## ⚙️ 𝗔𝗱𝗺𝗶𝘀𝘀𝗶𝗼𝗻 𝗖𝗼𝗻𝘁𝗿𝗼𝗹 𝗙𝗹𝗼𝘄

1️⃣ Authentication: Is the requester who they claim to be?

2️⃣ Authorization: Is the requester allowed to perform this action?

3️⃣ Admission Control:
   - First, mutating admission controllers run
   - Then, validating admission controllers run
   - If any controller rejects the request, it's immediately denied with an error

4️⃣ Persistence to etcd: Only after passing all checks is the object stored

🔧 𝗣𝗿𝗮𝗰𝘁𝗶𝗰𝗮𝗹 𝗘𝘅𝗮𝗺𝗽𝗹𝗲: 𝗜𝗺𝗮𝗴𝗲𝗣𝗼𝗹𝗶𝗰𝘆 𝗶𝗻 𝗣𝗼𝗱 𝗖𝗿𝗲𝗮𝘁𝗶𝗼𝗻

→ A mutating webhook could inject default resource limits for containers without them

→ A validating webhook could then check if all container images come from your approved registry

→ If someone tries to deploy a pod with an image from an unauthorized source, the admission controller rejects it before it even reaches your cluster! 🛑

💡 𝗪𝗵𝘆 𝗨𝘀𝗲 𝗔𝗱𝗺𝗶𝘀𝘀𝗶𝗼𝗻 𝗖𝗼𝗻𝘁𝗿𝗼𝗹𝗹𝗲𝗿𝘀?

→ Enforce security policies consistently across your entire cluster

→ Automate the injection of sidecars for observability or security

→ Apply organization-wide defaults without modifying application code

→ Prevent misconfigurations from reaching production environments

𝙍𝙚𝙢𝙚𝙢𝙗𝙚𝙧: In Kubernetes, prevention is better than cure! Admission controllers let you shift security left and catch issues before they become problems. The best part you can create your own Controllers 🔐 #Kubernetes #Security #DevOps #CloudNative


Diagram -> https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#admission-control-phases
