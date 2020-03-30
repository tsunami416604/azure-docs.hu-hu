---
title: Az Azure Active Directory Azure Kubernetes Service-szel való integrálása
description: Azure Active Directory-kompatibilis Azure Kubernetes-szolgáltatás (AKS) fürtjének létrehozása
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: 0476acadf5af3a3e2c470fe6c08ebbd355653e22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596589"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Az Azure Active Directory Azure Kubernetes Service-szel való integrálása

Az Azure Kubernetes-szolgáltatás (AKS) konfigurálható az Azure Active Directory (Azure AD) használatára a felhasználói hitelesítéshez. Ebben a konfigurációban az Azure AD-hitelesítési jogkivonat használatával bejelentkezhet egy AKS-fürtbe.

A fürtrendszergazdák konfigurálhatják a Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) a felhasználó identitás- vagy címtárcsoport-tagsága alapján.

Ez a cikk a következőket ismerteti:

- Az AKS és az Azure AD előfeltételeinek üzembe helyezése.
- Üzembe helyezhet egy Azure AD-kompatibilis fürtöt.
- Hozzon létre egy alapvető RBAC-szerepkört az AKS-fürtben az Azure Portal használatával.

Ezeket a lépéseket az [Azure CLI][azure-ad-cli]használatával is végrehajthatja.

> [!NOTE]
> Az Azure AD csak akkor engedélyezhető, ha új RBAC-kompatibilis fürtöt hoz létre. Az Azure AD nem engedélyezhető egy meglévő AKS-fürtön.

## <a name="authentication-details"></a>Hitelesítésrészletei

Az Azure AD-hitelesítés openID Connecttel rendelkező AKS-fürtök számára biztosított. Az OpenID Connect az OAuth 2.0 protokoll ra épülő identitásréteg.

Az OpenID Connect szolgáltatásról az [OpenID Connect és az Azure AD használatával a webalkalmazásokhoz való hozzáférés engedélyezése][open-id-connect]című témakörben talál további információt.

A Kubernetes-fürtön belül webhook token hitelesítési hitelesítési jogkivonatok használják. A Webhook token hitelesítése az AKS-fürt részeként van konfigurálva és kezelve.

A webhook token hitelesítéséről a Kubernetes-dokumentáció [Webhook token hitelesítése][kubernetes-webhook] című szakaszában olvashat bővebben.

Az AKS-fürt Azure AD-hitelesítésének biztosításához két Azure AD-alkalmazás jön létre. Az első alkalmazás egy kiszolgálói összetevő, amely felhasználói hitelesítést biztosít. A második alkalmazás egy ügyfél-összetevő, amely akkor használatos, amikor a CLI hitelesítésre kéri. Ez az ügyfélalkalmazás a kiszolgálóalkalmazást használja az ügyfél által megadott hitelesítő adatok tényleges hitelesítéséhez.

> [!NOTE]
> Amikor az Azure AD-t AKS-hitelesítéshez konfigurálja, két Azure AD-alkalmazás van konfigurálva. Az egyes alkalmazások engedélyeinek delegálásához szükséges lépéseket egy Azure-bérlői rendszergazdának kell elvégeznie.

## <a name="create-the-server-application"></a>A kiszolgálóalkalmazás létrehozása

Az első Azure AD-alkalmazás a felhasználó Azure AD-csoporttagságának lekért alkalmazásával kerül alkalmazásra. Az alkalmazás létrehozása az Azure Portalon:

1. Válassza az **Azure Active Directory** > **alkalmazás regisztrációit** > **Új regisztráció.**

    a. Adjon nevet az alkalmazásnak, például *AKSAzureADServer*.

    b. A **Támogatott fióktípusok**esetén válassza a Fiókok lehetőséget **ebben a szervezeti címtárban.**
    
    c. Válassza a **Web** lehetőséget az Átirányítás URI-típusához, majd *https://aksazureadserver*adja meg az URI-formátumú értéket, például .

    d. Ha végzett, válassza a **Regisztráció** lehetőséget.

2. Válassza **a Manifest**lehetőséget, majd szerkessze a **groupMembershipClaims:** value as **All**értéket. Ha végzett a frissítésekkel, válassza a **Mentés gombot.**

    ![Csoporttagság frissítése az összesre](media/aad-integration/edit-manifest.png)

