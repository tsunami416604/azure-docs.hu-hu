---
title: Az Azure Active Directory integrálása az Azure Kubernetes Service
description: Fürtök létrehozása az Azure Active Directory-kompatibilis Azure Kubernetes Service-ben.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7ae3818795cddf5dfbb93ca6cc8dfff9d1c44c03
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341239"
---
# <a name="integrate-azure-active-directory-with-aks---preview"></a>Az Azure Active Directory integrálása az AKS - előzetes verzió

Az Azure Kubernetes Service (AKS) beállítható úgy, hogy az Azure Active Directory használata a felhasználók hitelesítéséhez. Ebben a konfigurációban amelyre be tud jelentkezni egy Azure Kubernetes Service-fürtön az Azure Active Directory-hitelesítési tokent. Fürt a rendszergazdák emellett képesek a felhasználók identitás- vagy a csoport tagsága alapján Kubernetes szerepköralapú hozzáférés-vezérlés konfigurálása.

Ez a dokumentum létrehozása AKS és Azure ad-ben az összes szükséges előfeltételeket, az Azure AD-kompatibilis fürt üzembe helyezése és az AKS-fürtöt hoz létre egy egyszerű RBAC szerepkör részletes.

> [!IMPORTANT]
> Az Azure Kubernetes Service (AKS) RBAC és az Azure AD integrációja jelenleg **előzetes**. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.
>

## <a name="authentication-details"></a>Hitelesítés részletei

OpenID-kapcsolattal az Azure-beli Kubernetes-fürtök az Azure AD-hitelesítés van megadva. OpenID Connect, amely egy identitás-ra épülő az OAuth 2.0 protokollt. További információ az OpenID Connect megtalálható a [Open ID connect dokumentáció][open-id-connect].

A belül a Kubernetes-fürt Webhook Eszközjogkivonattal történő hitelesítés segítségével ellenőrizze a hitelesítési tokenek. Webhook tokent használó hitelesítés biztosítását konfigurálja és kezeli az AKS-fürt részeként. További információ az eszközjogkivonattal történő hitelesítés található Webhook a [webhook authentication – dokumentáció][kubernetes-webhook].

> [!NOTE]
> AKS-hitelesítéshez az Azure AD konfigurálása, ha két Azure AD-alkalmazás vannak konfigurálva. Ez a művelet egy Azure-bérlő rendszergazdája kell elvégezni.

## <a name="create-server-application"></a>Kiszolgálói alkalmazás létrehozása

Az első Azure AD-alkalmazást az Azure AD-felhasználók csoport tagságának első szolgál.

1. Válassza az **Azure Active Directory** > **Alkalmazásregisztrációk** > **Új alkalmazás regisztrálása** lehetőséget.

  Adja meg az alkalmazás nevét, válassza ki **webalkalmazás / API** az alkalmazás típusához, és adja meg a formázott URI érték **bejelentkezési URL-**. Válassza ki **létrehozás** végeztével.

  ![Azure AD-regisztrációs létrehozása](media/aad-integration/app-registration.png)

2. Válassza ki **Manifest** és szerkesztheti a `groupMembershipClaims` értéket a következőre `"All"`.

  Miután elkészült a frissítések mentése.

  ![Az összes frissíteni a csoporttagságot](media/aad-integration/edit-manifest.png)

3. Vissza az Azure AD-alkalmazást, válassza ki **beállítások** > **kulcsok**.

  Adjon hozzá egy kulcs leírása, egy lejárati határidőt, válassza ki és **mentése**. Jegyezze fel a kulcs értékét. Ha az AKS-fürt üzembe helyezése az Azure AD engedélyezve, ez az érték a neve a `Server application secret`.

  ![Az alkalmazás titkos kulcs lekérése](media/aad-integration/application-key.png)

4. Térjen vissza az Azure AD alkalmazást, jelölje be **beállítások** > **szükséges engedélyek** > **Hozzáadás**  >   **API kiválasztása** > **Microsoft Graph** > **kiválasztása**.

  ![Válassza ki a graph API-val](media/aad-integration/graph-api.png)

5. A **ALKALMAZÁSENGEDÉLYEK** melletti négyzetet **címtáradatok olvasása**.

  ![Alkalmazás graph-engedélyek beállítása](media/aad-integration/read-directory.png)

6. A **DELEGÁLT engedélyek**, melletti négyzetet **jelentkezzen be a felhasználói profil olvasása és** és **címtáradatok olvasása**. Miután befejezte a frissítések mentése.

  ![Alkalmazás graph-engedélyek beállítása](media/aad-integration/delegated-permissions.png)

7. Válassza ki **kész**, válassza a *Microsoft Graph* API-t, majd válassza ki a listáról **engedélyek megadása**. Ez a lépés sikertelen lesz, ha a jelenlegi fiókot nem Bérlői rendszergazda.

  ![Alkalmazás graph-engedélyek beállítása](media/aad-integration/grant-permissions.png)

