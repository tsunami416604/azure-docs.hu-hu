---
title: Csatlakoztassa a Qlik Sense-t az Azure Cosmos DB-hez, és jelenítse meg az adatokat
description: Ez a cikk ismerteti az Azure Cosmos DB qlik sense és az adatok megjelenítéséhez szükséges lépéseket.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 3a955060eb5f19544860c1c97abe1577084bef24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67985545"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Csatlakoztassa a Qlik Sense-t az Azure Cosmos DB-hez, és jelenítse meg az adatokat

A Qlik Sense egy adatvizualizációs eszköz, amely a különböző forrásokból származó adatokat egyetlen nézetben egyesíti. A Qlik Sense indexeli az adatok minden lehetséges kapcsolatát, így azonnal betekintést nyerhet az adatokba. Az Azure Cosmos DB-adatokat a Qlik Sense használatával jelenítheti meg. Ez a cikk ismerteti az Azure Cosmos DB qlik sense és az adatok megjelenítéséhez szükséges lépéseket. 

> [!NOTE]
> A Qlik Sense csatlakoztatása az Azure Cosmos DB-hez jelenleg csak az SQL API és az Azure Cosmos DB API-ja csak MongoDB-fiókokhoz támogatott.

A Qlik Sense-t az Azure Cosmos DB-hez csatlakoztathatja a következőkkel:

* Cosmos DB SQL API az ODBC-összekötő használatával.

* Az Azure Cosmos DB MongoDB-hoz való API-ja a Qlik Sense MongoDB-összekötő használatával (jelenleg előzetes verzióban).

* Az Azure Cosmos DB API-ja a MongoDB-hoz és az SQL API-hoz a REST API-összekötő használatával a Qlik Sense-ben.

* Cosmos DB Mongo DB API a Qlik Core gRPC-csatlakozójának használatával.
Ez a cikk ismerteti a Cosmos DB SQL API-hoz az ODBC-összekötő használatával való csatlakozás részleteit.

Ez a cikk ismerteti a Cosmos DB SQL API-hoz az ODBC-összekötő használatával való csatlakozás részleteit.

## <a name="prerequisites"></a>Előfeltételek

A cikkben található utasítások bekövetése előtt győződjön meg arról, hogy a következő erőforrások készen állnak:

* Töltse le a [Qlik Sense Desktop-ot,](https://www.qlik.com/us/try-or-buy/download-qlik-sense) vagy állítsa be a Qlik Sense-t az Azure-ban [a Qlik Sense piactéri elem telepítésével.](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense)

* Töltse le a [videojáték-adatokat,](https://www.kaggle.com/gregorut/videogamesales)ezek a mintaadatok CSV formátumban vannak. Ezeket az adatokat egy Cosmos DB-fiókban fogja tárolni, és qlik sense-ben jelenítheti meg.

* Hozzon létre egy Azure Cosmos DB SQL API-fiókot a rövid útmutató cikk fiók létrehozása című szakaszában [ismertetett](create-sql-api-dotnet.md#create-account) lépések használatával.

* [Hozzon létre egy adatbázist és egy gyűjteményt](create-sql-api-java.md#add-a-container) – a gyűjtemény átviteli értékének 1000 RU/s-ra állítható. 

* Töltse be a minta videojáték értékesítési adatokat a Cosmos DB-fiókba. Importálhatja az adatokat az Azure Cosmos DB adatáttelepítési eszközzel, egymás [után vagy](import-data.md#SQLSeqTarget) [tömegesen importálhatja](import-data.md#SQLBulkTarget) az adatokat. Körülbelül 3-5 percet vesz igénybe, amíg az adatok importálása a Cosmos DB-fiókba.

* Töltse le, telepítse és konfigurálja az ODBC-illesztőprogramot a [Cosmos DB-hez az ODBC illesztőprogrammal való csatlakozás](odbc-driver.md) című cikkben leírt lépésekkel. A videojáték-adatok egy egyszerű adatkészlet, és nem kell szerkeszteni a sémát, csak használja az alapértelmezett gyűjtemény-leképezésséma.

## <a name="connect-qlik-sense-to-cosmos-db"></a>A Qlik Sense csatlakoztatása a Cosmos DB-hoz

1. Nyissa meg a Qlik Sense alkalmazást, és válassza **az Új alkalmazás létrehozása lehetőséget.** Adja meg az alkalmazás nevét, és válassza a **Létrehozás gombot.**

   ![Új Qlik Sense alkalmazás létrehozása](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. Az új alkalmazás sikeres létrehozása után válassza az **Alkalmazás megnyitása** lehetőséget, és válassza az Adatok **hozzáadása fájlokból és más forrásokból**lehetőséget. 

3. Az adatforrásokból válassza az **ODBC** lehetőséget az új kapcsolatbeállítási ablak megnyitásához. 

4. Váltson **a felhasználói adatforrásra,** és válassza ki a korábban létrehozott ODBC-kapcsolatot. Adja meg a kapcsolat nevét, és válassza a **Létrehozás gombot.** 

   ![Új kapcsolat létrehozása](./media/visualize-qlik-sense/create-new-connection.png)

5. A kapcsolat létrehozása után kiválaszthatja az adatbázist, a gyűjteményt, ahol a videojáték-adatok találhatók, majd megtekintheti azt.

   ![Válassza ki az adatbázist és a gyűjteményt](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Ezután válassza **az Adatok hozzáadása lehetőséget** az adatok Qlik Sense-be való betöltéséhez. Miután betölti az adatokat a Qlik Sense-be, elemzéseket hozhat létre, és elemzést végezhet az adatokon. Használhatja az elemzési adatokat, vagy létrehozhat saját alkalmazást a videojátékok értékesítésének feltárására. Az alábbi képen látható 

   ![Adatok vizualizációja](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>Az ODBC-vel való csatlakozás korra vonatkozó korlátozások 

A Cosmos DB egy séma nélküli elosztott adatbázis, amelynek illesztőprogramjai a fejlesztői igények körül vannak modellezve. Az ODBC-illesztőprogramnak sémával rendelkező adatbázisra van szükség az oszlopok, adattípusok és egyéb tulajdonságok kikövetkeztetéséhez. A rendszeres SQL-lekérdezés vagy a DML-szintaxis relációs képességgel nem alkalmazható a Cosmos DB SQL API-ra, mert az SQL API nem ANSI SQL. Ennek oka az ODBC-illesztőprogramon keresztül kiadott SQL-utasítások a Cosmos DB-specifikus SQL szintaxissá vannak lefordítva, amely nem rendelkezik az összes konstrukció megfelelőivel. A fordítási problémák elkerülése érdekében az ODBC-kapcsolat beállításakor sémát kell alkalmaznia. A [connect with ODBC illesztőprogram-cikk](odbc-driver.md) javaslatokat és módszereket tartalmaz a séma konfigurálásához. Győződjön meg róla, hogy ezt a leképezést a Cosmos DB-fiók on-kon belüli minden adatbázishoz/gyűjteményhez hozza létre.

## <a name="next-steps"></a>Következő lépések

Ha más vizualizációs eszközt használ, például a Power BI-t, az alábbi dokumentum utasításai val csatlakozhat hozzá:

* [A Cosmos DB-adatok megjelenítése a Power BI-összekötő használatával](powerbi-visualize.md)
