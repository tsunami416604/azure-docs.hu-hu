---
title: Hitelesítés felügyelt identitással
description: Hozzáférést biztosítanak a rendszerképek a privát tároló beállításjegyzékben egy felhasználó által hozzárendelt vagy a rendszer által hozzárendelt felügyelt Azure-identitás használatával.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 9b8bed78629d3a9739ec00772ad5c8216a04c122
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456487"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Azure felügyelt identitás használata az Azure-tároló beállításjegyzékében való hitelesítéshez 

Az [Azure-erőforrások felügyelt identitáshasználatával](../active-directory/managed-identities-azure-resources/overview.md) hitelesítheti magát egy Azure-tároló beállításjegyzékében egy másik Azure-erőforrásból anélkül, hogy meg kellene adnia vagy kezelnie kellene a rendszerleíró hitelesítő adatokat. Például állítson be egy felhasználó által hozzárendelt vagy rendszer-hozzárendelt felügyelt identitást egy Linux virtuális gépen a tárolórendszerképek eléréséhez a tároló beállításjegyzékéből, olyan könnyen, mint egy nyilvános beállításjegyzék használata.

Ebben a cikkben további információ a felügyelt identitásokról és a következőkről:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt vagy rendszeráltal hozzárendelt identitás engedélyezése Azure-beli virtuális gépen
> * Az identitás-hozzáférés megadása egy Azure-tároló beállításjegyzékéhez
> * A felügyelt identitás használata a beállításjegyzék eléréséhez és egy tárolórendszerkép lekérése 

Az Azure-erőforrások létrehozásához ez a cikk megköveteli, hogy futtassa az Azure CLI 2.0.55-ös vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

