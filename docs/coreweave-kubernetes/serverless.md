# Serverless

Deploying applications as serverless sercices is a good alternative to a regular Deployment / Service model when the applications purpose is to serve HTTP or gRPC requests, either internal or from the Internet. CoreWeave uses the [KNative](https://knative.dev/docs/serving/getting-started-knative-app/) runtime to support deploying a serverless application with a single manifest. No installation necessary.

![](../.gitbook/assets/serverless_deployment.png)

#### Serverless Benefits

* Automatic public HTTPS endpoints
* Auto-scaling, including scale to zero
* No public IP charges
* [Canary deployments](https://knative.dev/docs/serving/samples/blue-green-deployment/) and other advanced deployment strategies

**One Step Example**

{% code title="helloworld-ksvc.yaml" %}
```yaml
apiVersion: serving.knative.dev/v1 # Current version of Knative
kind: Service
metadata:
  name: helloworld # The name of the app
  annotations:
    autoscaling.knative.dev/minScale: "0" # Allow scale to Zero
    autoscaling.knative.dev/maxScale: "10" # Maximum number of Pods allowed to auto-scale to
spec:
  template:
    spec:
      containerConcurrency: 10 # Container can handle 10 concurrent requests
      containers:
        - image: gcr.io/knative-samples/helloworld-go # The URL to the image of the app
          resources:
            limits:
              cpu: 2
              memory: 4Gi
          env:
            - name: TARGET # The environment variable printed out by the sample app
              value: "Go Sample v1"
          
```
{% endcode %}

After applying the manifest, get the public URL of the service.

```bash
$ kubectl get ksvc
NAME            URL                                                       LATESTCREATED         LATESTREADY           READY   REASON
helloworld      https://helloworld.default.knative.chi.coreweave.com      helloworld-ngzsn      helloworld-ngzsn      True
```
