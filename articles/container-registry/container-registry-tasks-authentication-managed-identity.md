---
title: Felügyelt identitás használata Azure Container Registry feladatokkal
description: Felügyelt identitás engedélyezése az Azure-erőforrásokhoz egy Azure Container Registry feladatban, hogy a feladat hozzáférhessen más Azure-erőforrásokhoz, beleértve a privát tárolók beállításjegyzékeit is.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/11/2019
ms.author: danlep
ms.openlocfilehash: 9f7c083a079e42172a9e2865f90293fa4d6813d8
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640414"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Azure által felügyelt identitás használata az ACR-feladatokban 

Felügyelt [identitás](../active-directory/managed-identities-azure-resources/overview.md) engedélyezése az Azure-erőforrásokhoz egy [ACR](container-registry-tasks-overview.md)-feladatban, így a feladat hozzáférhet más Azure-erőforrásokhoz anélkül, hogy hitelesítő adatokat kellene megadnia vagy kezelnie. Például egy felügyelt identitás használatával engedélyezheti a feladatok lépéseit a tárolók rendszerképeinek egy másik beállításjegyzékbe való lekéréséhez vagy leküldéséhez.

Ebből a cikkből megtudhatja, hogyan használhatja az Azure CLI-t egy felhasználó által hozzárendelt vagy rendszerhez rendelt felügyelt identitás engedélyezésére egy ACR-feladatban. Az Azure CLI Azure Cloud Shell vagy helyi telepítését használhatja. Ha helyileg szeretné használni, a 2.0.68 vagy újabb verziót kötelező megadni. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

A biztonságos erőforrásoknak a felügyelt identitás használatával való eléréséhez az ACR-feladatokból a következő témakörben talál további információt:

