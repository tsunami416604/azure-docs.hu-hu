---
title: Az Azure Active Directory integrálása az Azure Kubernetes Service
description: Ismerje meg, hogyan hozhat létre az Azure CLI-vel és az Azure Active Directory-kompatibilis Azure Kubernetes Service (AKS)-fürt
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: mlearned
ms.openlocfilehash: 5b99d76ef20c288d6ae0bd33e1e2b6a75a359d3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616276"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Az Azure Active Directory integrálása az Azure Kubernetes Service-ben az Azure CLI használatával

Az Azure Kubernetes Service (AKS) beállítható úgy, hogy a felhasználók hitelesítéséhez az Azure Active Directory (AD) használja. Ebben a konfigurációban amelyre be tud jelentkezni egy AKS-fürtöt az Azure AD hitelesítési token használatával. Fürt operátorok Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC) a felhasználó identitását, vagy a könyvtár a csoport tagsága alapján is konfigurálható.

Ez a cikk bemutatja, hogyan hozhat létre a szükséges az Azure AD-összetevők, majd fürt üzembe helyezése az Azure AD-kompatibilis, és hozzon létre egy alapszintű RBAC szerepkör az AKS-fürtöt. Emellett [hajtsa végre ezeket a lépéseket az Azure portal használatával][azure-ad-portal].

A teljes minta parancsfájl a cikk ezt használja, lásd: [Azure CLI-minták – AKS-integráció az Azure ad-vel][complete-script].

Az alábbi korlátozások érvényesek:

- Az Azure AD csak egy új, az RBAC-t a fürt létrehozásakor engedélyezhető. Az Azure AD egy meglévő AKS-fürt nem engedélyezhető.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.61 verziójára van szükség, vagy később telepített és konfigurált. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

A konzisztencia és érdekében ez a cikk a parancsok futtatását hozzon létre egy változót a kívánt AKS-fürt nevét. Az alábbi példában a neve *myakscluster*:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Az Azure AD-hitelesítés áttekintése

Az Azure AD-hitelesítés OpenID-kapcsolattal az AKS-fürtök van megadva. OpenID Connect, amely egy identitás-ra épülő az OAuth 2.0 protokollt. Az OpenID Connect további információkért lásd: a [Open ID connect dokumentáció][open-id-connect].

A belül a Kubernetes-fürt Webhook Eszközjogkivonattal történő hitelesítés segítségével ellenőrizze a hitelesítési tokenek. Webhook tokent használó hitelesítés biztosítását konfigurálja és kezeli az AKS-fürt részeként. A Webhook eszközjogkivonattal történő hitelesítés további információkért lásd: a [webhook authentication – dokumentáció][kubernetes-webhook].

> [!NOTE]
> AKS-hitelesítéshez az Azure AD konfigurálása, ha a két Azure AD-alkalmazások úgy vannak konfigurálva. Ez a művelet egy Azure-bérlő rendszergazdája kell elvégezni.

## <a name="create-azure-ad-server-component"></a>Az Azure AD-kiszolgáló-összetevő létrehozása

Integrálható az aks-sel, hozzon létre, és az identitás-kérelmek-végpontként szolgáló Azure AD-alkalmazást használja. Az első Azure AD alkalmazást kell az Azure AD biztonságicsoport-tagság felhasználó beolvasása.

