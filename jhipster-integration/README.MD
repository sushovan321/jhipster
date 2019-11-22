
1. Create a cluster on Google Kubernetes Engine(GKE)
=====================================================
gcloud auth login
gcloud projects create jhipster-integration-service
gcloud config set project jhipster-integration-service
gcloud compute regions list
gcloud config set compute/region europe-west1
gcloud config set compute/zone europe-west1-b

gcloud container clusters create jhipster-integration \
    --cluster-version 1.13 \
    --num-nodes 4 \
    --machine-type n1-standard-2

gcloud container clusters get-credentials jhipster-integration

kubectl get nodes

2.Install and setup Istio
cd ~/

export ISTIO_VERSION=1.3.0
curl -L https://git.io/getLatestIstio | sh -

ln -sf istio-$ISTIO_VERSION istio

export PATH=~/istio/bin:$PATH

kLS
kubectl create namespace istio-system


cd ~/istio-$ISTIO_VERSION

# Install the Istio CRDs
$ helm template install/kubernetes/helm/istio-init --namespace istio-system | kubectl apply -f -

# Run this to verify all CRDs are installed. It should output 23 for this version of Istio.
$ kubectl get crds | grep 'istio.io\|certmanager.k8s.io' | wc -l

# Install the Istio demo set up so that we get Grafana, Jaeger & Kiali set up as well.
# For production, use the Istio default setup. Refer https://istio.io/docs/setup/kubernetes/install/helm/
$ helm template install/kubernetes/helm/istio --namespace istio-system \
    --values install/kubernetes/helm/istio/values-istio-demo.yaml | kubectl apply -f -

kubectl get pods -n istio-system

kubectl get svc istio-ingressgateway -n istio-system

export \
  INGRESS_IP=$(kubectl -n istio-system get svc \
  istio-ingressgateway \
  -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

  mkdir jhipster-integration && cd jhipster-integration
  $ jhipster import-jdl app-istio.jdl

  ./gradlew bootJar -Pprod jibDockerBuild

  docker image tag store sushovan321/store
$ docker push sushovan321/store

$ docker image tag invoice sushovan321/invoice
$ docker push sushovan321/invoice

$ docker image tag notification sushovan321/notification
$ docker push sushovan321/notification

$ docker image tag product sushovan321/product
$ docker push sushovan321/product

cd kubernetes
$ ./kubectl-apply.sh

echo store.jhipster.$INGRESS_IP.nip.io

echo grafana.istio-system.$INGRESS_IP.nip.io

kubectl -n istio-system \
    port-forward $(kubectl -n istio-system get pod -l \
    app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090

localhost:9090

echo jaeger.istio-system.$INGRESS_IP.nip.io

echo kiali.istio-system.$INGRESS_IP.nip.io
