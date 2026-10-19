# 5. Web apps and containers

[Contents](../README.md) | [Previous](04-virtual-machines.md) | [Next: networks and DNS](06-networks-and-dns.md)

**Goal:** separate the application from its hosting capacity and deployment method.

## App Service plans

An App Service plan supplies hosting capacity. Apps in a plan share its resources and operating-system/region constraints. Several compatible apps can share a plan to reduce cost; app count alone does not determine the number of plans.

For a minimum-plan question, group apps by required **region and operating system**, then verify runtime and feature requirements. Classic ASP.NET Framework and ASP.NET Core have different platform possibilities. Read the framework named in the question carefully.

Scale **up** changes the plan tier or machine capacity. Scale **out** changes instance count. A feature unavailable in the current tier is not unlocked simply by adding more of the same instance. [Hosting-plan guidance](https://learn.microsoft.com/en-us/azure/app-service/overview-hosting-plans).

**Quiz connections:** Q159, Q165, Q178, Q184, Q218.

## Deployment slots and rollback

A slot is a separate live deployment endpoint for the same app. A typical release is to deploy to staging, test, then swap with production. If a swap introduced a bad release, swapping back is usually the quickest rollback for the question's stated scenario.

Slots require a supported tier: Standard, Premium or Isolated. Read slot-specific settings before predicting behavior. Some configuration is sticky to a slot, so a swap is not simply an unconditional exchange of every setting. [Deployment-slot reference](https://learn.microsoft.com/en-us/azure/app-service/deploy-staging-slots).

**Quiz connections:** Q165, Q181, Q211, Q325.

## Custom domains, TLS and private access

DNS mapping proves and directs the desired hostname. HTTPS also needs an appropriate certificate binding. Creating a certificate alone does not point a hostname at the app.

For a subdomain such as `www.example.com`, a CNAME is a common mapping choice. Read the current App Service verification requirements. An apex/root name can require a different record pattern.

For a web app to initiate connections into a VNet, think **VNet integration**. For private inbound access to the web app, consider **private endpoints**. Those directions are different. Reaching an on-premises share additionally needs working hybrid connectivity, DNS, routing and application/protocol support; deploying a VPN gateway alone is not a complete application integration.

**Quiz connections:** Q64, Q171, Q189, Q228, Q243.

## Autoscale questions

Write down the starting count, min/max, metric, aggregation period, threshold, change amount and cooldown. Apply rules in time order. Do not allow scale-in below the minimum or scale-out above the maximum.

A sample that dips below a threshold does not prove that the entire required evaluation window is satisfied. Separate autoscale behavior from the question of whether an app's tier supports autoscale at all.

**Quiz connections:** Q160, Q206, Q217, and the case-study requirement for web-app scaling.

## Container images and persistent data

An image is a packaged application. A registry stores images. A running container is an instance of an image. Pushing an image to a registry does not deploy it to a cluster.

Two related workflows in Q194 are valid in different starting situations: `docker push` uploads a locally built tagged image; `az acr build` performs a registry-based build from source and pushes its result. The PDF mixes the two explanations. Work from the artifact that already exists in the scenario.

For Azure Container Instances, inspect image, CPU/memory, exposed ports, OS type, restart policy and any mounted volume. Container-local writes do not automatically survive replacement; use a supported persistent volume such as the Azure Files mount in the source scenario.

**Quiz connections:** Q127, Q179, Q194.

## AKS concepts retained from the PDF

The current study guide emphasizes ACR, ACI and Container Apps rather than the PDF's heavy AKS emphasis. Keep these concepts for understanding the source, then prioritize the current objectives.

| Task | Tool or concept |
| --- | --- |
| Manage the Azure cluster resource | `az aks` commands / Azure management interfaces |
| Apply Kubernetes YAML | `kubectl apply -f ...` |
| Install the Kubernetes CLI through Azure CLI | `az aks install-cli` |
| Connect users using Entra identities | Configure the supported Entra integration and cluster authorization |
| Change application replica count | Kubernetes workload scaling / HPA |
| Change node capacity automatically | AKS cluster autoscaler |

The **cluster autoscaler** changes nodes; the **Horizontal Pod Autoscaler** changes pod replicas. `kubectl autoscale` is not proof that cluster autoscaling is enabled. [Cluster-autoscaler guidance](https://learn.microsoft.com/en-us/azure/aks/cluster-autoscaler).

For network questions, inspect the actual network mode. Traditional Azure CNI pod-subnet networking and Azure CNI Overlay do not have identical routing behavior. The PDF's directly routable pod-IP scenario must not be generalized to every CNI configuration. A Kubernetes NetworkPolicy restricts pod traffic; a pod security admission setting is a different type of control. Legacy kubenet/Calico combinations are source context, not a universal current deployment recipe.

**Quiz connections:** Q42, Q153-155, Q162, Q177, Q193-194, Q205, Q290, Q316.

## Check your understanding

- Staging option unavailable? **Check plan tier; scale up if necessary.**
- Bad deployment immediately after slot swap? **Consider swapping back.**
- Need private outbound app access? **VNet integration, plus a complete route/DNS path.**
- More pods or more nodes? **HPA and cluster autoscaler solve different problems.**
