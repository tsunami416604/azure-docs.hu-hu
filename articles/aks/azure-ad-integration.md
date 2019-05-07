---
title: Az Azure Active Directory integrálása az Azure Kubernetes Service
description: Fürtök létrehozása az Azure Active Directory-kompatibilis Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: iainfou
ms.openlocfilehash: 2a218a48223c81e009b83cb1f129601a8035e18e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138517"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Az Azure Active Directory integrálása az Azure Kubernetes Service

Az Azure Kubernetes Service (AKS) beállítható úgy, hogy a felhasználók hitelesítéséhez az Azure Active Directory (AD) használja. Ebben a konfigurációban, bejelentkezhet egy AKS-fürtöt az Azure Active Directory hitelesítési token használatával. Ezenkívül a fürt a rendszergazdák olyan Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC) a felhasználó identitását, vagy a könyvtár a csoport tagsága alapján konfigurálhatja.

Ez a cikk bemutatja, hogyan helyezhet üzembe az AKS és Azure AD előfeltételei, akkor a fürt üzembe helyezése az Azure AD-kompatibilis, és a egy alapszintű RBAC-szerepkör létrehozása a az AKS-fürtöt az Azure portal használatával. Emellett [hajtsa végre ezeket a lépéseket az Azure CLI használatával][azure-ad-cli].

Az alábbi korlátozások érvényesek:

- Az Azure AD csak egy új, az RBAC-t a fürt létrehozásakor engedélyezhető. Az Azure AD egy meglévő AKS-fürt nem engedélyezhető.
- *Vendég* felhasználók az Azure AD-ben például, ha egy másik címtárból egy összevont bejelentkezést használ nem támogatottak.

## <a name="authentication-details"></a>Hitelesítés részletei

Az Azure AD-hitelesítés OpenID-kapcsolattal az AKS-fürtök van megadva. OpenID Connect, amely egy identitás-ra épülő az OAuth 2.0 protokollt. Az OpenID Connect további információkért lásd: a [Open ID connect dokumentáció][open-id-connect].

A belül a Kubernetes-fürt Webhook Eszközjogkivonattal történő hitelesítés segítségével ellenőrizze a hitelesítési tokenek. Webhook tokent használó hitelesítés biztosítását konfigurálja és kezeli az AKS-fürt részeként. A Webhook eszközjogkivonattal történő hitelesítés további információkért lásd: a [webhook authentication – dokumentáció][kubernetes-webhook].

Adjon meg egy AKS-fürtöt az Azure AD-hitelesítés, a két Azure AD-alkalmazások jönnek létre. Az első alkalmazás egy összetevő, amely a felhasználói hitelesítést nyújt. A második alkalmazás egy ügyfél-összetevő, amikor a rendszer kéri a parancssori felület a hitelesítéshez használt. Az ügyfélalkalmazás, a tényleges, az ügyfél által megadott hitelesítő adatok a kiszolgálói alkalmazás használ.

> [!NOTE]
> AKS-hitelesítéshez az Azure AD konfigurálása, ha két Azure AD-alkalmazás vannak konfigurálva. Az egyes alkalmazások engedélyeket delegálhatnak a lépéseket kell végrehajtania egy Azure-bérlő rendszergazdája.

## <a name="create-server-application"></a>Kiszolgálói alkalmazás létrehozása

Az első Azure AD-alkalmazást az Azure AD-felhasználók csoport tagságának első szolgál. Ez az alkalmazás létrehozása az Azure Portalon.

1. Válassza ki **Azure Active Directory** > **alkalmazásregisztrációk** > **új regisztrációs**.

    * Adja meg az alkalmazás nevét, például *AKSAzureADServer*.
    * A **támogatott fióktípusok**, válassza a *fiókok csak a szervezeti könyvtárban található*.
    * Válasszon *webes* számára a **átirányítási URI-t** írja be, és adja meg például formázott URI értéket *https://aksazureadserver*.
    * Válassza ki **regisztrálása** végeztével.

1. Válassza ki **Manifest** és szerkesztheti a `groupMembershipClaims` értéket a következőre `"All"`.

    ![Az összes frissíteni a csoporttagságot](media/aad-integration/edit-manifest.png)

    **Mentés** a frissítéseket, a folyamat végén.

1. Az Azure AD-alkalmazás bal oldali navigációs sávján válassza **tanúsítványok és titkos kulcsok**.

    * Válasszon **+ új titkos ügyfélkulcsot**.
    * Adjon hozzá egy kulcs leírása, például az *AKS az Azure AD-kiszolgáló*. Válassza ki a lejárati időt, majd válassza ki **Hozzáadás**.
    * Jegyezze fel a kulcs értékét. Azt már csak akkor jelenik meg a kezdeti ideje. Az Azure AD-kompatibilis AKS-fürt központi telepítése esetén ezt az értéket a neve a `Server application secret`.

