---
title: Azure Active Directory integrálása az Azure Kubernetes szolgáltatással
description: Azure Active Directory-kompatibilis Azure Kubernetes Service-(ak-) fürtök létrehozása
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 02/02/2019
ms.author: mlearned
ms.openlocfilehash: 956cfbafeebadaa23ba3a48f7d38c6f95b412bd9
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158213"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Azure Active Directory integrálása az Azure Kubernetes szolgáltatással

Az Azure Kubernetes Service (ak) konfigurálható úgy, hogy Azure Active Directory (Azure AD) használatát használja a felhasználói hitelesítéshez. Ebben a konfigurációban az Azure AD-hitelesítési token használatával tud bejelentkezni egy AK-fürtbe.

A Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) állíthat be a felhasználó identitása vagy a címtár csoporttagság alapján.

Ez a cikk a következőket ismerteti:

- Telepítse az AK-ra és az Azure AD-ra vonatkozó előfeltételeket.
- Azure AD-kompatibilis fürt üzembe helyezése.
- Hozzon létre egy alapszintű RBAC-szerepkört az AK-fürtben a Azure Portal használatával.

Ezeket a lépéseket az [Azure CLI][azure-ad-cli]használatával is elvégezheti.

> [!NOTE]
> Az Azure AD csak akkor engedélyezhető, ha új RBAC-kompatibilis fürtöt hoz létre. Az Azure AD nem engedélyezhető egy meglévő AK-fürtön.

## <a name="authentication-details"></a>Hitelesítés részletei

Az Azure AD-hitelesítést az OpenID Connecttel rendelkező AK-fürtökhöz biztosítjuk. Az OpenID Connect egy OAuth 2,0 protokollra épülő identitási réteg.

Az OpenID Connecttel kapcsolatos további információkért lásd: [hozzáférés engedélyezése webalkalmazásokhoz az OpenID Connect és az Azure ad használatával][open-id-connect].

A Kubernetes-fürtön belül a rendszer webhook token-hitelesítést használ a hitelesítési jogkivonatokhoz. A webhook-jogkivonat hitelesítése az AK-fürt részeként van konfigurálva és felügyelve.

A webhook jogkivonat-hitelesítéssel kapcsolatos további információkért tekintse meg a Kubernetes dokumentációjának [webhook token-hitelesítés][kubernetes-webhook] című szakaszát.

Ha az Azure AD-hitelesítést egy AK-fürthöz szeretné biztosítani, két Azure AD-alkalmazás jön létre. Az első alkalmazás egy kiszolgáló-összetevő, amely felhasználói hitelesítést biztosít. A második alkalmazás egy ügyfél-összetevő, amelyet a parancssori felület hitelesítésre való felszólításakor használ a rendszer. Ez az ügyfélalkalmazás a kiszolgálói alkalmazást használja az ügyfél által megadott hitelesítő adatok tényleges hitelesítéséhez.

> [!NOTE]
> Ha az Azure AD-t AK-hitelesítésre konfigurálja, két Azure AD-alkalmazás van konfigurálva. Az egyes alkalmazásokra vonatkozó engedélyek delegálásának lépéseit egy Azure bérlői rendszergazdának kell elvégeznie.

## <a name="create-the-server-application"></a>A kiszolgálóalkalmazás létrehozása

Az első Azure AD-alkalmazás a felhasználó Azure AD-csoporttagság beszerzéséhez lesz alkalmazva. Az alkalmazás létrehozása a Azure Portalban:

1. Válassza a **Azure Active Directory** > **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget.

    a. Adjon nevet az alkalmazásnak, például *AKSAzureADServer*.

    b. **Támogatott fióktípus**esetén **csak a szervezeti címtárban**válassza a fiókok lehetőséget.
    
    c. Válassza a **web** lehetőséget az átirányítási URI típushoz, majd adjon meg egy URI-formátumú értéket, például *https://aksazureadserver* .

    d. Ha elkészült, válassza a **regisztráció** lehetőséget.

2. Válassza a **jegyzékfájl**lehetőséget, majd szerkessze a **groupMembershipClaims:** Value **értéket.** Ha végzett a frissítésekkel, válassza a **Mentés**lehetőséget.

    ![Csoporttagság frissítése az összesre](media/aad-integration/edit-manifest.png)

