---
title: Felügyelt identitás az ACR-feladatban
description: Engedélyezze az Azure Resources felügyelt identitását egy Azure Container Registry feladatban, hogy a feladat más Azure-erőforrásokhoz, köztük más magántároló-beállításjegyzékekhez is hozzáférhessen.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111769"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Azure által felügyelt identitás használata az ACR-feladatokban 

Engedélyezze [az Azure-erőforrások felügyelt identitását](../active-directory/managed-identities-azure-resources/overview.md) egy [ACR-feladatban,](container-registry-tasks-overview.md)így a feladat más Azure-erőforrásokhoz is hozzáférhet anélkül, hogy hitelesítő adatokat kellene megadnia vagy kezelnie. Például egy felügyelt identitás használatával engedélyezheti, hogy egy feladatlépés lekérődzése vagy leküldéses tárolórendszerképek egy másik rendszerleíró adatbázisba.

Ebben a cikkben megtudhatja, hogyan használhatja az Azure CLI-t egy felhasználó által hozzárendelt vagy rendszeráltal hozzárendelt felügyelt identitás engedélyezéséhez egy ACR-feladaton. Használhatja az Azure Cloud Shell vagy az Azure CLI helyi telepítését. Ha helyileg szeretné használni, a 2.0.68-as vagy újabb verzió szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

Szemléltetésképpen a jelen cikkben található példaparancsok az [acr feladat létrehozása][az-acr-task-create] használatával hoznak létre egy alapszintű lemezkép-létrehozási feladatot, amely lehetővé teszi a felügyelt identitást. A felügyelt identitás használatával védett erőforrások elérésére vonatkozó mintaforgatókönyveket a következő témakörökben térhet hozzá:

* [Több regisztrációs adatbázisra kiterjedő hitelesítés](container-registry-tasks-cross-registry-authentication.md)
* [Külső erőforrások elérése az Azure Key Vaultban tárolt titkos adatokkal](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Miért érdemes felügyelt identitást használni?

Az Azure-erőforrások felügyelt identitása a kiválasztott Azure-szolgáltatások automatikusan felügyelt identitást biztosít az Azure Active Directoryban. Konfigurálhat egy ACR-feladatot felügyelt identitással, hogy a feladat más biztonságos Azure-erőforrásokhoz is hozzáférhessen anélkül, hogy hitelesítő adatokat afeladat lépésekben hitelesítő adatokat admeg.

A felügyelt identitások két típusa van:

* *Felhasználó által hozzárendelt identitások*, amelyek több erőforráshoz rendelhetők, és addig is megmaradnak, ameddig csak szeretné. A felhasználó által hozzárendelt identitások jelenleg előzetes verzióban vannak.

* Rendszerhez *rendelt identitás*, amely egy adott erőforrás, például egy ACR-feladat egyedi, és az erőforrás élettartamáig tart.

Az ACR-feladatokban engedélyezheti az identitások egyik vagy mindkét típusát. Adja meg az identitás hozzáférést egy másik erőforráshoz, csakúgy, mint bármely rendszerbiztonsági tag. Amikor a feladat fut, az identitás t használja az erőforrás eléréséhez minden olyan tevékenységi lépésben, amely hozzáférést igényel.

## <a name="steps-to-use-a-managed-identity"></a>Felügyelt identitás használatának lépései

Kövesse ezeket a magas szintű lépéseket felügyelt identitás a ACR-feladattal való használatához.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Nem kötelező) Felhasználó által hozzárendelt identitás létrehozása

Ha azt tervezi, hogy egy felhasználó által hozzárendelt identitást, használjon egy meglévő identitást, vagy hozza létre az identitást az Azure CLI vagy más Azure-eszközök használatával. Használja például az [az identity create][az-identity-create] parancsot. 

Ha azt tervezi, hogy csak a rendszer által hozzárendelt identitás, hagyja ki ezt a lépést. Az ACR-feladat létrehozásakor rendszeráltal hozzárendelt identitást hoz létre.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Identitás engedélyezése ACR-feladatban

ACR-feladat létrehozásakor tetszés szerint engedélyezze a felhasználó által hozzárendelt identitást, a rendszer által hozzárendelt identitást vagy mindkettőt. Például adja `--assign-identity` át a paramétert, amikor futtatja az [az acr feladat létrehozása][az-acr-task-create] parancsot az Azure CLI.For example, pass the parameter when you run the az acr task create command in the Azure CLI.

