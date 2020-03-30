---
title: Tárolócsoport frissítése
description: Megtudhatja, hogyan frissítheti a futó tárolókat az Azure Container Példányok tárolócsoportjaiban.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533302"
---
# <a name="update-containers-in-azure-container-instances"></a>Tárolók frissítése az Azure Container Instancesben

A tárolópéldányok normál működése során szükség lehet a futó tárolók frissítésére egy [tárolócsoportban.](container-instances-container-groups.md) Például frissítheti a lemezkép verzióját, módosíthatja a DNS-nevet, frissíteni szeretné a környezeti változókat, vagy frissíteni egy olyan tároló állapotát, amelynek alkalmazása összeomlott.

> [!NOTE]
> A leszakított vagy törölt tárolócsoportok nem frissíthetők. Miután egy tárolócsoport leállt (sikeres vagy sikertelen állapotban van), vagy törölték, a csoportot újként kell telepíteni.

## <a name="update-a-container-group"></a>Tárolócsoport frissítése

Frissítse a tárolók egy futó tárolócsoportban egy meglévő csoport, legalább egy módosított tulajdonsággal. Tárolócsoport frissítésekénél a csoport összes futó tárolója helyben újraindul, általában ugyanazon az alapul szolgáló tárológazdagépen.

Telepítsen újra egy meglévő tárolócsoportot a create parancs (vagy az Azure Portal használatával) kiadásával, és adja meg egy meglévő csoport nevét. Módosítsa a csoport legalább egy érvényes tulajdonságát, amikor kiadja a create parancsot az újratelepítés elindításához, és hagyja változatlanul a fennmaradó tulajdonságokat (vagy továbbra is használja az alapértelmezett értékeket). Nem minden tárolócsoport-tulajdonság érvényes az újratelepítéshez. Lásd: [Törlést igénylő tulajdonságok](#properties-that-require-container-delete) a nem támogatott tulajdonságok listájához.

A következő Azure CLI-példa frissíti a tárolócsoportot egy új DNS-névcímkével. Mivel a csoport DNS-névcímke tulajdonsága frissíthető, a tárolócsoport újratelepítése és tárolói újratelepítése lesz.

Kezdeti telepítés dns-névcímkével *myapplication-staging*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Frissítse a tárolócsoportot egy új DNS-névcímkével, *a myapplication*alkalmazással, és hagyja változatlanul a fennmaradó tulajdonságokat:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Előnyök frissítése

Egy meglévő tárolócsoport frissítésének elsődleges előnye a gyorsabb üzembe helyezés. Meglévő tárolócsoport újratelepítésekor a tárolórendszerkép-rétegek lekérése az előző központi telepítés által gyorsítótárazott atárolóból. Ahelyett, hogy az összes lemezképréteget frissen húzná ki a rendszerleíró adatbázisból, ahogy az az új központi telepítésekkel történik, csak a módosított rétegek (ha vannak) kerülnek lehúzásra.

A nagyobb tárolólemezképeken, például a Windows Server Core-on alapuló alkalmazások jelentősen javulhatnak a telepítési sebességben, amikor az új törlése és telepítése helyett frissít.

## <a name="limitations"></a>Korlátozások

A tárolócsoport nem minden tulajdonsága támogatja a frissítéseket. A tárolócsoport egyes tulajdonságainak módosításához először törölnie kell, majd újra telepítenie kell a csoportot. További információt a [Tároló törlését igénylő tulajdonságok](#properties-that-require-container-delete).

A tárolócsoport minden tárolója újraindul a tárolócsoport frissítésekén. Egy többtárolós csoportban egy adott tároló frissítése vagy helyi újraindítása nem hajtható végre.

A tároló IP-címe általában nem változik a frissítések között, de nem garantált, hogy ugyanaz marad. Mindaddig, amíg a tárolócsoport ugyanerre az alapul szolgáló állomásra van telepítve, a tárolócsoport megtartja IP-címét. Bár ritka, és bár az Azure Container Instances mindent megtesz, hogy újratelepíteni ugyanahhoz a gazdagéphez, vannak olyan Azure-belső események, amelyek okozhatnak átcsoportosítást egy másik gazdagépre. A probléma enyhítése érdekében mindig használjon DNS-névcímkét a tárolópéldányokhoz.

A leszakított vagy törölt tárolócsoportok nem frissíthetők. Miután egy tárolócsoport leállt *(leállított* állapotban van), vagy törölték, a csoport újként lesz telepítve.

## <a name="properties-that-require-container-delete"></a>Tárolótörlését igénylő tulajdonságok

Ahogy korábban említettük, nem minden tárolócsoport-tulajdonságok frissíthetők. Például egy tároló portjainak módosításához vagy a házirend újraindításához először törölnie kell a tárolócsoportot, majd újra létre kell hoznia.

Ezek a tulajdonságok az átcsoportosítás előtt tárolócsoport-törlést igényelnek:

* Operációs rendszer típusa
* CPU
* Memory (Memória)
* Újraindítási szabályzat
* Portok

Amikor töröl egy tárolócsoportot, és újra létrehozza, az nem "újratelepítve", hanem újat hoz létre. Az összes lemezképréteg frissen kerül lekéri a rendszerleíró adatbázisból, nem pedig egy korábbi központi telepítés gyorsítótárazása. A tároló IP-címe is változhat, mivel egy másik mögöttes gazdagépüzembe helyezése miatt.

## <a name="next-steps"></a>További lépések

Említett többször ebben a cikkben a **konténer csoport**. Az Azure Container Instances minden tárolója egy tárolócsoportban van üzembe helyezve, és a tárolócsoportok egynél több tárolót tartalmazhatnak.

[Tárolócsoportok az Azure Container Instancesben](container-instances-container-groups.md)

[Többtárolós csoport üzembe helyezése](container-instances-multi-container-group.md)

[Tárolók manuális leállítása vagy indítása az Azure Container-példányokban](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