1. Az Azure AD-alkalmazás bal oldali navigációs sávján válassza **API-engedélyek**, majd válassza a **+ adjon hozzá egy engedélyt**.

    * A **Microsoft APIs**, válassza a *Microsoft Graph*.
    * Válasszon **delegált engedélyek**, majd jelölje a **könyvtár > Directory.Read.All (címtáradatok olvasása)**.
        * Ha egy alapértelmezett delegált engedély **felhasználó > User.Read (jelentkezzen be és felhasználói profil olvasása)** nem létezik, tegyen pipa ezt az engedélyt.
    * Válasszon **Alkalmazásengedélyek**, majd jelölje a **könyvtár > Directory.Read.All (címtáradatok olvasása)**.

        ![Graph-engedélyek beállítása](media/aad-integration/graph-permissions.png)

    * Válasszon **engedélyek hozzáadása** menteni a frissítéseket.

    * Alatt a **hozzájárulását** területén válassza a **biztosítson rendszergazdai jóváhagyás**. Ez a gomb szürkén jelenik meg, és nem érhető el, ha a jelenlegi fiókot nem Bérlői rendszergazda.

        Ha az engedélyek sikeresen kapott, a portálon a következő értesítés jelenik meg:

        ![Értesítés a sikeres jogosultságaitól](media/aad-integration/permissions-granted.png)

1. Az Azure AD-alkalmazás bal oldali navigációs sávján válassza **közzé API-k**, majd válassza a **+ hatókör hozzáadása**.
    
    * Állítsa be a *hatókör neve*, *rendszergazdai jóváhagyás megjelenítendő neve*, és *rendszergazdai jóváhagyás leírása*, mint például *AKSAzureADServer*.
    * Győződjön meg arról, hogy a **állapot** értékre van állítva *engedélyezve*.

        ![Tegye elérhetővé a server app más szolgáltatásokkal való használatra API-ként](media/aad-integration/expose-api.png)

    * Válasszon **hatókör hozzáadása**.

