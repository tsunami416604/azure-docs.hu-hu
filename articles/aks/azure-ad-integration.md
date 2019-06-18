---
title: Az Azure Active Directory integrálása az Azure Kubernetes Service
description: Azure Active Directory-kompatibilis Azure Kubernetes Service (AKS)-fürtök létrehozása
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: iainfou
ms.openlocfilehash: db166c82e39e9184528fde67ff868229cf9b1d57
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061107"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Az Azure Active Directory integrálása az Azure Kubernetes Service

Az Azure Kubernetes Service (AKS) a felhasználói hitelesítés Azure Active Directory (Azure AD) használatára konfigurálható. Ebben a konfigurációban, bejelentkezhet egy AKS-fürtöt az Azure AD-hitelesítési token használatával.

Fürt-rendszergazdák konfigurálhatják a Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC) a felhasználó identitását, vagy a könyvtár a csoport tagsága alapján.

Ez a cikk azt ismerteti, hogyan lehet:

- Helyezze üzembe az AKS és Azure AD előfeltételei.
- Fürt üzembe helyezése az Azure AD-kompatibilis.
- Hozzon létre egy alapszintű RBAC-szerepkört az AKS-fürtöt az Azure portal használatával.

Használatával is elvégezheti ezeket a lépéseket a [Azure CLI-vel][azure-ad-cli].

> [!NOTE]
> Az Azure AD csak egy új RBAC-kompatibilis fürt létrehozásakor engedélyezhető. Az Azure AD egy meglévő AKS-fürt nem engedélyezhető.

## <a name="authentication-details"></a>Hitelesítés részletei

Azure AD-hitelesítés az AKS-fürtök, amelyek rendelkeznek az OpenID Connect van megadva. OpenID Connect, amely egy identitás-ra épülő az OAuth 2.0 protokollt.

OpenID Connect kapcsolatos további információkért lásd: [OpenID Connect és az Azure AD használatával webes alkalmazásokhoz való hozzáférés engedélyezése][open-id-connect].

Webhook eszközjogkivonattal történő hitelesítés egy Kubernetes-fürtben hitelesítési tokenek szolgál. Webhook tokent használó hitelesítés biztosítását konfigurálja és kezeli az AKS-fürt részeként.

Webhook tokent használó hitelesítés biztosítását kapcsolatos további információkért lásd: a [Webhook tokent használó hitelesítés biztosítását] [ kubernetes-webhook] Kubernetes dokumentációját szakaszát.

Adjon meg egy AKS-fürtöt az Azure AD-hitelesítés, a két Azure AD-alkalmazások jönnek létre. Az első alkalmazás egy összetevő, amely felhasználói hitelesítést nyújt. A második alkalmazás része a ügyfél kéri a parancssori felület a hitelesítéshez használt. Az ügyfélalkalmazás, a tényleges, az ügyfél által megadott hitelesítő adatok a kiszolgálói alkalmazás használ.

> [!NOTE]
> AKS-hitelesítéshez az Azure AD konfigurálásakor a két Azure AD-alkalmazások úgy vannak konfigurálva. Egyes alkalmazásokhoz tartozó engedélyek delegálásának lépéseit egy Azure bérlői rendszergazdának kell végrehajtania.

## <a name="create-the-server-application"></a>A kiszolgálói alkalmazás létrehozása

Az első Azure AD-alkalmazás alkalmazza a felhasználó Azure AD biztonságicsoport-tagság beolvasása. Ez az alkalmazás létrehozása az Azure Portalon:

1. Válassza ki **Azure Active Directory** > **alkalmazásregisztrációk** > **új regisztrációs**.

    a. Adja meg az alkalmazás nevét, például *AKSAzureADServer*.

    b. A **támogatott fióktípusok**válassza **fiókok csak a szervezeti könyvtárban található**.
    
    c. Válasszon **webes** az átirányítási URI-azonosítóhoz írja be, és írja be például a minden olyan URI-formátumú érték *https://aksazureadserver* .

    d. Válassza ki **regisztrálása** befejezésekor.

