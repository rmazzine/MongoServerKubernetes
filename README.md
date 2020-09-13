# MongoDB Server Kubernetes with persistent volume
This repo has an example of how create a MongoDB server on a Kubernetes cluster with a persistent volume. This allow to retain the database data even if the pod dies. In this case, the data will be store in the same server which the Kubernetes cluster is hosted.

I got some problems related to the path (spec.hostPath.path) directory on the persistent volume, so watch out that.

## Instructions
- Create a PersistentVolume
```
kubectl apply -f persistentvolume.yaml
```

- Create a PersistentVolumeClaim to your MongoDB
```
kubectl apply -f persistentvolumeclain.yaml
```

- Create the MongoDB deployment
```
kubectl apply -f deployment_mongodb.yaml
```

- Verify if the PersistentVolumeClaim is bounded to your PersistentVolume
```
kubectl get pvc
```
It should have something like:
```
NAME          STATUS   VOLUME       CAPACITY   ACCESS MODES   STORAGECLASS   AGE
mo-data-pvc   Bound    mo-data-pv   1000Mi     RWO            standard       26s
```

- Check name of the MongoDB pod created
```
kubectl get pods
```

- Connect using port-forward your MongoDB pod to your local machine, replace `<NAME_OF_YOUR_MONGODB_POD>` with the name of your MongoDB pod.
```
kubectl port-forward <NAME_OF_YOUR_MONGODB_POD>
```

- Use Mongo client (it can be the shell or Compass GUI) to create some data to test the persistent volume. The connection string is: `mongodb://admin:admin@localhost:27017`

- Delete pod to test the PersistentVolume
```
kubectl delete pod <NAME_OF_YOUR_MONGODB_POD>
```

- After a new pod is created, get it's name again
```
kubectl get pods
```

- Then, connect to your local machine using port-forward
```
kubectl port-forward <NAME_OF_YOUR_MONGODB_POD>
```

- Connect to the MongoDB and verify if the inserted data still there. If it's there, the PersistentVolume worked.