3. Az Azure AD-alkalmazás bal oldali ablaktábláján válassza a **tanúsítványok & Secrets**elemet.

    a. Válassza az **+ új ügyfél titkot**.

    b. Adja meg a kulcs leírását, például: *AK Azure ad Server*. Válassza ki a lejárati időt, majd kattintson a **Hozzáadás**gombra.

    c. Jegyezze fel a kulcs értékét, amely csak most jelenik meg. Amikor üzembe helyez egy Azure AD-kompatibilis AK-fürtöt, ezt az értéket a kiszolgálói alkalmazás titkának nevezzük.

4. Az Azure AD-alkalmazás bal oldali ablaktábláján válassza az **API-engedélyek**lehetőséget, majd válassza az **+ engedély hozzáadása**lehetőséget.

    a. A **Microsoft API**-k területen válassza a **Microsoft Graph**lehetőséget.

    b. Válassza a **delegált engedélyek**lehetőséget, majd jelölje be a **könyvtár > Directory. Read. All (címtáradatok olvasása)** jelölőnégyzetet.

    c. Ha a felhasználóhoz > felhasználó számára egy alapértelmezett delegált engedély van **. az olvasás (bejelentkezés és a felhasználói profil olvasása)** nem létezik, jelölje be a jelölőnégyzetet.

    d. Válassza az **alkalmazás engedélyei**lehetőséget, majd jelölje be a **könyvtár > Directory. Read. All (címtáradatok olvasása)** jelölőnégyzetet.

    ![Gráf engedélyeinek beállítása](media/aad-integration/graph-permissions.png)

    e. A frissítések mentéséhez válassza az **engedélyek** megadása lehetőséget.

    f. A **jóváhagyás engedélyezése**területen válassza a **rendszergazdai jóváhagyás megadása**lehetőséget. Ez a gomb nem érhető el, mert a jelenleg használt fiók nem szerepel bérlői rendszergazdaként.

    Az engedélyek sikeres megadása után a következő értesítés jelenik meg a portálon:

   ![A sikeres engedélyek bejelentése](media/aad-integration/permissions-granted.png)

5. Az Azure AD-alkalmazás bal oldali ablaktábláján válassza ki az **API közzététele**lehetőséget, majd válassza **a + hatókör hozzáadása**elemet.
    
    a. Adja meg a **hatókör nevét**, a rendszergazda által megadott **megjelenítendő nevet**, majd egy **rendszergazdai beleegyező leírást** , például *AKSAzureADServer*.

    b. Győződjön meg arról, hogy az **állapot** beállítás **engedélyezve**értékre van állítva.

    ![A kiszolgálói alkalmazás elérhetővé tétele API-ként más szolgáltatásokkal való használathoz](media/aad-integration/expose-api.png)

    c. Válassza a **hatókör hozzáadása**elemet.

