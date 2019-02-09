---
title: Az Azure Container Registry authentication felügyelt identitással
description: A privát tárolójegyzékben található rendszerképek hozzáférést biztosítanak a felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt Azure-identitás segítségével.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 01/16/2019
ms.author: danlep
ms.openlocfilehash: fdba8969ad326565834625fe1ca7ece5e089a904
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984205"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Egy Azure által felügyelt identitás hitelesítésére és egy Azure container registry használata 

Használja a [-identitás az Azure-erőforrások](../active-directory/managed-identities-azure-resources/overview.md) történő hitelesítéshez és az Azure container registry nélkül egy másik Azure-erőforrás kellene adja meg, vagy a tárolójegyzék hitelesítő adatainak kezeléséhez. Például állítsa be egy felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt identitás egy Linux rendszerű virtuális gépen való hozzáférés tárolórendszerképeket a tárolóregisztrációs adatbázisból egyszerűen használhatja a nyilvános beállításjegyzék.

Ebben a cikkben, tudjon meg többet a felügyelt identitásokból és:

> [!div class="checklist"]
> * Egy Azure-beli virtuális gépen egy felhasználó által hozzárendelt vagy rendszer által hozzárendelt identitás engedélyezése
> * Az identitás hozzáférést biztosít egy Azure container registrybe
> * Elérni a beállításjegyzéket, és kérje le a tároló rendszerképét a felügyelt identitás használata 

Az Azure-erőforrások létrehozását, ehhez a cikkhez futtatása az Azure CLI 2.0.55 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

