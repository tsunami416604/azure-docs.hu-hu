---
title: Manuálisan állítsa le, vagy indítsa el a tárolók az Azure Container Instances szolgáltatásban
description: Ismerje meg, hogy manuálisan állítsa le és indítsa el a tárolócsoport az Azure Container Instances szolgáltatásban.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 50f3ecf69561313a5bda67827cfb02d2f61d461f
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610217"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Manuálisan állítsa le, vagy indítsa el a tárolók az Azure Container Instances szolgáltatásban

A [újraindítási házirend](container-instances-restart-policy.md) egy tárolócsoport beállítás azt határozza meg, hogyan tárolópéldányok indítása vagy leállítása alapértelmezés szerint. Az alapértelmezett beállítás szerint manuálisan leállítása vagy elindítása egy tárolócsoport felül lehet bírálni.

## <a name="stop"></a>Leállítás

Egy futó tárolócsoportra - manuálisan állítsa le például használatával a [az tároló stop] [ az-container-stop] parancsot vagy az Azure Portalon. Az egyes tárolókhoz kapcsolódó számítási feladatok, érdemes leállítani egy hosszú ideig futó tárolócsoport menteni a költségeket a meghatározott időszak után. 

*Egy tárolócsoport leállított állapotba kerül, ha leállítja és újraindítja a csoportban lévő összes tárolót. Nem őrzi meg a tároló állapota.*

Bár egy leállított tárolócsoportban a tárolók lehetőség, a [erőforrások](container-instances-container-groups.md#resource-allocation) használható lefoglalva maradnak. Ezért a számlázás továbbra is egy leállított tárolócsoport.

A leállítási művelet nem befolyásolja, ha a tárolócsoport már befejeződött (sikeres vagy sikertelen állapotban van). Például egyszer futtatott tároló feladatok, amelyek sikeresen futtattak egy tárolócsoportot ér véget, a sikeres állapotú. Megkísérli leállítani a csoport, ebben az állapotban nem módosítja a állapotát. 

## <a name="start"></a>Indítás

A tárolócsoport le van állítva – vagy mert a tárolók saját leállt, vagy manuálisan leállították a csoport - elindításához a tárolók. Például a [az tároló kezdő] [ az-container-start] parancsot vagy az Azure Portalon manuálisan elindítani a tárolók a csoportban. Bármely tárolóra a tárolórendszerkép frissül, ha egy új rendszerképet kéri le. 

Új központi telepítést azonos konfigurációjú tároló indítása egy tárolócsoport kezdődik. Ez a művelet segítségével gyorsan felhasználhatja egy ismert tároló a portcsoportok, amely a várt módon működik. Nem kell ugyanazt a számítási feladatot futtatni egy új tárolócsoport létrehozásához.

Ez a művelet által elindított egy tárolócsoport összes tárolót. A csoport egy adott tároló nem lehet elindítani.

Manuálisan indítsa el, vagy indítsa újra egy tárolócsoportot, miután a tároló csoport fut a beállított megfelelően újraindítási házirend.
  
## <a name="restart"></a>Újraindítás

Futás közben – például az egy tárolócsoport újraindíthatja a [az tároló újraindítási] [ az-container-restart] parancsot. Ez a művelet újraindítja az összes tároló tárolócsoportban. Bármely tárolóra a tárolórendszerkép frissül, ha egy új rendszerképet kéri le. 

A tárolócsoport újraindítása akkor hasznos, ha a kívánt központi telepítési hiba elhárítása. Például ha egy ideiglenes erőforrás-korlátozás meggátolja, hogy a tárolók sikeresen fut, a csoport újraindítása megoldhatja a problémát.

A tárolócsoport összes tároló újra lesz indítva, ezzel a művelettel. A csoport egy adott tároló nem indítható újra.

Manuálisan indítsa újra egy tárolócsoportot, miután a tároló csoport fut a beállított megfelelően újraindítási házirend.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [indítsa újra a házirend-beállítások](container-instances-restart-policy.md) az Azure Container Instances szolgáltatásban.

Manuálisan leállítása folyamatban van, és a egy tárolócsoport kezdve a meglévő konfigurációt kívül is [-beállításainak frissítése](container-instances-update.md) egy futó tárolócsoportra vonatkozó.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
