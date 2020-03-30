![Argo](argo.png)
![Screenshot](argo-screenshot.png)

### Introduction
[Argo Workflows](https://argoproj.github.io/argo/) is a great tool to orchestrate parallel execution of GPU jobs.

ing Started

After installing `kubectl` and adding your CoreWeave Cloud access credentials, the following steps will deploy the Ethminer Deployment and service.

1. Apply the resource, this will install the Argo Workflow server into your namespace
   ```bash
    $ kubectl apply -f argo-install.yaml
    serviceaccount/argo unchanged
    serviceaccount/argo-server unchanged
    role.rbac.authorization.k8s.io/argo-role unchanged
    role.rbac.authorization.k8s.io/argo-server-role unchanged
    rolebinding.rbac.authorization.k8s.io/argo-binding unchanged
    rolebinding.rbac.authorization.k8s.io/argo-server-binding unchanged
    configmap/workflow-controller-configmap unchanged
    service/argo-server unchanged
    deployment.apps/argo-server unchanged
    deployment.apps/workflow-controller unchanged
   ````
   
2. Follow the [Argo documentation](https://github.com/argoproj/argo/blob/master/docs/getting-started.md) to install the Argo CLI

3. Submit an example workflow. The workflow takes a JSON Array and spins up one Pod with one GPU allocated for each, in parallel.
   `nvidia-smi` output as well as the parameter entry assigned for that Pod is printed to the log.
   
   ![Workflow submit](workflow-submit.gif)
   
   ```text
   $ argo submit --watch gpu-say-workflow.yaml -p messages='["Argo", "Is", "Awesome"]'
     Name:                gpu-sayzfwxc
     Namespace:           tenant-test
     ServiceAccount:      default
     Status:              Running
     Created:             Mon Feb 10 19:31:17 -0500 (15 seconds ago)
     Started:             Mon Feb 10 19:31:17 -0500 (15 seconds ago)
     Duration:            15 seconds
     Parameters:
       messages:          ["Argo", "Is", "Awesome"]
    
     STEP                                  PODNAME                  DURATION  MESSAGE
      ● gpu-sayzfwxc (main)
      └-·-✔ echo(0:Argo)(0) (gpu-echo)     gpu-sayzfwxc-391007373   10s
        ├-● echo(1:Is)(0) (gpu-echo)       gpu-sayzfwxc-3501791705  15s
        └-✔ echo(2:Awesome)(0) (gpu-echo)  gpu-sayzfwxc-3986963301  12s
   ```

4. Get the log output from all parallel containers
   ```text
   $ argo logs -w gpu-sayrbr6z
   echo(0:Argo)(0):	Tue Feb 11 00:25:30 2020
   echo(0:Argo)(0):	+-----------------------------------------------------------------------------+
   echo(0:Argo)(0):	| NVIDIA-SMI 440.44       Driver Version: 440.44       CUDA Version: 10.2     |
   echo(0:Argo)(0):	|-------------------------------+----------------------+----------------------+
   echo(0:Argo)(0):	| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
   echo(0:Argo)(0):	| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
   echo(0:Argo)(0):	|===============================+======================+======================|
   echo(0:Argo)(0):	|   0  GeForce GTX 107...  Off  | 00000000:08:00.0 Off |                  N/A |
   echo(0:Argo)(0):	| 28%   51C    P5    16W / 180W |     18MiB /  8119MiB |      0%      Default |
   echo(0:Argo)(0):	+-------------------------------+----------------------+----------------------+
   echo(0:Argo)(0):
   echo(0:Argo)(0):	+-----------------------------------------------------------------------------+
   echo(0:Argo)(0):	| Processes:                                                       GPU Memory |
   echo(0:Argo)(0):	|  GPU       PID   Type   Process name                             Usage      |
   echo(0:Argo)(0):	|=============================================================================|
   echo(0:Argo)(0):	+-----------------------------------------------------------------------------+
   echo(0:Argo)(0):	Input was: Argo
   echo(1:Is)(0):	Tue Feb 11 00:25:30 2020
   echo(1:Is)(0):	+-----------------------------------------------------------------------------+
   echo(1:Is)(0):	| NVIDIA-SMI 440.44       Driver Version: 440.44       CUDA Version: 10.2     |
   echo(1:Is)(0):	|-------------------------------+----------------------+----------------------+
   ...
   ```