1. Térjen vissza az alkalmazás **áttekintése** lapon, és jegyezze fel a **Alkalmazásazonosítót (ügyfél)**. Az Azure AD-kompatibilis AKS-fürt központi telepítése esetén ezt az értéket a neve a `Server application ID`.

   ![Alkalmazásazonosító beszerzése](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Ügyfélalkalmazás létrehozása

A második Azure AD-alkalmazást használja történő bejelentkezéskor a Kubernetes parancssori Felületét (`kubectl`).

1. Válassza ki **Azure Active Directory** > **alkalmazásregisztrációk** > **új regisztrációs**.

    * Adja meg az alkalmazás nevét, például *AKSAzureADClient*.
    * A **támogatott fióktípusok**, válassza a *fiókok csak a szervezeti könyvtárban található*.
    * Válasszon *webes* számára a **átirányítási URI-t** írja be, és adja meg például formázott URI értéket *https://aksazureadclient*.
    * Válassza ki **regisztrálása** végeztével.

1. Az Azure AD-alkalmazás bal oldali navigációs sávján válassza **API-engedélyek**, majd válassza a **+ adjon hozzá egy engedélyt**.

    * Válassza ki **saját API-k**, majd válassza ki az Azure ad-ben kiszolgálóalkalmazás, mint például az előző lépésben létrehozott *AKSAzureADServer*.
    * Válasszon **delegált engedélyek**, majd tegyen pipa mellett az Azure AD-kiszolgálóalkalmazás.

        ![Alkalmazás-engedélyek konfigurálása](media/aad-integration/select-api.png)

    * Válassza ki **engedélyek hozzáadása**.

    * Alatt a **hozzájárulását** területén válassza a **biztosítson rendszergazdai jóváhagyás**. Ez a gomb szürkén jelenik meg, és nem érhető el, ha a jelenlegi fiókot nem Bérlői rendszergazda.

        Ha az engedélyek sikeresen kapott, a portálon a következő értesítés jelenik meg:

        ![Értesítés a sikeres jogosultságaitól](media/aad-integration/permissions-granted.png)

1. A bal oldali navigációs az Azure AD-alkalmazás, jegyezze fel a **Alkalmazásazonosító**. Az Azure AD-kompatibilis AKS-fürt üzembe helyezésekor, ezt az értéket a neve a `Client application ID`.

   ![Az Alkalmazásazonosító beszerzése](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>A bérlőazonosító beszerzése

Végül kérje le a saját Azure bérlőjének Azonosítóját. Ezt az értéket az AKS-fürt létrehozásakor használja.

Az Azure Portalon, válassza ki a **Azure Active Directory** > **tulajdonságok** , és jegyezze fel a **címtár-azonosító**. Az Azure AD-kompatibilis AKS-fürt létrehozásakor ezt az értéket a neve a `Tenant ID`.

![Az Azure-Bérlőazonosító beszerzése](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Fürt üzembe helyezése

Használja a [az csoport létrehozása] [ az-group-create] paranccsal hozzon létre egy erőforráscsoportot az AKS-fürtöt.

```azurecli
az group create --name myResourceGroup --location eastus
```

Üzembe helyezés a fürt a [az aks létrehozása] [ az-aks-create] parancsot. Az értékeket az alábbi minta parancsban cserélje le a server app-ID és a titkos kulcsot, az ügyfélalkalmazás-azonosító és a bérlő azonosítója az Azure AD-alkalmazások létrehozása során gyűjtött értékek:

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

## <a name="create-rbac-binding"></a>Az RBAC-kötés létrehozása

Az AKS-fürtöt az Azure Active Directory-fiókkal használhatók legyenek, a szerepkör kötés vagy a fürt szerepkör-kötést kell létrehozni. *Szerepkörök* határozza meg az engedélyeket, és *kötések* azokat alkalmazni a kívánt felhasználókat. Ezeket a hozzárendeléseket is alkalmazható, egy adott névtérhez vagy az egész fürt között. További információkért lásd: [használatával RBAC-hitelesítés][rbac-authorization].

Először a [az aks get-credentials] [ az-aks-get-credentials] parancsot a `--admin` jelentkezzen be a fürt rendszergazdai hozzáféréssel rendelkező argumentumában.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Ezután hozzon létre egy ClusterRoleBinding-, hogy szeretné-e hozzáférést az AKS-fürtöt az Azure AD-fiókot. Az alábbi példa a teljes körű hozzáférést biztosít a fürt összes névtér.

- Ha a felhasználó biztosít az RBAC kötése van az Azure AD-bérlőhöz, engedélyeket az egyszerű felhasználónév (UPN) alapján. Folytassa a lépés a ClusterRuleBinding a YAML jegyzékfájl létrehozásához.

- Ha a felhasználó egy másik Azure ad-bérlővel, lekérdezheti, és használja a *objectId* tulajdonság helyett. Ha szükséges, a *objectId* , a megfelelő felhasználói fiók használatával a [az ad felhasználó show] [ az-ad-user-show] parancsot. Adja meg az egyszerű felhasználónév (UPN) a szükséges fiók:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Hozzon létre például egy fájl *rbac-aad-user.yaml*, és illessze be az alábbiakat. Az utolsó sorban cserélje le a *userPrincipalName_or_objectId* attól függően, hogy az egyszerű felhasználónév vagy az objektum azonosítója, a felhasználó az Azure AD-bérlőhöz, vagy sem.

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

A kötés használatával alkalmazza az [a kubectl a alkalmazni] [ kubectl-apply] parancsot az alábbi példában látható módon:

```console
kubectl apply -f rbac-aad-user.yaml
```

Egy szerepkör kötést is létrehozhatók az Azure AD-csoport összes tagja számára. A csoportobjektum azonosítója, használja az Azure AD-csoportok vannak megadva, az alábbi példában látható módon. Hozzon létre például egy fájl *rbac-aad-group.yaml*, és illessze be az alábbiakat. Frissítse a csoportobjektum azonosítója egy, az Azure AD-bérlőből:

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

A kötés használatával alkalmazza az [a kubectl a alkalmazni] [ kubectl-apply] parancsot az alábbi példában látható módon:

```console
kubectl apply -f rbac-aad-group.yaml
```

Az RBAC a Kubernetes-fürt biztonságossá tétele a további információkért lásd: [RBAC-hitelesítés használatával][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Az Azure AD hozzáférési fürt

Ezután kérje le a környezetben a nem rendszergazda jogosultságú felhasználói a [az aks get-credentials] [ az-aks-get-credentials] parancsot.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Futtatása után egy `kubectl` parancsot, az Azure-hitelesítésre kéri. Kövesse a képernyőn megjelenő utasításokat követve fejezze be a folyamatot, az alábbi példában látható módon:

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Amikor végzett, a rendszer gyorsítótárazza a hitelesítési jogkivonat. Csak reprompted bejelentkezni, amikor a jogkivonat lejárt vagy a Kubernetes konfigurációs fájl újból létrehozza.

Ha látja az engedélyezési hibaüzenet Miután sikeresen bejelentkezett, ellenőrizze, hogy:
1. A felhasználó bejelentkezik, az Azure AD-példányt (ebben a forgatókönyvben a helyzet gyakran egy másik címtárból pedig összevont fiók használatakor) nem a vendég nem.
2. A felhasználó nincs több mint 200 csoport tagja.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>További lépések

Az Azure AD-felhasználók és csoportok segítségével szabályozza a fürterőforrásokhoz való hozzáférést, lásd: [szerepköralapú hozzáférés-vezérlés és az Azure AD-identitások használata az AKS fürt erőforrásokhoz való hozzáférésének][azure-ad-rbac].

Kubernetes-fürtök biztonságos kapcsolatos további információkért lásd: [hozzáférési és azonosító beállításai az aks-ben)][rbac-authorization].

Identitás- és erőforrás-vezérlést ajánlott eljárásokat lásd: [gyakorlati tanácsok a hitelesítés és engedélyezés az aks-ben][operator-best-practices-identity].

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
