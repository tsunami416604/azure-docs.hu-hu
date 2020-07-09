---
title: A Azure Cosmos DB API-MongoDB kapcsolatos gyakori kérdések
description: Válaszok a Azure Cosmos DB API-MongoDB kapcsolatos gyakori kérdésekre
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: de75ea1bc0a1cf63c74be3f7d9e486e1fe38db6f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82614563"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>A Azure Cosmos DB API-MongoDB kapcsolatos gyakori kérdések

A MongoDB-hez készült Azure Cosmos DB API-protokoll kompatibilitási réteg, amely lehetővé teszi az alkalmazások számára, hogy a meglévő, Közösség által támogatott SDK-k és illesztőprogramok használatával könnyedén és transzparens módon kommunikáljanak a natív Azure Cosmos-adatbázis-motorral. A fejlesztők mostantól a meglévő MongoDB eszközlánccal és képességeiket is használhatják olyan alkalmazások készítéséhez, amelyek kihasználják a Azure Cosmos DB. A fejlesztők a Azure Cosmos DB egyedi képességeivel részesülnek, amelyek globális elosztást biztosítanak a több főkiszolgálós replikációval, az automatikus indexeléssel, a biztonsági mentési karbantartással, a pénzügyi támogatással rendelkező szolgáltatói szerződésekkel (SLA) stb.

## <a name="how-do-i-connect-to-my-database"></a>Hogyan csatlakozni az adatbázishoz?

A MongoDB Azure Cosmos DB API-jával a Cosmos-adatbázishoz való kapcsolódás leggyorsabb módja a [Azure Portal](https://portal.azure.com). Lépjen a fiókjába, majd a bal oldali navigációs menüben kattintson a **gyorskonfigurálás**elemre. A gyors üzembe helyezés a legjobb módszer az adatbázishoz való kapcsolódáshoz szükséges kódrészletek beszerzésére.

Azure Cosmos DB kényszeríti a szigorú biztonsági követelményeket és szabványokat. Azure Cosmos DB fiókoknak hitelesítésre és biztonságos kommunikációra van szükségük TLS-n keresztül, ezért mindenképpen a TLS 1.2-es verzióját használja.

További információ: a [Cosmos-adatbázishoz való kapcsolódás Azure Cosmos db API-MongoDB](connect-mongodb-account.md).

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Hibakódok Azure Cosmos DB API-MongoDB való használatakor?

A Common MongoDB-hibakódokkal együtt a Azure Cosmos DB API-ját a MongoDB saját specifikus hibakódokkal rendelkezik:

| Hiba               | Code  | Leírás  | Megoldás  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | A felhasználható kérelmek teljes száma nagyobb, mint a tárolóhoz kiépített kérelem-egység sebessége, és a rendszer szabályozott. | Érdemes lehet egy tárolóhoz rendelt átviteli sebességet, vagy a Azure Portal tárolók készletét vagy újrapróbálkozni. |
| ExceededMemoryLimit | 16501 | Több-bérlős szolgáltatásként a művelet túllépte az ügyfél memóriájának kiosztását. | Csökkentse a művelet hatókörét szigorúbb lekérdezési feltételekkel, vagy forduljon a [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)támogatási szolgálatához. <br><br> Például: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |

## <a name="supported-drivers"></a>Támogatott illesztőprogramok

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Támogatott-e a MongoDB-illesztőprogram a Azure Cosmos DB API-MongoDB való használatra?

Igen, használhatja a Simba Mongo ODBC-illesztőjét Azure Cosmos DB API-val a MongoDB

## <a name="next-steps"></a>További lépések

* [.NET-Webalkalmazás létrehozása a MongoDB-hez készült Azure Cosmos DB API-val](create-mongodb-dotnet.md)
* [Hozzon létre egy, a Java és a MongoDB API-t használó Console-alkalmazást Azure Cosmos DB](create-mongodb-java.md)
