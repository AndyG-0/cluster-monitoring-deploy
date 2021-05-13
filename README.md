# cluster-monitoring-deploy

Deploys k3s cluster monitoring using repo: [https://github.com/carlosedp/cluster-monitoring](https://github.com/carlosedp/cluster-monitoring)

## Notes

* If the database shows as locked, the easiest way to fix is to delete the files off the NFS.
* Easiest way to wipeout deployment is to delete the namespace. Don't forget to delete pvs as well. If the namespace is stuck in terminating, follow these [instructions](https://craignewtondev.medium.com/how-to-fix-kubernetes-namespace-deleting-stuck-in-terminating-state-5ed75792647e). If this doesn't work, reboot the main cluster node. 
* Ingress-fix is a work-around for the ingresses not working this may work in future versions and at that point can be removed. 
* Persistent Volumes are automated during the Jenkins deployment. PVCs are dynamically created by the carelosedp repo. 
