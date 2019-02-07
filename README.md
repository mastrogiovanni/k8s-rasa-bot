# README

k8s-rasa-bot is an Extension of kubernetes.

A github volume is responsible to download intents, stories and actions from GitHub.
An init container is responsible to train models and fill a volume with chatbot model.
The volume is used by a container with a Telegram 

A Controller allows to deploy as Custom Resource a Bot configuration (intents, stories and actions)



# Test

RASA_NLU=$(kubectl get service -l app=rasa-nlu -o jsonpath='{.items[0].spec.clusterIP}')

curl "http://$RASA_NLU:5000/parse?q=hello&project=current"

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