# Cloud-Ghost

Cloud Computing Applications and Services project. Master of Software Engineering at Universidade do Minho.

## Code Structure

- create-gke-cluster.yml: Ansible playbook to create a GKE cluster.
- destroy-gke-cluster.yml: Ansible playbook to destroy a GKE cluster.
- deploy-app.yml: Ansible playbook to install and configure the application.
  - The application is installed in the cluster created by the create-gke-cluster.yml playbook.
  - This playbook can be executed in a new installation or in an existing one (assuming the persistent data was not deleted).
  - In a new installation, the playbook should automatically create an administrator with the following credentials:
    - Username: ascn
    - Password: ascn123
    - Email: ascn@example.com
- undeploy-ghost.yml: Terminates the current installation of the application.
  - Only when flag "-e delete_data=true" is passed, the persistent installation data is deleted.
- test-all.yml: Ansible playbook to run all the automatic tests.
- roles/ : Contains the roles used by the playbooks.
  - Both gke_cluster create and destroy roles need a service account with Google Cloud CLI to access the GKE API.
  - The test role tests the availability of the application by, for example, sending HTTP requests to the application.
- inventory/ : Contains the inventory files used by the playbooks.
  - The gcp.yml file contains variables used by the previous roles.
  - Variables starting with "gcp\_" are used by the gke_cluster roles.
  - gcp_project: identifies the name of the Google Cloud project.
  - gcp_cred_file: identifies the path to the JSON file containting the service account credentials. (**include this file in .gitignore**)
  - ghost_ip: identifies the public IP address of the application.
  - ghost_port: identifies the port of the application.

Reminder: Shutdown the cluster when not in use to avoid incurring in costs.

## Start and Shutdown virtual machines in the cluster:

$ gcloud container clusters resize ascn-cluster --node-pool default-pool --num-nodes 0 --zone europe-southwest1-a


## Run playbooks:
$ ansible-playbook --vault-password-file pass.txt deploy-ghost.yml -i inventory/gcp.yml

$ ansible-playbook undeploy-ghost.yml -i inventory/gcp.yml

$ ansible-playbook undeploy-ghost.yml -i inventory/gcp.yml -e "delete_data=true"


## Other commands:
$ kubectl get pods -n <namespace>

$ kubectl exec -it <pod-name> -n <namespace> -- mysql -u root -p

$ ansible-vault encrypt_string --vault-password-file pass.txt '<pass to encrypt>' --name '<name of the variable>' > out.txt 
