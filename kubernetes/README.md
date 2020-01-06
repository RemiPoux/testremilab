# Deploy a Nest app

## 1 - Create the branches

```bash
git checkout -b staging
git push staging
```

The staging branch will be deployed on the staging env, the master one on the prod one.

## 2 - Customize the helm files

To deploy your app on your cluster:

1. Change the `<chart-name>` in the `Chart.yaml` file with the name of your API.
2. Edit the `values` file with values relevant to your app
   1. Parameters left with brackets `<>` NEED TO BE EDITED with your values
   2. Other parameters can be left to default values
   3. Environment variables will be injected in you pods. The following parameters can be edited, deleted... according to your needs 
3. Create the staging/api and prod/api secret with the DATABASE_USER and DATABASE_PASSWORD
4. Your are now set to deploy you app by pushing to `staging` for the staging env or to `master` for the prod env.

## NB : Handle secret 

**Note:** to fetch secrets for AWS Secret Manager

Change poller interval to fetch secrets every 5 seconds
`helm upgrade -i external-secrets external-secrets/kubernetes-external-secrets --set env.POLLER_INTERVAL_MILLISECONDS='5000' --set env.AWS_REGION='eu-west-1'`

Then back to fetching every 24h not to waist money
`helm upgrade -i external-secrets external-secrets/kubernetes-external-secrets --set env.POLLER_INTERVAL_MILLISECONDS='86400000' --set env.AWS_REGION='eu-west-1'`

**Remove this documentation at the end**

# Interact with the kubernetes cluster

## Prerequisites:

You should have installed the following tools:
 - [kubectl](https://kubernetes.io/fr/docs/tasks/tools/install-kubectl/)
 - [helm](https://helm.sh/docs/using_helm/#install-helm)
 - [awscli](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)

## Kubernetes basics
Kubernetes in a tool used to manage containers.
kubectl is the command line tool used to interact with the kubenetes api deployed inside the cluster.

Most `kubectl` commands follow this syntax 
```
kubectl <action> <resources> <resource-name>
```
Actions can be: get, describe, delete, run...
Resources can be: pods, deployments, services, secrets, configmaps, ingress, serviceaccount...

##### General

```
kubectl get nodes 					 	# list nodes in the cluster
kubectl get namespaces				 	# list namespaces in the cluster
kubectl get pods -n kube-system 	 	# list pods in the kube-system namespace
kubectl describe pods <pod-name> 	 	# describe pod named <pod-name>
kubectl get events 					 	# list events in the cluster
kubectl exec -it <pod-name> -- /bin/sh  # execute command /bin/sh in pod named <pod-name>
```

##### Check the logs of a pod

`kubectl logs <pod_name>`

`kubectl logs <pod_name> -c <container_name>`

`kubectl logs <pod_name> -f`

`kubectl logs <pod_name> --tail=<number_of_lines> -f`

##### Forward local network traffic to a pod

`kubectl port-forward <pod-name> <local-port>:<pod-port>`

Then you can reach your pod <pod-name> on port <pod-port> by accessing localhost:<local-port>


##### Scale the number of pods in my deployment

To scale up or down the number of pods of a deployment use the following command:

`kubectl scale deployments <deployment-name> --replicas=<number-of-replicas-wanted>`

NB: If a Horizontal Pod Autoscaler is deployed and tracks your deployment, the described command might not be enough as the hpa will scale back the deployment to its desired number of replicas.
If this is the case, you can edit the hpa
`kubectl edit hpa <hpa-name>`
to specify new min and max numbers of pods.




# Helm basics
Helm is a kubernetes resources manager. It allows managing kubernetes packages created by the community, templating your kubernetes resources and managing resources versions.

Here are some basic commands:
```
helm install -f <values_file> ./<chart_directory> 					# install a chart
helm upgrade -f <values_file> <release_name> ./<chart_directory> 	# upgrade a deployment
helm rollback <release_name> <revision_number>					    # rollback to a former version
```