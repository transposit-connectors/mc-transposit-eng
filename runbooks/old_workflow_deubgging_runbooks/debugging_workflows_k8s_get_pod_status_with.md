# Debugging Workflows - K8s Get Pod Status with Azure Functions Proxy

rocos/k8s_pod_status

## About
Morad and Dan created an Azure functions proxy to allow Rocos to communicate between the Transposit platform (`rocos/k8s_pod_status` workflow) and their Azure AKS kubernetes cluster. The k8s cluster API server is fronted by a self-signed SSL certificate that Transposit does not recognize as legitimate.

This function uses single bearer token authentication tied to a service account /cluster role binding pair configured on the kubernetes cluster in advance. This proxy **WILL NOT** work as is with regular user authentication (which typically depends on client certificate data found in tke kubeconfig file). It is meant to be a temporary measure for Rocos until it can be addressed more appropriately from the Transposit product side.

We have not scoped exactly which permissions the k8s cluster role binding will need to grant for this to work. It is likely to be dependant on the scope of the Transposit workflow making requests to the k8s API server.

## Debugging the proxy that calls the K8s(AKS) API Server
There is a Node function which proxies the request to the k8s (Azure AKS) API server and disables validation of the self-signed SSL cert fronting the specific k8s API server 
    
* Where it's deployed: Access https://portal.azure.com using the credentials for `test-user1@transposit.com` listed in 1password. Navigate to **App Services** and select the item titled *ankrim-test*
* From the Azure Functions app screen, use the nav bar on the left of the screen. Click on **Functions** in the Functions sub-menu, then click *ankrim-proxy-test*.
* Where the code lives: From the *ankrim-proxy-test* screen, look in the nav bar on the left side of the screen for the *Developer* sub-menu. Then click on **Code + test**
* To test: Since the function uses a portion of the incoming request URL to create the proxied request, I've been testing with curl from my local machine and adding `-H "Authorization: Bearer $TOKEN"` to pass the correct auth. Once that worked to satisfaction, I would test from the Transposit dev platform and eventually a runbook embedded action.
* The function outputs logging data to the embedded Azure console. The majority of issues have been visible from the request header, which is printed to the console logs on each invocation.

## Debugging the workflow rocos/k8s_pod_status
This kubernetes workflow calls sends its request to the Azure Functions proxy directly, with the same URL path which would be used if it were communicating diretly with the k8s API server directly. Only the hostname is different.

* Make sure that the connector's configuration is set correctly, using a kubernetes bearer token associated with a k8s service account / cluster role binding pair with correct permissions to get pod status and access the specified namespace
* Do not attempt to use this workflow with "human user" auth for one of the cloud provider kubernetes implementations. This user type generally requires client certificate data as well.
* Make sure that the workflow sends its request to the proxy correctly and returns the correct results. The `kubectl` CLI utility can be used to verify results using the same auth as the workflow (in the kubeconfig file format)