A rendszer hez rendelt identitás engedélyezéséhez adja `--assign-identity` `assign-identity [system]`át érték vagy nélkül. A következő példaparancs létrehoz egy Linux-feladatot egy nyilvános GitHub-tárházból, amely létrehozza a lemezképet, és lehetővé teszi a `hello-world` rendszer által hozzárendelt felügyelt identitást:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

A felhasználó által hozzárendelt identitás `--assign-identity` engedélyezéséhez adja át az identitás *erőforrás-azonosítójának* értékét. A következő példaparancs létrehoz egy Linux-feladatot egy nyilvános GitHub-tárházból, amely létrehozza a lemezképet, és lehetővé teszi a `hello-world` felhasználó által hozzárendelt felügyelt identitást:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Az identitásshow-parancs futtatásával lejuthat az [az identity show][az-identity-show] identitásazonosító-azonosító levezetésére. A *myResourceGroup* erőforráscsoport ban lévő *myUserAssignedIdentity* azonosító erőforrásazonosítója a következő képernyő: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Adja meg az identitásengedélyeket más Azure-erőforrások eléréséhez

A feladat követelményeitől függően adja meg az identitásengedélyeket más Azure-erőforrások eléréséhez. Példák erre vonatkozóan:

* Rendeljen a felügyelt identitás egy szerepkör lekéréses, leküldéses és lekéréses, vagy más engedélyeket a céltároló beállításjegyzék az Azure-ban. A beállításjegyzék-szerepkörök teljes listáját az [Azure Container Registry szerepkörei és engedélyei című témakörben található.](container-registry-roles.md) 
* Rendeljen a felügyelt identitás egy szerepkört az Azure key vault titkos kulcsok olvasásához.

Az [Azure CLI](../role-based-access-control/role-assignments-cli.md) vagy más Azure-eszközök használatával kezelheti az erőforrásokhoz való szerepköralapú hozzáférést. Futtassa például az [az szerepkör-hozzárendelés create][az-role-assignment-create] parancsot, hogy az identitást az erőforráshoz rendelje. 

A következő példa hozzárendel egy felügyelt identitást, amely hez lekell választani a tárolóbeállításjegyzékből lekéréses engedélyeket. A parancs megadja a feladatidentitás és a célbeállításjegyzék *erőforrás-azonosítójának* *elsődleges* azonosítóját.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Nem kötelező) Hitelesítő adatok hozzáadása a feladathoz

Ha a feladatnak hitelesítő adatokra van szüksége a lemezképek lekérése vagy leküldése egy másik egyéni beállításjegyzékbe, vagy más erőforrások eléréséhez, adjon hozzá hitelesítő adatokat a feladathoz. Futtassa az [az acr feladat hitelesítő adatok][az-acr-task-credential-add] `--use-identity` hozzáadása parancsot hitelesítő adatok hozzáadásához, és adja át a paramétert annak jelzésére, hogy az identitás hozzáférhet a hitelesítő adatokhoz. 

Ha például egy rendszer által kijelölt identitás hitelesítő adatait szeretné hozzáadni az Azure `use-identity [system]`container registry *targetregistry*használatával való hitelesítéshez, adja át a következőket:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Ha hitelesítő adatokat szeretne hozzáadni egy felhasználó által hozzárendelt identitásnak a *rendszerleíró*adatbázissal való hitelesítéshez, adja át `use-identity` az identitás *ügyfélazonosítójának* értékét. Példa:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Az identitásshow-parancs futtatásával lekaphatja [az identity show][az-identity-show] az identitásazonosító ügyfélazonosítóját. Az ügyfélazonosító az űrlap `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`GUID azonosítója.

### <a name="5-run-the-task"></a>5. A feladat futtatása

Miután konfigurált egy feladatot felügyelt identitással, futtassa a feladatot. Például a cikkben létrehozott feladatok egyikének teszteléséhez manuálisan indítsa el azt az [acr feladatfuttatási][az-acr-task-run] paranccsal. Ha további, automatikus feladateseményindítókat konfigurált, a feladat automatikusan elindul.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és használhatja a felhasználó által hozzárendelt vagy rendszeráltal hozzárendelt felügyelt identitást egy ACR-feladaton. Az ACR-feladatokvédett identitással védett erőforrásaihoz való hozzáférést a következő esetekben tetsző:

* [Több regisztrációs adatbázisra kiterjedő hitelesítés](container-registry-tasks-cross-registry-authentication.md)
* [Külső erőforrások elérése az Azure Key Vaultban tárolt titkos adatokkal](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
