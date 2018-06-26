---
title: Az Azure Active Directory integrálása az Azure Kubernetes szolgáltatás
description: Azure Active Directory-kompatibilis Azure Kubernetes szolgáltatás létrehozása a fürt.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/17/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7d157d50bbcd25edd9cd6693a71fb04535cbeb79
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937381"
---
# <a name="integrate-azure-active-directory-with-aks---preview"></a>Az Azure Active Directory integrálása AKS – előzetes

Az Azure Kubernetes szolgáltatás (AKS) beállítható úgy, hogy az Azure Active Directoryt a felhasználók hitelesítéséhez. Ebben a konfigurációban bejelentkezhet egy Azure Kubernetes szolgáltatás fürtbe, az Azure Active Directory hitelesítési tokent. Fürt rendszergazdák ezenfelül képesek a felhasználói identitás vagy a könyvtár csoporttagság alapján Kubernetes szerepköralapú hozzáférés-vezérlés konfigurálása.

Ez a dokumentum létrehozása AKS és az Azure AD az összes szükséges előfeltételeket, az Azure AD-kompatibilis fürt telepítése és egy egyszerű RBAC-szerepkör létrehozása a AKS fürt részletes.

> [!IMPORTANT]
> Az Azure RBAC Kubernetes szolgáltatás (AKS) és az Azure AD-integrációra jelenleg a **előzetes**. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.
>

## <a name="authentication-details"></a>Hitelesítés részletei

Az Azure AD hitelesítését az OpenID Connect Azure Kubernetes fürtökre. OpenID Connect egy identitásrétegének, az OAuth 2.0 protokollt platformra épül. További információ az OpenID Connect megtalálhatók a [nyitott ID connect dokumentációját][open-id-connect].

A Kubernetes fürtön belül Webhook tokent használó hitelesítés ellenőrzésére szolgál a hitelesítési tokenek. Webhook tokent használó hitelesítés beállítani és kezelni a AKS fürt részeként. Webhook tokent használó hitelesítés találhatók további információk a [webhook authentication – dokumentáció][kubernetes-webhook].

> [!NOTE]
> Azure ad-val AKS hitelesítés konfigurálásakor a két Azure AD-alkalmazást úgy vannak konfigurálva. Ez a művelet egy Azure bérlői rendszergazdának kell végrehajtania.

## <a name="create-server-application"></a>Kiszolgáló-alkalmazás létrehozása

Az első Azure AD-alkalmazás segítségével a felhasználók az Azure AD-csoport tagságának beolvasása.

1. Válassza az **Azure Active Directory** > **Alkalmazásregisztrációk** > **Új alkalmazás regisztrálása** lehetőséget.

  Nevezze el az alkalmazást, jelölje be **Web app / API** alkalmazás típusra, és írja be a formázott URI értéke **bejelentkezési URL-cím**. Válassza ki **létrehozása** végzett.

  ![Azure AD-regisztrációval létrehozása](media/aad-integration/app-registration.png)

2. Válassza ki **Manifest** és szerkesztése a `groupMembershipClaims` egy érték `"All"`.

  Miután befejeződött módosításainak mentéséhez.

  ![Az összes csoporttagság frissítése](media/aad-integration/edit-manifest.png)

3. Vissza az Azure AD-alkalmazást, jelölje ki **beállítások** > **kulcsok**.

  Adjon meg egy kulcs leírást, egy lejárati határidőt, válassza ki és **mentése**. Jegyezze fel a kulcs értékét. Ha AKS fürt üzembe helyezése az Azure AD engedélyezve van, ezt az értéket a neve a `Server application secret`.

  ![Az alkalmazás titkos kulcs lekérése](media/aad-integration/application-key.png)

4. Térjen vissza az Azure AD-alkalmazást, jelölje be **beállítások** > **szükséges engedélyek** > **Hozzáadás**  >   **Válassza ki az API-k** > **Microsoft Graph** > **válasszon**.

  A **ALKALMAZÁSENGEDÉLYEK** mellett jelölje **címtáradatok olvasása**.

  ![Alkalmazás graph engedélyek beállítása](media/aad-integration/read-directory.png)

5. A **DELEGÁLT engedélyek**, mellett jelölje **jelentkezzen be a felhasználói profil olvasása és** és **címtáradatok olvasása**. Mentse a frissítéseket, egyszer történik.

  ![Alkalmazás graph engedélyek beállítása](media/aad-integration/delegated-permissions.png)

6. Válassza ki **végzett** és **engedélyt adjon** a lépés befejezéséhez. Ez a lépés sikertelen lesz, ha a jelenlegi fiók nincs a bérlői rendszergazda segítségét.

  ![Alkalmazás graph engedélyek beállítása](media/aad-integration/grant-permissions.png)

