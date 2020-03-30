---
title: Gyakori hibák elhárítása az Azure Cosmos DB Mongo DB API-jában
description: Ez a dokumentum ismerteti, hogyan hárítsa el az Azure Cosmos DB MongoDB API-jában észlelt gyakori problémák elhárításának módjait.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941849"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Gyakori problémák elhárítása az Azure Cosmos DB MongoDB API-jában

Az Azure Cosmos DB megvalósítja a közös NoSQL-adatbázisok, köztük a MongoDB vezetékes protokolljait. A vezetékprotokoll-implementáció nak köszönhetően a NoSQL-adatbázisokkal dolgozó meglévő ügyfél-SDK-k, illesztőprogramok és eszközök használatával átlátható módon kommunikálhat az Azure Cosmos DB-vel. Az Azure Cosmos DB nem használja az adatbázisok forráskódját a NoSQL-adatbázisok bármelyikéhez vezetékes kompatibilis API-k biztosításához. Bármely MongoDB ügyfél-illesztőprogram, amely ismeri a vezetékes protokoll verziók csatlakozhat nak az Azure Cosmos DB.

Bár az Azure Cosmos DB MongoDB-hoz készült API-ja kompatibilis a MongoDB vezetékes protokolljának 3.2-es verziójával (a 3.4-es verzióban hozzáadott lekérdezés-operátorok és funkciók jelenleg előzetes verzióként érhetők el), vannak olyan egyéni hibakódok, amelyek megfelelnek az Azure Cosmos DB-nek konkrét hibákat. Ez a cikk ismerteti a különböző hibákat, hibakódokat és a hibák elhárításának lépéseit.

## <a name="common-errors-and-solutions"></a>Gyakori hibák és megoldások

| Hiba               | Kód  | Leírás  | Megoldás  |
|---------------------|-------|--------------|-----------|
| TooManyKéri     | 16500 | A felhasznált kérelemegységek teljes száma meghaladja a gyűjtemény kiépített kérelemegység-díját, és a csomagban szabályozták. | Fontolja meg a tárolóhoz vagy tárolók készletéhez rendelt átviteli kábel méretezését az Azure Portalról, vagy próbálkozzon újra a művelettel. |
| ExceededMemoryLimit | 16501 | Több-bérlős szolgáltatásként a művelet átment az ügyfél memóriaallokációja felett. | Csökkentse a művelet hatókörét szigorúbb lekérdezési feltételekkel vagy az [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)támogatási szolgálatával. Például: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| A megadott rendelési elemnek megfelelő indexelérési út kizárt / A lekérdezés i sorrendje nem rendelkezik megfelelő összetett indexszel, amelyből kiszolgálható. | 2 | A lekérdezés nem indexelt mezőre kér rendezést. | Hozzon létre egy megfelelő indexet (vagy összetett indexet) a megkísérelt rendezési lekérdezéshez. |
| MongoDB vonalprotokoll-verzióval kapcsolatos problémák | - | A MongoDB-illesztőprogramok régebbi verziói nem észlelik az Azure Cosmos-fiók nevét a kapcsolati karakterláncokban. | Hozzáfűzés *appName=@**accountName** @ * a Cosmos DB API-jának végén a MongoDB kapcsolati karakterlánchoz, ahol az ***accountName*** a Cosmos DB-fiók neve. |


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használhatja a [Studio 3T-t](mongodb-mongochef.md) az Azure Cosmos DB MongoDB-hoz való API-jával.
- Ismerje meg, hogyan használhatja a [Robo 3T-t](mongodb-robomongo.md) az Azure Cosmos DB MongoDB-hoz való API-jával.
- Fedezze fel a [MongoDB-mintákat](mongodb-samples.md) az Azure Cosmos DB MongoDB-hoz kapcsolódó API-jával.

