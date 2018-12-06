---
title: Az Azure Cosmos DB csatlakoztatása a Qlik Sense és -adatok megjelenítése
description: Ez a cikk az Azure Cosmos DB-hez csatlakozik a Qlik Sense, és az adatok megjelenítése a szükséges lépéseket ismerteti.
services: cosmos-db
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 57832b59d8b43f5aa157b278e67c50b25084c86c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52962835"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Az Azure Cosmos DB csatlakoztatása a Qlik Sense és -adatok megjelenítése

Qlik Sense egy adatvizualizációs eszköz, amely a különböző forrásokból származó adatok egyesít egyetlen nézetben. Qlik Sense indexeli minden lehetséges kapcsolat az adatokban, úgy, hogy az adatok azonnali elemzéseket is kaphat. Azure Cosmos DB-adatai Qlik Sense használatával jelenítheti meg. Ez a cikk az Azure Cosmos DB-hez csatlakozik a Qlik Sense, és az adatok megjelenítése a szükséges lépéseket ismerteti. 

> [!NOTE]
> Az Azure Cosmos DB SQL API és a MongoDB API-fiókok csak a Qlik Sense csatlakozik az Azure Cosmos DB jelenleg támogatott.

Az Azure Cosmos DB-hez való csatlakozás Qlik Sense a következőket teheti:

* Cosmos DB SQL API az ODBC-összekötő használatával.

* Cosmos DB MongoDB API-összekötővel a Qlik Sense MongoDB (jelenleg előzetes verzióban érhető el).

* Cosmos DB MongoDB API és az SQL API-t, a Qlik Sense REST API-összekötő használatával.

* Cosmos DB Mongo DB API-összekötővel a gRPC a Qlik Core.
Ez a cikk ismerteti az ODBC-összekötő használatával a Cosmos DB SQL API-t kapcsolódás részleteit.

Ez a cikk ismerteti az ODBC-összekötő használatával a Cosmos DB SQL API-t kapcsolódás részleteit.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt Ez a cikk utasításait követve ellenőrizze, hogy készen áll a következő erőforrások:

* Töltse le a [Qlik Sense asztali](https://www.qlik.com/us/try-or-buy/download-qlik-sense) , vagy hozzon létre az Azure-ban, a Qlik Sense [telepítése a Qlik Sense Piactéri elem](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Töltse le a [videojátékok adatok](https://www.kaggle.com/gregorut/videogamesales), a CSV formátumban van ezekkel a mintaadatokkal. Ön az adatok tárolásához a Cosmos DB-fiókban, és Qlik Sense megjelenítheti.

* Hozzon létre egy Azure Cosmos DB SQL API-fiókot az ismertetett lépéseket követve [hozzon létre egy fiókot](create-sql-api-dotnet.md#create-a-database-account) a rövid útmutató című szakaszban.

* [Hozzon létre egy adatbázis és gyűjtemény](create-sql-api-dotnet.md#add-a-collection) –, használati érték között állítható be a gyűjtemény átviteli 1000 RU/s. 

* A videó játék értékesítési mintaadatok betöltése a Cosmos DB-fiók. Azure Cosmos DB adatáttelepítési eszköz használatával importálhatja az adatokat, megteheti egy [szekvenciális](import-data.md#SQLSeqTarget) vagy egy [tömeges importálás](import-data.md#SQLBulkTarget) az adatok. Az adatok importálása a Cosmos DB-fiók körülbelül 3 – 5 percet vesz igénybe.

* Letöltése, telepítése és konfigurálása az ODBC-illesztő a lépések használatával a [Cosmos DB kapcsolódhat ODBC-illesztő](odbc-driver.md) cikk. A videojátékok adatok egy egyszerű adatkészletet, és szerkessze a sémát, használja az alapértelmezett gyűjtési-leképezési sémában nem kell.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Csatlakozás a Cosmos DB Qlik Sense

1. Nyissa meg a Qlik Sense és **új alkalmazás létrehozása**. Nevezze el az alkalmazást, és válassza **létrehozás**.

   ![Új Qlik Sense-alkalmazás létrehozása](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. Az új alkalmazás sikeres létrehozása után válassza ki a **alkalmazás megnyitása** válassza **fájlok és egyéb forrásokból származó adatok hozzáadása**. 

3. Válassza ki az adatforrásokat, **ODBC** az új kapcsolat beállítása ablak megnyitásához. 

4. Váltson **felhasználói DSN** , és válassza ki a korábban létrehozott ODBC-kapcsolat. Adjon meg egy nevet a kapcsolatot, és válasszon **létrehozás**. 

   ![Új kapcsolat létrehozása](./media/visualize-qlik-sense/create-new-connection.png)

5. Miután létrehozta a kapcsolatot, adja meg az adatbázis, gyűjtemény, ahol a videojátékok adatok, és ezután tekintse meg a lapot.

   ![Válassza ki az adatbázist és gyűjteményt](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Ezután válassza ki **adatok hozzáadása** Qlik Sense, az adatok betöltéséhez. Miután tölt be adatokat, a Qlik Sense, elemzések generálásához, és az adatok elemzését. Elemzések segítségével, vagy videojátékokkal játszom értékesítési felfedezése saját alkalmazásának létrehozását. Az alábbi képen látható 

   ![Adatok vizualizációja](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>Az ODBC kapcsolódáskor korlátozások 

A cosmos DB modellezve körül fejlesztői igényeinek illesztőprogramok séma nélküli elosztott adatbázis. Az ODBC-illesztő oszlopokat, az adattípusok és egyéb tulajdonságok célszámítógéppel sémával adatbázis szükséges. A szokásos SQL-lekérdezés vagy a DML-szintaxis és relációs funkció nem alkalmazható Cosmos DB SQL API-hoz, mert az SQL API-t nem ANSI SQL. Ezen okból az ODBC-illesztő keresztül kiadott SQL-utasítások fordítja, amely nem rendelkezik az összes szerkezeteket megfelelőit Cosmos DB-specifikus SQL-szintaxis. A fordítási hibák megelőzése érdekében telepítenie kell egy sémát az ODBC-kapcsolat beállítása során. A [ODBC-illesztő összekapcsolása](odbc-driver.md) cikk biztosít vonatkozó javaslatokat és módszerek segítségével konfigurálhatja a sémát. Ellenőrizze, hogy ez a leképezés a Cosmos DB-fiókban lévő minden adatbázis és gyűjtemény létrehozása.

## <a name="next-steps"></a>További lépések

Egy másik vizualizációs eszközeként, mint a Power BI használatakor is kapcsolódik hozzá a következő dokumentum utasításai szerint:

* [Cosmos DB-adatok megjelenítése Power BI-összekötő használatával](powerbi-visualize.md)
