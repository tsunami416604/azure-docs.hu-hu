---
title: Alkalmazásátjáró be- és visszasiklóvezérlőjének hibaelhárítása
description: Ez a cikk az Application Gateway ingres controllerjével kapcsolatos gyakori kérdések és/vagy problémák elhárításáról nyújt dokumentációt.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795515"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>A Be- és aberendezővel kapcsolatos gyakori problémák és problémák elhárítása

[Az Azure Cloud Shell](https://shell.azure.com/) a legkényelmesebb módja az AKS és az AGIC telepítésével kapcsolatos problémák elhárításának. Indítsa el a shell [shell.azure.com](https://shell.azure.com/) vagy kattintson a linkre:

[![Indítás beágyazása](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Tesztelés egyszerű Kubernetes alkalmazással

Az alábbi lépések a következőket feltételezik:
  - AKS-fürtje engedélyezve van az Advanced Networking szolgáltatással
  - Az AGIC telepítve van az AKS-fürtön
  - Már van egy alkalmazásátjáró az AKS-fürttel megosztott virtuális hálózaton

Annak ellenőrzéséhez, hogy az Application Gateway + AKS + AGIC telepítése megfelelően van-e beállítva, telepítse a lehető legegyszerűbb alkalmazást:

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

Másolja és illessze be az összes sort egyszerre a fenti parancsfájlból egy [Azure Cloud Shellbe.](https://shell.azure.com/) Kérjük, győződjön meg arról, hogy a teljes parancs másolt - `cat` kezdve, beleértve az utolsó `EOF`.

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Az alkalmazás sikeres üzembe helyezése után az AKS-fürt egy új pod, szolgáltatás és egy be- és egy be- és egy be- és egy be- és egy be- és egy be- és egy be- és egy be- és egy.

Szerezd meg a podok `kubectl get pods -o wide`listáját a Cloud [Shell](https://shell.azure.com/)segítségével: .
Arra számítunk, hogy egy pod nevű "test-agic-app-pod" hoztak létre. Lesz egy IP-címe. Ennek a címnek az Alkalmazásátjáró virtuális hálózatán belül kell lennie, amely az AKS-hez használatos.

![Hüvely](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Szerezd meg a `kubectl get services -o wide`szolgáltatások listáját: . Arra számítunk, hogy a szolgáltatás neve "test-agic-app-service".

![Hüvely](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Szerezd meg a listát a `kubectl get ingress`be- éseirányítók: . Arra számítunk, hogy egy "test-agic-app-ingress" nevű ingress erőforrás jött létre. Az erőforrás állomásneve "test.agic.contoso.com".

![Hüvely](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Az egyik kabin az AGIC lesz. `kubectl get pods`megjelenik a hüvelyek listája, amelyek közül az egyik a "ingress-azure" kezdetű. A pod összes naplójának `kubectl logs <name-of-ingress-controller-pod>` beszereznie, hogy ellenőrizze, hogy sikeres volt-e a telepítés. A sikeres telepítés a következő sorokat adta volna hozzá a naplóhoz:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Másik lehetőségként a [Cloud Shell](https://shell.azure.com/) lekérheti csak a sorokat jelzi a sikeres Application Gateway konfiguráció , `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`ahol `<ingress-azure....>` kell a pontos nevét az AGIC pod.

Az Application Gateway a következő konfigurációt alkalmazza:

- Hallgató: ![hallgató](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Útválasztási ![szabály: routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Háttérkészlet:
  - Lesz egy IP-címet a háttércímkészletben, és ez megegyezik a pod `kubectl get pods -o wide` 
 ![IP-címét, amelyet korábban észleltünk backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Végül használhatjuk `cURL` a parancsot a [Cloud Shell-en](https://shell.azure.com/) belül, hogy http-kapcsolatot hozzunk létre az újonnan telepített alkalmazással:

1. Az `kubectl get ingress` Application Gateway nyilvános IP-címének lekért
2. A(z) `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>` használata

![Hüvely](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Az eredmény `HTTP/1.1 200 OK` azt jelzi, hogy az Application Gateway + AKS + AGIC rendszer a várt módon működik.


## <a name="inspect-kubernetes-installation"></a>Kubernetes telepítésének vizsgálata

### <a name="pods-services-ingress"></a>Podok, szolgáltatások, be- és be- és visszaszállítás
Az Application Gateway Ingress Controller (AGIC) folyamatosan figyeli a következő Kubernetes-erőforrásokat: [Telepítés](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) vagy [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [Szolgáltatás](https://kubernetes.io/docs/concepts/services-networking/service/), [Beress](https://kubernetes.io/docs/concepts/services-networking/ingress/)


Az AGIC várható működéséhez a következőknek kell érvényben lennie:
  1. Az AKS-nek egy vagy több egészséges hüvelyt kell **rendelkeznie.**
     Ezt ellenőrizze a `kubectl get pods -o wide --show-labels` [Cloud Shell-ből,](https://shell.azure.com/) `apsnetapp`ha podja van, a kimenet e tekintetben így nézhet ki:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Egy vagy több **szolgáltatás,** hivatkozva a `selector` podok fenti keresztül megfelelő címkéket.
     Ezt ellenőrizze a [Cloud Shell-től](https://shell.azure.com/)`kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Be- és be- és be-** és be- és betolatott `kubernetes.io/ingress.class: azure/application-gateway`, a fenti szolgáltatásra hivatkozva Ellenőrizze ezt a Cloud [Shell-ből](https://shell.azure.com/) a`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. A fenti be- és be- és `kubectl get ingress aspnetapp -o yaml` betolatások megjegyzéseinek megtekintése: (helyettesítse `aspnetapp` a be- és a be- és énem nevét)
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

     A be- ésátuszerőforrást a `kubernetes.io/ingress.class: azure/application-gateway`jegyzeteléssel kell eljegyezni.
 

### <a name="verify-observed-namespace"></a>A megfigyelt névtér ellenőrzése

* A Meglévő névterek bekéselése a Kubernetes-fürtben. Milyen névtérben fut az alkalmazás? Az AGIC figyeli a névteret? A megfigyelt névterek helyes konfigurálásáról a [Többszörös névtér támogatás dokumentációjában](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) olvashat.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* Az AGIC podnak `default` a névtérben `NAMESPACE`kell lennie (lásd az oszlopot ). Egy egészséges hüvely `Running` lenne `STATUS` az oszlopban. Legalább egy AGIC podnak kell lennie.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Ha az AGIC pod`STATUS` nem kifogástalan ( oszlop `Running`a fenti parancs nem):
  - naplókat kaphat, hogy megértse, miért:`kubectl logs <pod-name>`
  - a pod előző példányához:`kubectl logs <pod-name> --previous`
  - írja le a pod, hogy minél több összefüggésben:`kubectl describe pod <pod-name>`


* Van Kubernetes [szolgáltatás](https://kubernetes.io/docs/concepts/services-networking/service/) és [a be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és](https://kubernetes.io/docs/concepts/services-networking/ingress/)
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Van a [be- ésefelújítás](https://kubernetes.io/docs/concepts/services-networking/ingress/) jegyzetekkel: `kubernetes.io/ingress.class: azure/application-gateway`? Az AGIC csak azokat a Kubernetes Ingress-erőforrásokat figyeli, amelyek rendelkeznek ezzel a valameddig.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* Az AGIC kubernetes eseményeket bocsát ki bizonyos kritikus hibák ra. Ezek a következők:
  - a terminálon keresztül`kubectl get events --sort-by=.metadata.creationTimestamp`
  - a böngészőben a [Kubernetes Web Felhasználói Felület (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) használatával


## <a name="logging-levels"></a>Naplózási szintek

Az AGIC 3 naplózási szinttel rendelkezik. Az 1- es szint az alapértelmezett, és minimális számú naplósort jelenít meg.
Az 5.

A Kubernetes közösség 9 szintű naplózást hozott létre a [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) eszközhöz. Ebben a tárházban 3 ilyen, hasonló szemantikát használnak:


| Részletesség | Leírás |
|-----------|-------------|
|  1        | Alapértelmezett naplószint; indítási részletek, figyelmeztetések és hibák megjelenítése |
|  3        | Az eseményekre és változásokra vonatkozó információk bővített tájékoztatása; létrehozott objektumok listái |
|  5        | Átszúrt objektumok naplói; azt mutatja, fertőtlenített JSON config alkalmazott ARM |


A részletességi szintek a `verbosityLevel` [helm-config.yaml](#sample-helm-config-file) fájlban lévő változón keresztül állíthatók be. Növelje a részletességi `5` szintet, hogy a JSON konfigurációt elküldjék az [ARM-nak:](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
  - hozzá `verbosityLevel: 5` egy sort önmagában [helm-config.yaml](#sample-helm-config-file) és újra telepíteni
  - naplók beszerez és`kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Minta Helm konfigurációs fájl
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

