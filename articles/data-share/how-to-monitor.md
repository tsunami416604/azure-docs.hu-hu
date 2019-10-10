---
title: Az Azure-beli adatmegosztás előzetes verziójának figyelése
description: Megtudhatja, hogyan figyelheti a meghívások állapotát, megoszthatja az előfizetéseket és a pillanatkép-előzményeket az Azure adatmegosztás
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 22c95f1ac541e1288494ed85cc9654d42780ea60
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169126"
---
# <a name="monitor-azure-data-share-preview"></a>Az Azure-beli adatmegosztás előzetes verziójának figyelése 

Ez a cikk azt ismerteti, hogyan figyelhetők meg az adatmegosztások az Azure-beli adatmegosztás előzetes verziójával. Adatszolgáltatóként az adatmegosztási kapcsolatok különböző szempontjait figyelheti. A részleteket, például azt, hogy az adatok fogyasztói elfogadták-e a meghívót az adatmegosztásra, valamint hogy létrehozott-e megosztási előfizetést, és megkezdte-e az adatok használatát a figyeléshez. 

Adatfogyasztóként nyomon követheti az Azure-előfizetésében aktivált pillanatképeket. 

## <a name="monitor-invitation-status"></a>Meghívási állapot figyelése

Megtekintheti az adatmegosztási meghívások állapotát, ha az elküldött megosztások – > meghívások pontra navigál. 

![Meghívás állapotának](./media/invitation-status.png "Meghívási állapota") 

A meghívónak három állapota lehet:

* Függőben – az adatmegosztási címzett még nem fogadta el a meghívót.
* Elfogadás – az adatmegosztási címzett elfogadta a meghívót.
* Elutasítva – az adatmegosztási címzett elutasította a meghívást.

> [!IMPORTANT]
> Ha töröl egy meghívót a már elfogadását követően, az nem felel meg a hozzáférés visszavonásának. Ha azt szeretné, hogy a jövőbeli Pillanatképek ne legyenek átmásolva az adatfogyasztók tárolási fiókjába, vissza kell vonnia a hozzáférést a *megosztás-előfizetések* lapon. 

## <a name="monitor-share-subscriptions"></a>Megosztási előfizetések figyelése

Megtekintheti a megosztási előfizetések állapotát, ha az eljuttatott megosztások – > megosztási előfizetések – gombra kattint. Ezzel a meghívót elfogadva megtekintheti az adatfogyasztók által létrehozott aktív előfizetésekre vonatkozó adatokat. Az adatfogyasztó jövőbeli frissítéseit az előfizetés megosztása és a *Visszavonás*lehetőség kiválasztásával állíthatja le. 

## <a name="snapshot-history"></a>Pillanatkép-előzmények 

Az Előzmények lapon megtekintheti azokat a pillanatképeket, amelyeket a rendszer az adatfogyasztó bérlője számára másolt. Nyomon követheti az egyes Pillanatképek intervallumának gyakoriságát és időtartamát. 

![Pillanatkép-előzmények](./media/sent-shares.png "Pillanatkép-előzményei") 

A pillanatképek futtatásával kapcsolatos további részletekért tekintse meg a Futtatás kezdő dátumra kattintva. 

Alapértelmezés szerint a pillanatképek előzményeinek akár 30 napja is megjelenik. Ha több mint 30 nappal korábbi előzményekre van szüksége, navigáljon a figyelés – > diagnosztikai beállítások elemre, és válassza a **diagnosztikai beállítás hozzáadása**lehetőséget. A naplók a-ba való tárolásához ki kell választania egy Storage-fiókot. 

![Pillanatkép-előzmények](./media/diagnostic-settings.png "diagnosztikai beállításai") 

## <a name="next-steps"></a>Következő lépések 

További információ az [Azure adatmegosztási terminológiáról](terminology.md)