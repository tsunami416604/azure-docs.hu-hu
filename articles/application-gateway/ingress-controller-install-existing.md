---
title: Be- és átjáró létrehozása meglévő alkalmazásátjáróval
description: Ez a cikk az Application Gateway ingress controller meglévő application gateway-rel való központi telepítéséről nyújt tájékoztatást.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 949f1b3ee3db72e1c541c3dd4c5f74f364f1b514
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869899"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Alkalmazásátjáró be- és élesztire vonatkozó vezérlőjének (AGIC) telepítése meglévő alkalmazásátjáró használatával

Az Application Gateway ingress controller (AGIC) egy pod a Kubernetes-fürtön belül.
Az AGIC figyeli a Kubernetes [ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) erőforrásokat, és létrehozza és alkalmazza az Application Gateway konfigurációját a Kubernetes-fürt állapota alapján.

## <a name="outline"></a>Vázlat:
- [Előfeltételek](#prerequisites)
- [Azure Resource Manager-hitelesítés (ARM)](#azure-resource-manager-authentication)
    - 1. lehetőség: [Aad-pod-identity beállítása és Azure-identitás](#set-up-aad-pod-identity) létrehozása AZ ARM-eken
    - 2. lehetőség: [Egyszerű szolgáltatás használata](#using-a-service-principal)
- [A be- és visszajáró vezérlő telepítése a Helm használatával](#install-ingress-controller-as-a-helm-chart)
- [Többfürtös / megosztott alkalmazásátjáró:](#multi-cluster--shared-application-gateway)Telepítse az AGIC-et olyan környezetben, ahol az Application Gateway meg van osztva egy vagy több AKS-fürt és/vagy más Azure-összetevők között.

## <a name="prerequisites"></a>Előfeltételek
Ez a dokumentum feltételezi, hogy már telepítve van a következő eszközök és infrastruktúra:
- [AKS,](https://azure.microsoft.com/services/kubernetes-service/) [ha engedélyezve van a speciális hálózatkezelés](https://docs.microsoft.com/azure/aks/configure-azure-cni)
- [Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant) ugyanabban a virtuális hálózatban, mint az AKS
- [Az AKS-fürtre telepített AAD pod identitás](https://github.com/Azure/aad-pod-identity)
- [A Cloud Shell](https://shell.azure.com/) az Azure `az` rendszerhéj-környezet, amely CLI-vel rendelkezik, `kubectl`és `helm` telepítve van. Ezek az eszközök szükségesek az alábbi parancsokhoz.

Az AGIC telepítése előtt __készítsen biztonsági másolatot az Application Gateway konfigurációjáról:__
  1. az [Azure Portal](https://portal.azure.com/) `Application Gateway` használata navigálhat a példányhoz
  2. a `Export template` kattintástól`Download`

A letöltött zip fájl JSON-sablonokkal, bash- és PowerShell-parancsfájlokkal fog rendelkezni, amelyekkel visszaállíthatja az App Gateway-t, ha ez szükségessé válik

## <a name="install-helm"></a>Helm telepítése
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) a Kubernetes csomagkezelője. Kihasználjuk a `application-gateway-kubernetes-ingress` csomag telepítéséhez.
A Helm telepítése a [Cloud Shell](https://shell.azure.com/) használatával:

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

## <a name="azure-resource-manager-authentication"></a>Azure Resource Manager hitelesítése

Az AGIC kommunikál a Kubernetes API-kiszolgálóval és az Azure Resource Managerrel. Ezmegköveteli az identitás t az API-k eléréséhez.

## <a name="set-up-aad-pod-identity"></a>AAD Pod-identitás beállítása

[Az AAD Pod Identity](https://github.com/Azure/aad-pod-identity) az AGIC-hez hasonló vezérlő, amely az AKS-en is fut. Az Azure Active Directory-identitások a Kubernetes-podok. Identitás szükséges egy kubernetes-podban egy alkalmazás, hogy képes legyen kommunikálni más Azure-összetevőkkel. Ebben az esetben itt engedélyre van szükségünk az AGIC pod, hogy HTTP kérelmek [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

Kövesse az [AAD Pod identity telepítési utasításokat,](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) hogy hozzáadja ezt az összetevőt az AKS-hez.

Ezután létre kell hoznunk egy Azure-identitást, és arm-engedélyeket kell adnunk neki.
A [Cloud Shell](https://shell.azure.com/) használatával futtathatja az alábbi parancsokat, és identitást hozhat létre:

1. Hozzon létre egy Azure-identitást **ugyanabban az erőforráscsoportban, mint az AKS-csomópontok.** Fontos a megfelelő erőforráscsoport kiválasztása. Az alábbi parancsban szükséges erőforráscsoport *nem* az AKS portálablaktáblán hivatkozott erőforráscsoport. Ez a `aks-agentpool` virtuális gépek erőforráscsoportja. Általában az erőforráscsoport `MC_` az AKS nevével kezdődik, és tartalmazza azt. Például:`MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Az alábbi szerepkör-hozzárendelési parancsokhoz be kell szereznünk `principalId` az újonnan létrehozott identitást:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Adja meg `Contributor` az identitás hozzáférést az Alkalmazásátjáróhoz. Ehhez szüksége van az Alkalmazásátjáró azonosítójára, amely valahogy így fog kinézni:`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Az alkalmazásátjáró-azonosítók listájának beszerezése az előfizetésben a következőkkel:`az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Adja meg `Reader` az identitás hozzáférést az Application Gateway erőforráscsoporthoz. Az erőforráscsoport azonosítója a `/subscriptions/A/resourceGroups/B`következőként fog kinézni: . Az összes erőforráscsoportot a következőkkel szerezheti be:`az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Egyszerű szolgáltatás használata
Az ARM-hoz egy Kubernetes-titokon keresztül is lehet hozzáférést biztosítani az ARM-hoz.

1. Hozzon létre egy Active Directory egyszerű szolgáltatást, és kódolja a base64.Create an Active Directory Service Principal and encode with base64. A base64 kódolás szükséges a JSON blob kubernetes menti.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Adja hozzá a base64 kódolású JSON blobot a `helm-config.yaml` fájlhoz. További információ `helm-config.yaml` a következő szakaszban található.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>A be- és visszavezető vezérlő telepítése helmdiagramként
Az első néhány lépésben telepítjük a Helm's Tiller-t a Kubernetes fürtre. Az AGIC Helm csomag telepítéséhez használja a [Cloud Shell](https://shell.azure.com/) t:

1. Adja `application-gateway-kubernetes-ingress` hozzá a kormánytárt, és hajtsa végre a helm frissítését

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Edit helm-config.yaml és töltse `appgw` ki `armAuth`az értékeket és .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > Az `<identity-resource-id>` `<identity-client-id>` és az előző szakaszban beállított Azure AD Identity tulajdonságai. Ezeket az adatokat a következő `az identity show -g <resourcegroup> -n <identity-name>`parancs `<resourcegroup>` futtatásával kérheti be: .

1. Helm-diagram `application-gateway-kubernetes-ingress` telepítése `helm-config.yaml` az előző lépés konfigurációjával

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Másik lehetőségként egy `helm-config.yaml` lépésben egyesítheti a és a Helm parancsot:
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
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Ellenőrizze az újonnan létrehozott pod naplójában, hogy ellenőrizze, hogy megfelelően indult-e el

Tekintse meg [ezt az útmutatót,](ingress-controller-expose-service-over-http-https.md) amelyből megtudhatja, hogyan teheti elérhetővé az AKS-szolgáltatást HTTP-n vagy HTTPS-en keresztül az interneten egy Azure Application Gateway használatával.



## <a name="multi-cluster--shared-application-gateway"></a>Többfürtös / megosztott alkalmazásátjáró
Alapértelmezés szerint az AGIC átveszi annak az Alkalmazásátjárónak a teljes tulajdonjogát, amelyhez kapcsolódik. Az AGIC 0.8.0-s és újabb verziója egyetlen Application Gateway-t oszthat meg más Azure-összetevőkkel. Használhatjuk például ugyanazt az Application Gateway alkalmazást a Virtuálisgép-méretezési csoportban üzemeltetett alkalmazáshoz, valamint egy AKS-fürthöz.

A beállítás engedélyezése előtt __készítsen biztonsági másolatot az Application Gateway konfigurációjáról:__
  1. az [Azure Portal](https://portal.azure.com/) `Application Gateway` használata navigálhat a példányhoz
  2. a `Export template` kattintástól`Download`

A letöltött zip-fájl JSON-sablonokkal, bash- és PowerShell-parancsfájlokkal fog rendelkezni, amelyekkel visszaállíthatja az Application Gateway-t

### <a name="example-scenario"></a>Példaforgatókönyv
Nézzünk meg egy képzeletbeli Application Gateway, amely kezeli a forgalmat két webhely:
  - `dev.contoso.com`- egy új AKS-en, az Application Gateway és az AGIC használatával
  - `prod.contoso.com`- üzemeltetett egy [Azure virtuális gép scale set](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Az alapértelmezett beállításokkal az AGIC 100%-os tulajdonjogot feltételez az alkalmazásátjáróban, amelyre rámutatott. Az AGIC felülírja az App Gateway összes konfigurációját. Ha úgy döntünk, hogy `prod.contoso.com` manuálisan hozzon létre egy figyelőt (az Application Gateway-en), anélkül, `prod.contoso.com` hogy definiálnánk azt a Kubernetes Ingressben, az AGIC másodperceken belül törli a konfigurációt.

Az AGIC telepítéséhez `prod.contoso.com` és a virtuális gépméretezési készletű gépeinkből `dev.contoso.com` való kiszolgáláshoz az AGIC-et csak konfigurálásra kell korlátoznunk. Ezt megkönnyíti a következő [CRD-k](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)példányosítása:

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

A fenti parancs `AzureIngressProhibitedTarget` létrehoz egy objektumot. Ez tudatja az AGIC-et (0.8.0-s vagy újabb `prod.contoso.com` verzió) az Application Gateway konfigurációjának létezéséről, és kifejezetten utasítja, hogy ne változtasson az adott állomásnévhez kapcsolódó konfigurációkat.


### <a name="enable-with-new-agic-installation"></a>Engedélyezés új AGIC telepítéssel
Ha az AGIC (0.8.0-s vagy újabb verzió) az `helm-config.yaml` Application Gateway konfigurációjának egy részhalmazára szeretné korlátozni, módosítsa a sablont.
A `appgw:` szakasz alatt `shared` adja hozzá a `true`kulcsot, és állítsa a .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Alkalmazza a Helm változások:
  1. Győződjön `AzureIngressProhibitedTarget` meg arról, hogy a CRD a következőkkel van telepítve:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Frissítés Helm:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Ennek eredményeként az AKS lesz `AzureIngressProhibitedTarget` egy `prohibit-all-targets`új példány a neve:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Az `prohibit-all-targets`objektum , ahogy a neve is mutatja, megtiltja az AGIC számára, hogy *bármely* állomás és elérési út konfigurációját módosítsa.
Helm telepítése `appgw.shared=true` telepíti AGIC, de nem módosítja az Application Gateway.


### <a name="broaden-permissions"></a>Engedélyek kiszélesítése
Mivel a `appgw.shared=true` Helm `prohibit-all-targets` és az alapértelmezett blokkolja az AGIC-et a konfigurációk alkalmazásában.

Az AGIC-engedélyek kiszélesítése a következőkkel:
1. Hozzon `AzureIngressProhibitedTarget` létre egy újat az adott beállítással:
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

2. Csak a saját egyéni tiltás létrehozása után törölheti az alapértelmezettet, amely túl tág:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Meglévő AGIC-telepítés engedélyezése
Tegyük fel, hogy már van egy működő AKS, Application Gateway, és konfigurált AGIC a fürtben. Van egy Ingress `prod.contosor.com` számára, és sikeresen szolgálja a forgalmat, hogy az AKS. Szeretnénk hozzáadni `staging.contoso.com` a meglévő Application Gateway, de meg kell, hogy a virtuális [gép.](https://azure.microsoft.com/services/virtual-machines/) Újra felfogjuk használni a meglévő Application Gateway alkalmazást, és manuálisan konfigurálunk egy figyelő- és háttérkészletet a számára. `staging.contoso.com` De manuálisan csípés Application Gateway config [(portálon](https://portal.azure.com)keresztül, [ARM API-k](https://docs.microsoft.com/rest/api/resources/) vagy [Terraform](https://www.terraform.io/)) ütközne AGIC feltételezések teljes tulajdon. Röviddel a módosítások alkalmazása után az AGIC felülírja vagy törli azokat.

Megtilthatjuk az AGIC-nek, hogy módosítsa a konfiguráció egy részhalmazát.

1. Objektum `AzureIngressProhibitedTarget` létrehozása:
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

3. Az Application Gateway konfigurációjának módosítása portálon keresztül - figyelők hozzáadása, útválasztási szabályok, háttérrendszerek stb. Az általunk létrehozott`manually-configured-staging-environment`új objektum ( ) megtiltja az `staging.contoso.com`AGIC számára az Alkalmazásátjáró konfigurációjának felülírását.