7. Vissza az alkalmazásba, és tekintse meg a **Alkalmazásazonosító**. Az Azure AD-kompatibilis AKS fürt telepítésekor ezt az értéket a neve a `Server application ID`.

  ![Alkalmazásazonosító beszerzése](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Ügyfél-alkalmazás létrehozása

A második az Azure AD-alkalmazást használatos jelentkezett a Kubernetes CLI (kubectl.)

1. Válassza az **Azure Active Directory** > **Alkalmazásregisztrációk** > **Új alkalmazás regisztrálása** lehetőséget.

  Nevezze el az alkalmazást, jelölje be **natív** alkalmazás típusra, és írja be a formázott URI értéke **átirányítási URI-**. Válassza ki **létrehozása** végzett.

  ![Az AAD-regisztrációs létrehozása](media/aad-integration/app-registration-client.png)

2. Válassza ki az Azure AD-alkalmazás **beállítások** > **szükséges engedélyek** > **hozzáadása** > **válasszon egy API** , és keresse meg a jelen dokumentum az előző lépésben létrehozott kiszolgálói alkalmazás nevét.

  ![Alkalmazás-engedélyek konfigurálása](media/aad-integration/select-api.png)

3. Jelölje be a mellett a alkalmazást, majd kattintson **válasszon**.

  ![Válassza ki a AKS AAD server alkalmazás végpont](media/aad-integration/select-server-app.png)

4. Válassza ki **végzett** és **engedélyt adjon** a lépés befejezéséhez.

  ![Engedélyek megadása](media/aad-integration/grant-permissions-client.png)

5. Vissza az AD-alkalmazás, vegye figyelembe a **Alkalmazásazonosító**. Az Azure AD-kompatibilis AKS fürt telepítésekor ezt az értéket a neve a `Client application ID`.

  ![Az Alkalmazásazonosító beszerzése](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>A bérlőazonosító beszerzése

Végezetül beolvasása az Azure-bérlőhöz Azonosítóját. Ez az érték a AKS fürt telepítése során is használható.

Válassza ki az Azure-portálon **Azure Active Directory** > **tulajdonságok** és tekintse meg a **könyvtár-azonosítója**. Az Azure AD-kompatibilis AKS fürt telepítésekor ezt az értéket a neve a `Tenant ID`.

![Az Azure-bérlőazonosító beszerzése](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Fürt központi telepítése

Használja a [az csoport létrehozása] [ az-group-create] parancs futtatásával hozzon létre egy erőforráscsoportot a AKS fürthöz.

```azurecli
az group create --name myAKSCluster --location eastus
```

A fürt szabályzatsablonokat a [az aks létrehozása] [ az-aks-create] parancsot. Cserélje le az Azure AD-alkalmazások létrehozása során gyűjtött értékek a minta-parancsot az értékeket.

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Az RBAC-kötés létrehozása

Egy Azure Active Directory-fiókot a AKS fürt használt, egy szerepkör binding vagy a fürt szerepkör kötést kell létrehozni.

Először is a [get-hitelesítő adatokat az aks] [ az-aks-get-credentials] parancsot a `--admin` argumentumának jelentkezzen be a fürt rendszergazdai hozzáféréssel.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

Ezt követően a következő jegyzékfájl segítségével hozzon létre egy ClusterRoleBinding az Azure AD-fiókot. Frissítse a felhasználónév az Azure AD-bérlő közül. Ez a példa a teljes körű hozzáférést biztosít a fürt összes névtér.

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

Egy szerepkör kötés is hozható létre egy Azure AD-csoport összes tagja. A következő jegyzékfájl biztosít minden tagját a `kubernetes-admin` csoportot a rendszergazdai hozzáférés a fürthöz.

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
   name: "kubernetes-admin"
```

Az RBAC Kubernetes fürt védelmével kapcsolatos további információkért lásd: [RBAC-engedély használatával][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Az Azure AD hozzáférési fürt

A környezetben a nem rendszergazdai felhasználó használja a következő lekéréses a [get-hitelesítő adatokat az aks] [ az-aks-get-credentials] parancsot.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

Bármely kubectl parancs futtatása után kérni fogja az Azure való hitelesítéshez szükséges. Kövesse a képernyőn megjelenő utasításokat.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

Művelet befejeződése után a rendszer gyorsítótárazza a hitelesítési jogkivonat. Csak reprompted bejelentkezni, amikor a jogkivonat lejárt vagy a Kubernetes konfigurációs fájl újból létrehozza.

## <a name="next-steps"></a>További lépések

További tudnivalók az RBAC a Kubernetes fürtök biztonságossá tétele a [RBAC-engedély használatával] [ rbac-authorization] dokumentációját.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/active-directory-protocols-openid-connect-code.md
