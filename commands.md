
# OTHER Resources  

## JOB

```yaml 
apiVersion : batch/v1
kind: Job
metadata :
 name: whalesay
spec:
 template :
 spec:
  containers :
  - name: whalesay
    image: docker/whalesay
    command: ["cowsay" , "This is a Kubernetes Job!" ]
 restartPolicy : Never
 backoffLimit : 4
 activeDeadlineSeconds : 60
 ```

## ConfigMap

```yaml
kubectl create configmap myconf --from-literal=APP_COLOR=blue
kubectl create configmap --from-literal=APP_COLOR=green --from-literal=APP_ENV=prod
kubectl create configmap --from-file=<location-to-file>
kubectl create configmap --from-env-file=<location-to-file>
```


```yaml 
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/sh", "-c", "env" ]
      env:
        # Define the environment variable
        - name: SPECIAL_LEVEL_KEY
          valueFrom:
            configMapKeyRef:
              # The ConfigMap containing the value you 
              # want to assign to SPECIAL_LEVEL_KEY
              name: special-config
              # Specify the key associated with the 
              # value
              key: special.how
  restartPolicy: Never
```
```yaml
kubectl create configmap special-config --from-literal=special.how=very
```

## Secret

```yaml
kubectl create secret generic mysecret --from-literal=PASSWROD=pass123
kubectl create secret generic--from-literal=DB_Host=mysql --from-literal=PASSWORD=pass123 --from-literal=DDB_User=root
kubectl create secret generic--from-file=<location-to-file>
kubectl create secret generic --from-env-file=<location-to-file>
```


```shell

### Encoding 
echo -n 'my-app' | base64
echo -n '39528$vdg7Jb' | base64

### Decoding 
echo YWRtaW4= | base64 --decode
echo 'MWYyZDFlMmU2N2Rm' | base64 --decode

```

`echo -n 'my-app' | base64`
`echo -n '39528$vdg7Jb' | base64`

`kubectl create secret generic test-secret --from-literal='username=my-app' --from-literal='password=39528$vdg7Jb'`

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: test-secret
data:
  username: bXktYXBw
  password: Mzk1MjgkdmRnN0pi
```

`kubectl apply -f secret.yaml`


```yaml
### pod-secret.yaml
apiVersion: v1
kind: Pod
metadata:
  name: envfrom-secret
spec:
  containers:
  - name: envars-test-container
    image: nginx
    envFrom:
    - secretRef:
        name: test-secret
```


sudo mv  /etc/kubernetes/manifests/kube-scheduler.yaml .

cat << EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
EOF

Result: It will be in pending state as kube-scheduler is not present in kube-system namespace.
-------

cat << EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx1
  name: nginx1
spec:
  nodeName: kworker1
  containers:
  - image: nginx
    name: nginx1
EOF

Result: It will be in running state
-------

sudo mv  kube-scheduler.yaml /etc/kubernetes/manifests/

-------

login on any worker node

cat /var/lib/kubelet/config.yaml | grep stat
cd /etc/kubernetes/manifests
nano pod.yaml (Add following lines)

apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    tier: front-end
spec:
   containers:
   - name: nginx-container
     image: nginx
     resources:
       requests:
         memory: 50Mi
         cpu: 0.1
       limits:
         memory: 300Mi
         cpu: 1

Save & exit


Note:
=====

* kubelet automatically create a static pod.
* static pod can only be deleted by removing the file.
* To view static pod by using docker ps command.
* kube-apiserver can list the static pod as kubelet create a mirror of this pod on kube-apiserver.
* While creating static pod, hostname append with static pod name.
* To delete static Pod, just delete file.