2. Válassza ki **Manifest**, majd szerkessze a **groupMembershipClaims:** érték szerint **összes**. Ha elkészült, a frissítéseket, válassza ki a **mentése**.

    ![Az összes frissíteni a csoporttagságot](media/aad-integration/edit-manifest.png)

3. Az Azure AD-alkalmazás bal oldali panelén válassza **tanúsítványok és titkos kulcsok**.

    a. Válassza ki **+ új titkos ügyfélkulcsot**.

    b. Adjon hozzá egy kulcs leírása, például az *AKS az Azure AD-kiszolgáló*. Válassza ki a lejárati időt, és válassza ki **Hozzáadás**.

    c. Megjegyzés: a kulcs értékét, amely jelenleg csak jelenik meg. Az Azure AD-kompatibilis AKS-fürt központi telepítése esetén ezt az értéket a kiszolgálótitok alkalmazás neve.

4. Az Azure AD-alkalmazás bal oldali panelén válassza **API-engedélyek**, majd válassza ki **+ adjon hozzá egy engedélyt**.

    a. A **Microsoft APIs**válassza **Microsoft Graph**.

    b. Válassza ki **delegált engedélyek**, majd válassza ki a jelölőnégyzetet a **könyvtár > Directory.Read.All (címtáradatok olvasása)** .

    c. Ha egy alapértelmezett delegált engedély **felhasználó > User.Read (jelentkezzen be és felhasználói profil olvasása)** nem létezik, jelölje be a jelölőnégyzetet, mellette.

    d. Válassza ki **Alkalmazásengedélyek**, majd válassza ki a jelölőnégyzetet a **könyvtár > Directory.Read.All (címtáradatok olvasása)** .

    ![Graph-engedélyek beállítása](media/aad-integration/graph-permissions.png)

    e. Válassza ki **engedélyek hozzáadása** menteni a frissítéseket.

    f. A **hozzájárulását**válassza **biztosítson rendszergazdai jóváhagyás**. Ez a gomb nem érhető el, ha a jelenlegi fiókot nem Bérlői rendszergazda.

    Ha sikeresen engedélyek a portálon a következő értesítés jelenik meg:

   ![Értesítés a sikeres jogosultságaitól](media/aad-integration/permissions-granted.png)

5. Az Azure AD-alkalmazás bal oldali panelén válassza **közzé API-k**, majd válassza ki **+ hatókör hozzáadása**.
    
    a. Adjon meg egy **hatókör neve**, egy **rendszergazdai jóváhagyás megjelenítendő neve**, majd egy **rendszergazdai jóváhagyás leírása** például *AKSAzureADServer*.

    b. Győződjön meg arról, hogy **állapot** értékre van állítva **engedélyezve**.

    ![Tegye elérhetővé a server app más szolgáltatásokkal való használatra API-ként](media/aad-integration/expose-api.png)

    c. Válassza ki **hatókör hozzáadása**.

