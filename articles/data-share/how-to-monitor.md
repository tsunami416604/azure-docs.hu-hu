---
title: Az Azure-adatmegosztás figyelése
description: Megtudhatja, hogyan figyelheti a meghívás állapotát, oszthat meg előfizetéseket és pillanatkép-előzményeket az Azure Data Share szolgáltatásban
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490510"
---
# <a name="monitor-azure-data-share"></a>Az Azure Data Share monitorozása  

Ez a cikk bemutatja, hogyan figyelheti az adatmegosztások az Azure Data Share használatával. Adatszolgáltatóként az adatmegosztási kapcsolatok különböző aspektusait figyelheti. Az adatok figyeléséhez olyan részletek állnak rendelkezésre, mint például, hogy az adatfogyasztók elfogadták-e az adatmegosztásra szóló meghívást, valamint hogy létrehoztak-e megosztási előfizetést, és elkezdték-e használni az ön adatait. 

Adatfogyasztóként figyelheti az Azure-előfizetésében aktivált pillanatképeket. 

## <a name="monitor-invitation-status"></a>Meghívó állapotának figyelése

Tekintse meg az adatmegosztási meghívók állapotát az Elküldött megosztások –meghívók > elemre navigálva. 

![Meghívó állapota](./media/invitation-status.png "Meghívó állapota") 

Három állam, hogy a meghíváslehet:

* Függőben – Az adatmegosztás címzettje még nem fogadta el a meghívást.
* Elfogadva – Az adatmegosztás címzettje elfogadta a meghívást.
* Elutasítva – Az adatmegosztás címzettje elutasította a meghívást.

> [!IMPORTANT]
> Ha a meghívást a már elfogadott hívás után törli, az nem egyenértékű a hozzáférés visszavonásával. Ha le szeretné állítani, hogy a jövőbeli pillanatképek átmásolja az adatfogyasztói tárfiókba, vissza kell vonnia a hozzáférést az *Előfizetések megosztása* lapon keresztül. 

## <a name="monitor-share-subscriptions"></a>Megosztási előfizetések figyelése

Tekintse meg a megosztási előfizetések állapotát az Elküldött megosztások -> megosztási előfizetések elemre navigálva. Ez a meghívó elfogadása után az adatfogyasztók által létrehozott aktív előfizetésekkel kapcsolatos részleteket tartalmazza. Az adatfogyasztó jövőbeli frissítéseit leállíthatja, ha kiválasztja a megosztási előfizetést, és a *Visszavonás lehetőséget választja.* 

## <a name="snapshot-history"></a>Pillanatfelvétel előzményei 

Az előzmények lapon megtekintheti az adatfogyasztó bérlőjének átmásolt pillanatképeket. Az egyes pillanatkép-időközök gyakoriságát és időtartamát figyelheti. 

![Pillanatfelvétel előzményei](./media/sent-shares.png "Pillanatfelvétel előzményei") 

Az egyes pillanatképek futtatásával kapcsolatos további részleteket a futtatás kezdő dátumára kattintva tekintheti meg. 

Alapértelmezés szerint legfeljebb 30 nap nyipillanatkép-előzmények jelennek meg. Ha több mint 30 napnyi előzményt szeretne látni, keresse meg a Figyelés -> diagnosztikai beállítások at, és válassza **a Diagnosztikai beállítás hozzáadása lehetőséget.** Ki kell választania egy tárfiókot a naplók tárolásához. 

![Pillanatfelvétel előzményei](./media/diagnostic-settings.png "Diagnosztikai beállítások") 

## <a name="next-steps"></a>Következő lépések 

További információ az [Azure Data Share terminológiájáról](terminology.md)