---
title: Tárolócsoport manuális leállítása vagy indítása
description: Megtudhatja, hogyan állíthatja le vagy indíthat el manuálisan egy tárolócsoportot az Azure Container Instances-ban.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533430"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Tárolók manuális leállítása vagy indítása az Azure Container-példányokban

A tárolócsoport [újraindítási házirend-beállítása](container-instances-restart-policy.md) határozza meg, hogy a tárolópéldányok alapértelmezés szerint hogyan kezdődnek vagy állnak le. Az alapértelmezett beállítást manuálisan is felülbírálhatja, ha leállít vagy elindít egy tárolócsoportot.

## <a name="stop"></a>Leállítás

Manuálisan állítsa le a futó tárolócsoportot – például az [az container stop][az-container-stop] parancs vagy az Azure Portal használatával. Bizonyos tároló-munkaterhelések esetén előfordulhat, hogy le szeretne állítani egy hosszú ideig futó tárolócsoportot egy meghatározott időszak után a költségek csökkentése érdekében. 

*Amikor egy tárolócsoport leállított állapotba kerül, leállítja és újrahasznosítja a csoport összes tárolóját. Nem őrzi meg a tároló állapotát.*

A tárolók újrahasznosítása után az [erőforrások](container-instances-container-groups.md#resource-allocation) felszabadítva vannak, és a tárolócsoport számlázási leáll.

A leállítási műveletnek nincs hatása, ha a tárolócsoport már le van állítva (sikeres vagy sikertelen állapotban van). Például egy tárolócsoport egyszeri tárolófeladatokat, amelyek sikeresen futtatta sikeresen leáll a Sikeres állapotban. Az adott állapotban lévő csoport leállítására tett kísérletek nem változtatják meg az állapotot. 

## <a name="start"></a>Indítás

Ha egy tárolócsoport le van állítva – vagy azért, mert a tárolók önmagukban leálltak, vagy manuálisan leállította a csoportot –, elindíthatja a tárolókat. Például az [az container start][az-container-start] parancs vagy az Azure Portal on manuálisan indítsa el a tárolókat a csoportban. Ha a tároló rendszerképe frissül, egy új lemezkép kerül lekérése. 

Egy tárolócsoport indítása új központi telepítést indít ugyanazzal a tárolókonfigurációval. Ez a művelet segítségével gyorsan újra felhasználhatja az ismert tárolócsoport-konfigurációt, amely a várt nak megfelel. Nem kell létrehozniegy új tárolócsoportot, hogy ugyanazt a számítási feladatot.

Ez a művelet a tárolócsoport összes tárolóját indítja el. Nem indíthat el egy adott tárolót a csoportban.

Miután manuálisan elindít vagy újraindít egy tárolócsoportot, a tárolócsoport a konfigurált újraindítási házirend nek megfelelően fut.
  
## <a name="restart"></a>Újraindítás

A tárolócsoportot újraindíthatja futás közben – például az [az container restart][az-container-restart] paranccsal. Ez a művelet újraindítja a tárolócsoport összes tárolóját. Ha a tároló rendszerképe frissül, egy új lemezkép kerül lekérése. 

A tárolócsoport újraindítása akkor hasznos, ha egy központi telepítési problémát szeretne elhárítani. Ha például egy ideiglenes erőforrás-korlátozás megakadályozza a tárolók sikeres futtatását, a csoport újraindítása megoldhatja a problémát.

Ez a művelet újraindítja a tárolócsoport összes tárolóját. A csoport ban nem lehet újraindítani egy adott tárolót.

Miután manuálisan újraindít egy tárolócsoportot, a tárolócsoport a konfigurált újraindítási házirend nek megfelelően fut.

## <a name="next-steps"></a>További lépések

További információ az Azure Container Instances [újraindítási szabályzatbeállításairól.](container-instances-restart-policy.md)

A meglévő konfigurációval rendelkező tárolócsoport manuális leállítása és indítása mellett [frissítheti egy](container-instances-update.md) futó tárolócsoport beállításait is.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
