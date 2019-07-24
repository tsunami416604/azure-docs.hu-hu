---
title: Tárolók frissítése Azure Container Instances
description: Megtudhatja, hogyan frissítheti a futó tárolókat a Azure Container Instances-tároló csoportjaiban.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: d555ba6b8c2b32fc6ec56d6c51dda9626b6f0cb0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325551"
---
# <a name="update-containers-in-azure-container-instances"></a>Tárolók frissítése Azure Container Instances

A tároló példányainak normál működése során előfordulhat, hogy a tárolók csoportjában frissítenie kell a tárolókat. Előfordulhat például, hogy frissíteni szeretné a rendszerkép verzióját, módosítania kell egy DNS-nevet, frissíti a környezeti változókat, vagy frissítenie kell egy olyan tároló állapotát, amelynek az alkalmazása összeomlott.

## <a name="update-a-container-group"></a>Tároló csoport frissítése

Frissítse a tárolókat egy adott csoportba egy meglévő csoport legalább egy módosított tulajdonsággal való újbóli üzembe helyezésével. Egy tároló csoport frissítésekor a rendszer a csoportban lévő összes futó tárolót újraindítja helyben.

Egy meglévő tároló csoport újratelepítése a Create parancs kiadásával (vagy a Azure Portal használatával) és egy meglévő csoport nevének megadásával. Módosítsa a csoport legalább egy érvényes tulajdonságát, amikor a Create parancs kiadja az újratelepítést. Nem minden tároló csoport tulajdonságai érvényesek az újratelepítéshez. A nem támogatott tulajdonságok listáját a [törlést igénylő tulajdonságok](#properties-that-require-container-delete) között tekintheti meg.

A következő Azure CLI-példa egy új DNS-név címkével rendelkező tároló-csoportot frissít. Mivel a csoport DNS-név címkéjének tulajdonsága módosul, a rendszer újratelepíti a tároló csoportot, és a tárolói újraindulnak.

Kezdeti üzembe helyezés DNS-névvel feliratú *myapplication – átmeneti*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Frissítse a tároló csoportot egy új DNS-név címkével, *myapplication*:

```azurecli-interactive
# Update container group (restarts container)
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Frissítési előnyök

Egy meglévő tároló csoport frissítésének elsődleges előnye a gyorsabb üzembe helyezés. Egy meglévő tároló-csoport újratelepítésekor a tároló képrétegei a korábbi üzemelő példány által gyorsítótárazott adatokból lesznek leképezve. Ahelyett, hogy az összes képréteget a beállításjegyzékből frissen, az új központi telepítésekkel végzett, csak a módosított rétegeket (ha vannak) húzta.

A nagyobb méretű tárolók rendszerképein alapuló alkalmazások, például a Windows Server Core, jelentősen javítják az üzembe helyezés sebességét, ha a törlés és az új telepítése helyett a frissítésre kerül.

## <a name="limitations"></a>Korlátozások

A Container Group egyik tulajdonsága sem támogatja a frissítéseket. A tárolók egyes tulajdonságainak módosításához először törölnie kell, majd újra kell telepítenie a csoportot. Részletekért lásd: [tároló törlését igénylő tulajdonságok](#properties-that-require-container-delete).

A tároló csoport összes tárolója újraindul a tároló csoport frissítésekor. Egy többtárolós csoporton belül nem végezheti el a megadott tároló frissítését vagy helyben történő újraindítását.

A tárolók IP-címe általában nem változik a frissítések között, de nem garantált, hogy változatlan marad. Ha a tároló csoport ugyanarra a mögöttes gazdagépre van telepítve, a tároló csoport megőrzi az IP-címét. Bár ritka, és bár Azure Container Instances minden erőfeszítést megtesz az ugyanarra a gazdagépre való újbóli üzembe helyezésre, néhány olyan Azure-belső esemény is van, amely egy másik gazdagépre való újratelepítést okozhat. A probléma megoldásához mindig használjon DNS-név címkét a tároló példányaihoz.

A leállított vagy törölt tároló-csoportok nem frissíthetők. Ha egy tároló csoport leállt (leállított állapotban  van), vagy törölve lett, a rendszer újként telepíti a csoportot.

## <a name="properties-that-require-container-delete"></a>Tároló törlését igénylő tulajdonságok

Ahogy korábban említettük, nem lehet frissíteni a Container Group összes tulajdonságát. Ha például egy tároló portjait vagy újraindítási szabályzatát szeretné módosítani, először törölnie kell a tároló csoportot, majd újra létre kell hoznia.

Ezek a tulajdonságok a tárolók csoportjának törlését igénylik az újratelepítést megelőzően:

* Operációs rendszer típusa
* CPU
* Memory (Memória)
* Újraindítási szabályzat
* Portok

Amikor töröl egy tároló csoportot, és újból létrehozza, azt nem "újból üzembe helyezi", hanem új létrehozása szükséges. Az összes képréteget a beállításjegyzékből frissen kell húzni, nem pedig a korábbi üzemelő példány által gyorsítótárazott adatokból. Előfordulhat, hogy a tároló IP-címe is változhat, mert egy másik mögöttes gazdagépre van telepítve.

## <a name="next-steps"></a>További lépések

Ebben a cikkben többször is említettük a **Container csoportot**. Azure Container Instances összes tárolója egy tároló csoportba van telepítve, és a tárolók csoportjai több tárolót is tartalmazhatnak.

[Tárolócsoportok az Azure Container Instancesben](container-instances-container-groups.md)

[Többtárolós csoport üzembe helyezése](container-instances-multi-container-group.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
