---
title: Tároló csoportjának frissítése
description: Megtudhatja, hogyan frissítheti a futó tárolókat a Azure Container Instances-tároló csoportjaiban.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533302"
---
# <a name="update-containers-in-azure-container-instances"></a>Tárolók frissítése Azure Container Instances

A tároló példányainak normál működése során előfordulhat, hogy frissítenie kell a futó tárolókat egy [tároló csoportba](container-instances-container-groups.md). Előfordulhat például, hogy frissíteni szeretné a rendszerkép verzióját, módosítania kell egy DNS-nevet, frissíti a környezeti változókat, vagy frissítenie kell egy olyan tároló állapotát, amelynek az alkalmazása összeomlott.

> [!NOTE]
> A leállított vagy törölt tároló-csoportok nem frissíthetők. Ha a tároló csoport leállt (vagy sikeres vagy sikertelen állapotban van), vagy törölve lett, a csoportot újként kell telepíteni.

## <a name="update-a-container-group"></a>Tároló csoport frissítése

Frissítse a tárolókat egy futó tároló csoportban egy meglévő csoport legalább egy módosított tulajdonsággal való újbóli üzembe helyezésével. Amikor frissít egy tároló csoportot, a csoportban lévő összes futó tárolót a rendszer helyben indítja újra, általában ugyanarra az alapul szolgáló tároló-gazdagépre.

Egy meglévő tároló csoport újratelepítése a Create parancs kiadásával (vagy a Azure Portal használatával) és egy meglévő csoport nevének megadásával. Módosítsa a csoport legalább egy érvényes tulajdonságát, amikor a Create parancsot kiadja az újratelepítési művelet elindításához, és a fennmaradó tulajdonságokat változatlanul hagyja (vagy továbbra is használja az alapértelmezett értékeket). Nem minden tároló csoport tulajdonságai érvényesek az újratelepítéshez. A nem támogatott tulajdonságok listáját a [törlést igénylő tulajdonságok](#properties-that-require-container-delete) között tekintheti meg.

A következő Azure CLI-példa egy új DNS-név címkével rendelkező tároló-csoportot frissít. Mivel a csoport DNS-név felirat tulajdonsága frissíthető, a tároló csoport újratelepítése történik, és a tárolók újraindulnak.

Kezdeti üzembe helyezés DNS-névvel feliratú *myapplication – átmeneti*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Frissítse a tároló csoportot egy új DNS-név címkével, *myapplication*, és hagyja változatlanul a többi tulajdonságot:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
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

A leállított vagy törölt tároló-csoportok nem frissíthetők. Ha egy tároló csoport leállt (leállított állapotban *van* ), vagy törölve lett, a rendszer újként telepíti a csoportot.

## <a name="properties-that-require-container-delete"></a>Tároló törlését igénylő tulajdonságok

Ahogy korábban említettük, nem lehet frissíteni a Container Group összes tulajdonságát. Ha például egy tároló portjait vagy újraindítási szabályzatát szeretné módosítani, először törölnie kell a tároló csoportot, majd újra létre kell hoznia.

Ezek a tulajdonságok a tárolók csoportjának törlését igénylik az újratelepítést megelőzően:

* Operációs rendszer típusa
* CPU
* Memory (Memória)
* Újraindítási szabályzat
* Portok

Amikor töröl egy tároló csoportot, és újból létrehozza, azt nem "újból üzembe helyezi", hanem új létrehozása szükséges. Az összes képréteget a beállításjegyzékből frissen kell húzni, nem pedig a korábbi üzemelő példány által gyorsítótárazott adatokból. Előfordulhat, hogy a tároló IP-címe is változhat, mert egy másik mögöttes gazdagépre van telepítve.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben többször is említettük a **Container csoportot**. Azure Container Instances összes tárolója egy tároló csoportba van telepítve, és a tárolók csoportjai több tárolót is tartalmazhatnak.

[Tárolócsoportok az Azure Container Instancesben](container-instances-container-groups.md)

[Többtárolós csoport üzembe helyezése](container-instances-multi-container-group.md)

[Tárolók manuális leállítása vagy elindítása Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
