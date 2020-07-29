---
title: Tároló csoport manuális leállítása vagy elindítása
description: Megtudhatja, hogyan állíthatja be manuálisan a tárolók csoportját a Azure Container Instancesban.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74533430"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Tárolók manuális leállítása vagy indítása az Azure Container Instancesben

A tároló-csoport [Újraindítási házirend](container-instances-restart-policy.md) beállítása határozza meg, hogy a tároló példányai alapértelmezés szerint hogyan kezdődnek vagy álljanak le. Az alapértelmezett beállítás felülbírálható egy tároló csoport manuális leállításával vagy indításával.

## <a name="stop"></a>Leállítás

Állítson le manuálisan egy futó tároló csoportot – például az az [Container stop][az-container-stop] paranccsal vagy a Azure Portal használatával. Bizonyos tároló-munkaterhelések esetében előfordulhat, hogy egy hosszú ideig futó tároló-csoportot kell leállítania a költségek megtakarítása érdekében meghatározott időszak után. 

*Ha egy tároló csoport leállított állapotba kerül, a leállítja és újrahasznosítja a csoportban lévő összes tárolót. Nem őrzi meg a tároló állapotát.*

A tárolók újrahasznosítása esetén az [erőforrások](container-instances-container-groups.md#resource-allocation) fel lesznek terhelve, és a tároló csoport számlázása leáll.

A Leállítás műveletnek nincs hatása, ha a tároló csoport már le van állítva (vagy sikeres vagy sikertelen állapotban van). Például egy olyan tároló-csoport, amelyen a Run-Once tároló feladatainak futtatása sikeres volt, sikeresen leállt a sikeres állapotba. Az adott állapotban lévő csoport leállítására tett kísérletek nem változtatják meg az állapotot. 

## <a name="start"></a>Indítás

Ha egy tároló csoport leáll – vagy azért, mert a tárolók le vannak zárva, vagy manuálisan leállította a csoportot –, elindíthatja a tárolókat. Például az az [Container Start][az-container-start] paranccsal vagy Azure Portal használatával manuálisan indíthatja el a csoportba tartozó tárolókat. Ha egy tárolóhoz tartozó tároló képe frissül, egy új rendszerképet kell meghúzni. 

A tároló-csoport elindítása egy új központi telepítést indít el ugyanazzal a tároló-konfigurációval. Ezzel a művelettel gyorsan újra felhasználhatja a várt módon működő, ismert tárolók csoportjainak konfigurációját. Nem kell létrehoznia új tároló csoportot, hogy ugyanazt a számítási feladatot futtassa.

Ez a művelet elindítja a tároló csoport összes tárolóját. A csoportban nem indítható el egy adott tároló.

A tároló csoport manuális elindítása vagy újraindítása után a tároló csoport a beállított újraindítási szabályzatnak megfelelően fut.
  
## <a name="restart"></a>Újraindítás

A tárolók csoportjának újraindításához futtathatja a rendszert, például az az [Container restart][az-container-restart] parancs használatával. Ez a művelet újraindítja a tároló csoportban lévő összes tárolót. Ha egy tárolóhoz tartozó tároló képe frissül, egy új rendszerképet kell meghúzni. 

A tároló-csoport újraindítása akkor hasznos, ha az üzembe helyezéssel kapcsolatos problémát szeretné elhárítani. Ha például egy ideiglenes erőforrás-korlátozás megakadályozza, hogy a tárolók sikeresen fussanak, a csoport újraindítása megoldhatja a problémát.

Ez a művelet újraindítja a tárolók csoportjának összes tárolóját. A csoportban lévő adott tároló nem indítható újra.

A tároló csoport manuális újraindítása után a tároló csoport a beállított újraindítási szabályzatnak megfelelően fut.

## <a name="next-steps"></a>További lépések

További információ a Azure Container Instances [Újraindítási házirend-beállításairól](container-instances-restart-policy.md) .

A meglévő konfigurációval rendelkező tárolók manuális leállítása és elindítása mellett a futó tárolók csoportjának [beállításait is frissítheti](container-instances-update.md) .

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
