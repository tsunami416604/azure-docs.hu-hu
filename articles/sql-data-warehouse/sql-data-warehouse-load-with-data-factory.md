---
title: Adatok betöltése az Azure SQL Data Warehouse – adat-előállító |} Microsoft Docs
description: Ez az oktatóanyag az Azure SQL Data Warehouse-adatokat tölt Azure Data Factory használatával, és egy SQL Server-adatbázist használja, mint az adatforrás.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: ''
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: loading
ms.date: 02/08/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: 6399f1a3390119685c1c9fd7332937e0cdb6f9ea
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2018
ms.locfileid: "30961754"
---
# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Adatok betöltése az SQL Data Warehouse Data Factory

Adatok betöltése az Azure SQL Data Warehouse bármely Azure Data Factory is használhatja a [támogatott forrás adattárolókhoz](../data-factory/copy-activity-overview.md). Például akkor lehet adatok betöltése az Azure SQL-adatbázis vagy Oracle-adatbázishoz az SQL data warehouse Data Factory használatával. Ez a cikk az oktatóanyag bemutatja, hogyan lehet adatokat betölteni a helyszíni SQL Server-adatbázist az SQL data warehouse.

**Becsült idő**: Ez az oktatóanyag befejezéséhez körülbelül 10 – 15 perc után az Előfeltételek teljesülését.

## <a name="prerequisites"></a>Előfeltételek

- Kell egy **SQL Server-adatbázis** táblákkal, amelyek tartalmazzák az SQL data warehouse keresztül másolja az adatokat.  

- Az online kell **SQL Data Warehouse**. Ha még nem rendelkezik adatraktárral, megtudhatja, hogyan [hozzon létre egy Azure SQL Data Warehouse](sql-data-warehouse-get-started-provision.md).

- Kell egy **Azure Storage-fiók**. Ha még nem rendelkezik egy tárfiókot, megtudhatja, hogyan [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md). A legjobb teljesítmény érdekében keresse meg a tárfiók és az adatraktár azonos Azure-régiót.