* [Beállításjegyzék közötti hitelesítés](container-registry-tasks-cross-registry-authentication.md)
* [Külső erőforrások elérése Azure Key Vaultban tárolt titkos kulcsokkal](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Miért érdemes felügyelt identitást használni?

Az Azure-erőforrások felügyelt identitása a kiválasztott Azure-szolgáltatásokat a Azure Active Directory (Azure AD) automatikusan felügyelt identitással biztosítja. Az ACR-feladatokat felügyelt identitással is konfigurálhatja, hogy a feladat hozzáférhessen más védett Azure-erőforrásokhoz anélkül, hogy a hitelesítő adatokat átadná a feladat lépéseibe.

A felügyelt identitások két típusúak:

* *Felhasználó által hozzárendelt identitások*, amelyek több erőforráshoz rendelhetők, és ameddig szeretnék megőrizni. A felhasználó által hozzárendelt identitások jelenleg előzetes verzióban érhetők el.

* Egy *rendszer által hozzárendelt identitás*, amely egyedi egy adott erőforráshoz, például egy ACR-feladathoz, és az adott erőforrás élettartamára vonatkozik.

Az ACR-feladatokban bármelyik vagy mindkét típusú identitás engedélyezhető. Az identitás hozzáférésének biztosítása egy másik erőforráshoz, ugyanúgy, mint bármely rendszerbiztonsági tag. A feladat futtatásakor a rendszer az identitás használatával fér hozzá az erőforráshoz bármely, hozzáférést igénylő feladat lépéseiben.

## <a name="steps-to-use-a-managed-identity"></a>A felügyelt identitás használatának lépései

Kövesse ezeket a magas szintű lépéseket, hogy felügyelt identitást alkalmazzon egy ACR-feladattal.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. Választható Felhasználó által hozzárendelt identitás létrehozása

Ha felhasználó által hozzárendelt identitást szeretne használni, használhat meglévő identitást is. Vagy hozza létre az identitást az Azure CLI vagy más Azure-eszközök használatával. Használja például az az [Identity Create][az-identity-create] parancsot. 

Ha úgy tervezi, hogy csak rendszerhez rendelt identitást használ, hagyja ki ezt a lépést. Az ACR-feladat létrehozásakor létrehozhat egy rendszer által hozzárendelt identitást is.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Identitás engedélyezése ACR-feladaton

Ha egy ACR-feladatot hoz létre, opcionálisan engedélyezheti a felhasználó által hozzárendelt identitást, a rendszerhez rendelt identitást vagy mindkettőt. Adja át például a `--assign-identity` paramétert az az [ACR Task Create][az-acr-task-create] parancs az Azure CLI-ben való futtatásakor.

A rendszer által hozzárendelt identitás engedélyezéséhez adjon `--assign-identity` meg értéket vagy `assign-identity [system]`értéket. A következő parancs egy nyilvános GitHub-tárházból hoz létre egy Linux-feladatot `hello-world` , amely egy git commit triggerrel és egy rendszerhez rendelt felügyelt identitással építi össze a rendszerképet:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity
```

A felhasználó által hozzárendelt identitás engedélyezéséhez adja `--assign-identity` meg az identitás erőforrás- *azonosítójának* értékét. A következő parancs egy nyilvános GitHub-tárházból hoz létre egy Linux-feladatot `hello-world` , amely egy git commit triggerrel és egy felhasználó által hozzárendelt felügyelt identitással építi fel a rendszerképet:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity <resourceID>
```

Az identitás erőforrás-AZONOSÍTÓját az az [Identity show][az-identity-show] parancs futtatásával kérheti le. Az erőforráscsoport *myResourceGroup* lévő azonosító *MYUSERASSIGNEDIDENTITY* erőforrás-azonosítója a következő: Form. 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Személyazonossági engedélyek megadása más Azure-erőforrások eléréséhez

A feladat követelményeitől függően adja meg az identitás engedélyeit más Azure-erőforrások eléréséhez. Példák erre vonatkozóan:

* A felügyelt identitást hozzárendelheti egy olyan szerepkörhöz, amely lekéréses, leküldéses és lekéréses, illetve egyéb engedélyekkel rendelkezik az Azure-beli cél tároló A beállításjegyzék szerepköreinek teljes listáját itt tekintheti meg: [Azure Container Registry szerepkörök és engedélyek](container-registry-roles.md). 
* Rendeljen hozzá egy szerepkört a felügyelt identitáshoz az Azure Key vaultban található titkos kódok olvasásához.

Az [Azure CLI](../role-based-access-control/role-assignments-cli.md) -vel vagy más Azure-eszközökkel kezelheti a szerepköralapú hozzáférést az erőforrásokhoz. Futtassa például az az [szerepkör-hozzárendelés létrehozási][az-role-assignment-create] parancsát, hogy hozzárendelje az identitás szerepkört az identitáshoz. 

A következő példa felügyelt identitást rendel hozzá a tároló-beállításjegyzékből való lekéréshez szükséges engedélyekhez. A parancs megadja az identitás *egyszerű szolgáltatásnév azonosítóját* és a cél beállításjegyzék *erőforrás* -azonosítóját.


```azurecli
az role assignment create --assignee <servicePrincipalID> --scope <registryID> --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. Választható Hitelesítő adatok hozzáadása a feladathoz

Ha a feladat lekéri vagy leküldi a képeket egy másik Azure Container registrybe, adja hozzá a hitelesítő adatokat a feladathoz az identitás hitelesítéséhez. Futtassa az az [ACR Task hitelesítőadat Add][az-acr-task-credential-add] parancsot, és adja `--use-identity` át a paramétert az identitás hitelesítő adatainak a feladathoz való hozzáadásához. 

Ha például egy rendszerhez rendelt identitás hitelesítő adatait szeretné felvenni a beállításjegyzék *targetregistry*való hitelesítéshez, adja `use-identity [system]`meg a következőt:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Ha a felhasználó által hozzárendelt identitáshoz hitelesítő adatokat szeretne hozzáadni a beállításjegyzék *targetregistry*való hitelesítéshez `use-identity` , adja meg az identitás *ügyfél* -azonosítójának értékét. Példa:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Az identitás ügyfél-AZONOSÍTÓját az az [Identity show][az-identity-show] parancs futtatásával kérheti le. Az ügyfél-azonosító az űrlap `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`GUID azonosítója.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és használhatja a felhasználó által hozzárendelt vagy rendszerhez rendelt felügyelt identitást egy ACR-feladatban. A biztonságos erőforrásoknak a felügyelt identitás használatával való eléréséhez az ACR-feladatokból a következő témakörben talál további információt:

* [Beállításjegyzék közötti hitelesítés](container-registry-tasks-cross-registry-authentication.md)
* [Külső erőforrások elérése Azure Key Vaultban tárolt titkos kulcsokkal](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