3. Az Azure AD-alkalmazás bal oldali ablaktáblájában válassza **a Tanúsítványok & titkos kulcsok**lehetőséget.

    a. Válassza a **+ Új ügyféltitok lehetőséget.**

    b. Adjon hozzá egy kulcsleírást, például *az AKS Azure AD-kiszolgálót.* Válassza ki a lejárati időt, majd válassza **a Hozzáadás lehetőséget.**

    c. Figyelje meg a kulcsértéket, amely csak ebben az időben jelenik meg. Ha egy Azure AD-kompatibilis AKS-fürt, ezt az értéket nevezzük a kiszolgálóalkalmazás titkos.

4. Az Azure AD-alkalmazás bal oldali ablaktáblájában válassza az **API-engedélyek lehetőséget,** majd válassza **a + Engedély hozzáadása**lehetőséget.

    a. A **Microsoft API-k csoportban**válassza a **Microsoft Graph**lehetőséget.

    b. Jelölje be a **Delegált engedélyek jelölőnégyzetet,** majd jelölje be a **Directory > Directory.Read.All (Könyvtáradatok olvasása) jelölőnégyzetet.**

    c. Ha nem létezik alapértelmezett delegált engedély a **User > User.Read (Bejelentkezés és felhasználói profil olvasása)** számára, jelölje be a mellette lévő jelölőnégyzetet.

    d. Jelölje be **az Alkalmazásengedélyek**lehetőséget, majd jelölje be a **Directory > Directory.Read.All (Könyvtáradatok olvasása) jelölőnégyzetet.**

    ![Diagramengedélyek beállítása](media/aad-integration/graph-permissions.png)

    e. A frissítések mentéséhez válassza az **Engedélyek hozzáadása** lehetőséget.

    f. A **Grant consent (Engedélyező) csoportban**válassza **a Rendszergazdai hozzájárulás megadása**lehetőséget. Ez a gomb nem lesz elérhető, a használt aktuális fiók nem szerepel bérlői rendszergazdaként.

    Az engedélyek sikeres megadása után a portálon a következő értesítés jelenik meg:

   ![Értesítés a megadott sikeres engedélyekről](media/aad-integration/permissions-granted.png)

5. Az Azure AD-alkalmazás bal oldali ablaktáblájában válassza az **API megjelenítése**lehetőséget, majd a + **Hatókör hozzáadása**lehetőséget.
    
    a. Adjon meg egy **hatókörnevet**, egy **rendszergazdai jóváhagyás megjelenítendő nevét,** majd egy **rendszergazdai hozzájárulási leírást,** például *az AKSAzureADServer.*

    b. Győződjön meg **arról,** hogy az Állapot beállítása **Engedélyezve**.

    ![A kiszolgálóalkalmazás api-ként való megjelenítése más szolgáltatásokkal való használatra](media/aad-integration/expose-api.png)

    c. Válassza **a Hatókör hozzáadása**lehetőséget.