## <a name="configure-a-data-factory"></a>Egy adat-előállító konfigurálása
1. Jelentkezzen be az [Azure portálra][].
2. Keresse meg az adatraktár, majd kattintson a megnyitásához.
3. A fő paneljén kattintson **adatok betöltése** > **Azure Data Factory**.

    ![Adatok betöltése varázsló indítása](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. Ha nem rendelkezik adat-előállítót az Azure-előfizetéssel, akkor tekintse meg a **új adat-előállító** párbeszédpanel a böngésző külön lapon. A kért információkat, majd kattintson **létrehozása**. Az adat-előállító létrehozása után a **új adat-előállító** párbeszédpanel bezárul, és megjelenik a **válassza ki a Data Factory** párbeszédpanel.

    Ha már megtalálható az Azure-előfizetés egy vagy több adat-előállítók, megjelenik a **válasszon adat-előállító** párbeszédpanel megnyitásához. Az ezen a párbeszédpanelen válassza ki valamelyik adat-előállítót, vagy kattintson a **hozzon létre új adat-előállító** számára hozzon létre egy újat.

    ![Adat-előállító konfigurálása](media/sql-data-warehouse-load-with-data-factory/configure-data-factory.png)

5. Az a **válasszon adat-előállító** párbeszédpanelen a **adatok betöltése** beállítás alapértelmezés szerint. Kattintson a **tovább** a feladat betöltése adatok létrehozásához.

## <a name="configure-the-data-factory-properties"></a>A data factory tulajdonságainak konfigurálása
Most, hogy egy adat-előállító létrehozott, a következő lépésre az adatok betöltése az ütemezés konfigurálása.

1. A **feladatnév**, adja meg **DWLoadData-fromSQLServer**.
2. Használja az alapértelmezett **futtassa egyszer most** , majd **következő**.

    ![Betöltési ütemezését úgy állítsa be](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-source-data-store-and-gateway"></a>A forrás-tárolót és az átjáró konfigurálása
Most pedig utasítani fogja a Data Factory a helyszíni SQL Server adatbázis-, amelyből el kívánja betölteni az adatokat.

1. Válasszon **SQL Server** a támogatott forrásadatok katalógus tárolja, és kattintson a **következő**.

    ![Válassza ki az SQL Server-adatforrás](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

2. A **adja meg a helyszíni SQL Server-adatbázis** párbeszédpanel jelenik meg. Az első **kapcsolatnév** mező rendszer automatikusan kitölti. A második mező nevét kéri a **átjáró**. Ha valamelyik adat-előállítót, amely már van egy átjáró használ, a legördülő listában használni az átjárót. Kattintson a **átjáró létrehozása** hivatkozás létrehozásához az adatkezelési átjárót.  

    > [!NOTE]
    > Ha az adatok tárolásához a helyszínen vagy egy Azure IaaS virtuális gépen, az adatkezelési átjáró szükséges. Átjáró egy adat-előállító 1-1 kapcsolattal rendelkezik. Az adat-előállító nem használható, de több Adatbetöltési adat-előállító feladatok használható. Átjáró segítségével több adattárolókhoz csatlakozás Adatbetöltési feladatok futtatásakor.
    >
    > Az átjáró kapcsolatos részletes információkért lásd: [az adatkezelési átjáró](../data-factory/v1/data-factory-data-management-gateway.md) cikk.

3. A **átjáró létrehozása** párbeszédpanel jelenik meg. A nevét, adja meg a **GatewayForDWLoading**, és kattintson a **létrehozása**.

4. A **átjáró konfigurálása** párbeszédpanel jelenik meg. Kattintson a **indítsa el a számítógépen a gyorstelepítés** automatikus letöltése, telepítése, és az adatkezelési átjáró regisztrálása az aktuális számítógépen. A folyamat egy előugró ablak látható. Ha a számítógép nem tud csatlakozni az adattároló, manuálisan is [töltse le és telepítse az átjáró](https://www.microsoft.com/download/details.aspx?id=39717) olyan gépen, amely csatlakozni tud-e az adatok tárolásához, és a kulcs segítségével regisztrálja.
    > [!NOTE]
    > A gyorstelepítés natív módon a Microsoft Edge és az Internet Explorer működik. Ha Google Chrome használ, először telepítse a ClickOnce bővítmény Chrome webes áruházból.

    ![Expressz telepítés elindítása](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

5. Várjon, amíg az átjáró-telepítés befejezéséhez. Miután az átjáró regisztrálása sikeres volt, és online állapotban, az előugró ablak bezárása, és az új átjáró átjáró mezőjében jelenik meg. Ezután a adja meg a többi kötelező mezők az alábbiak szerint, majd kattintson a **következő**.
    - **Kiszolgálónév**: a helyszíni SQL Server neve.
    - **Az adatbázisnév**: SQL Server-adatbázist.
    - **Hitelesítőadat-titkosítás**: használja az alapértelmezett "által webböngésző".
    - **Hitelesítés típusa**: válassza ki a hitelesítéshez használ.
    - **Felhasználónév** és **jelszó**: Adja meg a felhasználónevet és jelszót egy felhasználó, aki jogosult az adatok másolásához.

    ![Expressz telepítés elindítása](media/sql-data-warehouse-load-with-data-factory/configure-sql-server.png)

6. A következő lépés, hogy válassza körültekintően a táblákat, amelynek be kell másolnia az adatokat. Szűrheti a táblák kulcsszavak használatával. És az adatok és a tábla séma az alsó panelen megtekintheti. A kijelölés befejezése után kattintson **következő**.

    ![Select tables (Táblák kiválasztása)](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>A cél, az SQL Data Warehouse konfigurálása

Most pedig utasítani fogja a Data Factory cél információkkal kapcsolatos.

1. Az SQL Data Warehouse-kapcsolat adatainak automatikusan kitölti a rendszer. Adja meg felhasználónévhez tartozó jelszót. Kattintson **következő**.

    ![Cél konfigurálása](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

2. Egy intelligens táblaleképezés jelenik meg, hogy a maps forrás céltáblák táblanevek alapján. Ha a tábla nem létezik a cél, alapértelmezés szerint ADF létrehoz egy azonos nevű (Ez vonatkozik az SQL Server vagy az Azure SQL Database forrásaként). Választhatja azt is, egy meglévő táblára van leképezve. Tekintse át, és kattintson a **következő**.

    ![Táblázatok hozzárendelése](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

3. Tekintse át a séma-hozzárendeléséhez, és keressen hiba vagy figyelmeztető üzeneteket. Intelligens leképezési oszlopnév alapul. Ha egy nem támogatott típus a forrás és cél oszlop közötti átalakítás, lásd: hibaüzenetet a megfelelő tábla mellett. Lehetővé teszik a Data Factory automatikusan a táblák létrehozása mellett dönt, ha megfelelő adattípus konvertálása olyan esetben fordulhat elő, ha a kompatibilitási kijavításához forrás és a célkiszolgáló között.

    ![Térkép séma](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

4. Kattintson a **Tovább** gombra.

## <a name="configure-the-performance-settings"></a>A Teljesítménybeállítások konfigurálása
A teljesítmény-konfigurációk esetén az adatok átmeneti megelőzően a SQL Data Warehouse performantly használt Azure storage-fiók konfigurálja [PolyBase](sql-data-warehouse-best-practices.md#use-polybase-to-load-and-export-data-quickly). Miután végzett a másolat, a program automatikusan kiüríti az átmeneti adatok.

Jelöljön ki egy meglévő Azure Storage-fiók, majd a **következő**.

![Átmeneti blob konfigurálása](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>Tekintse át az összefoglaló információkat, és újra az adatcsatornát

Ellenőrizze a konfigurációt, és kattintson a **Befejezés** gombra kattintva az adatcsatornát.

![Adat-előállító telepítése](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>A figyelő adatok betöltése folyamatban

Megtekintheti a központi telepítésének állapotáról és az eredmények a **telepítési** lap.

1. Az üzembe helyezés, ha a hivatkozásra, amely szerint **kattintson ide a figyelő másolási folyamat** betöltése folyamatban lévő adatok figyelésére.

    ![Folyamat figyelése](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

2. Az újonnan létrehozott **DWLoadData-fromSQLServer** adatok betöltését folyamat kiválasztva a bal oldali az automatikus **erőforrás-kezelő**.

    ![Nézet folyamat](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

3. Kattintson a folyamat minden tábla, amely egy tevékenység van leképezve a részletes állapotát tekintheti meg a középső panelen be.

    ![Tábla tevékenység megtekintése](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

4. További kattintson be egy tevékenységet, és az adatokat, a jobb oldali panelen, mint például a adatméret vagy sorok, átviteli részletek betöltésekor látja.

    ![Tábla tevékenység részleteinek megtekintése](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

5. A figyelési nézetek későbbi, nyissa meg az SQL Data warehouse elindításához kattintson **adatok betöltése > Azure Data Factory**, válassza ki a gyári, és válassza a **meglévő betöltése feladatok figyelése**.

## <a name="next-steps"></a>További lépések

Telepítse át az adatbázist az SQL Data Warehouse, lásd: [áttelepítése – áttekintés](sql-data-warehouse-overview-migrate.md).

Ha többet szeretne megtudni az Azure Data Factory és az adatok mozgása képességeit, tekintse meg a következő cikkeket:

- [Az Azure Data Factory bemutatása](../data-factory/introduction.md)
- [Adatok áthelyezése a másolási tevékenység használatával](../data-factory/copy-activity-overview.md)
- [Adatok áthelyezése az Azure SQL Data Warehouse-ba és onnan máshová az Azure Data Factory használatával](../data-factory/connector-azure-sql-data-warehouse.md)

Az adatokba az SQL Data Warehouse, olvassa el a következő cikkeket:

- [Csatlakozás az SQL Data Warehouse a Visual Studio és az SSDT](sql-data-warehouse-query-visual-studio.md)
- [Visual adataiba a Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

<!-- Azure references -->
[Azure portálra]: https://portal.azure.com