6. Térjen vissza az alkalmazás **áttekintése** lapra, és jegyezze fel az **alkalmazás (ügyfél) azonosítóját**. Ha Azure AD-kompatibilis AK-fürtöt helyez üzembe, ezt az értéket kiszolgálóalkalmazás-AZONOSÍTÓnak nevezzük.

    ![Alkalmazás AZONOSÍTÓjának beolvasása](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Az ügyfélalkalmazás létrehozása

A második Azure AD-alkalmazást akkor használja a rendszer, amikor bejelentkezik a Kubernetes CLI-vel (kubectl).

1. Válassza a **Azure Active Directory** > **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget.

    a. Adjon nevet az alkalmazásnak, például *AKSAzureADClient*.

    b. **Támogatott fióktípus**esetén **csak a szervezeti címtárban**válassza a fiókok lehetőséget.

    c. Válassza a **web** lehetőséget az átirányítási URI típushoz, majd írjon be egy URI-formázott értéket, például *https://aksazureadclient* .

    >[!NOTE]
    >Ha új RBAC-kompatibilis fürtöt hoz létre a tárolók Azure Monitor támogatásához, adja hozzá a következő két további átirányítási URL-címet a listához **webalkalmazás-** típusokként. Az első URL-cím értékének `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`nak kell lennie, és a második alap URL-címnek `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`nak kell lennie.
    >
    >Ha ezt a funkciót az Azure China-ban használja, az első alap URL-címet `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` kell megadni, és a második alap URL-címet `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`kell megadni.
    >
    >További információkért lásd: [a Azure monitor élő adatok (előzetes verzió) szolgáltatásának beállítása](../azure-monitor/insights/container-insights-livedata-setup.md) a tárolók számára, valamint a hitelesítés konfigurálásának lépései az [ad integrált hitelesítés konfigurálása](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) szakaszban.

    d. Ha elkészült, válassza a **regisztráció** lehetőséget.

2. Az Azure AD-alkalmazás bal oldali ablaktábláján válassza az **API-engedélyek**lehetőséget, majd válassza az **+ engedély hozzáadása**lehetőséget.

    a. Válassza a **saját API**-k lehetőséget, majd válassza ki az előző lépésben létrehozott Azure ad Server-alkalmazást, például *AKSAzureADServer*.

    b. Válassza a **delegált engedélyek**lehetőséget, majd jelölje be az Azure ad Server-alkalmazás melletti jelölőnégyzetet.

    ![Alkalmazás engedélyeinek konfigurálása](media/aad-integration/select-api.png)

    c. Válassza az **engedélyek hozzáadása**lehetőséget.

    d. A **jóváhagyás engedélyezése**területen válassza a **rendszergazdai jóváhagyás megadása**lehetőséget. Ez a gomb nem érhető el, ha az aktuális fiók nem bérlői rendszergazda. Az engedélyek megadása után a következő értesítés jelenik meg a portálon:

    ![A sikeres engedélyek bejelentése](media/aad-integration/permissions-granted.png)

3. Az Azure AD-alkalmazás bal oldali ablaktábláján válassza a **hitelesítés**lehetőséget.

    - Az **alapértelmezett ügyfél típusa**területen válassza az **Igen** lehetőséget, ha **az ügyfelet nyilvános ügyfélként szeretné kezelni**.

5. Az Azure AD-alkalmazás bal oldali ablaktábláján jegyezze fel az alkalmazás AZONOSÍTÓját. Ha Azure AD-kompatibilis AK-fürtöt helyez üzembe, ezt az értéket ügyfélalkalmazás-AZONOSÍTÓnak nevezzük.

   ![Az alkalmazás AZONOSÍTÓjának beolvasása](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>A bérlő AZONOSÍTÓjának beolvasása

Ezután szerezze be az Azure-bérlő AZONOSÍTÓját. Ez az érték az AK-fürt létrehozásakor használatos.

A Azure Portal válassza ki **Azure Active Directory** > a **Tulajdonságok** elemet, és jegyezze fel a **könyvtár azonosítóját**. Amikor létrehoz egy Azure AD-kompatibilis AK-fürtöt, ezt az értéket a bérlői AZONOSÍTÓnak nevezzük.

![Az Azure-bérlő AZONOSÍTÓjának beolvasása](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Az AK-fürt üzembe helyezése

Az az [Group Create][az-group-create] parancs használatával hozzon létre egy ERŐFORRÁSCSOPORTOT az AK-fürthöz.

```azurecli
az group create --name myResourceGroup --location eastus
```

Használja az az [AK Create][az-aks-create] parancsot az AK-fürt üzembe helyezéséhez. Ezután cserélje le a következő példában szereplő parancs értékeit. Az Azure AD-alkalmazások a kiszolgálói alkalmazás AZONOSÍTÓjának, az alkalmazás titkos kódjának, az ügyfélalkalmazás azonosítójának és a bérlő AZONOSÍTÓjának létrehozásakor gyűjtött értékeket használja.

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

Egy AK-fürt létrehozása néhány percet vesz igénybe.

## <a name="create-an-rbac-binding"></a>RBAC kötés létrehozása

> [!NOTE]
> A fürt szerepkörének kötési neve megkülönbözteti a kis-és nagybetűket.

Mielőtt egy Azure Active Directory fiókot használ egy AK-fürthöz, létre kell hoznia egy szerepkör-kötés vagy egy fürt szerepkör-kötést. A szerepkörök határozzák meg a megadható engedélyeket, a kötések pedig a kívánt felhasználókra vonatkoznak. Ezek a hozzárendelések egy adott névtérre vagy a teljes fürtre is alkalmazhatók. További információ: RBAC- [hitelesítés használata][rbac-authorization].

Először is használja az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot a `--admin` argumentummal a fürtbe való bejelentkezéshez rendszergazdai hozzáféréssel.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Ezután hozzon létre ClusterRoleBinding egy Azure AD-fiókhoz, amelyhez hozzáférést szeretne adni az AK-fürthöz. A következő példa a fiók teljes hozzáférését adja a fürt összes névteréhez:

- Ha az RBAC-kötést megadó felhasználó ugyanahhoz az Azure AD-bérlőhöz van rendelve, akkor az egyszerű felhasználónév (UPN) alapján rendeljen engedélyeket. A ClusterRoleBinding YAML-jegyzékfájljának létrehozásához lépjen a lépéshez.

- Ha a felhasználó egy másik Azure AD-bérlőben található, a **objectId** tulajdonság lekérdezése és használata. Ha szükséges, kérje le a szükséges felhasználói fiók objectId az az [ad User show][az-ad-user-show] parancs használatával. Adja meg a szükséges fiók egyszerű felhasználónevét (UPN):

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Hozzon létre egy fájlt, például *RBAC-HRE-user. YAML*, majd illessze be a következő tartalmakat. Az utolsó sorban cserélje le a **userPrincipalName_or_objectIdt** az UPN-vagy OBJEKTUMAZONOSÍTÓ-azonosítóra. A választás attól függ, hogy a felhasználó azonos-e az Azure AD-Bérlővel, vagy sem.

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

Alkalmazza a kötést a [kubectl Apply][kubectl-apply] parancs használatával az alábbi példában látható módon:

```console
kubectl apply -f rbac-aad-user.yaml
```

Az Azure AD-csoportok összes tagjához szerepkör-kötés is létrehozható. Az Azure AD-csoportokat az alábbi példában látható módon adja meg a csoport objektum-azonosító használatával.

Hozzon létre egy fájlt, például *RBAC-HRE-Group. YAML*, majd illessze be a következő tartalmakat. Frissítse a csoport objektum AZONOSÍTÓját az Azure AD-bérlő egyikével:

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

Alkalmazza a kötést a [kubectl Apply][kubectl-apply] parancs használatával az alábbi példában látható módon:

```console
kubectl apply -f rbac-aad-group.yaml
```

A Kubernetes-fürtök RBAC-vel való biztonságossá tételével kapcsolatos további információkért lásd: a [RBAC-hitelesítés használata][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>A fürt elérése az Azure AD-vel

A nem rendszergazda felhasználó környezetének lekéréséhez használja az az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A `kubectl` parancs futtatása után a rendszer kérni fogja, hogy az Azure használatával hitelesítse magát. A folyamat befejezéséhez kövesse a képernyőn megjelenő utasításokat, ahogy az az alábbi példában is látható:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

A folyamat befejezésekor a rendszer gyorsítótárazza a hitelesítési jogkivonatot. A rendszer csak a jogkivonat lejáratakor vagy a Kubernetes konfigurációs fájl újbóli létrehozásakor kéri a bejelentkezést.

Ha a sikeres bejelentkezést követően megjelenik egy engedélyezési hibaüzenet, ellenőrizze a következő feltételeket:

```console
error: You must be logged in to the server (Unauthorized)
```


- A megfelelő objektumazonosítót vagy UPN-t definiálta attól függően, hogy a felhasználói fiók ugyanahhoz az Azure AD-bérlőhöz van-e társítva.
- A felhasználó nem tagja több mint 200 csoportnak.
- Az alkalmazás-regisztráció a kiszolgálón megadott titok megegyezik a `--aad-server-app-secret`használatával konfigurált értékkel.

## <a name="next-steps"></a>Következő lépések

Ha az Azure AD-felhasználókat és-csoportokat szeretné használni a fürterőforrások elérésének vezérléséhez, tekintse meg a [fürterőforrások hozzáférésének szabályozása szerepköralapú hozzáférés-vezérléssel és az AK-beli Azure ad-identitások használatával című részt][azure-ad-rbac].

További információ a Kubernetes-fürtök biztonságossá tételéről: [hozzáférési és identitási beállítások az AK][rbac-authorization]-hoz.

Az identitás-és erőforrás-vezérléssel kapcsolatos további információkért lásd: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][operator-best-practices-identity].

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
