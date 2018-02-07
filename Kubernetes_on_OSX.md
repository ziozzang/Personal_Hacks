

```
kubectl config get-contexts
kubectl config use-context docker-for-desktop
```

```
docker stack deploy --compose-file /path/to/docker-compose.yml mystack
docker stack services mystack
```

```
docker stack deploy --namespace my-app --compose-file /path/to/docker-compose.yml mystack
kubectl get services -n my-app
```



```
kubectl get nodes
kubectl get services
```
