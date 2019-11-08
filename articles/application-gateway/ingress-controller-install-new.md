---
title: Bejövő adatvezérlő létrehozása új Application Gateway
description: Ez a cikk tájékoztatást nyújt arról, hogyan helyezhet üzembe egy Application Gateway beléptetési vezérlőt egy új Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 30b5f6593d2d2ca17ad600a55f9dc7e2a379f0f0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795931"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Application Gateway beáramlási vezérlő (AGIC) telepítése új Application Gateway használatával

Az alábbi utasítások azt feltételezik, hogy Application Gateway beáramló vezérlőt (AGIC) olyan környezetben telepítik, amely nem rendelkezik már meglévő összetevőkkel.

## <a name="required-command-line-tools"></a>Szükséges parancssori eszközök

Az alábbi parancssori műveletekhez [Azure Cloud Shell](https://shell.azure.com/) használatát javasoljuk. Indítsa el a rendszerhéjt a shell.azure.com, vagy kattintson a hivatkozásra:

[![Beágyazás elindítása](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)

Másik lehetőségként indítsa el Cloud Shell a Azure Portal a következő ikon használatával:

![Portál elindítása](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

A [Azure Cloud Shell](https://shell.azure.com/) már rendelkezik az összes szükséges eszközzel. Ha másik környezetet szeretne használni, győződjön meg arról, hogy a következő parancssori eszközök vannak telepítve:

* `az` – Azure CLI: [telepítési utasítások](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl` – Kubernetes parancssori eszköz: [telepítési utasítások](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` – Kubernetes csomagkezelő: [telepítési utasítások](https://github.com/helm/helm/releases/latest)
* `jq`-parancssori JSON-processzor: [telepítési utasítások](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Identitás létrehozása

Az alábbi lépéseket követve hozzon létre egy Azure Active Directory (HRE) [egyszerű szolgáltatásnév-objektumot](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Jegyezze fel a `appId`, `password`és `objectId` értékeket – ezeket a következő lépésekben fogjuk használni.

1. AD egyszerű szolgáltatás létrehozása ([További információ a RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)):
    ```bash
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    A JSON-kimenet `appId` és `password` értékeit a következő lépésekben fogjuk használni


1. Használja az előző parancs kimenetének `appId` az új egyszerű szolgáltatás `objectId`ének beszerzéséhez:
    ```bash
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    A parancs kimenete `objectId`, amelyet a rendszer az alábbi Azure Resource Manager sablonban fog használni

1. Hozza létre a paramétert, amelyet később a Azure Resource Manager sablon üzembe helyezéséhez fog használni.
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
    **RBAC** -kompatibilis fürt üzembe helyezéséhez állítsa a `aksEnabledRBAC` mezőt `true`

## <a name="deploy-components"></a>Összetevők üzembe helyezése
Ez a lépés a következő összetevőket adja hozzá az előfizetéséhez:

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview) v2
- [Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 2 [alhálózattal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Nyilvános IP-cím](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [Felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), amelyet a [HRE Pod Identity](https://github.com/Azure/aad-pod-identity/blob/master/README.md) használ majd

1. Töltse le a Azure Resource Manager sablont, és szükség szerint módosítsa a sablont.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Telepítse a Azure Resource Manager sablont a `az cli`használatával. Ez akár 5 percet is igénybe vehet.
    ```bash
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

1. Miután az üzembe helyezés befejeződött, töltse le az üzembe helyezési kimenetet egy `deployment-outputs.json`nevű fájlba.
    ```bash
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Application Gateway bejövő adatkezelő beállítása

Az előző szakaszban ismertetett utasításokkal új AK-fürtöt és egy Application Gateway hoztunk létre és konfiguráltunk. Most már készen áll egy minta-alkalmazás és egy bejövő vezérlő üzembe helyezésére az új Kubernetes-infrastruktúrában.

### <a name="setup-kubernetes-credentials"></a>Kubernetes hitelesítő adatainak beállítása
A következő lépésekhez szükség van a Setup [kubectl](https://kubectl.docs.kubernetes.io/) parancsra, amelyet az új Kubernetes-fürthöz való kapcsolódáshoz fogunk használni. A [Cloud Shell](https://shell.azure.com/) `kubectl` már telepítve van. A Kubernetes hitelesítő adatainak beszerzéséhez `az` CLI-t fogjuk használni.

Hitelesítő adatok beolvasása az újonnan üzembe helyezett AK-hoz ([További információ](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)):
```bash
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>A HRE Pod-identitás telepítése
  Azure Active Directory Pod Identity jogkivonat-alapú hozzáférést biztosít [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)számára.

  A [HRE Pod Identity](https://github.com/Azure/aad-pod-identity) a következő összetevőket fogja hozzáadni a Kubernetes-fürthöz:
   * Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`
   * [Felügyelt identitás-vezérlő (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) összetevő
   * [Csomópont által felügyelt identitás (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) összetevő


A HRE Pod Identity telepítése a fürtre:

   - *RBAC engedélyezve* AK-fürt

    ```bash
    kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
    ```

   - *RBAC letiltva* AK-fürt

    ```bash
    kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
    ```

### <a name="install-helm"></a>A Helm telepítése
A [Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) a Kubernetes csomagkezelő. A `application-gateway-kubernetes-ingress` csomag telepítéséhez a következőt fogjuk használni:

1. Telepítse a [helmt](https://docs.microsoft.com/azure/aks/kubernetes-helm) , és futtassa a következőt `application-gateway-kubernetes-ingress` Helm-csomag hozzáadásához:

    - *RBAC engedélyezve* AK-fürt

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *RBAC letiltva* AK-fürt

        ```bash
        helm init
        ```

1. Adja hozzá a AGIC Helm-tárházat:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>A beáramló vezérlő Helm-diagramjának telepítése

1. Használja a fent létrehozott `deployment-outputs.json` fájlt, és hozza létre a következő változókat.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Töltse le a Helm-config. YAML fájlt, amely az AGIC-t konfigurálja:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Vagy másolja az alábbi YAML-fájlt: 
    
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

1. Szerkessze az újonnan letöltött Helm-config. YAML, és töltse ki a `appgw` és `armAuth`szakaszt.
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
     - `verbosityLevel`: a AGIC naplózási infrastruktúrájának részletességi szintjét állítja be. Lásd: a lehetséges értékek [naplózási szintjei](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) .
     - `appgw.subscriptionId`: az Azure-előfizetés azonosítója, amelyben Application Gateway található. Például: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: azon Azure-erőforráscsoport neve, amelyben a Application Gateway létrejött. Például: `app-gw-resource-group`
     - `appgw.name`: a Application Gateway neve. Például: `applicationgatewayd0f0`
     - `appgw.shared`: ezt a logikai jelzőt alapértelmezett értékre kell állítani a `false`. A `true` beállításnál [megosztott Application Gatewayra](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)van szükség.
     - `kubernetes.watchNamespace`: adja meg azt a AGIC, amelynek a nevét meg kell nézni. Ez lehet egy karakterlánc-érték vagy a névterek vesszővel tagolt listája.
    - `armAuth.type`: lehet `aadPodIdentity` vagy `servicePrincipal`
    - `armAuth.identityResourceID`: az Azure felügyelt identitás erőforrás-azonosítója
    - `armAuth.identityClientId`: az identitás ügyfél-azonosítója. Az identitással kapcsolatos további információkért lásd alább.
    - `armAuth.secretJSON`: csak akkor szükséges, ha a szolgáltatás egyszerű titkos típusa van kiválasztva (ha a `armAuth.type` beállítása `servicePrincipal`) 


   > [!NOTE]
   > A `identityResourceID` és a `identityClientID` az [identitás létrehozása](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-new.md#create-an-identity) lépések során létrehozott értékek, és a következő parancs használatával is megszerezhetők:
   > ```bash
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > a fenti parancsban `<resource-group>` a Application Gateway erőforráscsoport. `<identity-name>` a létrehozott identitás neve. Egy adott előfizetéshez tartozó összes identitás a következő használatával listázható: `az identity list`


1. Telepítse a Application Gateway beáramló vezérlő csomagot:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Minta alkalmazás telepítése
Most, hogy Application Gateway, AK és AGIC telepítve van, telepíthetünk egy minta alkalmazást [Azure Cloud Shell](https://shell.azure.com/)használatával:

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

A következőket teheti:

* Töltse le a fenti YAML fájlt:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* A YAML fájl alkalmazása:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Egyéb példák
Ez a [útmutató](ingress-controller-expose-service-over-http-https.md) több példát mutat be arra, hogyan tehet elérhetővé egy AK-szolgáltatást http-n vagy HTTPS-en keresztül az interneten Application Gateway használatával.
