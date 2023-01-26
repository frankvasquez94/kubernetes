# Role Base Access Control (RBAC)
- After installation, the cluster is accessed using a basic TLS certificate kubeadmin user. This is the kubeadmin user that you can use based on the configuration in the .kube/config directory.
- The client configuration is stored in .kube/config.
- For advanced user setup, custom users can be created.
- Permissions are taken care of using Role Base Access Control (RBAC).
- RBAC allows you to setup users with specific permissions to specific namespaces.


## Role

A role is an API resource that defines specific permissions.

## ServiceAccount

A ServiceAccount is a API resource that can be used to give **pods** additional access permissions to cluster resources.

Every Pod by default is configured to use some specific ServiceAccount.


## RoleBinding

A role binding is an API resource that defines access to a **role**.

RoleBindings are asigned to **users** or **ServiceAccounts**


## Permissions on Cluster

ClusterRoles or ClusterRoleBindings can be used to grant permissions to the entire cluster intead of just a specific namespace.

## Basic schema

![Role Base Acccess Control](rbac.png)


