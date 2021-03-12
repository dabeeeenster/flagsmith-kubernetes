# Read me for flagsmith operator

## Install operator sdk

The initial conversion from the helm chart to a helm based operator is done with this command:

```bash
# On a mac:
brew install operator-sdk
operator-sdk init --plugins=helm --domain=flagsmith.com --helm-chart=../flagsmith-kubernetes/ 
```

## Build process for operator

To build the operator set an environment variable pointing to the image of the flagsmith operator. This was the command in my case

``` bash
export IMG=flagsmith/flagsmith-operator:v0.0.1
```

The next step ist to build the operator itself and upload it to the defined docker registry with this command:

``` bash
make docker-build docker-push IMG=$IMG
```

With this command you can deploy the operator in the project "":

``` bash
make deploy IMG=$IMG
```

To deploy the flagsmith application in the current project run the following command:

```bash
kubectl apply -f config/samples/charts_v1alpha1_flagsmith.yaml
```

## Delete flagsmith application from cluster

To remove the flagsmith from your cluster you need to run the command:

``` bash
kubectl delete -f config/samples/charts_v1alpha1_flagsmith.yaml
```

## Delete operator from cluster

To remove the operator from the cluster run the command:

``` bash
make undeploy
```

## Test small changes in helm chart

To test small changes in the helm chart you need to remove the flagsmith application. The next step is to build and push to image to the remote registry. Finally delete the flagsmith operator pod. This pod should be restarted with the changed images

## Build and deploy flagsmith bundle operator

In order to build and push the flagsmith bundle operator execute the following commands:

``` bash
make bundle-build BUNDLE_IMG=flagsmith/flagsmith-operator-bundle:v0.0.1
make docker-push IMG=flagsmith/flagsmith-operator-bundle:v0.0.1
```

## Test and rund bundle operator image

In order to test the bundle operator execute the following command:

``` bash
operator-sdk run bundle flagsmith/flagsmith-operator-bundle:v0.0.1 --namespace=flagsmith-operator --verbose --install-mode=AllNamespaces
```

## Remove bundle operator

If you want to remove the bundle opertor execute the following statements

``` bash
oc delete flagsmiths.charts.flagsmith.com -A --all
oc delete catalogsources.operators.coreos.com flagsmith-operator-catalog -n flagsmith-operator
oc delete subscriptions.operators.coreos.com flagsmith-operator-v0-0-1-sub -n flagsmith-operator
oc delete csvs flagsmith-operator.v0.0.1 -n flagsmith-operator
```

## ToDos

- [X] Extend CRD to include basic description
- [X] Install Flagsmith without InfluxDB and Postgresql
- [X] Definition of Flagsmith logo in csv
- [ ] Change ImagePullPolicy for operator from `Always` to `IfNotPresent` to speed up container start
- [ ] Add tests from operator
- [ ] Add helm hooks for Database update