8. Térjen vissza az alkalmazást, és jegyezze fel a **Alkalmazásazonosító**. Az Azure AD-kompatibilis AKS-fürt üzembe helyezésekor, ezt az értéket a neve a `Server application ID`.

  ![Alkalmazásazonosító beszerzése](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Ügyfélalkalmazás létrehozása

A második Azure AD-alkalmazást használja történő bejelentkezéskor a Kubernetes parancssori Felületet (kubectl).

1. Válassza az **Azure Active Directory** > **Alkalmazásregisztrációk** > **Új alkalmazás regisztrálása** lehetőséget.

  Adja meg az alkalmazás nevét, válassza ki **natív** az alkalmazás típusához, és adja meg a formázott URI érték **átirányítási URI-t**. Válassza ki **létrehozás** végeztével.

  ![Hozzon létre az AAD-regisztráció](media/aad-integration/app-registration-client.png)

2. Válassza ki az Azure AD-alkalmazás **beállítások** > **szükséges engedélyek** > **Hozzáadás** > **válassza egy API** , és keresse meg a jelen dokumentum az előző lépésben létrehozott kiszolgálói alkalmazás nevére.

  ![Alkalmazás-engedélyek konfigurálása](media/aad-integration/select-api.png)

3. Jelölje be a mellett az alkalmazásra, majd **kiválasztása**.

  ![Válassza ki az AKS AAD alkalmazás kiszolgálóvégpontok](media/aad-integration/select-server-app.png)

4. Válassza ki **kész** és **engedélyek megadása** a lépés végrehajtásához.

  ![Engedélyek megadása](media/aad-integration/grant-permissions-client.png)

5. Vissza az AD-alkalmazásra, jegyezze fel a **Alkalmazásazonosító**. Az Azure AD-kompatibilis AKS-fürt üzembe helyezésekor, ezt az értéket a neve a `Client application ID`.

  ![Az Alkalmazásazonosító beszerzése](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>A bérlőazonosító beszerzése

Végül kérje le a saját Azure bérlőjének Azonosítóját. Ez az érték is szolgál az AKS-fürt üzembe helyezésekor.

Az Azure Portalon, válassza ki a **Azure Active Directory** > **tulajdonságok** , és jegyezze fel a **címtár-azonosító**. Az Azure AD-kompatibilis AKS-fürt üzembe helyezésekor, ezt az értéket a neve a `Tenant ID`.

![Az Azure-Bérlőazonosító beszerzése](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Fürt üzembe helyezése

Használja a [az csoport létrehozása] [ az-group-create] paranccsal hozzon létre egy erőforráscsoportot az AKS-fürtöt.

```azurecli
az group create --name myAKSCluster --location eastus
```

Üzembe helyezés a fürt a [az aks létrehozása] [ az-aks-create] parancsot. Az értékeket az alábbi minta parancsban cserélje le az Azure AD-alkalmazások létrehozása során gyűjtött értékek.

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Az RBAC-kötés létrehozása

Az AKS-fürtöt az Azure Active Directory-fiókkal használhatók legyenek, a szerepkör kötés vagy a fürt szerepkör-kötést kell létrehozni.

Először a [az aks get-credentials] [ az-aks-get-credentials] parancsot a `--admin` jelentkezzen be a fürt rendszergazdai hozzáféréssel rendelkező argumentum.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

Ezután használja a következő jegyzékfájl egy ClusterRoleBinding egy olyan Azure AD-fiók létrehozásához. Frissítse a felhasználónevet egy, az Azure AD-bérlőből. Ebben a példában a teljes körű hozzáférést biztosít a fürt összes névtér.

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
  name: "user@contoso.com"
```

Egy szerepkör kötést is létrehozhatók az Azure AD-csoport összes tagja számára. Az Azure AD-csoportok vannak megadva, a csoport-objektum azonosítójával

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

Az RBAC a Kubernetes-fürt biztonságossá tétele a további információkért lásd: [RBAC-hitelesítés használatával][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Az Azure AD hozzáférési fürt

Ezután kérje le a környezetben a nem rendszergazda jogosultságú felhasználói a [az aks get-credentials] [ az-aks-get-credentials] parancsot.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

Bármely kubectl parancs futtatása után kéri az Azure-hitelesítésre. Kövesse a képernyőn megjelenő utasításokat.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

Ha elkészült, a rendszer gyorsítótárazza a hitelesítési jogkivonat. Csak reprompted jelentkezzen be, amikor a jogkivonat lejárt vagy a Kubernetes konfigurációs fájl újból létrehozza.

Ha az engedélyezési hibaüzenet azért jelent meg, miután sikeresen bejelentkezett, ellenőrizze, hogy a felhasználó bejelentkezik, nem a vendég nem (Ez gyakran a helyzet, ha egy másik címtárban való összevont bejelentkezést használ) az Azure AD-ben.
```console
error: You must be logged in to the server (Unauthorized)
```


## <a name="next-steps"></a>További lépések

További információ az RBAC a Kubernetes-fürtök védelme a [RBAC-hitelesítés használatával] [ rbac-authorization] dokumentációját.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/active-directory-protocols-openid-connect-code.md
