---
title: Az Azure Cosmos DB API-ban gyakran előforduló hibák elhárítása a Mongo DB-hez készült
description: Ezt a dokumentumot a módon történt az Azure Cosmos DB API a mongodb-hez gyakori problémáinak elhárítását ismerteti.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 5b3d3993a497240c1ea18f0fcf852c0e834f6e79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735706"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>A mongodb-hez az Azure Cosmos DB API-t kapcsolódás gyakori problémáinak elhárítása

Az Azure Cosmos DB valósítja meg a közös NoSQL-adatbázisok, például a MongoDB átviteli protokoll. Az átviteli protokoll megvalósítása miatt transzparens módon használhatja az Azure Cosmos DB használatával, a meglévő ügyfél-SDK-k, illesztőprogramok és eszközök, amelyek együttműködnek a NoSQL-adatbázisok. Az Azure Cosmos DB nem használja az adatbázisok forrás kód bármely, a NoSQL-adatbázisok, amelyek biztosítják az átviteli-kompatibilis API-k. Bármely MongoDB ügyfélen lévő meghajtó, amely együttműködik az átviteli protokoll verziója képes csatlakozni az Azure Cosmos DB.

Noha az Azure Cosmos DB MongoDB API-kompatibilis a MongoDB átviteli protokoll (a lekérdezési operátor és a szolgáltatások 3.4-es verziójában hozzáadva olyan jelenleg előzetes verzióban elérhető) 3.2-es verzióját, van néhány egyéni hibakódok, melyek megfelelnek az Azure Cosmos DB hibaüzenetek. Ez a cikk ismerteti a különféle hibák, hibakódok és a lépések ezeket a hibákat.

## <a name="common-errors-and-solutions"></a>Gyakori hibák és megoldások

| Hiba               | Kód  | Leírás  | Megoldás  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Felhasznált kérelemegységek teljes száma nagyobb, mint a gyűjteményben kiosztott kérelemegység díjaival, és megfelelően szabályozva lett. | Az átviteli sebességet egy tárolót vagy tárolók az Azure Portalról hozzárendelt megfontolni vagy is próbálja megismételni a műveletet. |
| ExceededMemoryLimit | 16501 | A művelet több-bérlős szolgáltatás, az ügyfél memória kiosztása feletti csökkent. | Csökkentse a szigorúbb lekérdezési feltételek használatával művelet hatókörének, vagy forduljon az ügyfélszolgálathoz a a [az Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Például: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| A MongoDB átviteli verzió kapcsolatos problémák | - | MongoDB-illesztőprogramok régebbi verziói nem tudja észlelni az Azure Cosmos-fiók neve a kapcsolati karakterláncokban lévő. | Hozzáfűző *appName = @**accountName**@* végén található a Cosmos DB API a MongoDB kapcsolati karakterláncot, ahol ***accountName*** a Cosmos DB-fiók neve . |


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [a Studio 3T használata](mongodb-mongochef.md) az Azure Cosmos DB API a mongodb-hez.
- Ismerje meg, hogyan [Robo 3T használata](mongodb-robomongo.md) az Azure Cosmos DB API a mongodb-hez.
- Ismerkedés a MongoDB [minták](mongodb-samples.md) az Azure Cosmos DB API a mongodb-hez.

