---
title: A Mongo adatbázis Azure Cosmos DB API-jával kapcsolatos gyakori hibák elhárítása
description: Ez a dokumentum a Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárításának módszereit tárgyalja.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: lbosq
ms.openlocfilehash: f75374fc88923a0f131d513bebf0ffe1feeca359
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076774"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Az Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárítása

A következő cikk a MongoDB Azure Cosmos DB API-t használó adatbázisok gyakori hibáit és megoldásait ismerteti.

>[!Note]
> Azure Cosmos DB nem üzemelteti a MongoDB motort. Lehetővé teszi a 3,6-es MongoDB- [protokoll](mongodb-feature-support-36.md) és a 3,2-es [verzió](mongodb-feature-support.md)örökölt támogatásának megvalósítását, ezért a hibák némelyike csak a Azure Cosmos db API-ban található a MongoDB-ben. 

## <a name="common-errors-and-solutions"></a>Gyakori hibák és megoldások

| Hiba               | Code  | Description  | Megoldás  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | A kérelem túllépte a végrehajtás 60 másodperces időtúllépését. | Ennek a hibának számos oka lehet. Az egyik oka az, hogy a jelenlegi lefoglalt kérési egység kapacitása nem elegendő a kérelem teljesítéséhez. Ez a gyűjtemény vagy az adatbázis kérési egységének növelésével oldható meg. Más esetekben ez a hiba a nagyméretű kérések kisebbekre bontásával is feldolgozható. |
| TooManyRequests     | 16500 | A felhasználható kérelmek teljes száma nagyobb, mint a gyűjteményre kiépített kérelem-egység aránya, és a szabályozás megtörtént. | Érdemes lehet egy tárolóhoz vagy tárolóhoz rendelt átviteli sebességet méretezni a Azure Portal, vagy megismételni a műveletet. |
| ExceededMemoryLimit | 16501 | Több-bérlős szolgáltatásként a művelet túllépte az ügyfél memóriájának kiosztását. | Csökkentse a művelet hatókörét szigorúbb lekérdezési feltételekkel, vagy forduljon a [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)támogatási szolgálatához. Például: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| A megadott Order-by elemnek megfelelő index elérési útja ki van zárva/a lekérdezési sorrend nem rendelkezik a megfelelő összetett indexszel, amelyből a szolgáltatás kiszolgálható. | 2 | A lekérdezés nem indexelt mezőre vonatkozó rendezést kér. | Hozzon létre egy egyező indexet (vagy kompozit indexet) a megkísérelt rendezési lekérdezéshez. |
| MongoDB vonalprotokoll-verzióval kapcsolatos problémák | - | A MongoDB-illesztőprogramok régebbi verziói nem tudják felderíteni az Azure Cosmos-fiók nevét a kapcsolódási karakterláncokban. | Fűzze hozzá a *appName = @**accountName** @ * a Cosmos db API-ját a MongoDB-kapcsolatok karakterláncához, ahol a ***accountName*** a Cosmos db fiók neve. |

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.

