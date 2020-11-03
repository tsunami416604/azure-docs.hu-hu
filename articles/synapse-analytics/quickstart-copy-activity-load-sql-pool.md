---
title: Gyors útmutató az SQL-készletbe való betöltéséhez másolási tevékenység használatával
description: Az Azure szinapszis Analytics használata az SQL-készletbe való betöltéshez
services: synapse-analytics
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: synapse-analytics
ms.topic: quickstart
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: 12b5530ccf154220b11f9d1286d629caf2209475
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280903"
---
# <a name="quickstart-load-data-into-sql-pool-using-copy-activity"></a>Gyors útmutató: az SQL-készletbe való betöltés a másolási tevékenység használatával

Az Azure szinapszis Analytics számos analitikai motort kínál az adatai betöltéséhez, átalakításához, modellezéséhez és elemzéséhez. Az SQL-készlet A T-SQL-alapú számítási és tárolási funkciókat kínál. Miután létrehozta az SQL-készletet a szinapszis munkaterületen, az adatmennyiség betölthető, modellezhető, feldolgozható és leküldhető a gyorsabb elemzési elemzéshez.

Ebből a rövid útmutatóból megtudhatja, hogyan *tölthetők be a Azure SQL Databaseból származó adatok az Azure szinapszis Analytics szolgáltatásba*. Az adatok más típusú adattárakból történő másolásához hasonló lépéseket kell követnie. És a hasonló folyamat a más forrás és a fogadó közötti adatmásolásra is vonatkozik.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* Azure szinapszis-munkaterület: hozzon létre egy szinapszis-munkaterületet a Azure Portal használatával a gyors útmutató [: szinapszis-munkaterület létrehozása](quickstart-create-workspace.md)című témakör utasításait követve.
* Azure SQL Database: az oktatóanyag az Adventure Works LT minta adatkészletből másolja az adatokat Azure SQL Database. A mintaadatbázis létrehozásához SQL Database a [mintaadatbázis létrehozása a Azure SQL Databaseban](../azure-sql/database/single-database-create-quickstart.md)című témakör útmutatását követve. Más adattárakat is használhat a hasonló lépések követésével.
* Azure Storage-fiók: az Azure Storage a másolási művelet *előkészítési* területén van használatban. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-account-create.md) ismertető cikkben találja.
* Azure szinapszis Analytics: SQL-készletet használ fogadó adattárként. Ha nem rendelkezik Azure-beli szinapszis Analytics-példánnyal, tekintse meg az [SQL-készlet létrehozása](quickstart-create-sql-pool-portal.md) a létrehozás lépéseihez című témakört.

### <a name="navigate-to-the-synapse-studio"></a>Navigáljon a szinapszis studióhoz

Az Azure szinapszis-munkaterület létrehozása után kétféleképpen nyithatja meg a szinapszis Studio alkalmazást:

* Nyissa meg a szinapszis munkaterületet a [Azure Portal](https://ms.portal.azure.com/#home). Az Áttekintés szakasz tetején válassza a **szinapszis Studio elindítása** lehetőséget.
* Nyissa meg az [Azure szinapszis Analytics szolgáltatást](https://web.azuresynapse.net/) , és jelentkezzen be a munkaterületére.

Ebben a rövid útmutatóban példaként használjuk a "adftest2020" nevű munkaterületet. A rendszer automatikusan átirányítja a szinapszis Studio kezdőlapjára.

![Szinapszis Studio kezdőlapja](media/doc-common-process/synapse-studio-home.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Az Azure szinapszis Analyticsben a társított szolgáltatás a kapcsolati adatok más szolgáltatásokhoz való definiálására szolgál. Ebben a szakaszban az alábbi két társított szolgáltatást fogja létrehozni: Azure SQL Database és Azure Data Lake Storage Gen2 társított szolgáltatásokat.

1. A szinapszis Studio kezdőlapján válassza a **kezelés** fület a bal oldali navigációs sávon.
1. A külső kapcsolatok területen válassza a társított szolgáltatások elemet.
  
   ![Új társított szolgáltatás létrehozása](media/doc-common-process/new-linked-service.png)

1. Társított szolgáltatás hozzáadásához válassza az **új** lehetőséget.
1. Válassza ki **Azure SQL Database** a katalógusból, majd válassza a **Folytatás** lehetőséget. Az összekötők szűréséhez a keresőmezőbe írja be az "SQL" kifejezést.

   ![Új Azure SQL Database társított szolgáltatás létrehozása](media/quickstart-copy-activity-load-sql-pool/new-azure-sql-linked-service.png)

1. Az új társított szolgáltatás oldalon válassza ki a kiszolgáló nevét és az adatbázis nevét a legördülő listából, majd adja meg a felhasználónevet és a jelszót. Kattintson a **Kapcsolódás tesztelése** elemre a beállítások ellenőrzéséhez, majd válassza a **Létrehozás** lehetőséget.

   ![Azure SQL Database társított szolgáltatás konfigurálása](media/quickstart-copy-activity-load-sql-pool/azure-sql-linked-service-configuration.png)

1. Ismételje meg a 3-4 lépést, de a katalógus helyett válassza a **Azure Data Lake Storage Gen2** lehetőséget. Az új társított szolgáltatás oldalon válassza ki a Storage-fiók nevét a legördülő listából. Kattintson a **Kapcsolódás tesztelése** elemre a beállítások ellenőrzéséhez, majd válassza a **Létrehozás** lehetőséget. 

   ![Azure Data Lake Storage Gen2 konfigurálása](media/quickstart-copy-activity-load-sql-pool/adls-gen2-linked-service-configuration.png)
 
## <a name="create-a-pipeline"></a>Folyamat létrehozása

A folyamatok egy adott tevékenységek végrehajtásának logikai folyamatát tartalmazzák. Ebben a szakaszban egy másolási tevékenységet tartalmazó folyamatot fog létrehozni, amely a Azure SQL Databaseból származó adatok SQL-készletbe való betöltését teszi elérhetővé.

1. Nyissa meg az **integrálás** lapot. Válassza a folyamatok fejléc melletti plusz ikont, és válassza a folyamat lehetőséget.

   ![Új folyamat létrehozása](media/doc-common-process/new-pipeline.png)

1. Az *áthelyezés és átalakítás* területen a *tevékenységek* panelen húzza az **Adatmásolás** elemet a folyamat vászonra.
1. Válassza ki a másolási tevékenységet, és lépjen a forrás lapra. Az **új** elemre kattintva hozzon létre egy új forrás-adatkészletet.

   ![Forrásadatkészlet létrehozása](media/quickstart-copy-activity-load-sql-pool/new-source-dataset.png)

1. Válassza a **Azure SQL Database** lehetőséget az adattárban, majd válassza a **Folytatás** lehetőséget.
1. A *készlet tulajdonságai* ablaktáblán válassza ki a korábbi lépésben létrehozott Azure SQL Database társított szolgáltatást. 
1. A táblázat neve területen válasszon ki egy, a másolási tevékenységben használni kívánt mintát. Ebben a rövid útmutatóban példaként használjuk a "SalesLT. Customer" táblát. 

   ![Forrás adatkészlet tulajdonságainak beállítása](media/quickstart-copy-activity-load-sql-pool/source-dataset-properties.png)
1. Ha elkészült, kattintson **az OK gombra** .
1. Válassza ki a másolási tevékenységet, és lépjen a fogadó lapra. Új fogadó adatkészlet létrehozásához válassza az **új** lehetőséget.
1. Válassza az **SQL Analytics-készlet** lehetőséget adattárként, és válassza a **Folytatás** lehetőséget.
1. A  **készlet tulajdonságai** ablaktáblán válassza ki a korábbi lépésben létrehozott SQL Analytics-készletet. Ha egy meglévő táblába ír, a *tábla neve* alatt válassza ki azt a legördülő listából. Ellenkező esetben kattintson a "szerkesztés" gombra, és adja meg az új táblanév nevét. Ha elkészült, kattintson **az OK gombra** .
1. A fogadó adatkészlet beállításainál engedélyezze az **Automatikus létrehozás táblát** a tábla beállítás mezőjében.

   ![Automatikus létrehozás engedélyezése](media/quickstart-copy-activity-load-sql-pool/auto-create-table.png)

1. A **Beállítások** lapon jelölje be az **előkészítés engedélyezése** jelölőnégyzetet. Ez a beállítás akkor érvényes, ha a forrásadatok nem kompatibilisek a Base típussal. Az **átmeneti beállítások** szakaszban válassza ki azt a Azure Data Lake Storage Gen2 társított szolgáltatást, amelyet a korábbi lépésben hozott létre az átmeneti tárolóként. 

    A tárterület az adatok előkészítéséhez használatos, mielőtt betöltődik az Azure szinapszis Analyticsbe a Base használatával. A másolás befejezése után a rendszer automatikusan megtisztítja a Azure Data Lake Storage Gen2 lévő ideiglenes adatkészletet.

   ![Előkészítés engedélyezése](media/quickstart-copy-activity-load-sql-pool/staging-linked-service.png)

1. A folyamat érvényesítéséhez kattintson az **Érvényesítés** elemre az eszköztáron. Az oldal jobb oldalán megjelenik a folyamat-ellenőrzési kimenet eredménye. 

## <a name="debug-and-publish-the-pipeline"></a>A folyamat hibakeresése és közzététele

Ha befejezte a folyamat konfigurálását, hibakeresési futtatást hajthat végre, mielőtt közzéteszi az összetevőket, hogy minden helyes legyen.

1. A folyamat hibakereséséhez válassza a **Hibakeresés** elemet az eszköztáron. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg. 

   ![Hibakeresés a folyamaton](media/quickstart-copy-activity-load-sql-pool/debugging-result.png)

1. A folyamat sikeres futtatása után a felső eszköztáron válassza az **összes közzététele** lehetőséget. Ez a művelet közzéteszi a szinapszis Analytics szolgáltatásban létrehozott entitásokat (adatkészleteket és folyamatokat).
1. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetek megtekintéséhez kattintson a jobb felső sarokban található harang gombra. 


## <a name="trigger-and-monitor-the-pipeline"></a>A folyamat elindítása és figyelése

Ebben a szakaszban manuálisan indítja el a folyamatot az előző lépésben közzétett folyamaton. 

1. Válassza az **aktiválás hozzáadása** lehetőséget az eszköztáron, majd válassza az **aktiválás most** lehetőséget. A **folyamat futtatása** lapon kattintson az **OK gombra**.  
1. Lépjen a **figyelés** lapra a bal oldali oldalsávban. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. 
1. Ha a folyamat futása sikeresen befejeződött, válassza a **folyamat neve** oszlop alatt lévő hivatkozást a tevékenység futtatási részleteinek megtekintéséhez vagy a folyamat újrafuttatásához. Ebben a példában csak egy tevékenység van, így csak egy bejegyzés jelenik meg a listában. 
1. A másolási művelet részleteinek megtekintéséhez kattintson a **tevékenység neve** oszlop **részletek** hivatkozására (szemüveg ikon). A forrásról a fogadóra másolt adatok mennyiségét, az adatátvitelt, a végrehajtási lépéseket és a megfelelő időtartamot, valamint a használt konfigurációkat is figyelheti.

   ![Tevékenység részletei](media/quickstart-copy-activity-load-sql-pool/activity-details.png)

1. Ha vissza szeretne váltani a folyamat futási nézetére, válassza az **összes folyamat futtatása** hivatkozást a felső részen. A lista frissítéséhez kattintson a **Frissítés** gombra.
1. Ellenőrizze, hogy az adatai helyesen vannak-e írva az SQL-készletben.


## <a name="next-steps"></a>Következő lépések

A következő cikkből megismerheti az Azure szinapszis Analytics támogatását:

> [!div class="nextstepaction"]
> [Folyamat és tevékenységek](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 
>  [Összekötő áttekintése](https://docs.microsoft.com/azure/data-factory/connector-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 
>  [Másolási tevékenység](https://docs.microsoft.com/azure/data-factory/copy-activity-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)