6. Térjen vissza az alkalmazás **áttekintése** lapot, és jegyezze fel a **Alkalmazásazonosítót (ügyfél)** . Az Azure AD-kompatibilis AKS-fürt központi telepítése esetén ez az érték nevezzük az alkalmazás azonosítóját.

    ![Alkalmazásazonosító beszerzése](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Az ügyfélalkalmazás létrehozása

A második Azure AD-alkalmazást használt, jelentkezzen be a Kubernetes parancssori Felületet (kubectl).

1. Válassza ki **Azure Active Directory** > **alkalmazásregisztrációk** > **új regisztrációs**.

    a. Adja meg az alkalmazás nevét, például *AKSAzureADClient*.

    b. A **támogatott fióktípusok**válassza **fiókok csak a szervezeti könyvtárban található**.

    c. Válassza ki **webes** az átirányítási URI-azonosítóhoz írja be, és írja be például a minden olyan URI-formátumú érték *https://aksazureadclient* .

    d. Válassza ki **regisztrálása** befejezésekor.

2. Az Azure AD-alkalmazás bal oldali panelén válassza **API-engedélyek**, majd válassza ki **+ adjon hozzá egy engedélyt**.

    a. Válassza ki **saját API-k**, majd válassza az Azure ad-ben kiszolgálóalkalmazás, mint például az előző lépésben létrehozott *AKSAzureADServer*.

    b. Válassza ki **delegált engedélyek**, majd válassza ki a az Azure AD-kiszolgálóalkalmazás melletti jelölőnégyzetet.

    ![Alkalmazás-engedélyek konfigurálása](media/aad-integration/select-api.png)

    c. Válassza ki **engedélyek hozzáadása**.

    d. A **hozzájárulását**válassza **biztosítson rendszergazdai jóváhagyás**. Ez a gomb nem érhető el, ha a jelenlegi fiókot nem Bérlői rendszergazda. Engedélyek, ha a portálon a következő értesítés jelenik meg:

    ![Értesítés a sikeres jogosultságaitól](media/aad-integration/permissions-granted.png)

3. Az Azure AD-alkalmazás bal oldali panelén válassza **hitelesítési**.

    - Alatt **ügyféltípus alapértelmezett**válassza **Igen** való **kezeli az ügyfél nyilvános ügyfél**.

5. A bal oldali panelen, az Azure AD-alkalmazás jegyezze fel az alkalmazásazonosítót Az Azure AD-kompatibilis AKS-fürt központi telepítése esetén ez az érték nevezzük az ügyféloldali alkalmazás azonosítója.

   ![Az Alkalmazásazonosító beszerzése](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>A Bérlőazonosító beszerzése

Ezután kérdezze le az Azure-bérlő azonosítója. Ezt az értéket az AKS-fürt létrehozásakor használja.

Az Azure Portalon, válassza ki a **Azure Active Directory** > **tulajdonságok** , és jegyezze fel a **címtár-azonosító**. Amikor létrehoz egy Azure AD-kompatibilis AKS-fürtöt, az érték neve a bérlő azonosítója.

![Az Azure-Bérlőazonosító beszerzése](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Az AKS-fürt üzembe helyezése

Használja a [az csoport létrehozása] [ az-group-create] paranccsal hozzon létre egy erőforráscsoportot az AKS-fürtöt.

```azurecli
az group create --name myResourceGroup --location eastus
```

Használja a [az aks létrehozása] [ az-aks-create] parancsot az AKS-fürt üzembe helyezéséhez. Ezután cserélje le a következő mintaparancs értékeit. A server app-ID, a titkos Alkalmazáskulcs, az ügyfélalkalmazás-azonosító és a bérlő azonosítója. az Azure AD-alkalmazások létrehozása során gyűjtött értékek használata

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

AKS-fürt létrehozása néhány percet vesz igénybe.

## <a name="create-an-rbac-binding"></a>Az RBAC-kötés létrehozása

Az AKS-fürt használata az Azure Active Directory-fiókkal, előtt létre kell hoznia a kötési szerepkör vagy a fürt szerepkör-kötést. A szerepkörök határozzák meg az engedélyeket, és kötések alkalmazza őket a kívánt felhasználók számára. Ezeket a hozzárendeléseket is alkalmazható, egy adott névtérhez vagy az egész fürt között. További információkért lásd: [használatával RBAC-hitelesítés][rbac-authorization].

Először a [az aks get-credentials] [ az-aks-get-credentials] parancsot a `--admin` jelentkezzen be a fürt rendszergazdai hozzáféréssel rendelkező argumentumában.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Ezután hozzon létre egy olyan, hogy szeretné-e hozzáférést az AKS-fürtöt az Azure AD-fiók ClusterRoleBinding. Az alábbi példa a teljes körű hozzáférést biztosít a fürt összes névtér:

- Ha a felhasználó biztosít az RBAC kötése van az Azure AD-bérlőhöz, engedélyeket az egyszerű felhasználónév (UPN) alapján. Folytassa a lépés a YAML ClusterRoleBinding jegyzékfájl létrehozásához.

- Ha a felhasználó egy másik Azure ad-bérlővel, lekérdezheti, és használja a **objectId** tulajdonság helyett. Ha szükséges, első objectid azonosítóját, a szükséges felhasználói fiók használatával a [az ad felhasználó show] [ az-ad-user-show] parancsot. Adja meg az egyszerű felhasználónév (UPN) a szükséges fiók:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Hozzon létre például egy fájl *rbac-aad-user.yaml*, majd illessze be a következő tartalmakat. Az utolsó sorban cserélje le a **userPrincipalName_or_objectId** UPN vagy az objektum az azonosítóval. A választás attól függ, a felhasználónak-e az Azure AD-bérlőhöz vagy sem.

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

A kötés alkalmazása használatával a [a kubectl a alkalmazni] [ kubectl-apply] parancsot az alábbi példában látható módon:

```console
kubectl apply -f rbac-aad-user.yaml
```

Egy szerepkör kötést is létrehozhatók az Azure AD-csoport összes tagja számára. Az Azure AD-csoportok a csoport Objektumazonosítóját használatával vannak meghatározva, az alábbi példában látható módon.

Hozzon létre például egy fájl *rbac-aad-group.yaml*, majd illessze be a következő tartalmakat. Frissítse a csoportobjektum azonosítója egy, az Azure AD-bérlőből:

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

A kötés alkalmazása használatával a [a kubectl a alkalmazni] [ kubectl-apply] parancsot az alábbi példában látható módon:

```console
kubectl apply -f rbac-aad-group.yaml
```

Az RBAC a Kubernetes-fürt biztonságossá tétele a további információkért lásd: [RBAC-hitelesítés használatával][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Hozzáférés a fürt az Azure ad-vel

Kérje le a nem rendszergazdai felhasználó számára a környezet használatával a [az aks get-credentials] [ az-aks-get-credentials] parancsot.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Futtatása után a `kubectl` parancs kérni fogja az Azure használatával történő hitelesítéshez. Kövesse a képernyőn megjelenő utasításokat követve fejezze be a folyamatot, az alábbi példában látható módon:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Ha a folyamat befejeződött, a rendszer gyorsítótárazza a hitelesítési jogkivonat. Csak kéri, hogy jelentkezzen be újra a jogkivonat lejár, vagy a Kubernetes konfigurációs fájl nem hozza létre újra.

Ha engedélyezési hibaüzenet jelenik meg, miután sikeresen bejelentkezett, ellenőrizze a következő feltételeknek:

```console
error: You must be logged in to the server (Unauthorized)
```


- A megfelelő Objektumazonosítóval vagy egyszerű felhasználónév, attól függően, ha a felhasználói fiók pedig az Azure AD-bérlőhöz vagy nem meghatározott.
- A felhasználó nem több mint 200 csoport tagja.
- A kiszolgáló megfelel a konfigurált értéket az alkalmazás regisztrálása a definiált titkos `--aad-server-app-secret`.

## <a name="next-steps"></a>További lépések

Az Azure AD-felhasználók és csoportok segítségével szabályozza a fürterőforrásokhoz való hozzáférést, lásd: [szerepköralapú hozzáférés-vezérlés és az Azure AD-identitások használata az AKS fürt erőforrásokhoz való hozzáférésének][azure-ad-rbac].

Kubernetes-fürtök biztonságos kapcsolatos további információkért lásd: [hozzáférési és azonosító beállításai az aks-ben][rbac-authorization].

Identitás- és erőforrás-vezérléssel kapcsolatos további tudnivalókért lásd: [gyakorlati tanácsok a hitelesítés és engedélyezés az aks-ben][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
