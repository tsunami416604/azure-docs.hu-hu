---
title: Azure Container Instances a tárolók frissítése
description: Ismerje meg az Azure Container Instances-tároló csoportokat a futó tárolók frissítése.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: 2df6a2724cbdcd6bbb6c6ca6636256b7e399da8e
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854541"
---
# <a name="update-containers-in-azure-container-instances"></a>Azure Container Instances a tárolók frissítése

A container Instances szolgáltatásban a szokásos működés során előfordulhat, a tárolók egy tárolócsoport frissítéséhez szükséges. Például előfordulhat, hogy szeretné frissíteni a rendszerkép verziószámát, módosítsa a DNS-név, környezeti változók vagy egy tároló, amelynek az alkalmazás-összeomlást tapasztaltak állapotának frissítése.

## <a name="update-a-container-group"></a>A tárolócsoport frissítése

Frissítés a tárolócsoportban a tárolók újbóli üzembe helyezés egy meglévő csoporthoz legalább egy módosított tulajdonsággal. Amikor frissít egy tárolócsoportot, a csoportban lévő összes futó tárolók újraindul helyben.

Ismételt üzembe helyezése egy meglévő tárolócsoport kiadásával a create parancs (vagy használja az Azure Portalon), és adja meg a meglévő csoport nevét. Módosítsa a csoport legalább egy érvényes tulajdonság, az újbóli üzembe helyezés indításához a create parancs kiadásakor. Nem minden tárolócsoport tulajdonságainak jsou platné Pro újbóli üzembe helyezés. Lásd: [delete igénylő tulajdonságok](#properties-that-require-container-delete) nem támogatott tulajdonságok listáját.

Az alábbi Azure CLI-példa egy új DNS-névcímke egy tárolócsoport frissíti. A DNS-név címke, a csoport tulajdonsága módosítanak, mert a tárolócsoport újratelepítése van, és a tárolók újraindul.

Az első üzembe helyezés a DNS-névcímke *myapplication – átmeneti*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Egy új DNS-névcímke, frissítse a tárolócsoport *myapplication*:

```azurecli-interactive
# Update container group (restarts container)
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Frissítés előnyei

Meglévő tároló csoport frissítési elsődleges előnye gyorsabb üzembe helyezéshez. Ha egy meglévő tárolócsoport újbóli telepítése, a tároló rendszerképek rétegeit kikerülnek azokat, a korábbi központi telepítés által gyorsítótárazott. Összes lemezkép réteg lekérése a beállításjegyzékből friss, ahogyan az az új központi, helyett csak módosított rétegek (ha vannak) kéri le.

Nagyobb tárolórendszerképek alapján, mint a Windows Server Core jelentős fejlesztéseket tartalmaz az üzembe helyezés sebessége látható helyett frissítésekor alkalmazások törlése, és új központi telepítése.

## <a name="limitations"></a>Korlátozások

A tárolócsoport nem minden tulajdonság támogatja a frissítést. Ha módosítani szeretné egy tárolócsoport néhány tulajdonságát, meg kell először törölje, majd telepítse újra a csoport. További információkért lásd: [tároló igénylő tulajdonságok törlése](#properties-that-require-container-delete).

A tárolócsoport összes tároló újra lesz indítva, a tárolócsoport frissítésekor. Többtárolós csoport frissítés vagy egy adott tároló helyi újraindítása nem végezhetők el.

Az IP-cím, egy adott tároló általában nem módosítja a frissítések között, de nem garantálta változatlan marad. Mindaddig, amíg az alapul szolgáló ugyanazon a gazdagépen a tárolócsoport telepít, a tárolócsoport megőrzi az IP-címét. Habár ritkán fordul elő, és közben az Azure Container Instances ugyanazon a gazdagépen üzembe helyezése a mindent megtesz, vannak bizonyos Azure – belső eseményeket, amelyek miatt újbóli üzembe helyezés másik gazdagépen. A probléma megoldásához mindig használja a DNS-névcímke a container Instances.

Leállított vagy törölt tároló csoportokat nem lehet frissíteni. Miután leállt egy tárolócsoport (szerepel a *kilépett* állapot) vagy lett törölve, a csoport üzemel, amikor új.

## <a name="properties-that-require-container-delete"></a>Tároló törlése igénylő tulajdonságai

Ahogy korábban említettük, nem minden tároló tulajdonságai frissíthetők. Például portok módosításához, vagy indítsa újra a csoportházirend-tároló, kell először törölje az erőforráscsoportot, majd újra létre kell hoznia.

Ezek a Tulajdonságok tároló csoport törlése előtt újbóli üzembe helyezés van szükség:

* Operációs rendszer típusa
* CPU
* Memory (Memória)
* Újraindítási szabályzat
* Portok

Ha egy tárolócsoport törlése, és hozza létre újból, azt nem "újratelepítés", de létrehozott új. A beállításjegyzékből, nem a korábbi központi telepítés által gyorsítótárazott rendszer friss lekért összes rendszerképek rétegeit. Az IP-címe a tárolóhoz is egy másik alapjául szolgáló gazdagépre telepített miatt előfordulhat, hogy módosíthatja.

## <a name="next-steps"></a>További lépések

Számos alkalommal ebben a cikkben az említett a **tárolócsoport**. Minden az Azure Container Instances-tároló üzembe helyezése egy tárolócsoport és tárolócsoportok egynél több tárolót is tartalmazhatnak.

[Tárolócsoportok az Azure Container Instancesben](container-instances-container-groups.md)

[Többtárolós csoport üzembe helyezése](container-instances-multi-container-group.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