Hozzon létre a kiszolgáló alkalmazást összetevő a a [az ad-alkalmazás létrehozása][az-ad-app-create] command, then update the group membership claims using the [az ad app update][az-ad-app-update] parancsot. Az alábbi példában a *aksname* definiált változó a [megkezdése előtt](#before-you-begin) szakaszt, és létrehoz egy változót

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Most hozzon létre egy szolgáltatásnevet a server alkalmazás használata a [létrehozásához az ad sp][az-ad-sp-create] command. This service principal is used to authenticate itself within the Azure platform. Then, get the service principal secret using the [az ad sp credential reset][az-ad-sp-credential-reset] parancsot, és rendelje hozzá a nevű változóhoz *serverApplicationSecret* használható a következő lépések egyikét:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Az Azure AD engedélyre van szüksége a következő műveletek végrehajtásához:

* Címtáradatok olvasása
* Bejelentkezés és felhasználói profil olvasása

Ezek segítségével engedélyeket rendelhet a [az ad app engedély hozzáadása][az-ad-app-permission-add] parancsot:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Végül adja meg a kiszolgálói alkalmazás használatára vonatkozó az előző lépésben hozzárendelt engedélyeket a [az ad-alkalmazás engedélymegadásának][az-ad-app-permission-grant] command. This step fails if the current account is not a tenant admin. You also need to add permissions for Azure AD application to request information that may otherwise require administrative consent using the [az ad app permission admin-consent][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Az Azure AD ügyfél összetevő létrehozása

A második Azure AD-alkalmazást használja, amikor egy felhasználó bejelentkezik az AKS-fürtöt, a Kubernetes parancssori felülettel (`kubectl`). Az ügyfélalkalmazás a hitelesítési kérelmet telik el a felhasználót, és ellenőrzi a saját hitelesítő adataival és engedélyeivel. Az ügyfél összetevő az Azure AD-alkalmazás létrehozása a [az ad-alkalmazás létrehozása][az-ad-app-create] parancsot:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Hozzon létre egy szolgáltatásnevet a használó ügyfélalkalmazás a [létrehozásához az ad sp][az-ad-sp-create] parancsot:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Szerezze be a server app oAuth2 Azonosítóját, hogy a két alkalmazás-összetevők használatával közötti hitelesítési folyamat a [az ad app show][az-ad-app-show] parancsot. A következő lépésben használja az oAuth2-Azonosítóját.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

A hozzáférés biztosítása az ügyfélalkalmazásnak és az oAuth2-kommunikáció használata az alkalmazás-összetevők server flow használatával a [hozzáadása az ad app engedély][az-ad-app-permission-add] command. Then, grant permissions for the client application to communication with the server application using the [az ad app permission grant][az-ad-app-permission-grant] parancsot:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>A fürt üzembe helyezése

A két Azure AD-alkalmazások létrehozása most már létre magát az AKS-fürtöt. Először is hozzon létre egy erőforrás csoport a [az csoport létrehozása][az-group-create] parancsot. A következő parancs létrehozza az erőforráscsoportot a *EastUS* régió:

Hozzon létre egy erőforráscsoportot, a fürt:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Az Azure-előfizetés használatának a Bérlőazonosító beszerzése a [az fiók show][az-account-show] command. Then, create the AKS cluster using the [az aks create][az-aks-create] parancsot. A parancs az AKS-fürt létrehozása a kiszolgáló és az ügyfél biztosítja alkalmazásazonosítót, a server application szolgáltatás egyszerű titka és a bérlő Azonosítóját:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Végül kérje a fürt rendszergazdai hitelesítő adatok használatával a [az aks get-credentials][az-aks-get-credentials] parancsot. Az alábbi lépések egyikét, a normál kap *felhasználói* fürt hitelesítő adatait, tekintse meg az Azure AD-hitelesítés folyamat.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Az RBAC-kötés létrehozása

Az AKS-fürtöt az Azure Active Directory-fiókkal használhatók legyenek, a szerepkör kötés vagy a fürt szerepkör-kötést kell létrehozni. *Szerepkörök* határozza meg az engedélyeket, és *kötések* azokat alkalmazni a kívánt felhasználókat. Ezeket a hozzárendeléseket is alkalmazható, egy adott névtérhez vagy az egész fürt között. További információkért lásd: [használatával RBAC-hitelesítés][rbac-authorization].

Az egyszerű felhasználónév (UPN) beolvasni a felhasználó bejelentkezett használatával a [az ad aláírt felhasználói show][az-ad-signed-in-user-show] parancsot. Ez a felhasználói fiók engedélyezve van a következő lépésben az Azure AD-integrációhoz.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Ha a felhasználó számára biztosítson az RBAC kötése az Azure AD-bérlőhöz, alapján engedélyek hozzárendelése a *userPrincipalName*. Ha a felhasználó egy másik Azure ad-bérlővel, lekérdezheti, és használja a *objectId* tulajdonság helyett.

Hozzon létre egy YAML-jegyzékfájlt nevű `basic-azure-ad-binding.yaml` , és illessze be a következő tartalmakat. Az utolsó sorban cserélje le a *userPrincipalName_or_objectId* és az egyszerű felhasználónév vagy az objektum-azonosító kimenetét az előző parancs:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Létrehozhatja a ClusterRoleBinding a [a kubectl a alkalmazni][kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Az Azure AD hozzáférési fürt

Most tekintsük tesztelése az AKS-fürtöt az Azure AD-hitelesítés integrációját. Állítsa be a `kubectl` config környezet normál felhasználói hitelesítő adatokkal. Ebben a környezetben az Azure AD vissza minden hitelesítési kérelemre továbbítja.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Mostantól használhatja a [kubectl get pods][kubectl-get] parancsot minden névtér több podok megtekintéséhez:

```console
kubectl get pods --all-namespaces
```

Bejelentkezési kérés egy böngészőben az Azure AD hitelesítő adatok használatával történő hitelesítéshez kap. Miután sikeresen hitelesítése megtörtént, a `kubectl` parancs megjeleníti a a podok az AKS-fürtöt, az alábbi példa kimenetében látható módon:

```console
$ kubectl get pods --all-namespaces

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

A hitelesítési jogkivonat érkezett `kubectl` van gyorsítótárazva. Csak reprompted bejelentkezni, amikor a jogkivonat lejárt, vagy a Kubernetes konfigurációs fájl nem hozza létre újra.

Ha engedélyezési hibaüzenet jelenik meg, miután sikeresen bejelentkezett egy böngészőben, ahogy az alábbi példa kimenetében, ellenőrizze az alábbi lehetséges okokat:

```console
error: You must be logged in to the server (Unauthorized)
```

* A megfelelő Objektumazonosítóval vagy egyszerű felhasználónév, attól függően, ha a felhasználói fiók pedig az Azure AD-bérlőhöz vagy nem meghatározott.
* A felhasználó nincs több mint 200 csoport tagja.
* A kiszolgáló regisztrációja meghatározott titkos konfiguráltak értéke megegyezik. `--aad-server-app-secret`

## <a name="next-steps"></a>További lépések

A teljes szkriptet, amely tartalmazza az ebben a cikkben bemutatott parancsok, lásd: a [az az aks-ben az Azure AD-integrációs szkript minták tárház][complete-script].

Az Azure AD-felhasználók és csoportok segítségével szabályozza a fürterőforrásokhoz való hozzáférést, lásd: [szerepköralapú hozzáférés-vezérlés és az Azure AD-identitások használata az AKS fürt erőforrásokhoz való hozzáférésének][azure-ad-rbac].

Kubernetes-fürtök biztonságos kapcsolatos további információkért lásd: [hozzáférési és azonosító beállításai az aks-ben)][rbac-authorization].

Identitás- és erőforrás-vezérlést ajánlott eljárásokat lásd: [gyakorlati tanácsok a hitelesítés és engedélyezés az aks-ben][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
