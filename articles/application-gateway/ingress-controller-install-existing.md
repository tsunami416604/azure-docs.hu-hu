---
title: Bejövő vezérlő létrehozása meglévő Application Gateway
description: Ez a cikk tájékoztatást nyújt arról, hogyan helyezhet üzembe egy Application Gateway beléptetési vezérlőt egy meglévő Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: dec43a4d7eb5a9546fcd77cce972b93542ea3b10
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795956"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Application Gateway beáramlási vezérlő (AGIC) telepítése meglévő Application Gateway használatával

A Application Gateway beáramló vezérlő (AGIC) a Kubernetes-fürtön belüli Pod.
A AGIC figyeli a Kubernetes [bejövő](https://kubernetes.io/docs/concepts/services-networking/ingress/) erőforrásait, és a Kubernetes-fürt állapota alapján létrehozza és alkalmazza Application Gateway konfigurációt.

## <a name="outline"></a>Szerkezeti
- [Előfeltételek](#prerequisites)
- [Azure Resource Manager hitelesítés (ARM)](#azure-resource-manager-authentication)
    - 1\. lehetőség: a [HRE-Pod-Identity beállítása](#set-up-aad-pod-identity) és az Azure-identitás létrehozása a Fegyvereken
    - 2\. lehetőség: [egyszerű szolgáltatás használata](#using-a-service-principal)
- [A beáramló vezérlő telepítése a Helm használatával](#install-ingress-controller-as-a-helm-chart)
- [Több fürt/megosztott Application Gateway](#multi-cluster--shared-application-gateway): telepítse a AGIC egy olyan környezetben, ahol a Application Gateway egy vagy több AK-fürt és/vagy más Azure-összetevő között meg van osztva.

## <a name="prerequisites"></a>Előfeltételek
Ez a dokumentum azt feltételezi, hogy már telepítve van a következő eszközök és infrastruktúra:
- [AK](https://azure.microsoft.com/services/kubernetes-service/) , [speciális hálózatkezelés](https://docs.microsoft.com/azure/aks/configure-azure-cni) engedélyezve
- [Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant) ugyanabban a virtuális hálózatban, mint AK
- [HRE Pod-identitás](https://github.com/Azure/aad-pod-identity) telepítve az AK-fürtön
- A [Cloud Shell](https://shell.azure.com/) az Azure rendszerhéj-környezet, amely `az` parancssori felülettel, `kubectl`tel és `helm` telepítve van. Ezek az eszközök az alábbi parancsokhoz szükségesek.

A AGIC telepítése előtt __készítsen biztonsági másolatot a Application Gateway konfigurációról__ :
  1. [Azure Portal](https://portal.azure.com/) navigáljon a `Application Gateway`-példányhoz
  2. `Export template` kattintson a `Download`

A letöltött zip-fájl JSON-sablonokkal, bash-és PowerShell-parancsfájlokkal is rendelkezik, amelyekkel visszaállíthatók az alkalmazás-átjárók, amelyek szükségesek lesznek

## <a name="install-helm"></a>A Helm telepítése
A [Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) a Kubernetes csomagkezelő. A `application-gateway-kubernetes-ingress`-csomag telepítéséhez használni fogjuk.
[Cloud Shell](https://shell.azure.com/) használata a Helm telepítéséhez:

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

## <a name="azure-resource-manager-authentication"></a>Azure Resource Manager hitelesítés

A AGIC a Kubernetes API-kiszolgálóval és a Azure Resource Manager kommunikál. Az API-k eléréséhez identitás szükséges.

## <a name="set-up-aad-pod-identity"></a>A HRE Pod-identitás beállítása

A [HRE Pod Identity](https://github.com/Azure/aad-pod-identity) egy, a AGIC-hoz hasonló vezérlő, amely szintén az AK-on fut. A Kubernetes-hüvelyek Azure Active Directory identitásait köti össze. A Kubernetes Pod-ban lévő alkalmazás identitásának megléte szükséges ahhoz, hogy kommunikálni tudjon más Azure-összetevőkkel. Ebben az esetben szükség van a AGIC Pod engedélyezésére a HTTP-kérések [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-re való elvégzéséhez.

A [HRE Pod Identity telepítési utasításait](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) követve adja hozzá ezt az összetevőt az AK-hoz.

Ezután létre kell hoznia egy Azure-identitást, és hozzá kell adnia az IT-részleg engedélyeit.
A [Cloud Shell](https://shell.azure.com/) használatával futtassa az alábbi parancsokat, és hozzon létre egy identitást:

1. Hozzon létre egy Azure-identitást **ugyanabban az erőforráscsoporthoz, amelyben az AK-csomópontok vannak**. A megfelelő erőforráscsoport kiválasztása fontos. Az alábbi parancsban szükséges erőforráscsoport *nem* az AK-portál ablaktáblán hivatkozott. Ez a `aks-agentpool` virtuális gépek erőforráscsoport. Az erőforráscsoport általában a `MC_`tel kezdődik, és tartalmazza az AK nevét. Például: `MC_resourceGroup_aksABCD_westus`

    ```bash
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Az alábbi szerepkör-hozzárendelési parancsokhoz be kell szereznie `principalId` az újonnan létrehozott identitáshoz:

    ```bash
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Adja meg az identitásnak `Contributor` hozzáférést a Application Gatewayhoz. Ehhez a Application Gateway AZONOSÍTÓjának kell kinéznie, amely a következőhöz hasonló lesz: `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Az előfizetésben található Application Gateway-azonosítók listájának lekérése a: `az network application-gateway list --query '[].id'`

    ```bash
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Adja meg az identitásnak `Reader` hozzáférést a Application Gateway erőforráscsoporthoz. Az erőforráscsoport azonosítója a következőképpen fog kinézni: `/subscriptions/A/resourceGroups/B`. Az összes erőforráscsoport az alábbiakkal szerezhető be: `az group list --query '[].id'`

    ```bash
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Egyszerű szolgáltatás használata
A AGIC hozzáférését az ARM-Kubernetes titkos kulcson keresztül is megadhatja.

1. Hozzon létre egy Active Directory egyszerű szolgáltatást, és Base64-kódolással kódolja. A JSON-blob Kubernetes való mentéséhez Base64-kódolás szükséges.

```bash
az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0
```

2. Adja hozzá a Base64 kódolású JSON-blobot a `helm-config.yaml` fájlhoz. `helm-config.yaml` a következő szakaszban talál további információt.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>A bejövő vezérlő telepítése Helm-diagramként
Az első néhány lépésben a Helm-t a Kubernetes-fürtön telepítjük. A AGIC Helm-csomag telepítéséhez használja a [Cloud shellt](https://shell.azure.com/) :

1. Adja hozzá a `application-gateway-kubernetes-ingress` Helm-tárházat, és hajtson végre egy Helm-frissítést

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
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

1. Szerkessze a Helm-config. YAML, és töltse ki `appgw` és `armAuth`értékeit.
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > A `<identity-resource-id>` és `<identity-client-id>` az előző szakaszban beállított Azure AD-identitás tulajdonságai. Ezt az információt a következő parancs futtatásával kérheti le: `az identity show -g <resourcegroup> -n <identity-name>`, ahol `<resourcegroup>` az az erőforráscsoport, amelyben a legfelső szintű AK-fürt objektum, Application Gateway és felügyelt azonosítás van üzembe helyezve.

1. A Helm chart `application-gateway-kubernetes-ingress` telepítése az előző lépés `helm-config.yaml` konfigurációjával

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Azt is megteheti, hogy a `helm-config.yaml` és a Helm parancsot egyetlen lépésben kombinálja:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Ellenőrizze, hogy az újonnan létrehozott Pod naplója megfelelően elindult-e

Tekintse meg [ezt a útmutatót](ingress-controller-expose-service-over-http-https.md) , amelyből megtudhatja, hogyan tehet elérhetővé egy AK-szolgáltatást http-n vagy HTTPS-en keresztül az interneten egy Azure-Application Gateway használatával.



## <a name="multi-cluster--shared-application-gateway"></a>Több fürt/megosztott Application Gateway
Alapértelmezés szerint a AGIC feltételezi, hogy a Application Gatewayhoz társított teljes tulajdonosi jogosultsággal rendelkezik. A AGIC 0.8.0 és újabb verziói egyetlen Application Gateway is megoszthatnak más Azure-összetevőkkel. Előfordulhat például, hogy ugyanazt a Application Gatewayt használjuk a virtuálisgép-méretezési csoporton és egy AK-fürtön üzemeltetett alkalmazásokhoz.

A beállítás engedélyezése előtt __készítsen biztonsági másolatot a Application Gateway konfigurációjától__ :
  1. [Azure Portal](https://portal.azure.com/) navigáljon a `Application Gateway`-példányhoz
  2. `Export template` kattintson a `Download`

A letöltött zip-fájl JSON-sablonokat, bash-és PowerShell-parancsfájlokat tartalmaz, amelyeket a Application Gateway visszaállítására használhat.

### <a name="example-scenario"></a>Példaforgatókönyv
Tekintsük át a két webhely forgalmát kezelő képzeletbeli Application Gateway:
  - `dev.contoso.com` – egy új AK-ban üzemeltetve, Application Gateway és AGIC használatával
  - `prod.contoso.com` – [Azure virtuálisgép-méretezési csoporton](https://azure.microsoft.com/services/virtual-machine-scale-sets/) futtatva

Az alapértelmezett beállításokkal a AGIC 100%-os tulajdonjogot feltételez a Application Gateway számára. A AGIC felülírja az összes app Gateway konfigurációját. Ha a `prod.contoso.com` (Application Gateway) szolgáltatásban manuálisan hozunk létre figyelőt, anélkül, hogy az a Kubernetes-ba kerül, a AGIC másodpercek alatt törli a `prod.contoso.com`-konfigurációt.

A AGIC telepítéséhez és a virtuálisgép-méretezési csoportba tartozó gépek `prod.contoso.com` kiszolgálásához a csak `dev.contoso.com` konfigurálásához korlátozni kell a AGIC. Ez megkönnyíti a következő [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)létrehozásával:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

A fenti parancs egy `AzureIngressProhibitedTarget` objektumot hoz létre. Ez lehetővé teszi a AGIC (0.8.0 és újabb verziók) ismeretét, hogy megApplication Gateway létét a `prod.contoso.com`, és explicit módon arra utasítja, hogy ne módosítsa az adott állomásnévhez kapcsolódó konfigurációt.


### <a name="enable-with-new-agic-installation"></a>Engedélyezés új AGIC-telepítéssel
A AGIC (0.8.0 és újabb verzió) a Application Gateway konfiguráció egy részhalmazára való korlátozásához módosítsa a `helm-config.yaml` sablont.
A `appgw:` szakaszban adja hozzá `shared` kulcsot, és állítsa be a következőre: `true`.

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

A Helm módosításainak alkalmazása:
  1. Győződjön meg arról, hogy a `AzureIngressProhibitedTarget` CRD telepítve van a szolgáltatással:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. A Helm frissítése:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Ennek eredményeképpen az AK új példánya lesz `AzureIngressProhibitedTarget` nevű `prohibit-all-targets`:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Az objektum `prohibit-all-targets`, ahogy a neve is jelzi, tiltja, hogy a AGIC *bármely* gazdagép és elérési út esetében megváltoztassa a konfigurációt.
A Helm telepítése `appgw.shared=true` telepíti a AGIC-t, de nem módosítja a Application Gateway.


### <a name="broaden-permissions"></a>Kiterjesztési engedélyek
Mivel a `appgw.shared=true`, és az alapértelmezett `prohibit-all-targets` blokkolja a AGIC-t a konfiguráció alkalmazásából.

AGIC engedélyek kiterjesztése a rel:
1. Hozzon létre egy új `AzureIngressProhibitedTarget` a megadott beállítással:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Csak a saját egyéni tiltás létrehozása után törölheti az alapértelmezettet, amely túl széles:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Meglévő AGIC-telepítés engedélyezése
Tegyük fel, hogy már van egy működő AK-beli, Application Gateway és konfigurált AGIC a fürtben. A `prod.contosor.com` és az AK-ból érkező adatforgalom sikeresen szolgál. Szeretnénk `staging.contoso.com` hozzáadni a meglévő Application Gatewayhoz, de [virtuális gépen](https://azure.microsoft.com/services/virtual-machines/)kell üzemeltetni. Újra felhasználjuk a meglévő Application Gateway, és manuálisan konfiguráljuk a figyelőt és a háttér-készleteket a `staging.contoso.com`hoz. De a Application Gateway konfiguráció manuális csípése (a [portálon](https://portal.azure.com), az [ARM API](https://docs.microsoft.com/rest/api/resources/) -kon vagy a [Terraform](https://www.terraform.io/)-on keresztül) ütközne a teljes tulajdonú AGIC. Röviddel a módosítások alkalmazása után a AGIC felülírja vagy törli őket.

A AGIC megtilthatja, hogy a konfiguráció egy részhalmaza módosítható legyen.

1. `AzureIngressProhibitedTarget` objektum létrehozása:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Az újonnan létrehozott objektum megtekintése:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Application Gateway konfiguráció módosítása a portálon keresztül – figyelők hozzáadása, útválasztási szabályok, háttérrendszer stb. Az általunk létrehozott új objektum (`manually-configured-staging-environment`) letiltja a `staging.contoso.com`hoz kapcsolódó Application Gateway-konfiguráció felülírását AGIC.
