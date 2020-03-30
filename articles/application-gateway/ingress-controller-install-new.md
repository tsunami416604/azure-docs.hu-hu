---
title: Be- és be- és átjáró létrehozása
description: Ez a cikk az alkalmazásátjáró be- és átjáróvezérlőjének új application gateway-rel történő központi telepítéséről nyújt tájékoztatást.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a0bc6aef1becd53217be0eeb8c865b5c78a5d69f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239467"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Alkalmazásátjáró be- és élesztire vonatkozó vezérlő (AGIC) telepítése új alkalmazásátjáró használatával

Az alábbi utasítások feltételezik, hogy az Application Gateway ingress controller (AGIC) olyan környezetben lesz telepítve, ahol nincsenek már meglévő összetevők.

## <a name="required-command-line-tools"></a>Szükséges parancssori eszközök

Javasoljuk, hogy az [Azure Cloud Shell](https://shell.azure.com/) az alábbi összes parancssori művelethez használható. Indítsa el a shell shell.azure.com vagy kattintson a linkre:

[![Indítás beágyazása](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)

Másik lehetőségként indítsa el a Cloud Shellt az Azure Portalról az alábbi ikon használatával:

![A portál elindítása](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

Az [Azure Cloud Shell](https://shell.azure.com/) már rendelkezik az összes szükséges eszközzel. Ha másik környezetet szeretne használni, győződjön meg arról, hogy a következő parancssori eszközök vannak telepítve:

* `az`- Azure CLI: [telepítési utasítások](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl`- Kubernetes parancssori eszköz: [telepítési utasítások](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm`- Kubernetes csomagkezelő: [telepítési utasítások](https://github.com/helm/helm/releases/latest)
* `jq`- parancssori JSON processzor: [telepítési utasítások](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Identitás létrehozása

Az alábbi lépéseket követve hozzon létre egy Azure Active Directory (AAD) [egyszerű szolgáltatásobjektumot.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) A , `appId` `password`és `objectId` az értékeket a következő lépésekben kell használni.

1. AD szolgáltatásnév létrehozása ([További információ az RBAC-ról):](https://docs.microsoft.com/azure/role-based-access-control/overview)
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    A `appId` `password` JSON-kimenet és értékei a következő lépésekben lesznek


1. Használja `appId` az előző parancs kimenetét az `objectId` új szolgáltatásnév bekéséhez:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    A parancs kimenete `objectId`a , amelyet az alábbi Azure Resource Manager sablonban fog használni.

1. Hozza létre azt a paraméterfájlt, amelyet később az Azure Resource Manager-sablon központi telepítése során fog használni.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    **RBAC-kompatibilis** fürt telepítéséhez `aksEnabledRBAC` állítsa a mezőt`true`

## <a name="deploy-components"></a>Összetevők telepítése
Ez a lépés a következő összetevőket adja hozzá az előfizetéshez:

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [Alkalmazásátjáró](https://docs.microsoft.com/azure/application-gateway/overview) v2
- [Virtuális hálózat](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 2 [alhálózattal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Nyilvános IP-cím](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [Felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), amelyet az [AAD Pod Identity](https://github.com/Azure/aad-pod-identity/blob/master/README.md) fog használni

1. Töltse le az Azure Resource Manager sablont, és szükség szerint módosítsa a sablont.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Telepítse az Azure Resource `az cli`Manager sablont a használatával. Ez akár 5 percet is igénybe vehet.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. A telepítés befejezése után töltse le a `deployment-outputs.json`központi telepítési kimenetet egy .
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Az alkalmazásátjáró be- és feledésvezérlőjének beállítása

Az előző szakaszban található utasításokkal létrehoztunk és konfiguráltunk egy új AKS-fürtöt és egy Alkalmazásátjárót. Most már készen állunk egy mintaalkalmazás és egy be- és be- és be- éshálózat-vezérlő üzembe helyezésére az új Kubernetes-infrastruktúránkba.

### <a name="setup-kubernetes-credentials"></a>Kubernetes-hitelesítő adatok beállítása
A következő lépésekhez szükségünk van a [telepítő kubectl](https://kubectl.docs.kubernetes.io/) parancsra, amelyet az új Kubernetes-fürthöz való csatlakozáshoz fogunk használni. [A](https://shell.azure.com/) Cloud `kubectl` Shell már telepítve van. A CLI-t a Kubernetes hitelesítő adatainak megszerzésére használjuk. `az`

Az újonnan telepített AKS hitelesítő adatainak beolvasása ([tovább):](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Az AAD Pod Identity telepítése
  Az Azure Active Directory Pod Identity tokenalapú hozzáférést biztosít az [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)szolgáltatáshoz.

  [Az AD Pod Identity](https://github.com/Azure/aad-pod-identity) a következő összetevőket adja hozzá a Kubernetes-fürthöz:
   * Kubernetes [CRD-k:](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) `AzureIdentity`, `AzureAssignedIdentity``AzureIdentityBinding`
   * [Felügyelt identitásvezérlő (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) összetevő
   * [Csomópontfelügyelt identitás (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) összetevője


Az AAD Pod-identitás telepítése a fürtre:

   - *RBAC engedélyezve* AKS-fürt

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *RBAC letiltva* AKS-fürt

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Helm telepítése
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) a Kubernetes csomagkezelője. Mi lesz a tőkeáttétel, hogy telepítse a `application-gateway-kubernetes-ingress` csomagot:

1. Telepítse [a Helm-et,](https://docs.microsoft.com/azure/aks/kubernetes-helm) és futtassa a következőt a helm csomag hozzáadásához: `application-gateway-kubernetes-ingress`

    - *RBAC engedélyezve* AKS-fürt

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *RBAC letiltva* AKS-fürt

        ```bash
        helm init
        ```

1. Adja hozzá az AGIC Helm adattárat:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>A be- és nagybe: A vezérlő vezérlőjének diagramja

1. Használja `deployment-outputs.json` a fent létrehozott fájlt, és hozza létre a következő változókat.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Letöltés helm-config.yaml, amely konfigurálja AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Vagy másolja az alábbi YAML fájlt: 
    
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

1. Edit az újonnan letöltött helm-config.yaml `appgw` és `armAuth`töltse ki a szakaszok és .
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Értékek:
     - `verbosityLevel`: Beállítja az AGIC naplózási infrastruktúra részletességi szintjét. A lehetséges értékeket a [Naplózási szintek](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) című témakörben olvassa el.
     - `appgw.subscriptionId`: Az Azure-előfizetési azonosító, amelyben az Application Gateway található. Például: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Annak az Azure Resource Groupnak a neve, amelyben az Application Gateway-t létrehozták. Például: `app-gw-resource-group`
     - `appgw.name`: Az alkalmazásátjáró neve. Például: `applicationgatewayd0f0`
     - `appgw.shared`: Ezt a logikai jelzőt alapértelmezetten a. `false` `true` Ha [megosztott alkalmazásátjáróra](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)van szüksége, állítsa be.
     - `kubernetes.watchNamespace`: Adja meg azt a névközt, amelyet az AGIC-nek figyelnie kell. Ez lehet egyetlen karakterlánc-érték, vagy a névterek vesszővel tagolt listája.
    - `armAuth.type`: lehet `aadPodIdentity` vagy`servicePrincipal`
    - `armAuth.identityResourceID`: Az Azure felügyelt identitásának erőforrásazonosítója
    - `armAuth.identityClientId`: Az identitás ügyfélazonosítója. Az identitással kapcsolatos további információkért lásd alább
    - `armAuth.secretJSON`: Csak akkor szükséges, ha az `armAuth.type` elsődleges szolgáltatástípust választja `servicePrincipal`(ha a beállítás ) 


   > [!NOTE]
   > A `identityResourceID` `identityClientID` és értékek, amelyek során létrehozott [létrehozása identitás lépéseit,](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-new.md#create-an-identity) és lehet beszerezni újra a következő paranccsal:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>`a fenti parancs az Application Gateway erőforráscsoportja. `<identity-name>`a létrehozott identitás neve. Egy adott előfizetés összes identitása a következő használatával jelenik meg:`az identity list`


1. Az Application Gateway be- és vissza- és visszatelepítése vezérlőcsomagjának telepítése:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Mintaalkalmazás telepítése
Most, hogy telepítettük az Application Gateway, az AKS és az AGIC alkalmazást, az [Azure Cloud Shell-en](https://shell.azure.com/)keresztül telepíthetünk egy mintaalkalmazást:

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
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
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

Másik lehetőségként:

* Töltse le a fenti YAML fájlt:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* A YAML-fájl alkalmazása:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Egyéb példák
Ez az útmutató további példákat tartalmaz arra vonatkozóan, hogyan teheti elérhetővé az [AKS-szolgáltatásokat](ingress-controller-expose-service-over-http-https.md) HTTP-n vagy HTTPS-en keresztül az interneten az Application Gateway segítségével.
