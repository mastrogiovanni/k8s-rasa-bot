# README

k8s-rasa-bot is an Extension of kubernetes.

A github volume is responsible to download intents, stories and actions from GitHub.
An init container is responsible to train models and fill a volume with chatbot model.
The volume is used by a container with a Telegram 

A Controller allows to deploy as Custom Resource a Bot configuration (intents, stories and actions)

## Installing the Chart

In order to add the chart you need a volume:

```console
cat <<EOF | kubectl apply -f -
kind: PersistentVolume
apiVersion: v1
metadata:
  name: rasa-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
EOF
```

Add repository:

```console
$ helm repo add rasa https://mastrogiovanni.github.io/k8s-rasa-bot/charts
```

```console
$ helm repo update
```

To install the chart with the release name `rasa`:

```console
$ helm install rasa/rasa --name rasa \
  --set storage.gitUrl=https://github.com/<project with intents, stories and actions>
```

The command deploys ferrucciofacts Bot on the Kubernetes cluster in the default namespace.

## Uninstalling the Chart

To uninstall/delete the `ferrucciofacts-bot` deployment:

```console
$ helm delete --purge ferrucciofacts-bot
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following tables lists the configurable parameters of the Grafana chart and their default values.

Parameter | Description | Default
--- | --- | ---
`telegram_bot_id` | Telegram Bot Token | `-`

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

Alternatively, a YAML file that specifies the values for the above parameters can be provided while installing the chart. For example,

```console
$ helm install ferrucciofacts/ferrucciofacts --name ferrucciofacts-bot -f values.yaml
```

> **Tip**: You can use the default [values.yaml](values.yaml)
```





# Test

RASA_NLU=$(kubectl get service -l app=rasa-nlu -o jsonpath='{.items[0].spec.clusterIP}')

curl -XPOST http://$RASA_NLU:5000/parse -d '{"q":"hello there", "project": "current"}'

RSA_CORE=$(kubectl get service -l app=rasa-core -o jsonpath='{.items[0].spec.clusterIP}')

curl --request POST \
    --url "http://$RSA_CORE:5005/webhooks/rest/webhook" \
    --header 'content-type: application/json' \
    --data '
    {
        "message": "hello"
    }'

ACTION_SERVER=$(kubectl get service -l app=action-server -o jsonpath='{.items[0].spec.clusterIP}')



kubectl exec -it $(kubectl get pod -l app=rasa-nlu -o jsonpath='{.items[0].metadata.name}') bash