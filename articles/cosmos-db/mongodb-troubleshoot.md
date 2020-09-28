---
title: A Mongo adatbázis Azure Cosmos DB API-jával kapcsolatos gyakori hibák elhárítása
description: Ez a dokumentum a Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárításának módszereit tárgyalja.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: jasonh
ms.openlocfilehash: 27a9c7eb48c4a0148401c0d146a50a5197593806
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409629"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Az Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárítása

A következő cikk a MongoDB Azure Cosmos DB API-t használó adatbázisok gyakori hibáit és megoldásait ismerteti.

>[!Note]
> Azure Cosmos DB nem üzemelteti a MongoDB motort. Lehetővé teszi a 3,6-es MongoDB- [protokoll](mongodb-feature-support-36.md) és a 3,2-es [verzió](mongodb-feature-support.md)örökölt támogatásának megvalósítását, ezért a hibák némelyike csak a Azure Cosmos db API-ban található a MongoDB-ben. 

## <a name="common-errors-and-solutions"></a>Gyakori hibák és megoldások

| Hiba               | Code  | Leírás  | Megoldás  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | A kérés túllépte a 60 másodperces végrehajtási időkorlátot. | Ennek a hibának számos oka lehet. Ennek egyik oka az lehet, hogy az aktuális lefoglalt kérelemegységek kapacitása nem elegendő a kérelem teljesítéséhez. Erre az adott gyűjtemény vagy adatbázis kérelemegységeinek növelése nyújthat megoldást. Más esetekben ez a hiba a nagyméretű kérések kisebbekre bontásával is feldolgozható. |
| TooManyRequests     | 16500 | A felhasznált kérelemegységek teljes száma nagyobb, mint a gyűjteményhez kiosztott kérelemegységek száma, ezért szabályozva lett. | Érdemes lehet skálázni az adott tárolóhoz vagy tárolókészlethez megadott átviteli sebességet az Azure portálon, vagy újból elvégezni a műveletet. |
| ExceededMemoryLimit | 16501 | Több-bérlős szolgáltatásként a művelet túllépte az ügyfél memóriájának kiosztását. | Csökkentse a művelet hatókörét szigorúbb lekérdezési feltételekkel, vagy forduljon a [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)támogatási szolgálatához. Például: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| A megadott rendezési elemnek megfelelő index elérési útja ki van zárva / A rendezett lekérdezés nem rendelkezik a végrehajtásához szükséges megfelelő összetett indexszel. | 2 | A lekérdezés nem indexelt mezőre kér rendezést. | Hozzon létre egy egyező indexet (vagy kompozit indexet) a megkísérelt rendezési lekérdezéshez. |
| A MongoDB vonalprotokoll-verziójával kapcsolatos problémák | - | A MongoDB-illesztőprogramok régebbi verziói nem tudják felderíteni az Azure Cosmos-fiók nevét a kapcsolódási karakterláncokban. | Fűzze hozzá a *appName = @**accountName** @ * a Cosmos db API-ját a MongoDB-kapcsolatok karakterláncához, ahol a ***accountName*** a Cosmos db fiók neve. |

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.

