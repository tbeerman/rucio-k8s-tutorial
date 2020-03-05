# rucio-k8s-tutorial

* clone this repo to your local machine
* install kubectl: https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-macos
* install helm: https://helm.sh/docs/intro/install/
* install minikube: https://kubernetes.io/docs/tasks/tools/install-minikube/ / https://minikube.sigs.k8s.io/docs/start/
* add repos:

      > helm repo add stable https://kubernetes-charts.storage.googleapis.com/
      > helm repo add rucio https://rucio.github.io/helm-charts

* enable ingress:

      > minikube addons enable ingress

* install postgres:

      > helm install postgres stable/postgresql -f postgres_values.yaml
      
* run init container:

      > kubectl apply -f init-pod.yaml 
      
* install server:

      > helm install server rucio/rucio-server -f server.yaml

* check cluster ingress ip and add to /etc/hosts:

      > kubectl get ing
        NAME                       HOSTS               ADDRESS          PORTS     AGE
        server-rucio-server        rucio-server.info   192.168.39.162   80, 443   30m
        server-rucio-server-auth   rucio-auth.info     192.168.39.162   80        30m
  
      > (editor) /etc/hosts
        192.168.39.162 rucio-server.info
        192.168.39.162 rucio-auth.info

* check if servers are running:

        > curl http://rucio-server.info/ping
          {"version": "1.21.10"}
        > curl http://rucio-auth.info/ping
          {"version": "1.21.10"}

* check if clients are working:

         > kubectl apply -f client.yaml
         > kubectl exec -it client /bin/bash
         > bash-4.2# rucio whoami
            status     : ACTIVE
            account    : root
            account_type : SERVICE
            created_at : 2020-03-02T14:21:06
            updated_at : 2020-03-02T14:21:06
            suspended_at : None
            deleted_at : None
            email      : None

* install xrootd containers:

            > kubectl apply -f xrd.yaml
       
* install fts:

            > kubectl apply -f ftsdb.yaml
            > kubectl apply -f fts.yaml

* install the daemons and run fts delegation once:

            > helm install daemons rucio/rucio-daemons -f daemons.yaml
            > kubectl create job renew-manual-1 --from=cronjob/daemons-renew-fts-proxy