6. Térjen vissza az alkalmazás **áttekintése** lapra, és jegyezze fel az **alkalmazás (ügyfél) azonosítóját**. Ha egy Azure AD-kompatibilis AKS-fürt, ezt az értéket nevezzük a kiszolgálóalkalmazás-azonosító.

    ![Alkalmazásazonosító beszereznie](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Az ügyfélalkalmazás létrehozása

A második Azure AD-alkalmazás akkor használatos, amikor bejelentkezik a Kubernetes CLI (kubectl).

1. Válassza az **Azure Active Directory** > **alkalmazás regisztrációit** > **Új regisztráció.**

    a. Adjon nevet az alkalmazásnak, például *AKSAzureADClient*.

    b. A **Támogatott fióktípusok**esetén válassza a Fiókok lehetőséget **ebben a szervezeti címtárban.**

    c. Válassza a **Web** lehetőséget az Átirányítás URI-típusához, majd *https://aksazureadclient*adja meg az URI-formátumú értéket, például .

    >[!NOTE]
    >Ha egy új RBAC-kompatibilis fürt támogatja az Azure Monitor tárolók, adja hozzá a következő **Web** két további átirányítási URL-címeket a listához webalkalmazás-típusok. Az első alap URL-értéknek, `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` a második `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`alap URL-értéknek pedig a legyen .
    >
    >Ha ezt a funkciót az Azure China-ban használja, `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` az első alap URL-értéknek kell lennie, és a második alap URL-értéknek kell lennie. `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >További információ: [A Live Data (előzetes verzió) szolgáltatás beállítása az](../azure-monitor/insights/container-insights-livedata-setup.md) Azure Monitor tárolókhoz, és a hitelesítés konfigurálásának lépései az [AD integrált hitelesítés konfigurálása](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) szakaszban.

    d. Ha végzett, válassza a **Regisztráció** lehetőséget.

2. Az Azure AD-alkalmazás bal oldali ablaktáblájában válassza az **API-engedélyek lehetőséget,** majd válassza **a + Engedély hozzáadása**lehetőséget.

    a. Válassza **a Saját API-k**lehetőséget, majd válassza ki az előző lépésben létrehozott Azure AD-kiszolgálóalkalmazást, például az *AKSAzureADServer alkalmazást.*

    b. Válassza **a Delegált engedélyek lehetőséget,** majd jelölje be az Azure AD-kiszolgálóalkalmazás melletti jelölőnégyzetet.

    ![Alkalmazásengedélyek konfigurálása](media/aad-integration/select-api.png)

    c. Válassza **az Engedélyek hozzáadása**lehetőséget.

    d. A **Grant consent (Engedélyező) csoportban**válassza **a Rendszergazdai hozzájárulás megadása**lehetőséget. Ez a gomb nem érhető el, ha az aktuális fiók nem bérlői rendszergazda. Az engedélyek megadásakor a portálon a következő értesítés jelenik meg:

    ![Értesítés a megadott sikeres engedélyekről](media/aad-integration/permissions-granted.png)

3. Az Azure AD-alkalmazás bal oldali ablaktáblájában válassza a **Hitelesítés**lehetőséget.

    - Az **Alapértelmezett ügyféltípus csoportban**válassza az **Igen** lehetőséget az ügyfél **nyilvános ügyfélként való kezelésére.**

5. Az Azure AD-alkalmazás bal oldali ablaktáblájában jegyezze fel az alkalmazásazonosítóját. Ha egy Azure AD-kompatibilis AKS-fürt, ezt az értéket nevezzük az ügyfélalkalmazás-azonosító.

   ![Az alkalmazásazonosító beszereznie](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>A bérlőazonosító beszereznie

Ezután az Azure-bérlő azonosítóját. Ez az érték az AKS-fürt létrehozásakor használatos.

Az Azure Portalon válassza az Azure Active > **Directory-tulajdonságok lehetőséget,** és jegyezze fel a **címtárazonosítót.** **Azure Active Directory** Amikor létrehoz egy Azure AD-kompatibilis AKS-fürt, ezt az értéket nevezzük a bérlői azonosító.

![Az Azure-beli bérlői azonosító beszereznie](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Az AKS-fürt telepítése

Az [az csoport létrehozása][az-group-create] paranccsal hozzon létre egy erőforráscsoportot az AKS-fürthöz.

```azurecli
az group create --name myResourceGroup --location eastus
```

Az [AKS-fürt][az-aks-create] üzembe helyezéséhez használja az aks create parancsot. Ezután cserélje le a következő mintaparancs értékeit. Használja az Azure AD-alkalmazások létrehozásakor gyűjtött értékeket a kiszolgálóalkalmazás-azonosítóhoz, az alkalmazástitkos kulcsot, az ügyfélalkalmazás-azonosítót és a bérlői azonosítót.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

Az AKS-fürt létrehozása néhány percet vesz igénybe.

## <a name="create-an-rbac-binding"></a>RBAC-kötés létrehozása

> [!NOTE]
> A fürtszerepkör kötési neve a kis- és nagybetűket megkülönböztető.

Mielőtt egy AKS-fürthöz Azure Active Directory-fiókot használna, létre kell hoznia a szerepkör-kötést vagy a fürtszerepkör-kötést. A szerepkörök határozzák meg a megadandó engedélyeket, és a kötések a kívánt felhasználókra is alkalmazzák azokat. Ezek a hozzárendelések alkalmazhatók egy adott névtérre vagy a teljes fürtre. További információ: [Az RBAC-engedélyezés használata.][rbac-authorization]

Először használja az [aks get-credentials][az-aks-get-credentials] parancsot az `--admin` argumentummal, hogy jelentkezzen be a fürtbe rendszergazdai hozzáféréssel.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Ezután hozzon létre ClusterRoleBinding egy Azure AD-fiók, amely hozzáférést szeretne adni az AKS-fürthöz. A következő példa teljes hozzáférést biztosít a fióknak a fürt összes névteréhez:

- Ha a felhasználó, amiért megadja az RBAC-kötés ugyanabban az Azure AD-bérlőben van, rendeljen hozzá engedélyeket az egyszerű felhasználónév (UPN) alapján. Lépjen tovább a lépésre a ClusterRoleBinding YAML-jegyzékfájljának létrehozásához.

- Ha a felhasználó egy másik Azure AD-bérlő, lekérdezése és használata az **objectId** tulajdonság helyett. Szükség esetén az [az ad user show][az-ad-user-show] paranccsal szerezd be a szükséges felhasználói fiók objectId azonosítóját. Adja meg a szükséges fiók egyszerű felhasználónevét (UPN):

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Hozzon létre egy fájlt, például *rbac-aad-user.yaml*, majd illessze be a következő tartalmat. Az utolsó sorban cserélje le **userPrincipalName_or_objectId** az upn vagy az objektumazonosítóra. A választás attól függ, hogy a felhasználó ugyanaz az Azure AD-bérlő, vagy sem.

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

Alkalmazza a kötést a [kubectl apply][kubectl-apply] paranccsal, ahogy az a következő példában látható:

```console
kubectl apply -f rbac-aad-user.yaml
```

Egy szerepkör-kötés is létrehozható az Azure AD-csoport minden tagja számára. Az Azure AD-csoportok a csoportobjektum-azonosító használatával vannak megadva, ahogy az a következő példában látható.

Hozzon létre egy fájlt, például *rbac-aad-group.yaml*, majd illessze be a következő tartalmat. Frissítse a csoportobjektum-azonosítót az Azure AD-bérlőegyikdel:

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
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Alkalmazza a kötést a [kubectl apply][kubectl-apply] paranccsal, ahogy az a következő példában látható:

```console
kubectl apply -f rbac-aad-group.yaml
```

A Kubernetes-fürt RBAC-mal való védelméről az [RBAC-engedélyezés használata című][rbac-authorization]témakörben talál további információt.

## <a name="access-the-cluster-with-azure-ad"></a>A fürt elérése az Azure AD-vel

Húzza le a környezetben a nem rendszergazdai felhasználó az [az aks get-credentials][az-aks-get-credentials] paranccsal.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Miután futtatta a parancsot, a rendszer kéri, hogy hitelesítse magát az `kubectl` Azure használatával. A folyamat befejezéséhez kövesse a képernyőn megjelenő utasításokat, ahogy az a következő példában látható:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Amikor a folyamat befejeződött, a hitelesítési jogkivonat gyorsítótárba kerül. A rendszer csak akkor kéri, hogy jelentkezzen be újra, amikor a jogkivonat lejár, vagy a Kubernetes konfigurációs fájl újra létrejön.

Ha a sikeres bejelentkezés után megjelenik egy engedélyezési hibaüzenet, ellenőrizze az alábbi feltételeket:

```console
error: You must be logged in to the server (Unauthorized)
```


- Ön definiálta a megfelelő objektumazonosítót vagy upn-t attól függően, hogy a felhasználói fiók ugyanabban az Azure AD-bérlőben van-e vagy sem.
- A felhasználó nem tagja 200-nál több csoportnak.
- A kiszolgáló alkalmazásregisztrációjában megadott titkos titok megegyezik `--aad-server-app-secret`a használatával konfigurált értékkel.

## <a name="next-steps"></a>További lépések

Ha az Azure AD-felhasználók és csoportok használatával szabályozhatja a fürterőforrásokhoz való hozzáférést, [olvassa el a Fürterőforrásokhoz való hozzáférés szabályozása szerepköralapú hozzáférés-vezérlés sel és az Azure AD-identitások az AKS-ben című témakört.][azure-ad-rbac]

A Kubernetes-fürtök védelméről az [AKS hozzáférési és identitáskezelési beállításai][rbac-authorization]című témakörben talál további információt.

Az identitás- és erőforrás-vezérlésről az [AKS-ben a hitelesítéssel és engedélyezéssel kapcsolatos gyakorlati tanácsok][operator-best-practices-identity]című témakörben olvashat bővebben.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
