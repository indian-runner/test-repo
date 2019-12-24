To deploy your app on your cluster:
1. change the `<chart-name>` in the `Chart.yaml` file
2. change the `env` of `values.env.yaml` with the env you want to deploy to
3. edit the `values` file with values relevant to your app
   1. parameters left with brackets `<>` NEED TO BE EDITED with your values
   2. other parameters can be left to default values
   3. environment variables will be injected in you pods. The following parameters can be edited, deleted... according to your needs 
      1. parameters in the `configmap` section
      2. parameters in the `secret.external` section. You will need to create secrets of the specified name and keys in AWS Secret Manager.
4. Your are now set to deploy you app with `helm upgrade -i <release-name> . -f values.env.yaml --namespace <namespace-name>`

**Note:** to fetch secrets for AWS Secret Manager

Change poller interval to fetch secrets every 5 seconds
`helm upgrade -i external-secrets external-secrets/kubernetes-external-secrets --set env.POLLER_INTERVAL_MILLISECONDS='5000' --set env.AWS_REGION='eu-west-1'`

Then back to fetching every 24h not to waist money
`helm upgrade -i external-secrets external-secrets/kubernetes-external-secrets --set env.POLLER_INTERVAL_MILLISECONDS='86400000' --set env.AWS_REGION='eu-west-1'`

The best thing would be for this to be a job applied when le release is upgraded