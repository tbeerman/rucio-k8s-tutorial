# Rucio Kubernetes Tutorial

* Clone this repo to your local machine

* Install kubectl

      https://kubernetes.io/docs/tasks/tools/install-kubectl/

* Install helm

      https://helm.sh/docs/intro/install/

* Install minikube

      https://kubernetes.io/docs/tasks/tools/install-minikube/
	  https://minikube.sigs.k8s.io/docs/start/

* Start minikube with extra RAM:

      > minikube start --memory='4000mb'

* Add Helm chart repositories:

      > helm repo add stable https://kubernetes-charts.storage.googleapis.com/
      > helm repo add rucio https://rucio.github.io/helm-charts

* Install the main Rucio database (PostgreSQL):

      > helm install postgres stable/postgresql -f postgres_values.yaml

* Run init container once to setup the Rucio database once the PostgreSQL container is running:

      > kubectl apply -f init-pod.yaml

* Install the Rucio server:

      > helm install server rucio/rucio-server -f server.yaml

* Prepare a client container for interactive use:

      > kubectl apply -f client.yaml

* Jump into the client container and check if the clients are working:

      > kubectl exec -it client /bin/bash

      > rucio whoami
        status     : ACTIVE
        account    : root
        account_type : SERVICE
        created_at : 2020-03-02T14:21:06
        updated_at : 2020-03-02T14:21:06
        suspended_at : None
        deleted_at : None
        email      : None

* Install three XRootD storage systems:

      > kubectl apply -f xrd.yaml

* Install the FTS database (MySQL):

      > kubectl apply -f ftsdb.yaml

* Install FTS, once the FTS database container is up and running:

      > kubectl apply -f fts.yaml

* Install the Rucio daemons:

      > helm install daemons rucio/rucio-daemons -f daemons.yaml

* Run FTS storage authentication delegation once:

      > kubectl create job renew-manual-1 --from=cronjob/daemons-renew-fts-proxy
