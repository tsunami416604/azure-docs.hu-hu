---
title: Azure fájlmegosztás Adatelőnézet figyelése
description: Azure fájlmegosztás Adatelőnézet figyelése
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 869c1ed41d7f78df184461bc1d8cab6c6eb8d426
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789097"
---
# <a name="monitor-azure-data-share-preview"></a>A figyelő az Azure Data megosztás előzetes verzió 

Ez a cikk bemutatja, hogyan figyelheti az Azure Data megosztás előzetes verziójának adatmegosztások. Adatszolgáltató, mint Ön figyelheti az adatmegosztási kapcsolatok üzemeltetés minőségének különböző szempontjait. Például hogy az adatfelhasználók fogadták el a meghívást, az adatok megosztása, valamint hogy rendelkezik egy megosztás az előfizetés létrehozása és használatba az adatok érhetők el az összes figyeléséhez. 

Vásárlói adatok a pillanatképek lett kiváltó figyelheti az Azure-előfizetésben helyezi. 

## <a name="monitor-invitation-status"></a>A figyelő meghívás állapota

Az adatok megosztási meghívókat az elküldött megosztások állapotának megtekintése -> meghívók. 

![Meghívás állapota](./media/invitation-status.png "meghívás állapota") 

Nincsenek a meghívást, három állapota:

* Függőben lévő - adatok megosztása címzett van még nem fogadta el a meghívást.
* Elfogadva – adatok megosztás címzett elfogadta a meghívást.
* Elutasított - adatok megosztása címzett elutasította a meghívót.

> [!IMPORTANT]
> Ha már elfogadták után törli egy meghívást arra, azt, nem egyenértékű, visszavonja az elérését. Állítsa le az adatokat a fogyasztók tárfiókba történő másolásának jövőbeli pillanatképeket, szeretné, ha vissza kell vonnia elérés a *előfizetések megosztása* lapon. 

## <a name="monitor-share-subscriptions"></a>A figyelő megosztás előfizetések

Az elküldött megosztások megosztás előfizetés állapotának megtekintése -> előfizetések megosztani. Ekkor kap a meghívás elfogadása után az adatfelhasználók által létrehozott aktív előfizetések részleteit. Az adatfogyasztó jövőbeli frissítései a megosztás előfizetés kiválasztásával leállíthatja *visszavonása*. 

## <a name="snapshot-history"></a>Pillanatkép-előzmények 

Az Előzmények lapon megtekintheti a pillanatképek erőforrástármegosztásba megtörtént-e a bérlő az adatfogyasztó áll. Ön figyelheti a gyakorisága és időtartama minden pillanatkép-készítési időköz. 

![Pillanatkép-előzmények](./media/sent-shares.png "pillanatkép-előzmények") 

Megtekintheti, hogy minden egyes futtatásához kattintson a Futtatás kezdő időpontjától pillanatkép kapcsolatos további részletekért. 

## <a name="next-steps"></a>További lépések 

Tudjon meg többet [az adatok megosztása az Azure-terminológia](terminology.md)