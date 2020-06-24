---
title: Az Azure AD használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatja az Azure AD-t az Azure Kubernetes szolgáltatásban (ak)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/04/2020
ms.openlocfilehash: 8d446d82550a6bc790d162ee944b0753979b6546
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84782670"
---
# <a name="integrate-aks-managed-azure-ad-preview"></a>Az AK által felügyelt Azure AD integrálása (előzetes verzió)

> [!Note]
> Az új AK által felügyelt Azure AD-felület nem érinti a meglévő AK (Azure Kubernetes Service) fürtök Azure Active Directory (Azure AD) integrációját.

Az AK-val felügyelt Azure ad-integrációval egyszerűbbé válik az Azure AD integrációs felülete, ahol a felhasználóknak korábban egy ügyfélalkalmazás, egy kiszolgálói alkalmazás létrehozása és az Azure AD-bérlő számára szükséges a címtár-olvasási engedélyek megadása. Az új verzióban az AK erőforrás-szolgáltató kezeli az ügyfél-és kiszolgálói alkalmazásokat.

## <a name="limitations"></a>Korlátozások

* Jelenleg nem frissíthet egy meglévő AK Azure AD-integrált fürtöt az új AK által felügyelt Azure AD-élményre.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói az önkiszolgáló, a választható lehetőségek alapján érhetők el. Az előzetes verziók az "adott állapotban" és "elérhetőként" jelennek meg, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A (z) és az ügyfél-támogatási szolgálatok a lehető leghatékonyabban fedezik az előzetes verziókat. Ezért ezeket a funkciókat nem éles használatra szánták. További információkért lásd a következő támogatási cikkeket:
>
> - [AK-támogatási szabályzatok](support-policies.md)
> - [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="before-you-begin"></a>Előkészületek

* Keresse meg az Azure-fiók bérlői AZONOSÍTÓját a Azure Portal navigálva, és válassza ki Azure Active Directory > tulajdonságok > könyvtár-azonosító

> [!Important]
> A Kubectl-et legalább 1,18-es verzióval kell használni

A következő erőforrásokat kell telepítenie:

- Az Azure CLI, 2.5.1-es vagy újabb verzió
- Az AK – előzetes verziójú 0.4.38 bővítmény
- Kubectl legalább [1,18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180) -es verzióval

A következő Azure CLI-parancsokkal telepítheti/frissítheti az AK-előnézet bővítményt vagy újabb verziót:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

A kubectl telepítéséhez használja a következő parancsokat:

```azurecli
sudo az aks install-cli
kubectl version --client
```

[Ezeket az utasításokat](https://kubernetes.io/docs/tasks/tools/install-kubectl/) más operációs rendszerekhez használhatja.

> [!CAUTION]
> Miután regisztrált egy szolgáltatást egy előfizetéshez, jelenleg nem tudja törölni a szolgáltatást. Az előzetes verziójú funkciók engedélyezésekor az alapértelmezett beállítások az előfizetésben később létrehozott AK-fürtökhöz is használhatók. Ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Ehelyett használjon külön előfizetést az előzetes verziójú funkciók tesztelésére és visszajelzések gyűjtésére.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Több percet is igénybe vehet, amíg az állapot **regisztrálva**jelenik meg. A regisztrációs állapotot az az [Feature List](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Ha az állapot regisztrálva értékre van állítva, frissítse az `Microsoft.ContainerService` erőforrás-szolgáltató regisztrációját az az [Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```
## <a name="azure-ad-authentication-overview"></a>Az Azure AD-hitelesítés áttekintése

A Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) állíthat be a felhasználó identitása vagy a címtár csoporttagság alapján. Az Azure AD-hitelesítés az OpenID-kapcsolattal rendelkező AK-fürtökhöz van megadva. Az OpenID Connect egy OAuth 2,0 protokollra épülő identitási réteg. Az OpenID Connecttel kapcsolatos további információkért tekintse meg az [ID Connect dokumentációját][open-id-connect].

A Kubernetes-fürtön belül a rendszer webhook jogkivonat-hitelesítést használ a hitelesítési tokenek ellenőrzéséhez. A webhook-jogkivonat hitelesítése az AK-fürt részeként van konfigurálva és felügyelve.

## <a name="webhook-and-api-server"></a>Webhook és API-kiszolgáló

:::image type="content" source="media/aad-integration/auth-flow.png" alt-text="Webhook és API-kiszolgáló hitelesítési folyamata":::

Ahogy az a fenti ábrán is látható, az API-kiszolgáló meghívja az AK webhook-kiszolgálót, és a következő lépéseket hajtja végre:

1. Az Azure AD ügyfélalkalmazás a kubectl által a OAuth 2,0-es eszköz- [engedélyezési folyamattal](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)rendelkező felhasználók bejelentkezésekor használható.
2. Az Azure AD egy access_token, id_token és egy refresh_token biztosít.
3. A felhasználó kérést küld a kubectl access_token a kubeconfig.
4. A Kubectl elküldi a access_tokent a APIServer.
5. Az API-kiszolgáló a hitelesítési webhook-kiszolgálóval van konfigurálva az érvényesítés végrehajtásához.
6. A hitelesítési webhook-kiszolgáló megerősíti, hogy a JSON Web Token aláírása érvényes az Azure AD nyilvános aláíró kulcsának ellenőrzésével.
7. A kiszolgálóalkalmazás felhasználó által megadott hitelesítő adatok használatával kérdezi le a bejelentkezett felhasználó csoporttagságait az MS Graph APIból.
8. A rendszer választ kap a APIServer olyan felhasználói adatokkal, mint például a hozzáférési jogkivonat egyszerű felhasználóneve (UPN) jogcíme, valamint a felhasználó csoporttagság az objektumazonosító alapján.
9. Az API a Kubernetes szerepkör/RoleBinding alapján hajt végre engedélyezési döntést.
10. Az engedélyezést követően az API-kiszolgáló egy választ küld a kubectl.
11. A Kubectl visszajelzést nyújt a felhasználónak.


## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>AK-fürt létrehozása az Azure AD-vel engedélyezve

Hozzon létre egy AK-fürtöt az alábbi CLI-parancsok használatával.

Azure-erőforráscsoport létrehozása:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Használhat meglévő Azure AD-csoportot, vagy létrehozhat egy újat. Szüksége lesz az Azure AD-csoport objektumazonosító-AZONOSÍTÓJÁRA.

```azurecli-interactive
# List existing groups in the directory
az ad group list
```

Ha új Azure AD-csoportot szeretne létrehozása a fürt rendszergazdái számára, használja a következő parancsot:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name MyDisplay --mail-nickname MyDisplay
```

AK-fürt létrehozása és rendszergazdai hozzáférés engedélyezése az Azure AD-csoport számára

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Az AK által felügyelt Azure AD-fürtök sikeres létrehozása a válasz törzsének következő szakasza.
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

A fürt néhány percen belül létrejön.

## <a name="access-an-azure-ad-enabled-cluster"></a>Hozzáférés egy Azure AD-kompatibilis fürthöz

A következő lépések elvégzéséhez szüksége lesz az [Azure Kubernetes Service cluster felhasználói](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-user-role) beépített szerepkörre.

A fürt eléréséhez szükséges felhasználói hitelesítő adatok beszerzése:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
A bejelentkezéshez kövesse az utasításokat.

A fürt csomópontjainak megtekintéséhez használja a kubectl Get Nodes parancsot:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
[Szerepköralapú Access Control (RBAC)](https://review.docs.microsoft.com/azure/aks/azure-ad-rbac?branch=pr-en-us-117564) konfigurálása a fürtökhöz tartozó további biztonsági csoportok konfigurálásához.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Az Azure AD hozzáférési problémáinak elhárítása

> [!Important]
> Az alábbiakban ismertetett lépések megkerülik a normál Azure AD-csoport hitelesítését. Csak vészhelyzetben használható.

Ha véglegesen letiltja azt, hogy nem fér hozzá a fürthöz hozzáféréssel rendelkező érvényes Azure AD-csoporthoz, továbbra is beszerezheti a rendszergazdai hitelesítő adatokat a fürt közvetlen eléréséhez.

A lépések végrehajtásához hozzá kell férnie az [Azure Kubernetes szolgáltatás-fürt rendszergazdai](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-admin-role) beépített szerepköréhez.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

## <a name="non-interactive-login-with-kubelogin"></a>Nem interaktív bejelentkezés a kubelogin

Vannak olyan nem interaktív forgatókönyvek, mint például a folyamatos integrációs folyamatok, amelyek jelenleg nem érhetők el a kubectl. A [kubelogin](https://github.com/Azure/kubelogin) használatával nem interaktív helyzetekben is elérheti a fürtöt.

## <a name="next-steps"></a>További lépések

* Az [Azure ad szerepköralapú Access Control][azure-ad-rbac]megismerése.
* [Kubelogin](https://github.com/Azure/kubelogin) használata az Azure-hitelesítés olyan szolgáltatásainak eléréséhez, amelyek nem érhetők el a kubectl-ben.
* A [Azure Resource Manager-(ARM-) Sablonok][aks-arm-template] használatával hozzon létre AK által felügyelt Azure ad-kompatibilis fürtöket.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

