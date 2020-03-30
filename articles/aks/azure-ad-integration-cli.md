---
title: Az Azure Active Directory Azure Kubernetes Service-szel való integrálása
description: Megtudhatja, hogy miként hozhat létre az Azure CLI-t és az Azure Active Directory-kompatibilis Azure Kubernetes-fürt (AKS) fürtjét
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: d17ae12beecf9d83ef6d688af799787c5ccf322b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253051"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integrálja az Azure Active Directoryt az Azure Kubernetes szolgáltatással az Azure CLI használatával

Az Azure Kubernetes-szolgáltatás (AKS) konfigurálható az Azure Active Directory (AD) használatára a felhasználói hitelesítéshez. Ebben a konfigurációban egy AKS-fürtbe egy Azure AD-hitelesítési jogkivonat használatával jelentkezhet be. A fürtfelelősök konfigurálhatják a Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) a felhasználó identitás- vagy címtárcsoport-tagsága alapján.

Ez a cikk bemutatja, hogyan hozhat létre a szükséges Azure AD-összetevőket, majd üzembe helyezhet egy Azure AD-kompatibilis fürtöt, és hozzon létre egy alapvető RBAC-szerepkört az AKS-fürtben. [Ezeket a lépéseket az Azure Portal használatával][azure-ad-portal]is végrehajthatja.

A cikkben használt teljes mintaparancsfájlról az [Azure CLI-minták – AKS-integráció az Azure AD-vel][complete-script]című témakörben olvashat.

Az alábbi korlátozások érvényesek:

- Az Azure AD csak akkor engedélyezhető, ha új, RBAC-kompatibilis fürtöt hoz létre. Az Azure AD nem engedélyezhető egy meglévő AKS-fürtön.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.61-es vagy újabb verziójára van szükség telepítve és konfigurálva. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