Egy tároló beállításjegyzék beállítása és leküldéses egy tároló rendszerképet, akkor is kell docker helyileg telepítve kell lennie. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [macOS][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="why-use-a-managed-identity"></a>Miért érdemes felügyelt identitást használni?

Az Azure-erőforrások felügyelt identitása automatikusan felügyelt identitást biztosít az Azure-szolgáltatásokszámára az Azure Active Directoryban (Azure AD). [Konfigurálhat bizonyos Azure-erőforrásokat](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), beleértve a felügyelt identitással rendelkező virtuális gépeket is. Ezután az identitás használatával más Azure-erőforrások eléréséhez, anélkül, hogy hitelesítő adatokat a kód vagy a parancsfájlok.

A felügyelt identitások két típusa van:

* *Felhasználó által hozzárendelt identitások*, amelyek több erőforráshoz rendelhetők, és addig is megmaradnak, ameddig csak szeretné. A felhasználó által hozzárendelt identitások jelenleg előzetes verzióban vannak.

* A *rendszer által felügyelt identitás*, amely egy adott erőforrás, például egyetlen virtuális gép, és az erőforrás élettartama tart.

Miután beállított egy Azure-erőforrást felügyelt identitással, adja meg az identitásnak a kívánt hozzáférést egy másik erőforráshoz, ugyanúgy, mint bármely rendszerbiztonsági tag. Például rendeljen hozzá egy felügyelt identitás egy szerepkör lekéréses, leküldéses és lekéréses, vagy más engedélyeket egy privát beállításjegyzék az Azure-ban. (A beállításjegyzék-szerepkörök teljes listáját az [Azure Container Registry szerepkörei és engedélyei](container-registry-roles.md)című témakörben található.) Egy vagy több erőforráshoz identitás-hozzáférést adhat.

Ezután az identitás használatával hitelesítheti magát minden olyan [szolgáltatás, amely támogatja az Azure AD-hitelesítést,](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)anélkül, hogy a kódhitelesítő adatokat. Az identitás használatával egy Azure-tároló beállításjegyzék egy virtuális gépről való hozzáféréshez, hitelesíti magát az Azure Resource Manager használatával. A forgatókönyvtől függően válassza ki, hogyan hitelesíthető a felügyelt identitás használatával:

* [Azure AD-hozzáférési jogkivonat beszerzése](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programozott módon HTTP- vagy REST-hívások használatával

* Az [Azure SDK-k](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md) használata

* [Jelentkezzen be az Azure CLI-be vagy a PowerShellbe](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) az identitással. 

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ha még nem rendelkezik egy Azure-tároló beállításjegyzék, hozzon létre egy beállításjegyzéket, és leküldéses egy mintatároló-lemezképet. Lépések: [Rövid útmutató: Hozzon létre egy privát tároló beállításjegyzék az Azure CLI használatával.](container-registry-get-started-azure-cli.md)

Ez a cikk feltételezi, hogy a `aci-helloworld:v1` tárolórendszerkép a beállításjegyzékben van tárolva. A példák a *myContainerRegistry*rendszerleíró adatbázisnevét használják. Cserélje le a saját rendszerleíró adatbázis és a kép nevét a későbbi lépésekben.

## <a name="create-a-docker-enabled-vm"></a>Docker-kompatibilis virtuális gép létrehozása

Hozzon létre egy Docker-kompatibilis Ubuntu virtuális gépet. Az [Azure CLI-t](/cli/azure/install-azure-cli?view=azure-cli-latest) is telepítenie kell a virtuális gépen. Ha már rendelkezik egy Azure virtuális géppel, hagyja ki ezt a lépést a virtuális gép létrehozásához.

Telepítsen egy alapértelmezett Ubuntu Azure virtuális gépet [az vm create][az-vm-create]segítségével. A következő példa létrehoz egy *myDockerVM* nevű virtuális gép egy meglévő erőforráscsoportban, amelynek neve *myResourceGroup:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása néhány percig tart. Amikor a parancs befejeződik, `publicIpAddress` vegye figyelembe az Azure CLI által megjelenített. Ezzel a címmel SSH-kapcsolatokat létesítanek a virtuális géppel.

### <a name="install-docker-on-the-vm"></a>A Docker telepítése a virtuális gépre

A virtuális gép futtatása után, hogy egy SSH-kapcsolat a virtuális gép. Cserélje le *a nyilvános IpAddress-t* a virtuális gép nyilvános IP-címére.

```bash
ssh azureuser@publicIpAddress
```

Futtassa a következő parancsot a Docker telepítéséhez a virtuális gépre:

```bash
sudo apt install docker.io -y
```

A telepítés után futtassa a következő parancsot annak ellenőrzéséhez, hogy a Docker megfelelően fut-e a virtuális számítógépen:

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

Kövesse az [Azure CLI telepítése apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) az Azure CLI telepítéséhez az Ubuntu virtuális gépen. Ebben a cikkben győződjön meg arról, hogy a 2.0.55-ös vagy újabb verziót telepíti.

Lépjen ki az SSH-munkamenetből.

## <a name="example-1-access-with-a-user-assigned-identity"></a>1. példa: Hozzáférés felhasználó által hozzárendelt identitással

### <a name="create-an-identity"></a>Identitás létrehozása

Hozzon létre egy identitást az előfizetésben az [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) paranccsal. Használhatja ugyanazt az erőforráscsoportot, amelyet korábban használt a tároló beállításjegyzék vagy a virtuális gép létrehozásához, vagy egy másikat.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Az identitás konfigurálásához a következő lépésekben, használja az [az identity show][az-identity-show] parancsot az identitás erőforrás-azonosító és a szolgáltatás egyszerű azonosítóját változókban tárolja.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Mivel egy későbbi lépésben szüksége van az identitásazonosítóra, amikor bejelentkezik a CLI-be a virtuális gépről, mutassa meg az értéket:

```bash
echo $userID
```

Az azonosító a következő formában van:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>A virtuális gép konfigurálása az identitással

A következő [az vm identitáshozzárendelési][az-vm-identity-assign] parancs konfigurálja a Docker virtuális gép a felhasználó által hozzárendelt identitás:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Identitás-hozzáférés megadása a tároló beállításjegyzékéhez

Most konfigurálja az identitást a tároló beállításjegyzékének eléréséhez. Először használja az [az acr show][az-acr-show] parancsot a rendszerleíró adatbázis erőforrás-azonosítójának leéséhez:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Az [az szerepkör-hozzárendelés létrehozása parancs][az-role-assignment-create] segítségével rendelje hozzá az AcrPull szerepkört a rendszerleíró adatbázishoz. Ez a szerepkör [lekéréses engedélyeket](container-registry-roles.md) biztosít a beállításjegyzékhez. A lekéréses és leküldéses engedélyek biztosításához rendelje hozzá az ACRPush szerepkört.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Az identitás használata a rendszerleíró adatbázis eléréséhez

SSH-t a Docker-virtuális gépbe, amely az identitással van konfigurálva. Futtassa a következő Azure CLI-parancsokat a virtuális gépre telepített Azure CLI használatával.

Először hitelesítse magát az Azure CLI az [bejelentkezési][az-login]használatával a virtuális gépkonfigurált identitás használatával. A `<userID>`területen helyettesítse az előző lépésben beolvasott identitás azonosítóját. 

```azurecli
az login --identity --username <userID>
```

Ezután hitelesítse magát a rendszerleíró [adatbázisban az acr bejelentkezéssel.][az-acr-login] Ha ezt a parancsot használja, a CLI a `az login` ransorán létrehozott Active Directory-jogkivonatot használja a munkamenet zökkenőmentes hitelesítéséhez a tároló beállításjegyzékével. (A ttól függően, hogy a virtuális gép beállítása, előfordulhat, `sudo`hogy futtatni a parancsot, és docker parancsokat .)

```azurecli
az acr login --name myContainerRegistry
```

Látnod kellene `Login succeeded` egy üzenetet. Ezután hitelesítő `docker` adatok megadása nélkül is futtathat parancsokat. Futtassa például a [docker-lekéréset][docker-pull] a `aci-helloworld:v1` lemezkép lehívásához, megadva a rendszerleíró adatbázis bejelentkezési kiszolgálójának nevét. A bejelentkezési kiszolgáló neve a tároló rendszerleíró nevéből (az összes `mycontainerregistry.azurecr.io`kisbetűből) áll, amelyet - `.azurecr.io` például .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>2. példa: Hozzáférés rendszerhez rendelt identitással

### <a name="configure-the-vm-with-a-system-managed-identity"></a>A virtuális gép konfigurálása rendszer által felügyelt identitással

A következő [az vm identitáshozzárendelési][az-vm-identity-assign] parancs a Docker virtuális gép egy rendszer által hozzárendelt identitással konfigurálja:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Az [az vm show][az-vm-show] paranccsal állítsa `principalId` be a változó értékét (a szolgáltatás egyszerű azonosítója) a virtuális gép identitását, későbbi lépésekben használható.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Identitás-hozzáférés megadása a tároló beállításjegyzékéhez

Most konfigurálja az identitást a tároló beállításjegyzékének eléréséhez. Először használja az [az acr show][az-acr-show] parancsot a rendszerleíró adatbázis erőforrás-azonosítójának leéséhez:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Az [az szerepkör-hozzárendelés létrehozása][az-role-assignment-create] parancs használatával rendelje hozzá az AcrPull szerepkört az identitáshoz. Ez a szerepkör [lekéréses engedélyeket](container-registry-roles.md) biztosít a beállításjegyzékhez. A lekéréses és leküldéses engedélyek biztosításához rendelje hozzá az ACRPush szerepkört.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Az identitás használata a rendszerleíró adatbázis eléréséhez

SSH-t a Docker-virtuális gépbe, amely az identitással van konfigurálva. Futtassa a következő Azure CLI-parancsokat a virtuális gépre telepített Azure CLI használatával.

Először hitelesítse az Azure [CLI-t][az-login]az az bejelentkezéssel, a rendszer által hozzárendelt identitás használatával a virtuális gépen.

```azurecli
az login --identity
```

Ezután hitelesítse magát a rendszerleíró [adatbázisban az acr bejelentkezéssel.][az-acr-login] Ha ezt a parancsot használja, a CLI a `az login` ransorán létrehozott Active Directory-jogkivonatot használja a munkamenet zökkenőmentes hitelesítéséhez a tároló beállításjegyzékével. (A ttól függően, hogy a virtuális gép beállítása, előfordulhat, `sudo`hogy futtatni a parancsot, és docker parancsokat .)

```azurecli
az acr login --name myContainerRegistry
```

Látnod kellene `Login succeeded` egy üzenetet. Ezután hitelesítő `docker` adatok megadása nélkül is futtathat parancsokat. Futtassa például a [docker-lekéréset][docker-pull] a `aci-helloworld:v1` lemezkép lehívásához, megadva a rendszerleíró adatbázis bejelentkezési kiszolgálójának nevét. A bejelentkezési kiszolgáló neve a tároló rendszerleíró nevéből (az összes `mycontainerregistry.azurecr.io`kisbetűből) áll, amelyet - `.azurecr.io` például .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan kell felügyelt identitásokat használni az Azure Container Registry szolgáltatással, és hogyan:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt vagy rendszerhez rendelt identitás engedélyezése Az Azure virtuális gépben
> * Az identitás-hozzáférés megadása egy Azure-tároló beállításjegyzékéhez
> * A felügyelt identitás használata a beállításjegyzék eléréséhez és egy tárolórendszerkép lekérése

* További információ [az Azure-erőforrások felügyelt identitásairól.](/azure/active-directory/managed-identities-azure-resources/)


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
