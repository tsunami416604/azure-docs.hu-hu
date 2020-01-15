---
title: A Mongo adatbázis Azure Cosmos DB API-jával kapcsolatos gyakori hibák elhárítása
description: Ez a dokumentum a Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárításának módszereit tárgyalja.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941849"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Az Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárítása

Azure Cosmos DB a Common NoSQL-adatbázisok (például a MongoDB) átviteli protokolljait implementálja. A huzal protokoll implementációja miatt transzparens módon használhatja a Azure Cosmos DBeket a NoSQL-adatbázisokkal működő meglévő ügyféloldali SDK-k, illesztőprogramok és eszközök használatával. A Azure Cosmos DB nem használja az adatbázisok egyik forráskódját sem a NoSQL-adatbázisok vezeték nélküli API-jai biztosításához. Minden olyan MongoDB-ügyfél-illesztőprogram, amely megérti, hogy a hálózati protokollok verziója csatlakozni tud a Azure Cosmos DBhoz.

Habár Azure Cosmos DB API-MongoDB kompatibilis a MongoDB átviteli protokolljának 3,2-es verziójával (az 3,4-es verzióban hozzáadott lekérdezési operátorok és szolgáltatások jelenleg előzetes verzióként érhetők el), vannak olyan egyéni hibakódok, amelyek megfelelnek Azure Cosmos DB adott hibák. Ez a cikk különböző hibákat, hibakódokat és a hibák elhárításának lépéseit ismerteti.

## <a name="common-errors-and-solutions"></a>Gyakori hibák és megoldások

| Hiba               | Kód  | Leírás  | Megoldás  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | A felhasználható kérelmek teljes száma nagyobb, mint a gyűjteményre kiépített kérelem-egység aránya, és a szabályozás megtörtént. | Érdemes lehet egy tárolóhoz vagy tárolóhoz rendelt átviteli sebességet méretezni a Azure Portal, vagy megismételni a műveletet. |
| ExceededMemoryLimit | 16501 | Több-bérlős szolgáltatásként a művelet túllépte az ügyfél memóriájának kiosztását. | Csökkentse a művelet hatókörét szigorúbb lekérdezési feltételekkel, vagy forduljon a [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)támogatási szolgálatához. Például: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| A megadott Order-by elemnek megfelelő index elérési útja ki van zárva/a lekérdezési sorrend nem rendelkezik a megfelelő összetett indexszel, amelyből a szolgáltatás kiszolgálható. | 2 | A lekérdezés nem indexelt mezőre vonatkozó rendezést kér. | Hozzon létre egy egyező indexet (vagy kompozit indexet) a megkísérelt rendezési lekérdezéshez. |
| MongoDB kapcsolatos problémák | - | A MongoDB-illesztőprogramok régebbi verziói nem tudják felderíteni az Azure Cosmos-fiók nevét a kapcsolódási karakterláncokban. | Fűzze hozzá a *appName = @**accountName**@* a Cosmos db API-ját a MongoDB-kapcsolatok karakterláncához, ahol a ***accountName*** a Cosmos db fióknév. |


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.