Tároló-beállításjegyzék beállítását, és a egy tároló rendszerképének leküldése, is rendelkeznie kell a Docker helyi telepítése. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [macOS][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="why-use-a-managed-identity"></a>Miért érdemes használni egy felügyelt identitás?

Egy felügyelt identitás, az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban (Azure AD) automatikusan felügyelt identitást biztosít. Konfigurálható [bizonyos Azure-erőforrások](../active-directory/managed-identities-azure-resources/services-support-msi.md), beleértve a virtuális gépek felügyelt identitással. Ezután használja az identitás programkódban vagy parancsprogramban hitelesítő adatok nélkül férhetnek hozzá a többi Azure-erőforrások.

Felügyelt identitások két típusa van:

* *Felhasználó által hozzárendelt identitások*, amely hozzárendelése több erőforrást, és addig, amíg a kívánt fenntartását. Felhasználó által hozzárendelt identitások jelenleg előzetes verzióban érhető el.

* A *rendszer által felügyelt identitás*, amely egy adott erőforrás, például egyetlen virtuális gép egyedi, és az erőforrás teljes tart.

Egy Azure-erőforrás egy felügyelt identitás beállítása után hozzáférést az identitás a szeretne egy másik erőforrás, mint egy rendszerbiztonsági tagot. Például egy felügyelt identitás szerepkör hozzárendelése lekéréses, leküldéses és lekéréses vagy egyéb engedélyek az Azure-beli privát regisztrációs adatbázisba. (Beállításjegyzék szerepkörök teljes listáját lásd: [Azure Container Registry-szerepkörökről és engedélyekről](container-registry-roles.md).) Egy vagy több erőforrást akkor is hozzáférést egy identitás.

Ezután használja az identitás hitelesítésére bármely [szolgáltatás, amely támogatja az Azure AD-hitelesítés](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication), anélkül, hogy a hitelesítő adatokat a kódban. Az identitás használatával egy virtuális gépéről elérni az Azure container registry, az Azure Resource Manager végez hitelesítést. A forgatókönyvtől függően a felügyelt identitás használatával történő hitelesítéshez módjának kiválasztása:

* [Az Azure AD hozzáférési jogkivonat beszerzése](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programozott módon használja a HTTP- vagy REST-hívások

* Használja a [Azure SDK-k](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Jelentkezzen be az Azure parancssori felület vagy PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) az identitáshoz. 

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ha még nem rendelkezik egy Azure container Registry tárolóregisztrációs adatbázist, hozzon létre egy beállításjegyzéket, és a egy minta-tároló rendszerképének leküldése. Útmutató: [a rövid útmutató: Az Azure CLI használatával privát tárolóregisztrációs adatbázis létrehozása](container-registry-get-started-azure-cli.md).

Ez a cikk feltételezi, hogy a `aci-helloworld:v1` tárolórendszerképet a beállításjegyzék tárolja. A beállításjegyzék neve a példákban *myContainerRegistry*. Cserélje le a saját beállításjegyzék és a rendszerképnevek a későbbi lépésekben.

## <a name="create-a-docker-enabled-vm"></a>Docker-kompatibilis virtuális gép létrehozása

Hozzon létre egy Docker-kompatibilis Ubuntu virtuális gép. Telepítenie kell a [Azure CLI-vel](/cli/azure/install-azure-cli?view=azure-cli-latest) a virtuális gépen. Ha már rendelkezik egy Azure virtuális gépen, hagyja ki ezt a lépést a virtuális gép létrehozásához.

Alapértelmezés szerint üzembe helyezése Ubuntu Azure virtuális gép [az virtuális gép létrehozása][az-vm-create]. A következő példában létrehozunk egy nevű virtuális Gépet *myDockerVM* egy meglévő erőforráscsoportot nevű *myResourceGroup*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása néhány percig tart. Ha a parancs végrehajtása befejeződött, jegyezze fel a `publicIpAddress` az Azure CLI által megjelenített. Használja ezt a címet, hogy az SSH-kapcsolatokat a virtuális géphez.

### <a name="install-docker-on-the-vm"></a>A gépen telepítheti a Dockert

Miután a virtuális gép fut, győződjön meg arról, az SSH-kapcsolatot a virtuális géphez. Cserélje le *publicIpAddress* a virtuális gép nyilvános IP-címét.

```bash
ssh azureuser@publicIpAddress
```

Futtassa a következő parancsot a gépen telepítheti a Dockert:

```bash
sudo apt install docker.io -y
```

A telepítés után futtassa a következő paranccsal ellenőrizze, hogy a Docker a virtuális gép megfelelően fut:

```bash
sudo docker run -it hello-world
```

Kimenet:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Telepítse az Azure CLI-t

Kövesse a [Azure CLI telepítése az Apt használatával](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) az Azure CLI telepítése az Ubuntus virtuális gép. Ebben a cikkben győződjön meg arról, hogy 2.0.55 verzió telepítése vagy újabb.

Lépjen ki az SSH-munkamenetből.

## <a name="example-1-access-with-a-user-assigned-identity"></a>1. példa: Hozzáférés egy felhasználó által hozzárendelt identitással

### <a name="create-an-identity"></a>Hozzon létre egy azonosítót

Az előfizetés használatával hozzon létre egy azonosítót a [az identitás létrehozása](/cli/azure/identity?view=azure-cli-latest#az-identity-create) parancsot. Használhatja a korábban a tárolóregisztrációs adatbázis vagy a virtuális gép vagy egy másik létrehozásához használt ugyanabban az erőforráscsoportban.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Az alábbi lépéseket az identitás beállításához használja a [az identitás show] [ az-identity-show] parancsot az identitáshoz tartozó erőforrás-azonosító és a résztvevő-azonosító tárolására változókba.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Van szüksége a szolgáltatásidentitás azonosítója egy későbbi lépésben bejelentkezéskor a CLI a virtuális gép számára, mert az érték megjelenítése:

```bash
echo $userID
```

A csomagazonosítója a következő formában:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>A virtuális gép konfigurálása az identitáshoz

A következő [az virtuális gép identitás hozzárendelése] [ az-vm-identity-assign] parancs beállítja a Docker VM-a felhasználó által hozzárendelt identitással:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>A tároló-beállításjegyzék identitás hozzáférés engedélyezése

Mostantól konfigurálhatja az identitás a tárolóregisztrációs adatbázis eléréséhez. Első alkalommal használják a [az acr show] [ az-acr-show] parancs használatával beszerezheti a beállításjegyzék erőforrás-Azonosítóját:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Használja a [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] parancsot a AcrPull szerepkör hozzárendelése a beállításjegyzékben. Ez a szerepkör biztosít [engedélyek lekéréses](container-registry-roles.md) a regisztrációs adatbázisba. Adja meg mindkét lekéréses és engedélyek, rendelje hozzá a ACRPush szerepkört.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Az identitás használatával hozzáférni a beállításjegyzékhez

SSH-t a Docker virtuális gép, amelynek része az identitást. Futtassa az alábbi Azure CLI-parancsokat, a virtuális gépen telepített Azure CLI használatával.

Első lépésként jelentkezzen be az Azure CLI-t [az bejelentkezési][az-login], konfigurálta a virtuális gép azonosítójának használatával. A <userID>, helyettesítse be az identitást, az előző lépésben lekért azonosítója. 

```azurecli
az login --identity --username <userID>
```

Ezután jelentkezzen be a tárolójegyzékbe [az acr bejelentkezési][az-acr-login]. Ez a parancs használatakor a parancssori Felületet használja-e az Active Directory-jogkivonat jön létre, amikor futtatta `az login` a munkamenet a tárolóregisztrációs adatbázis zökkenőmentesen hitelesítéséhez. (A telepítés a virtuális gép függően szüksége lehet a parancs és a docker-parancsokat futtatni `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Megjelenik egy `Login succeeded` üzenet. Ezután futtassa a `docker` parancsok hitelesítő adatok megadása nélkül. Futtasson például [docker pull] [ docker-pull] , lekéréses a `aci-helloworld:v1` lemezképet, adja meg a tárolójegyzék bejelentkezési kiszolgálójának nevére. A bejelentkezési kiszolgáló nevét a tárolóregisztrációs adatbázis nevét (csupa kisbetűket) követ áll `.azurecr.io` – például `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>2. példa Hozzáférés a rendszer által hozzárendelt identitással

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Konfigurálja a virtuális Gépet egy rendszer által felügyelt identitással

A következő [az virtuális gép identitás hozzárendelése] [ az-vm-identity-assign] parancs beállítja a Docker VM egy rendszer által hozzárendelt identitással:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Használja a [az vm show] [ az-vm-show] parancsot az egy változóra értékéhez `principalId` (a szolgáltatásnév-Azonosítót), a virtuális gép identitása, a későbbi lépésekben használni.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>A tároló-beállításjegyzék identitás hozzáférés engedélyezése

Mostantól konfigurálhatja az identitás a tárolóregisztrációs adatbázis eléréséhez. Első alkalommal használják a [az acr show] [ az-acr-show] parancs használatával beszerezheti a beállításjegyzék erőforrás-Azonosítóját:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Használja a [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] parancsot a AcrPull szerepkör hozzárendelése az identitást. Ez a szerepkör biztosít [engedélyek lekéréses](container-registry-roles.md) a regisztrációs adatbázisba. Adja meg mindkét lekéréses és engedélyek, rendelje hozzá a ACRPush szerepkört.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Az identitás használatával hozzáférni a beállításjegyzékhez

SSH-t a Docker virtuális gép, amelynek része az identitást. Futtassa az alábbi Azure CLI-parancsokat, a virtuális gépen telepített Azure CLI használatával.

Első lépésként jelentkezzen be az Azure CLI-t [az bejelentkezési][az-login], a rendszer által hozzárendelt identitás használatával a virtuális gépen.

```azurecli
az login --identity
```

Ezután jelentkezzen be a tárolójegyzékbe [az acr bejelentkezési][az-acr-login]. Ez a parancs használatakor a parancssori Felületet használja-e az Active Directory-jogkivonat jön létre, amikor futtatta `az login` a munkamenet a tárolóregisztrációs adatbázis zökkenőmentesen hitelesítéséhez. (A telepítés a virtuális gép függően szüksége lehet a parancs és a docker-parancsokat futtatni `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Megjelenik egy `Login succeeded` üzenet. Ezután futtassa a `docker` parancsok hitelesítő adatok megadása nélkül. Futtasson például [docker pull] [ docker-pull] , lekéréses a `aci-helloworld:v1` lemezképet, adja meg a tárolójegyzék bejelentkezési kiszolgálójának nevére. A bejelentkezési kiszolgáló nevét a tárolóregisztrációs adatbázis nevét (csupa kisbetűket) követ áll `.azurecr.io` – például `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az Azure Container Registryvel felügyelt identitások használatával, és hogyan:

> [!div class="checklist"]
> * Egy Azure-beli virtuális gépen egy felhasználó által hozzárendelt vagy rendszer által hozzárendelt identitás engedélyezése
> * Az identitás hozzáférést biztosít egy Azure container registrybe
> * Elérni a beállításjegyzéket, és kérje le a tároló rendszerképét a felügyelt identitás használata

* Tudjon meg többet [felügyelt identitások az Azure-erőforrások](/azure/active-directory/managed-identities-azure-resources/).


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli