---
title: Azure Cosmos DB adatok biztonsági másolatból való visszaállítása
description: Ez a cikk azt ismerteti, hogyan lehet visszaállítani az adatok biztonsági másolatból történő Azure Cosmos DB visszaállítását, az Azure-támogatással való kapcsolatfelvételhez pedig az adatok visszaállításához szükséges lépéseket.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1a0075f9b4fc3ff919d4db4bd440a5435d711c83
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261732"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Adatok visszaállítása biztonsági másolatból Azure Cosmos DB 

Ha véletlenül törli az adatbázist vagy egy tárolót, [egy támogatási jegyet]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) is megadhat, vagy az [Azure-támogatás meghívásával]( https://azure.microsoft.com/support/options/) visszaállíthatja az adatokat az automatikus online biztonsági mentésből. Az Azure-támogatás csak olyan kiválasztott csomagokhoz érhető el, mint a **standard**, a **fejlesztői**és a magasabb szintű csomagok. Az Azure-támogatás nem érhető el **alapszintű** csomaggal. A különböző támogatási csomagokról az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/) oldalon tájékozódhat. 

A biztonsági mentés egy adott pillanatképének visszaállításához a Azure Cosmos DB megköveteli, hogy az adatok elérhetők legyenek a pillanatkép biztonsági mentési ciklusának időtartamára.

## <a name="request-a-restore"></a>Visszaállítás kérése

A visszaállítás kérelmezése előtt a következő adatokat kell megkapnia:

* Készítse elő az előfizetés-AZONOSÍTÓját.

* Az adatok véletlen törlése vagy módosítása alapján készüljön fel további információkra. Javasoljuk, hogy a rendelkezésre álló információk alapján csökkentse a háttér-és a korlátot, amely bizonyos időérzékeny esetekben hátrányos lehet.

* Ha a teljes Azure Cosmos DB fiókot törli, meg kell adnia a Törölt fiók nevét. Ha egy olyan fiókot hoz létre, amelynek a neve megegyezik a Törölt fiók nevével, ossza meg a támogatási csapattal, mert ez segít meghatározni a megfelelő fiókot. Javasoljuk, hogy minden egyes törölt fiókhoz különböző támogatási jegyeket kelljen benyújtani, mivel ez a művelet lekicsinyíti a visszaállítás állapotának összekeveredését.

* Ha egy vagy több adatbázis törölve van, meg kell adnia az Azure Cosmos-fiókot, valamint az Azure Cosmos-adatbázis nevét, és meg kell adnia, hogy létezik-e új adatbázis ugyanazzal a névvel.

* Ha egy vagy több tároló törölve van, adja meg az Azure Cosmos-fiók nevét, az adatbázis nevét és a tároló nevét. És adja meg, hogy létezik-e azonos nevű tároló.

* Ha véletlenül törölte vagy megsérült az adatai, akkor 8 órán belül kapcsolatba kell lépnie az [Azure támogatási szolgálatával](https://azure.microsoft.com/support/options/) , hogy a Azure Cosmos db csapat segítséget nyújtson a biztonsági másolatokból származó adatok visszaállításához.
  
  * Ha véletlenül törölte az adatbázist vagy a tárolót, nyisson meg egy "B" vagy "C" számú Azure-támogatási esetet. 
  * Ha véletlenül törölt vagy megsérült néhány dokumentumot a tárolón belül, nyisson meg egy-egy-egy támogatási esetet. 

Ha az adatsérülés történik, és ha egy tárolóban lévő dokumentumokat módosítanak vagy törölnek, **a lehető leghamarabb törölje a tárolót**. A tároló törlésével elkerülhető Azure Cosmos DB a biztonsági mentések felülírásával. Ha valamilyen okból kifolyólag a törlés nem lehetséges, a lehető leghamarabb be kell mutatnia a jegyet. Az Azure Cosmos-fiók neve, az adatbázis neve és a tároló neve mellett adja meg azt az időpontot is, ameddig az adatok visszaállíthatók. Fontos, hogy a lehető legpontosabban lehessen megállapítani az elérhető legjobb biztonsági mentéseket. Azt is fontos, hogy az időpontot UTC szerint határozza meg. 

Az alábbi képernyőfelvétel azt szemlélteti, hogyan lehet támogatási kérést létrehozni egy tárolóhoz (gyűjtemény/gráf/tábla) az adatAzure Portal használatával történő visszaállításához. Adjon meg további részleteket, például az adatok típusát, a visszaállítás célját, az adatok törlésének időpontját, hogy segítsen a kérés rangsorolásában.

:::image type="content" source="./media/how-to-backup-and-restore/backup-support-request-portal.png" alt-text="Biztonsági mentési támogatási kérelem létrehozása Azure Portal használatával":::

## <a name="post-restore-actions"></a>Visszaállítás utáni műveletek

Az adatvisszaállítás után értesítést kap az új fiók nevéről (általában a formátuma `<original-name>-restored1` ), valamint azt az időpontot, amikor a fiókot visszaállították. A visszaállított fióknak ugyanaz a kiosztott átviteli sebessége, az indexelési szabályzatok és az eredeti fiókkal azonos régióban kell lennie. Az előfizetés-rendszergazda vagy a rendszergazda láthatja a visszaállított fiókot.

Az adatok visszaállítása után ellenőrizze és ellenőrizze a visszaállított fiókban lévő adatok vizsgálatát, és győződjön meg róla, hogy a várt verziót tartalmazza. Ha minden jól látható, az adatátvitelt az eredeti fiókba [Azure Cosmos db módosítási hírcsatorna](change-feed.md) vagy [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)használatával kell visszatelepítenie.

Javasoljuk, hogy azonnal törölje a tárolót vagy az adatbázist az áttelepítés után. Ha nem törli a visszaállított adatbázisokat vagy tárolókat, a kérések egysége, a tárterület és a kimenő forgalom költségeit is felszámítjuk.

## <a name="next-steps"></a>További lépések

A következő cikkekből megtudhatja, hogyan telepítheti vissza az adatait az eredeti fiókjába:

* A visszaállítási kérelem elvégzéséhez forduljon az Azure ügyfélszolgálatához, és kérjen [jegyet a Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* Az adatAzure Cosmos DBba való áthelyezéshez [használja a Cosmos db módosítási csatornát](change-feed.md) .

* A [Azure Data Factory használatával](../data-factory/connector-azure-cosmos-db.md) viheti át az adatAzure Cosmos DBba.