Nyissa [https://shell.azure.com](https://shell.azure.com) meg a Cloud Shell t a böngészőjében.

A konzisztencia és a cikkben szereplő parancsok futtatásának elősegítése érdekében hozzon létre egy változót a kívánt AKS-fürtnevéhez. A következő példa a *myakscluster*nevet használja:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Azure AD-hitelesítés – áttekintés

Az Azure AD-hitelesítés az OpenID Connect segítségével rendelkező AKS-fürtök számára biztosított. Az OpenID Connect az OAuth 2.0 protokoll ra épülő identitásréteg. Az OpenID Connectről további információt az [Open ID connect dokumentációjában][open-id-connect]talál.

A Kubernetes-fürt belsejéből webhook token hitelesítési hitelesítés i a hitelesítési jogkivonatok ellenőrzésére szolgál. A Webhook token hitelesítése az AKS-fürt részeként van konfigurálva és kezelve. A Webhook tokenhitelesítésről a [webhook hitelesítési dokumentációjában][kubernetes-webhook]olvashat bővebben.

> [!NOTE]
> Az Azure AD AKS-hitelesítéshez történő konfigurálásakor két Azure AD-alkalmazás van konfigurálva. Ezt a műveletet egy Azure-bérlői rendszergazdának kell végrehajtania.

## <a name="create-azure-ad-server-component"></a>Azure AD-kiszolgálóösszetevő létrehozása

Az AKS-sel való integrációhoz hozzon létre és használjon egy Azure AD-alkalmazást, amely az identitáskérelmek végpontjaként működik. Az első Azure AD-alkalmazás, amire szüksége van, megkapja az Azure AD-csoporttagságot egy felhasználó számára.

Hozza létre a kiszolgálóalkalmazás-összetevőt az [az ad app create][az-ad-app-create] paranccsal, majd frissítse a csoporttagsági jogcímeket az az [ad app update][az-ad-app-update] paranccsal. A következő példa a [Before you begin](#before-you-begin) szakaszban definiált *aksname* változót használja, és létrehoz egy változót

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Most hozzon létre egy egyszerű szolgáltatás a kiszolgáló alkalmazás segítségével az [azad sp create][az-ad-sp-create] parancsot. Ez az egyszerű szolgáltatás az Azure platformon belüli hitelesítésre szolgál. Ezután az [az ad sp hitelesítőadatok visszaállítása][az-ad-sp-credential-reset] paranccsal lekéri a szolgáltatás fő titkos adatmódját, és rendelje hozzá a *serverApplicationSecret* nevű változóhoz az alábbi lépések egyikében:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Az Azure AD-nek engedélyekre van szüksége a következő műveletek végrehajtásához:

* Címtáradatok olvasása
* Bejelentkezés és felhasználói profil olvasása

Rendelje hozzá ezeket az engedélyeket az [ad app engedély hozzáadása][az-ad-app-permission-add] paranccsal:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Végül adja meg az előző lépésben a kiszolgálóalkalmazáshoz rendelt engedélyeket az [az ad app engedély-adadási][az-ad-app-permission-grant] parancs használatával. Ez a lépés sikertelen, ha az aktuális fiók nem bérlői rendszergazda. Az Azure AD-alkalmazáshoz is hozzá kell adnia az Azure AD-alkalmazás engedélyeit, hogy olyan információkat kérjen, amelyek egyébként rendszergazdai jóváhagyást igényelhetnek az [az ad alkalmazás engedélyének rendszergazdai hozzájárulásával:][az-ad-app-permission-admin-consent]

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Azure AD-ügyfélösszetevő létrehozása

A második Azure AD-alkalmazás akkor használatos, amikor egy felhasználó bejelentkezik az`kubectl`AKS-fürtbe a Kubernetes CLI ( ). Ez az ügyfélalkalmazás átveszi a hitelesítési kérelmet a felhasználótól, és ellenőrzi a hitelesítő adatokat és engedélyeket. Hozza létre az Azure AD alkalmazást az ügyfélösszetevőhöz az [az ad app create][az-ad-app-create] paranccsal:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Hozzon létre egy egyszerű szolgáltatás az ügyfélalkalmazás hoz létre az [ad sp create][az-ad-sp-create] parancs:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

A kiszolgálóalkalmazás oAuth2 azonosítójának beszereznie, hogy a hitelesítési folyamat a két alkalmazás-összetevő között az [az ad app show][az-ad-app-show] paranccsal lehetővé tegye a hitelesítést. Ezt az oAuth2 azonosítót a következő lépésben használja a következő lépés.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Adja hozzá az ügyfélalkalmazás és a kiszolgálóalkalmazás-összetevők engedélyeit az oAuth2 kommunikációs folyamat használatához az [az ad app engedély hozzáadása][az-ad-app-permission-add] parancs használatával. Ezután adjon engedélyeket az ügyfélalkalmazásnak a kiszolgálóalkalmazással való kommunikációhoz az [az ad app engedély-ad idparancs][az-ad-app-permission-grant] használatával:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>A fürt telepítése

A két Létrehozott Azure AD-alkalmazással most hozza létre magát az AKS-fürtöt. Először hozzon létre egy erőforráscsoportot az [az csoport létrehozása][az-group-create] paranccsal. A következő példa létrehozza az erőforráscsoportot az *EastUS* régióban:

Erőforráscsoport létrehozása a fürthöz:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Az [az-fiókmegjelenítése][az-account-show] kontbéssze parancsával az Azure-előfizetés bérlői azonosítóját. Ezután hozza létre az AKS-fürtöt az [aks create][az-aks-create] paranccsal. Az AKS-fürt létrehozásának parancsa biztosítja a kiszolgáló- és ügyfélalkalmazás-azonosítókat, a kiszolgálóalkalmazás-szolgáltatás főtitkos adatmódját és a bérlői azonosítót:

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

Végül az [az aks get-credentials][az-aks-get-credentials] paranccsal a fürt felügyeleti hitelesítő adatait. Az alábbi lépések egyikében a rendszeres *felhasználói* fürt hitelesítő adatait kapja meg az Azure AD hitelesítési folyamat működés közben.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>RBAC-kötés létrehozása

Mielőtt egy Azure Active Directory-fiók használható az AKS-fürt, egy szerepkör-kötés vagy fürtszerepkör-kötés kell létrehozni. *A szerepkörök* határozzák meg a megadandó engedélyeket, és *a kötések* a kívánt felhasználókra is alkalmazzák azokat. Ezek a hozzárendelések alkalmazhatók egy adott névtérre vagy a teljes fürtre. További információ: [Az RBAC-engedélyezés használata.][rbac-authorization]

Az [az ad signed-in-user show][az-ad-signed-in-user-show] paranccsal beírt felhasználó egyszerű felhasználónevének (UPN) beolvasása. Ez a felhasználói fiók engedélyezve van az Azure AD-integráció a következő lépésben.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Ha az RBAC-kötést megadó felhasználó ugyanabban az Azure AD-bérlőben található, rendeljen hozzá engedélyeket a *userPrincipalName*alapján. Ha a felhasználó egy másik Azure AD-bérlő, lekérdezése és használata az *objectId* tulajdonság helyett.

Hozzon létre egy `basic-azure-ad-binding.yaml` YAML jegyzékfájl nevű és illessze be a következő tartalmat. Az utolsó sorban cserélje le *userPrincipalName_or_objectId* az előző parancs upn vagy objektumazonosító kimenetére:

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

Hozza létre a ClusterRoleBinding-t a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl fájlnevét:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Access-fürt az Azure AD-vel

Most teszteljük az Azure AD-hitelesítés integrációját az AKS-fürthöz. Állítsa `kubectl` be a konfigurációs környezetet a normál felhasználói hitelesítő adatok használatára. Ez a környezet továbbítja az összes hitelesítési kérelmek et az Azure AD-n keresztül.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Most használja a [kubectl get pods][kubectl-get] parancsot podok megtekintéséhez az összes névterek:

```console
kubectl get pods --all-namespaces
```

Kap egy bejelentkezési kérdést az Azure AD hitelesítő adatok használatával webböngészőhasználatával hitelesítésre. A sikeres hitelesítés t követően a `kubectl` parancs megjeleníti a podokat az AKS-fürtben, ahogy az a következő példa kimenetben látható:

```console
kubectl get pods --all-namespaces
```

```output
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

A kapott `kubectl` hitelesítési jogkivonat a gyorsítótárba kerül. Csak akkor jelentkezik be újra, ha a jogkivonat lejárt, vagy a Kubernetes konfigurációs fájl újra létrejön.

Ha a következő példakimeneti adatoknak megfelelően a webböngészőhasználatával sikeresen bejelentkezett engedélyezési hibaüzenet jelenik meg, ellenőrizze az alábbi lehetséges problémákat:

```output
error: You must be logged in to the server (Unauthorized)
```

* Ön definiálta a megfelelő objektumazonosítót vagy upn-t attól függően, hogy a felhasználói fiók ugyanabban az Azure AD-bérlőben van-e vagy sem.
* A felhasználó nem tagja több mint 200 csoportnak.
* A kiszolgáló alkalmazásregisztrációjában definiált titkos név megegyezik a használatával konfigurált értékkel`--aad-server-app-secret`

## <a name="next-steps"></a>További lépések

A cikkben látható parancsokat tartalmazó teljes parancsfájlt az [AKS-minták tártárában található Azure AD-integrációs parancsfájl tartalmazza.][complete-script]

Ha az Azure AD-felhasználók és csoportok használatával szabályozhatja a fürterőforrásokhoz való hozzáférést, [olvassa el a Fürterőforrásokhoz való hozzáférés szabályozása szerepköralapú hozzáférés-vezérlés sel és az Azure AD-identitások az AKS-ben című témakört.][azure-ad-rbac]

A Kubernetes-fürtök védelméről az [AKS hozzáférési és identitáskezelési beállításai című témakörben][rbac-authorization]talál további információt.

Az identitás- és erőforrás-vezérléssel kapcsolatos gyakorlati tanácsok az [AKS-ben a hitelesítéssel és engedélyezéssel kapcsolatos gyakorlati tanácsok című témakörben található.][operator-best-practices-identity]

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
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
