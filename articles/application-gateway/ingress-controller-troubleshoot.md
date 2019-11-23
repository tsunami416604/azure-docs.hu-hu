---
title: Bejövő Application Gateway-vezérlő hibaelhárítása
description: Ez a cikk a Application Gateway beáramlási vezérlővel kapcsolatos gyakori kérdések és/vagy problémák elhárításával kapcsolatos dokumentációt tartalmaz.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795515"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>A bejövő adatkezelővel kapcsolatos gyakori kérdések és problémák elhárítása

A [Azure Cloud Shell](https://shell.azure.com/) a legkényelmesebb módszer az AK-val és a AGIC-telepítéssel kapcsolatos problémák elhárítására. Indítsa el a rendszerhéjt a [shell.Azure.com](https://shell.azure.com/) , vagy kattintson a hivatkozásra:

[![Beágyazás elindítása](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Tesztelés egyszerű Kubernetes alkalmazással

Az alábbi lépések feltételezik:
  - Van egy AK-fürtje, amelyen engedélyezve van a speciális hálózatkezelés
  - A AGIC telepítve van az AK-fürtön
  - Már hav egy Application Gatewayt az AK-fürttel megosztott VNET

Annak ellenőrzéséhez, hogy a Application Gateway + AK + AGIC telepítése helyesen van-e beállítva, telepítse a legegyszerűbb lehetséges alkalmazást:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Másolja és illessze be az összes sort a fenti szkriptből egy [Azure Cloud Shellba](https://shell.azure.com/). Győződjön meg arról, hogy a teljes parancs másolása megtörtént – kezdve a `cat` és az utolsó `EOF`is.

![alkalmazása](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Miután az AK-fürt felett sikeresen telepítette az alkalmazást, egy új Pod, szolgáltatás és bejövő forgalom lesz.

A hüvelyek listájának lekérése [Cloud Shell](https://shell.azure.com/): `kubectl get pods -o wide`.
Elvárjuk, hogy létrehozták a "test-agic-app-Pod" nevű Pod-t. IP-címmel fog rendelkezni. Ennek a címnek a Application Gateway VNET belül kell lennie, amely az AK-val együtt használható.

![hüvely](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

A szolgáltatások listájának lekérése: `kubectl get services -o wide`. A rendszer a "test-agic-app-Service" nevű szolgáltatást várta.

![hüvely](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

A ingresses listájának beolvasása: `kubectl get ingress`. Elvárjuk, hogy létrejött a "test-agic-app-beáramló" nevű bejövő erőforrás. Az erőforrás "test.agic.contoso.com" állomásnévvel fog rendelkezni.

![hüvely](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Az egyik hüvely AGIC lesz. `kubectl get pods` a hüvelyek listáját jeleníti meg, amelyek közül az egyik a "beáramló – Azure" lesz. A pod összes naplójának beolvasása `kubectl logs <name-of-ingress-controller-pod>` annak ellenőrzéséhez, hogy sikeres volt-e az üzembe helyezés. A sikeres telepítés a következő sorokat adta hozzá a naplóhoz:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Azt is megteheti, hogy a [Cloud Shell](https://shell.azure.com/) csak azokat a sorokat kérdezi le, amelyekkel a sikeres Application Gateway konfiguráció `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`, ahol a `<ingress-azure....>`nak a AGIC Pod pontos nevét kell megadnia.

Application Gateway a következő konfigurációt fogja alkalmazni:

- Figyelő: ![figyelő](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Útválasztási szabály: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Háttér-készlet:
  - A háttér-címkészlet egyetlen IP-címmel rendelkezik, és a `kubectl get pods -o wide`
![backend_pool által korábban megfigyelt Pod IP-címével fog megjelenni](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Végül a `cURL` parancsot [Cloud Shellon](https://shell.azure.com/) belülről is használhatja az újonnan telepített alkalmazáshoz való http-kapcsolat létesítéséhez:

1. `kubectl get ingress` használata a Application Gateway nyilvános IP-címének lekéréséhez
2. `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>` használata

![hüvely](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

`HTTP/1.1 200 OK` eredménye azt jelzi, hogy a Application Gateway + AK + AGIC rendszer a vártnak megfelelően működik.


## <a name="inspect-kubernetes-installation"></a>Kubernetes telepítésének vizsgálata

### <a name="pods-services-ingress"></a>Hüvelyek, szolgáltatások, bejövő forgalom
A Application Gateway beáramló vezérlő (AGIC) folyamatosan figyeli a következő Kubernetes-erőforrásokat: [üzembe helyezés](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) vagy [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), szolgáltatás, [bejövő](https://kubernetes.io/docs/concepts/services-networking/ingress/) [forgalom](https://kubernetes.io/docs/concepts/services-networking/service/)


A következőnek kell megfelelnie ahhoz, hogy a AGIC a várt módon működjön:
  1. Az ak-nak egy vagy több kifogástalan állapotú **hüvelyrel**kell rendelkeznie.
     Ellenőrizze ezt a [Cloud Shell](https://shell.azure.com/) és a `kubectl get pods -o wide --show-labels`, ha a pod `apsnetapp`rendelkezik, a kimenet így néz ki:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Egy vagy több **szolgáltatás**, amely a fenti hüvelyre hivatkozik a megfelelő `selector` címkéken keresztül.
     [Cloud Shell](https://shell.azure.com/) ellenőrzése a `kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. A bemenő adatok **`kubernetes.io/ingress.class: azure/application-gateway`, a**fenti szolgáltatásra hivatkozva a [Cloud Shell](https://shell.azure.com/) a `kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Tekintse meg a fenti bejövő forgalomhoz tartozó jegyzeteket: `kubectl get ingress aspnetapp -o yaml` (helyettesítse be a `aspnetapp`t a bejövő forgalom nevével)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     A bejövő erőforrást `kubernetes.io/ingress.class: azure/application-gateway`tel kell kiegészíteni.
 

### <a name="verify-observed-namespace"></a>Megfigyelt névtér ellenőrzése

* Szerezze be a meglévő névtereket a Kubernetes-fürtben. Milyen névtérben fut az alkalmazás? A AGIC figyeli ezt a névteret? A megfigyelt névterek megfelelő konfigurálásával kapcsolatban tekintse meg a [több névtér támogatási](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) dokumentációját.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* Az AGIC Pod a `default` névtérben kell lennie (lásd: oszlop `NAMESPACE`). Az egészséges Pod `Running` a `STATUS` oszlopban. Legalább egy AGIC Pod-nek kell lennie.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Ha a AGIC pod nem kifogástalan (`STATUS` a fenti parancsból nem `Running`):
  - a naplók megismerése: `kubectl logs <pod-name>`
  - a pod előző példánya esetében: `kubectl logs <pod-name> --previous`
  - adja meg a pod-t, hogy minél több kontextust kapjon: `kubectl describe pod <pod-name>`


* Rendelkezik Kubernetes- [szolgáltatással](https://kubernetes.io/docs/concepts/services-networking/service/) és a [bejövő](https://kubernetes.io/docs/concepts/services-networking/ingress/) erőforrásokkal?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* A Beérkezők [megjegyzése a következőket](https://kubernetes.io/docs/concepts/services-networking/ingress/) adja meg: `kubernetes.io/ingress.class: azure/application-gateway`? A AGIC csak a Kubernetes bejövő erőforrásait figyeli.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* A AGIC bizonyos kritikus hibák esetén Kubernetes-eseményeket bocsát ki. A következőket tekintheti meg:
  - a terminálon keresztül `kubectl get events --sort-by=.metadata.creationTimestamp`
  - a böngészőben a [Kubernetes webes felülete (irányítópult)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) használatával


## <a name="logging-levels"></a>Naplózási szintek

A AGIC 3 naplózási szinttel rendelkezik. Az 1. szint az alapértelmezett érték, amely a naplózási sorok minimális számát mutatja.
Az 5. szint viszont az összes naplót megjeleníti, beleértve az ARM-re alkalmazott konfiguráció fertőtlenített tartalmát is.

A Kubernetes-Közösség 9 naplózási szintet létesített a [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) eszközhöz. Ebben a tárházban ezek közül hármat használunk, hasonló szemantikai megoldásokkal:


| Részletességi | Leírás |
|-----------|-------------|
|  1        | Alapértelmezett naplózási szint; indítási részletek, figyelmeztetések és hibák megjelenítése |
|  3        | Részletes információk az eseményekről és a változásokról; létrehozott objektumok listája |
|  5        | Átadott objektumok naplója; az ARM-re alkalmazott megtisztított JSON-konfiguráció megjelenítése |


A részletességi szintek az `verbosityLevel` változón keresztül állíthatók be a [Helm-config. YAML](#sample-helm-config-file) fájlban. Növelje a részletességi szintet, hogy `5` a JSON-konfiguráció [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-ből való elküldéséhez:
  - `verbosityLevel: 5` hozzáadása egy sorban a [Helm-config. YAML](#sample-helm-config-file) és újratelepítése
  - naplók beolvasása `kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Példa Helm konfigurációs fájlra
```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

