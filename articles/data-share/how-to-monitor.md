---
title: Az Azure-beli adatmegosztás figyelése
description: Megtudhatja, hogyan figyelheti a Meghívási állapotot, megoszthatja az előfizetéseket és a pillanatkép-előzményeket
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 2a986afa1916d179834c516a26f76a2789f82452
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511887"
---
# <a name="monitor-azure-data-share"></a>Az Azure Data Share monitorozása  

Ez a cikk azt ismerteti, hogyan figyelhetők meg az adatmegosztások az Azure-adatmegosztás használatával. Adatszolgáltatóként az adatmegosztási kapcsolatok különböző szempontjait figyelheti. A részleteket, például azt, hogy az adatok fogyasztói elfogadták-e a meghívót az adatmegosztásra, valamint hogy létrehozott-e megosztási előfizetést, és megkezdte-e az adatok használatát a figyeléshez. 

Adatfogyasztóként nyomon követheti az Azure-előfizetésében aktivált pillanatképeket. 

## <a name="monitor-invitation-status"></a>Meghívási állapot figyelése

Megtekintheti az adatmegosztási meghívások állapotát, ha az elküldött megosztások – > meghívások pontra navigál. 

![Meghívás állapota](./media/invitation-status.png "Meghívás állapota") 

A meghívónak három állapota lehet:

* Függőben – az adatmegosztási címzett még nem fogadta el a meghívót.
* Elfogadás – az adatmegosztási címzett elfogadta a meghívót.
* Elutasítva – az adatmegosztási címzett elutasította a meghívást.

> [!IMPORTANT]
> Ha töröl egy meghívót a már elfogadását követően, az nem felel meg a hozzáférés visszavonásának. Ha azt szeretné, hogy a jövőbeli Pillanatképek ne legyenek átmásolva az adatfogyasztók tárolási fiókjába, vissza kell vonnia a hozzáférést a *megosztás-előfizetések* lapon. 

## <a name="monitor-share-subscriptions"></a>Megosztási előfizetések figyelése

Megtekintheti a megosztási előfizetések állapotát, ha az eljuttatott megosztások – > megosztási előfizetések – gombra kattint. Ezzel a meghívót elfogadva megtekintheti az adatfogyasztók által létrehozott aktív előfizetésekre vonatkozó adatokat. Az adatfogyasztó jövőbeli frissítéseit az előfizetés megosztása és a *Visszavonás*lehetőség kiválasztásával állíthatja le. 

## <a name="snapshot-history"></a>Pillanatkép-előzmények 

Egy megosztás **Előzmények** lapján megtekintheti, hogy mikor másolja az adatok az adatszolgáltatóról az adatfogyasztó adattárára. Lehetősége van az egyes Pillanatképek gyakoriságának, időtartamának és állapotának figyelésére. 

![Pillanatkép-előzmények](./media/sent-shares.png "Pillanatkép-előzmények") 

A pillanatképek futtatásával kapcsolatos további részletekért tekintse meg a Futtatás kezdő dátumra kattintva. Ezután az egyes adatkészletek állapotára kattintva megtekintheti az átvitt adatok mennyiségét, a másolt fájlok/rekordok számát, a pillanatkép időtartamát, a felhasznált virtuális mag számát és a hibaüzenetet, ha van ilyen. 

A pillanatképek előzményeinek akár 30 napja is megjelenik. Ha mentenie kell, és több mint 30 nappal korábbi előzményeket szeretne látni, használhatja a diagnosztikai beállításokat.

## <a name="diagnostic-setting"></a>Diagnosztikai beállítás

A diagnosztikai beállításokat a naplófájlok és az események mentéséhez is konfigurálhatja. Navigáljon a figyelés – > diagnosztikai beállítások elemre, és válassza a **diagnosztikai beállítás hozzáadása**lehetőséget. Válassza ki a naplózni kívánt vagy az Önt érdeklő eseményeket, és hogy hol szeretné tárolni vagy elküldeni azokat. 

![Pillanatkép-előzmények](./media/diagnostic-settings.png "Diagnosztikai beállítások") 

## <a name="next-steps"></a>Következő lépések 

További információ az [Azure adatmegosztási terminológiáról](terminology.md)