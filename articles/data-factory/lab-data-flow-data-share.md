---
title: Adatintegráció az Azure Data Factory és az Azure Data Share használatával
description: Adatok másolása, átalakítása és megosztása az Azure Data Factory és az Azure Data Share használatával
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 7d453b2724c308e48366d653a51d9e6aa8e82c96
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415930"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Adatintegráció az Azure Data Factory és az Azure Data Share használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ahogy az ügyfelek belekezdenek a modern adattárház- és elemzési projektekbe, nem csak több adatra van szükségük, hanem jobban rálátásuk van az adatterületükön lévő adataikra. Ez a műhely bemutatja, hogy az Azure Data Factory és az Azure Data Share fejlesztései hogyan egyszerűsítik az adatok integrációját és kezelését az Azure-ban. 

A kódmentes ETL/ELT engedélyezésétől az adatok átfogó nézetének létrehozásáig az Azure Data Factory fejlesztései lehetővé teszik az adatmérnökök számára, hogy magabiztosan hozzanak be több adatot, és így több értéket a vállalatszámára. Az Azure Data Share lehetővé teszi, hogy az üzleti és üzleti megosztás tegyék lehetővé a szabályozott módon.

Ebben a műhelyben az Azure Data Factory (ADF) használatával fogja beadni az Azure SQL-adatbázisból (SQL DB) származó adatokat az Azure Data Lake Storage gen2 (ADLS gen2) rendszerbe. Miután leszállt a tóban, átfogja alakítani azokat az adatfolyamok leképezésével, az adatgyár natív átalakítási szolgáltatásával, és az Azure Synapse Analytics (korábban SQL DW) szolgáltatásba kell besülnie. Ezután megosztja a táblát az átalakított adatokkal, valamint néhány további adatot az Azure Data Share használatával. 

A laborban használt adatok New York-i taxi adatok. Az Azure SQL-adatbázisba való importáláshoz töltse le a [taxi-adatok bacpac fájlját.](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac)

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* **Azure SQL Database:** Ha nem rendelkezik SQL DB-vel, ismerje meg, hogyan [hozhat létre SQL DB-fiókot](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

* **Azure Data Lake Storage Gen2 tárfiók:** Ha nem rendelkezik ADLS Gen2 tárfiókkal, ismerje meg, hogyan [hozhat létre ADLS Gen2 tárfiókot.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account)

* **Azure Synapse Analytics (korábban SQL DW)**: Ha nem rendelkezik Azure Synapse Analytics (korábban SQL DW) használatával, ismerje meg, hogyan [hozhat létre egy Azure Synapse Analytics-példányt.](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)

* **Azure Data Factory:** Ha még nem hozott létre adat-előállító, hogyan [hozhat létre egy adat-előállító.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal)

* **Azure-adatmegosztás:** Ha még nem hozott létre adatmegosztást, olvassa el, hogyan [hozhat létre adatmegosztást.](https://docs.microsoft.com/azure/data-share/share-your-data#create-a-data-share-account)

## <a name="set-up-your-azure-data-factory-environment"></a>Az Azure Data Factory környezet beállítása

Ebben a szakaszban megtudhatja, hogyan érheti el az Azure Data Factory felhasználói élményét (ADF UX) az Azure Portalon. Az ADF-felhasználói felületen három összekapcsolt szolgáltatást konfigurálhat az általunk használt adattárakmindegyikéhez: az Azure SQL DB,ADLS Gen2 és az Azure Synapse Analytics.

Az Azure Data Factory kapcsolódó szolgáltatások határozzák meg a kapcsolat adatait a külső erőforrásokhoz. Az Azure Data Factory jelenleg több mint 85 összekötőt támogat.

### <a name="open-the-azure-data-factory-ux"></a>Az Azure Data Factory UX megnyitása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com) a Microsoft Edge vagy a Google Chrome böngészőben.
1. Az oldal tetején található keresősáv használatával keressen rá az "Adatgyárak" kifejezésre

    ![Portál](media/lab-data-flow-data-share/portal1.png)
1. Kattintson az adatgyári erőforrásra az erőforráspanel megnyitásához.

    ![Portál](media/lab-data-flow-data-share/portal2.png)
1. Kattintson a **Szerző és monitor** gombra az ADF UX megnyitásához. Az ADF UX is elérhető adf.azure.com.

    ![Portál](media/lab-data-flow-data-share/portal3.png)
1. A rendszer átirányítja az ADF UX kezdőlapjára. Ez az oldal gyorsindításokat, oktató videókat és oktatóanyagokra mutató hivatkozásokat tartalmaz az adatgyári fogalmak megismeréséhez. A szerzői jog megkezdéséhez kattintson a ceruza ikonra a bal oldali sávban.

    ![Portál](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL-adatbázishoz csatolt szolgáltatás létrehozása

1. A szerzői lap, ahol adat-gyári erőforrások, például a folyamatok, adatkészletek, adatfolyamok, eseményindítók és a kapcsolódó szolgáltatások létrehozása. Csatolt szolgáltatás létrehozásához kattintson a jobb alsó sarokban található **Kapcsolatok** gombra.

    ![Portál](media/lab-data-flow-data-share/configure2.png)
1. Új csatolt szolgáltatás hozzáadásához kattintson a Kapcsolatok lapon az **Új** gombra.

    ![Portál](media/lab-data-flow-data-share/configure3.png)
1. Az első összekapcsolt szolgáltatás konfigurálni fogja az Azure SQL DB. A keresősáv segítségével szűrheti az adattárlistát. Kattintson az **Azure SQL Database** csempére, és kattintson a Folytatás gombra.

    ![Portál](media/lab-data-flow-data-share/configure4.png)
1. Az SQL DB konfigurációs ablaktábláján adja meg az "SQLDB" értéket csatolt szolgáltatásnévként. Adja meg a hitelesítő adatokat, hogy az adat-előállító csatlakozni az adatbázishoz. SQL-hitelesítés használata esetén írja be a kiszolgáló nevét, az adatbázist, a felhasználónevet és a jelszót. A **Kapcsolat**tesztelése gombra kattintva ellenőrizheti, hogy a kapcsolat adatai helyesek-e. Ha végzett, kattintson a **Létrehozás** gombra.

    ![Portál](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Azure Synapse Analytics-alapú szolgáltatás létrehozása

1. Ismételje meg ugyanezt a folyamatot egy Azure Synapse Analytics-kapcsolt szolgáltatás hozzáadásához. A kapcsolatok lapon kattintson az **Új**gombra. Válassza ki az **Azure Synapse Analytics (korábban SQL DW)** csempét, és kattintson a folytatás gombra.

    ![Portál](media/lab-data-flow-data-share/configure6.png)
1. A csatolt szolgáltatás konfigurációs ablaktábláján adja meg a "SQLDW" értéket csatolt szolgáltatásnévként. Adja meg a hitelesítő adatokat, hogy az adat-előállító csatlakozni az adatbázishoz. SQL-hitelesítés használata esetén írja be a kiszolgáló nevét, az adatbázist, a felhasználónevet és a jelszót. A **Kapcsolat**tesztelése gombra kattintva ellenőrizheti, hogy a kapcsolat adatai helyesek-e. Ha végzett, kattintson a **Létrehozás** gombra.

    ![Portál](media/lab-data-flow-data-share/configure7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Azure Data Lake Storage Gen2 kapcsolt szolgáltatás létrehozása

1. A laborhoz szükséges utolsó összekapcsolt szolgáltatás egy Azure Data Lake Storage gen2.  A kapcsolatok lapon kattintson az **Új**gombra. Válassza ki az **Azure Data Lake Storage Gen2** csempét, és kattintson a Folytatás gombra.

    ![Portál](media/lab-data-flow-data-share/configure8.png)
1. A csatolt szolgáltatás konfigurációs ablaktábláján adja meg az "ADLSGen2" értéket csatolt szolgáltatásnévként. Ha fiókkulcs-hitelesítést használ, válassza ki az adls gen2 tárfiókot a **Tárfiók nevének** legördülő menüből. A **Kapcsolat**tesztelése gombra kattintva ellenőrizheti, hogy a kapcsolat adatai helyesek-e. Ha végzett, kattintson a **Létrehozás** gombra.

    ![Portál](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Adatfolyam-hibakeresési mód bekapcsolása

Az *Adatok átalakítása leképezési adatfolyam használatával*szakaszban térképészeti adatfolyamokat fog építeni. Ajánlott eljárás létrehozása előtt leképezési adatfolyamok bekapcsolása hibakeresési mód, amely lehetővé teszi, hogy tesztelje átalakítási logika másodpercek alatt egy aktív spark-fürt.

A hibakeresés bekapcsolását a gyárfelső sávon található **Adatfolyam-hibakeresési** csúszka csúszkájára kattintva kapcsolhatja be. Kattintson az OK gombra, amikor a megerősítő párbeszédpanel előugró ablakai jelennek meg. A fürt indítása körülbelül 5-7 percet vesz igénybe. Folytassa az *Azure SQL DB-ből az ADLS gen2-be történő adatok betöltéséhez a másolási tevékenység használatával az* inicializálás közben.

![Portál](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Adatok betöltése a másolási tevékenység használatával

Ebben a szakaszban hozzon létre egy folyamatot egy másolási tevékenység, amely betöltése egy tábla egy Azure SQL DB egy ADLS gen2 tárfiókba. Megtudhatja, hogyan adhat hozzá egy folyamatot, konfigurálhat egy adatkészletet, és hogyan hibakeresést biztosíthat egy folyamathoz az ADF felhasználói felületén keresztül. Az ebben a szakaszban használt konfigurációs minta a relációs adattárból fájlalapú adattárba történő másolásra vonatkozik.

Az Azure Data Factoryban a folyamat olyan tevékenységek logikai csoportosítása, amelyek együttesen hajtanak végre egy feladatot. A tevékenység határozza meg az adatokon végrehajtandó műveletet. Az adatkészlet a csatolt szolgáltatásban használni kívánt adatokra mutat.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Folyamat létrehozása másolási tevékenységgel

1. A gyári erőforrások ablaktáblán kattintson a plusz ikonra az új erőforrásmenü megnyitásához. Válassza a **Folyamat lehetőséget.**

    ![Portál](media/lab-data-flow-data-share/copy1.png)
1. A folyamatvászon Általános lapján nevezze el a folyamatot valami leírónak, például "IngestAndTransformTaxiData".In **the General** tab of the pipeline canvas, name your pipeline something descriptive such such as 'IngestAndTransformTaxiData'.

    ![Portál](media/lab-data-flow-data-share/copy2.png)
1. A folyamatvászon tevékenységablakában nyissa meg az **Áthelyezés és átalakítás** harmonikát, és húzza az **Adatmásolás idot** a vászonra. Adjon a másolási tevékenységnek egy leíró nevet, például "IngestIntoADLS".Give the copy activity a descriptive name such as 'IngestIntoADLS'.

    ![Portál](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Az Azure SQL DB forrásadatkészlet konfigurálása

1. Kattintson a másolási tevékenység **Forrás** fülére. Új adatkészlet létrehozásához kattintson az **Új**gombra. A forráslesz az asztal dbo. TripData" található a korábban konfigurált "SQLDB" csatolt szolgáltatásban.

    ![Portál](media/lab-data-flow-data-share/copy4.png)
1. Keressen az **Azure SQL Database-t,** és kattintson a Folytatás gombra.

    ![Portál](media/lab-data-flow-data-share/copy5.png)
1. Hívja az adatkészletet "TripData".Call your dataset 'TripData'. Válassza ki az "SQLDB"-t csatolt szolgáltatásként. Válassza ki a tábla nevét 'dbo. TripData' a tábla név legördülő menüből. Importálja a sémát **a kapcsolatból/tárolóból.** Ha végzett, kattintson az OK gombra.

    ![Portál](media/lab-data-flow-data-share/copy6.png)

Sikeresen létrehozta a forrásadatkészletet. Győződjön meg arról, hogy a forrásbeállításokban az alapértelmezett **tábla** érték van kiválasztva a lekérdezési mezőben.

### <a name="configure-adls-gen-2-sink-dataset"></a>ADLS Gen 2 fogadó adatkészletének konfigurálása

1. Kattintson a másolási tevékenység **Elfogadó** fülére. Új adatkészlet létrehozásához kattintson az **Új**gombra.

    ![Portál](media/lab-data-flow-data-share/copy7.png)
1. Keressen rá az **Azure Data Lake Storage Gen2 kifejezésre,** és kattintson a Folytatás gombra.

    ![Portál](media/lab-data-flow-data-share/copy8.png)
1. A formátum kiválasztása ablaktáblán válassza a **Detagoltszöveg** lehetőséget, amikor csv-fájlba ír. Kattintson a Folytatás gombra.

    ![Portál](media/lab-data-flow-data-share/copy9.png)
1. Nevezze el a fogadó adatkészletét "TripDataCSV". Válassza az "ADLSGen2" lehetőséget csatolt szolgáltatásként. Adja meg, hogy hol szeretné írni a csv fájlt. Az adatokat például a `trip-data.csv` tárolóba `staging-container`írhatja. Állítsa az **első sort fejlécként** igaz értékre, ahogy azt szeretné, hogy a kimeneti adatok fejlécekkel rendelkeznek. Mivel még nincs fájl a célban, állítsa **a Séma importálása** mezőt **Nincs (Nincs)** beállításra. Ha végzett, kattintson az OK gombra.

    ![Portál](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>A másolási tevékenység tesztelése folyamathiba-keresési futtatással

1. A hibakeresési futtatás végrehajtásához kattintson a hibakeresési program gombra a folyamatvászon tetején található **Hibakeresés** gombra. A hibakeresési futtatás lehetővé teszi, hogy tesztelje a folyamatot akár végpontok között, vagy amíg egy töréspont közzététele előtt az adatgyári szolgáltatás.

    ![Portál](media/lab-data-flow-data-share/copy11.png)
1. A hibakeresési futtatás figyeléséhez lépjen a folyamatvászon **Kimenet** lapjára. A figyelési képernyő 20 másodpercenként automatikusan frissül, vagy amikor manuálisan kattint a frissítés gombra. A másolási tevékenység speciális figyelési nézetgel rendelkezik, amely a **Műveletek** oszlopban a szemüveg ikonra kattintva érhető el.

    ![Portál](media/lab-data-flow-data-share/copy12.png)
1. A másolásfigyelési nézet a tevékenység végrehajtási részleteit és teljesítményjellemzőit adja meg. Megtekintheti az olyan információkat, mint az adatok olvasása/írása, a sorok olvasása/írása, a fájlok olvasása/írása és az átviteli. Ha mindent helyesen konfigurált, 49 999 sort kell látnia egy fájlba írva az ADLS-fogadóban.

    ![Portál](media/lab-data-flow-data-share/copy13.png)
1. Mielőtt továbblépne a következő szakaszra, javasoljuk, hogy tegye közzé a módosításokat az adatgyári szolgáltatásban a Gyári felső **sávösszes közzététele** elemre kattintva. Bár ez a labor nem foglalkozik, az Azure Data Factory támogatja a teljes git-integrációt. A Git-integráció lehetővé teszi a verziókövetést, a tárházban való ismétlődő mentést és az adat-előállítóegyüttműködést. További információt az [Azure Data Factory forrásvezérlése című témakörben talál.](https://docs.microsoft.com/azure/data-factory/source-control#troubleshooting-git-integration)

    ![Portál](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Adatok átalakítása adatfolyam-leképezéssel

Most, hogy sikeresen másoltadatokat az Azure Data Lake Storage-ba, itt az ideje, hogy összekapcsolódjon és összesítse ezeket az adatokat egy adattárházba. Az Azure Data Factory vizuálisan megtervezett átalakítási szolgáltatása leképezési adatfolyamot fog használni. Az adatfolyamok leképezése lehetővé teszi a felhasználók számára, hogy átalakítási logikakódmentes, és hajtsa végre őket az ADF szolgáltatás által kezelt spark-fürtök.

Az ebben a lépésben létrehozott adatfolyam belső egyesíti az előző szakaszban létrehozott "TripDataCSV" adatkészletet egy 'dbo táblával. TripFares" tárolt "SQLDB" alapján négy kulcsfontosságú oszlopok. Ezután az adatok at `payment_type` kap összesített oszlop alapján kiszámítja az átlagos bizonyos mezők és írt egy Azure Synapse Analytics-tábla.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Adatfolyam-tevékenység hozzáadása a folyamathoz

1. A folyamatvászon tevékenységablakában nyissa meg az **Áthelyezés és átalakítás** harmonikát, és húzza az **Adatfolyam-tevékenységet** a vászonra.

    ![Portál](media/lab-data-flow-data-share/dataflow1.png)
1. A megnyíló oldalsó ablaktáblán válassza **az Új adatfolyam létrehozása lehetőséget,** és válassza **az Adatfolyam leképezése**lehetőséget. Kattintson az **OK** gombra.

    ![Portál](media/lab-data-flow-data-share/dataflow2.png)
1. A rendszer az adatfolyam-vászonra irányítja, ahol az átalakítási logikát fogja építeni. Az általános lapon nevezze el az adatfolyamot "JoinAndAggregateData" néven.

    ![Portál](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Az utazási adatok csv-forrásának konfigurálása

1. Az első dolog, amit szeretne tenni, konfigurálja a két forrás átalakítások. Az első forrás a "TripDataCSV" DelimitedText adatkészletre mutat. Forrásátalakítás hozzáadásához kattintson a forrás **hozzáadása** mezőre a vásznon.

    ![Portál](media/lab-data-flow-data-share/dataflow4.png)
1. Nevezze el a forrást "TripDataCSV", és válassza ki a "TripDataCSV" adatkészletet a forrás legördülő menüből. Ha emlékszik, akkor nem importált sémát először az adatkészlet létrehozásakor, mivel nem voltak ott adatok. Mivel `trip-data.csv` most már létezik, kattintson a **Szerkesztés** gombra az adatkészlet beállításai lap ra kattintva.

    ![Portál](media/lab-data-flow-data-share/dataflow5.png)
1. Nyissa meg a **Lapséma lapot,** és kattintson **a Séma importálása gombra.** Válassza **a Kapcsolat/tároló lehetőséget,** ha közvetlenül a fájltárolóból szeretne importálni. 14 karakterlánc típusú oszlopnak kell megjelennie.

    ![Portál](media/lab-data-flow-data-share/dataflow6.png)
1. Lépjen vissza a "JoinAndAggregateData" adatfolyamhoz. Ha a hibakeresési fürt elindult (amelyet a hibakeresési csúszka melletti zöld kör jelez), az adatok **Refresh** előnézetéről pillanatképet kaphat az **Adatelőnézet** lapon.

    ![Portál](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Az adatelőnézet nem ír adatokat.

### <a name="configure-your-trip-fares-sql-db-source"></a>Konfigurálja az utazás viteldíjak SQL DB forrás

1. A második forrás, amelyet hozzáad, az SQL DB 'dbo táblára mutat. TripFares'. A "TripDataCSV" forrás alatt egy másik **Forrás hozzáadása** mező jelenik meg. Kattintson rá egy új forrásátalakítás hozzáadásához.

    ![Portál](media/lab-data-flow-data-share/dataflow8.png)
1. Nevezze el ezt a forrást "TripFaresSQL". Új SQL DB-adatkészlet létrehozásához kattintson az **Új** gombra a forrásadatkészlet mező je mellett.

    ![Portál](media/lab-data-flow-data-share/dataflow9.png)
1. Válassza ki az **Azure SQL Database** csempét, és kattintson a Folytatás gombra. *Megjegyzés: Előfordulhat, hogy az adat-előállító összekötők közül sok nem támogatott az adatfolyam leképezésében. Az ilyen forrásokból származó adatok átalakításához a másolási tevékenység használatával egy támogatott forrássá kell betöltése.*

    ![Portál](media/lab-data-flow-data-share/dataflow10.png)
1. Hívja az adatkészletet "TripFares"-nek. Válassza ki az "SQLDB"-t csatolt szolgáltatásként. Válassza ki a tábla nevét 'dbo. TripFares a tábla név legördülő menüjéből. Importálja a sémát **a kapcsolatból/tárolóból.** Ha végzett, kattintson az OK gombra.

    ![Portál](media/lab-data-flow-data-share/dataflow11.png)
1. Az adatok ellenőrzéséhez olvassa be az adatelőnézetet az **Adatelőnézet** lapon.

    ![Portál](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Belső csatlakozás TripDataCSV és TripFaresSQL

1. Új átalakítás hozzáadásához kattintson a plus ikonra a "TripDataCSV" jobb alsó sarkában. A **Több bemenet/kimenet csoportban**válassza a **Csatlakozás**lehetőséget.

    ![Portál](media/lab-data-flow-data-share/join1.png)
1. Nevezze el a csatlakozás transzformációját "InnerJoinWithTripFares". Válassza a "TripFaresSQL" lehetőséget a jobb oldali stream legördülő menüből. Válassza a **Belső** elemet illesztési típusként. Ha többet szeretne tudni az adatfolyam leképezésében lévő különböző illesztéstípusokról, olvassa el az [Illesztéstípusok című témakört.](https://docs.microsoft.com/azure/data-factory/data-flow-join#join-types)

    Válassza ki, hogy mely oszlopokat szeretné megjelölni az egyes adatfolyamokból a **Csatlakozás feltételei** legördülő menüben. További illesztési feltétel hozzáadásához kattintson a plusz ikonra egy meglévő feltétel mellett. Alapértelmezés szerint az összes illesztési feltétel egy AND operátorral van kombinálva, ami azt jelenti, hogy az egyezéshez minden feltételnek teljesülnie kell. Ebben a laborban oszlopokon `medallion`, `hack_license` `vendor_id`,`pickup_datetime`

    ![Portál](media/lab-data-flow-data-share/join2.png)
1. Ellenőrizze, hogy sikeresen csatlakozott-e 25 oszlophoz az adatelőnézettel együtt.

    ![Portál](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Aggregátum payment_type szerint

1. Miután befejezte a csatlakozás transzformációját, adjon hozzá egy összesített átalakítást az "InnerJoinWithTripFares" melletti plusz ikonra kattintva. Válassza az **Összesítés** lehetőséget a **Sémamódosító csoportban.**

    ![Portál](media/lab-data-flow-data-share/agg1.png)
1. Nevezze el az összesítő átalakítást "AggregateByPaymentType" néven. Kijelölés `payment_type` oszloponként.

    ![Portál](media/lab-data-flow-data-share/agg2.png)
1. Nyissa meg az **Összesítések** lapot. Itt két összesítést adhat meg:
    * Az átlagos viteldíj fizetéstípus szerint csoportosítva
    * A teljes utazási távolság kifizetéstípus szerint csoportosítva

    Először létrehozza az átlagos viteldíj-kifejezést. Az Oszlop hozzáadása **vagy kijelölése**feliratú szövegdobozba írja be az "average_fare".In the text box labeled Add or select a column , enter 'average_fare'.

    ![Portál](media/lab-data-flow-data-share/agg3.png)
1. Aggregációs kifejezés megadásához kattintson az Enter kifejezés feliratú kék **mezőre.** Ez megnyitja az adatfolyam-kifejezés szerkesztőjét, amely egy olyan eszköz, amellyel vizuálisan adatfolyam-kifejezéseket hozhat létre bemeneti sémával, beépített függvényekkel és műveletekkel, valamint a felhasználó által definiált paraméterekkel. A kifejezésszerkesztő képességeiről a [kifejezésszerkesztő dokumentációjában](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder)olvashat bővebben.

    Az átlagos viteldíj lehívásához használja az összesítési `avg()` függvényt az `total_amount` `toInteger()`oszlop egész számra történő összesítéséhez. Az adatfolyam-kifejezés nyelvén ez `avg(toInteger(total_amount))`a meghatározás: . Kattintson a **Mentés gombra, és ha** elkészült.

    ![Portál](media/lab-data-flow-data-share/agg4.png)
1. További összesítési kifejezés hozzáadásához kattintson a `average_fare`plusz ikonra a gomb mellett. Válassza **az Oszlop hozzáadása**lehetőséget.

    ![Portál](media/lab-data-flow-data-share/agg5.png)
1. Az Oszlop hozzáadása **vagy kijelölése**feliratú szövegdobozba írja be az "total_trip_distance".In the text box labeled Add or select a column , enter "total_trip_distance". Az utolsó lépéshez is nyissa meg a kifejezésszerkesztőt, hogy belépjen a kifejezésbe.

    A teljes úttávolság lehívásához használja az összesítési `sum()` függvényt az `trip_distance` `toInteger()`oszlop egész számra való összesítéséhez. Az adatfolyam-kifejezés nyelvén ez `sum(toInteger(trip_distance))`a meghatározás: . Kattintson a **Mentés gombra, és ha** elkészült.

    ![Portál](media/lab-data-flow-data-share/agg6.png)
1. Tesztelje az átalakítási logikát az **Adatelőnézet** lapon. Mint látható, lényegesen kevesebb sor és oszlop van, mint korábban. Az átalakulásban definiált három csoport és összesítés oszlop továbbhalad. Mivel a mintában csak öt fizetési típuscsoport van, a program csak öt sort eredményez.

    ![Portál](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Az Azure Synapse Analytics-fogadó konfigurálása

1. Most, hogy befejeztük az átalakítási logikát, készen állunk arra, hogy az adatokat egy Azure Synapse Analytics-táblában süllyesztsük el. Adjon hozzá egy fogadóátalakítást a **Cél** szakaszban.

    ![Portál](media/lab-data-flow-data-share/sink1.png)
1. Nevezze el a mosogató "SQLDWSink". Kattintson az **Új** a fogadó adatkészlet mező mellett egy új Azure Synapse Analytics adatkészlet létrehozásához.

    ![Portál](media/lab-data-flow-data-share/sink2.png)

1. Válassza ki az **Azure Synapse Analytics (korábban SQL DW)** csempét, és kattintson a folytatás gombra.

    ![Portál](media/lab-data-flow-data-share/sink3.png)
1. Hívja az adatkészletet "AggregatedTaxiData".Call your dataset 'AggregatedTaxiData'. Válassza ki a "SQLDW"-t összekapcsolt szolgáltatásként. Válassza **az Új tábla létrehozása lehetőséget,** és nevezze el az új dbo táblát. Összesített taxiadatok. Ha végzett, kattintson az OK gombra.

    ![Portál](media/lab-data-flow-data-share/sink4.png)
1. Nyissa meg a mosogató **Beállítások** lapját. Mivel új táblát hozunk létre, a **Tábla létrehozása a** tábla művelet alatt lehetőséget kell választanunk. Törölje a jelet **az Átmeneti beállítás engedélyezése**jelölőnégyzetből, amely azt jelzi, hogy sorról sorra vagy kötegbe szúrjuk-e be.

    ![Portál](media/lab-data-flow-data-share/sink5.png)

Sikeresen létrehozta az adatfolyamot. Most itt az ideje, hogy futtassa a csővezeték-tevékenység.

### <a name="debug-your-pipeline-end-to-end"></a>A folyamat hibakeresése végpontok között

1. Lépjen vissza az **IngestAndTransformData** folyamat lapjára. Figyelje meg a zöld mezőt a "IngestIntoADLS" másolási tevékenységen. Húzza át a "JoinAndAggregateData" adatfolyam-tevékenységre. Ez létrehoz egy "sikeres", ami az adatfolyam-tevékenység csak akkor fut, ha a másolat sikeres.

    ![Portál](media/lab-data-flow-data-share/pipeline1.png)
1. Amásolási tevékenységhez ugyanúgy kattintson a **Hibakeresés gombra** a hibakeresési futtatás végrehajtásához. A hibakeresési futtatások esetén az adatfolyam-tevékenység az aktív hibakeresési fürtöt fogja használni egy új fürt felpörgetése helyett. Ez a folyamat végrehajtása kicsivel több mint egy percet vesz igénybe.

    ![Portál](media/lab-data-flow-data-share/pipeline2.png)
1. A másolási tevékenységhez hasonlóan az adatfolyam is rendelkezik egy speciális figyelési nézetgel, amelyet a szemüveg ikon a tevékenység befejezésekor elér.

    ![Portál](media/lab-data-flow-data-share/pipeline3.png)
1. A figyelési nézetben egy egyszerűsített adatfolyam-grafikont láthat az egyes végrehajtási fázisokban a végrehajtási idővel és a sorokkal együtt. Ha helyesen történt, akkor 49 999 sort kellett volna összevonnia öt sorba ebben a tevékenységben.

    ![Portál](media/lab-data-flow-data-share/pipeline4.png)
1. Az átalakításra kattintva további részleteket kaphat a végrehajtásról, például a particionálási információkat és az új/frissített/eldobott oszlopokat.

    ![Portál](media/lab-data-flow-data-share/pipeline5.png)

Ezzel befejezte a labor adatgyári részét. Tegye közzé az erőforrásokat, ha szeretné üzembe foglalni őket eseményindítókkal. Sikeresen futtatott egy folyamatot, amely adatokat használt be az Azure SQL Database-ből az Azure Data Lake Storage-ba a másolási tevékenység használatával, majd összesítette ezeket az adatokat egy Azure Synapse Analytics-be. Ellenőrizheti, hogy az adatok sikeresen megírása megtörtént-e, ha magát az SQL Servert nézi meg.

## <a name="share-data-using-azure-data-share"></a>Adatok megosztása az Azure Data Share használatával

Ebben a szakaszban megtudhatja, hogyan állíthat be egy új adatmegosztást az Azure Portal használatával. Ez magában foglalja egy új adatmegosztás létrehozását, amely az Azure Data Lake Store Gen2 és az Azure SQL Data Warehouse adatkészleteit tartalmazza. Ezután konfigurálja a pillanatkép ütemezését, amely lehetővé teszi az adatfogyasztók számára, hogy automatikusan frissítsék a velük megosztott adatokat. Ezután meghívja a címzetteket az adatmegosztásba. 

Miután létrehozott egy adatmegosztást, kalapot vált, és az *adatfogyasztóvá*válik. Adatfogyasztóként végigvezeti az adatmegosztási meghívók elfogadásának folyamatán, konfigurálva azt, hogy hol szeretné fogadni az adatokat, és leképezheti az adatkészleteket a különböző tárolási helyekre. Ezután elindítja a pillanatképet, amely másolja az Önnel megosztott adatokat a megadott célhelyre. 

### <a name="sharing-data-data-provider-flow"></a>Adatok megosztása (adatszolgáltatói folyamat)

1. Nyissa meg az Azure Portalt a Microsoft Edge vagy a Google Chrome böngészőben.

1. A lap tetején lévő keresősáv használatával keressen **adatmegosztások kifejezésre**

    ![Portál](media/lab-data-flow-data-share/portal-ads.png)

1. Válassza ki az adatmegosztási fiókot a "Szolgáltató" névvel a nevében. Például **DataProvider0102**. 

1. Válassza **az Adatok megosztásának megkezdése lehetőséget.**

    ![Megosztás megkezdése](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Válassza **a +Létrehozás** lehetőséget az új adatmegosztás konfigurálásának megkezdéséhez. 

1. A **Megosztás ineve csoportban**adja meg a kívánt nevet. Ez az a megosztási név, amelyet az adatfogyasztó látni fog, ezért mindenképpen adjon neki egy leíró nevet, például a TaxiData nevet.

1. A **Leírás**csoportban írjon be egy mondatot, amely leírja az adatmegosztás tartalmát. Az adatmegosztás világszerte tartalmazza a taxiút adatait, amelyek számos üzletben tárolódnak, beleértve az Azure SQL Data Warehouse-t és az Azure Data Lake Store-t. 

1. A **Használati feltételek**csoportban adja meg azokat a feltételeket, amelyeket be szeretne tartani az adatfogyasztónak. Néhány példa: "Ne ossza el ezeket az adatokat a szervezeten kívül" vagy "Jogi megállapodáshoz való hivatkozás". 

    ![Részletek megosztása](media/lab-data-flow-data-share/ads-details.png)

1. Válassza a **Folytatás** elemet. 

1. Adatkészletek **hozzáadása jelölőnégyzetet** 

    ![Adatkészlet hozzáadása](media/lab-data-flow-data-share/add-dataset.png)

1. Válassza ki az **Azure SQL Data Warehouse-t,** hogy kiválasszon egy táblát az Azure SQL Data Warehouse-ból, amelyben az ADF-átalakítások landolt.

    ![Adatkészlet hozzáadása](media/lab-data-flow-data-share/add-dataset-sql.png)

> [!NOTE]
> Az Azure SQL Data Warehouse már ismert Azure Synapse Analytics néven

1. A folytatás előtt egy parancsfájlfuttatását kell futtatnia. A megadott parancsfájl létrehoz egy felhasználót az SQL-adatbázisban, amely lehetővé teszi az Azure Data Share MSI hitelesítését a nevében. 

> [!IMPORTANT]
> A parancsfájl futtatása előtt be kell állítania magát az SQL Server Active Directory-rendszergazdájaként. 

1. Nyisson meg egy új lapot, és keresse meg az Azure Portalon. Másolja a kódot a megadott parancsfájlra, és hozzon létre egy felhasználót az adatbázisban, amelyből adatokat szeretne megosztani. Ehhez jelentkezzen be az EDW adatbázisba a Query Explorer (előzetes verzió) használatával AAD-hitelesítés használatával. 

    Módosítania kell a parancsfájlt, hogy a létrehozott felhasználó zárójelben jelenjen meg. Pl:
    
    hozzon létre felhasználót [dataprovider-xxxx] külső bejelentkezésből;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Váltson vissza az Azure Data Share-re, ahol adatkészleteket adott hozzá az adatmegosztáshoz. 

1. Válassza ki az **EDW-t** az SQL Data Warehouse-hoz, és válassza a **AggregatedTaxiData** lehetőséget a táblához. 

1. Adatkészlet **hozzáadása lehetőséget.**

    Most már rendelkezünk egy SQL-táblával, amely az adatkészletünk része. Ezután további adatkészleteket adunk hozzá az Azure Data Lake Store-ból. 

1. Válassza **az Adatkészlet hozzáadása** és az Azure Data Lake Store **Gen2 lehetőséget.**

    ![Adatkészlet hozzáadása](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Válassza a **Tovább lehetőséget**

1. Bontsa ki *a wwtaxidata -t*. Bontsa ki *a Boston Taxi adatokat.* Figyelje meg, hogy a fájlszintig megoszthatja. 

1. A *Boston Taxi Data* mappát válassza ki, ha a teljes mappát hozzá szeretné adni az adatmegosztáshoz. 

1. Adatkészletek **hozzáadása jelölőnégyzetet**

1. Tekintse át a hozzáadott adatkészleteket. Az adatmegosztáshoz hozzá kell adni egy SQL-táblát és egy ADLSGen2 mappát. 

1. Válassza a **Continue (Folytatás) lehetőséget.**

1. Ezen a képernyőn hozzáadhat címzetteket az adatmegosztáshoz. A hozzáadott címzettek meghívókat kapnak az adatmegosztásra. Ennek a labornak az alkalmazásában két e-mail címet kell megadnia:

    1. Annak az Azure-előfizetésnek az e-mail címe, amelyben vagy. 

        ![Címzettek hozzáadása](media/lab-data-flow-data-share/add-recipients.png)

    1. Adja hozzá a kitalált *janedoe@fabrikam.com*adatfogyasztó nevét.

1. Ezen a képernyőn beállíthatja az adatfogyasztó pillanatkép-beállítását. Ez lehetővé teszi számukra, hogy az Ön által meghatározott időközönként rendszeresfrissítéseket kapjanak az adatokról. 

1. Jelölje be **a Pillanatképütemezés jelölőnégyzetet,** és konfigurálja az adatok óránkénti frissítését az *Ismétlődés legördülő* menühasználatával.  

1. Kattintson a **Létrehozás** gombra.

    Most már aktív adatmegosztást kap. Lehetővé teszi, hogy adatszolgáltatóként láthassa az adatmegosztás létrehozásakor látható adatokat. 

1. Válassza ki a létrehozott adatmegosztást **DataProvider**címmel. Az **adatmegosztásban** **küldött megosztások** kiválasztásával navigálhat rá. 

1. Kattintson a Pillanatkép ütemezése. Ha úgy dönt, letilthatja a pillanatkép ütemezését. 

1. Ezután válassza az **Adatkészletek** lapot. Az adatmegosztáshoz a létrehozás után további adatkészleteket is hozzáadhat. 

1. Válassza az **Előfizetések megosztása** lapot. Még nincs megosztási előfizetés, mert az adatfogyasztó még nem fogadta el a meghívást.

1. Nyissa meg a **Meghívók** lapot. Itt megjelenik a függőben lévő meghívók listája. 

    ![Függőben lévő meghívók](media/lab-data-flow-data-share/pending-invites.png)

1. Válassza ki *janedoe@fabrikam.com*a meghívót a hoz. Válassza a Törlés elemet. Ha a címzett még nem fogadta el a meghívást, a továbbiakban nem fogja tudni megtenni. 

1. Válassza az **Előzmények** lapot. Még semmi sem jelenik meg, mert az adatfogyasztó még nem fogadta el a meghívást, és indított el egy pillanatképet. 

### <a name="receiving-data-data-consumer-flow"></a>Adatok fogadása (Adatfogyasztói folyamat)

Most, hogy áttekintettük az adatmegosztásunkat, készen állunk a kontextus váltásra és az adatfogyasztói kalap viselésére. 

Most már rendelkeznie kell egy Azure-adatmegosztási meghívóval a Microsoft Azure-ból érkező postaládájában. Indítsa el az Outlook Web Access (outlook.com) alkalmazást, és jelentkezzen be az Azure-előfizetéséhez megadott hitelesítő adatokkal.

Az e-mailben, amelyet meg kellett volna kapnia, kattintson a "Meghívó > megtekintése" gombra. Ezen a ponton az adatfogyasztói élményt fogja szimulálni, amikor elfogadja az adatszolgáltatók adatmegosztásra szóló meghívását. 

![Meghívó e-mailben](media/lab-data-flow-data-share/email-invite.png)

Előfordulhat, hogy a rendszer kéri, hogy válasszon ki egy előfizetést. Győződjön meg arról, hogy válassza ki az előfizetést, amelyen dolgozott ehhez a tesztkörnyezethez. 

1. Kattintson a *DataProvider*című meghívóra. 

1. Ezen a Meghívó képernyőn különböző részleteket fog észlelni az adatszolgáltatóként korábban konfigurált adatmegosztásról. Tekintse át a részleteket, és fogadja el a használati feltételeket, ha meg van adva.

1. Válassza ki azt az Előfizetés és erőforráscsoportot, amely már létezik a tesztkörnyezetben. 

1. **Adatmegosztási fiók**esetén válassza a **DataConsumer**lehetőséget. Új adatmegosztási fiókot is létrehozhat. 

1. A **Fogadott megosztás neve**területen észre fogod venni, hogy az alapértelmezett megosztásnév az adatszolgáltató által megadott név. Adjon a megosztásnak egy rövid nevet, amely leírja a kapni kívánt adatokat, például **a TaxiDataShare.**

    ![A meghívó elfogadva](media/lab-data-flow-data-share/consumer-accept.png)

1. Választhatja az **Elfogadás és konfigurálás,** illetve az **Elfogadás és a későbbi beállítást.** Ha most úgy dönt, hogy elfogadja és konfigurálja, meg kell adnia egy tárfiókot, ahol az összes adatot másolni kell. Ha később elfogadja és konfigurálja, a megosztás adatkészletei leképezése megszűnik, és manuálisan kell leképezniőket. Majd később ezt választjuk. 

1. Válassza **az Elfogadás és konfigurálás későbbi lehetőséget.** 

    A beállítás konfigurálásakor létrejön egy megosztási előfizetés, de nincs hová tenni az adatok, mivel nincs cél leképezve. 

    Ezután konfiguráljuk az adatkészlet-leképezéseket az adatmegosztáshoz. 

1. Válassza ki a Fogadott megosztást (az 5. lépésben megadott nevet).

    **Az eseményindító pillanatképe** szürkén jelenik meg, de a megosztás aktív. 

1. Válassza az **Adatkészletek** lapot. Figyelje meg, hogy minden adatkészlet nincs leképezve, ami azt jelenti, hogy nincs cél, hogy adatokat másolni. 

    ![nem leképezett adatkészletek](media/lab-data-flow-data-share/unmapped.png)

1. Jelölje ki az SQL Data Warehouse Táblát, majd válassza **a + Leképezés a célhoz**lehetőséget.

1. A képernyő jobb oldalán válassza a **Cél adattípus legördülő** menüt. 

    Az SQL-adatokat az adattárak széles köréhez képezheti le. Ebben az esetben egy Azure SQL-adatbázishoz rendelünk le.

    ![Hozzárendelés](media/lab-data-flow-data-share/mapping-options.png)
    
    (Nem kötelező) Válassza ki az **Azure Data Lake Store Gen2** céladat-típusként. 
    
    (Nem kötelező) Válassza ki azt az előfizetési, erőforráscsoport- és tárfiókot, amelyben dolgozott. 
    
    (Nem kötelező) Választhat, hogy az adatokat csv vagy parketta formátumban fogadja az adattóba. 

1. A **Cél adattípus**mellett válassza az Azure SQL Database lehetőséget. 

1. Válassza ki azt az előfizetési, erőforráscsoport- és tárfiókot, amelyben dolgozott. 

    ![térkép sql](media/lab-data-flow-data-share/map-to-sqldb.png)

1. A folytatás előtt létre kell hoznia egy új felhasználót az SQL Serverben a megadott parancsfájl futtatásával. Először másolja a vágólapra mellékelt parancsfájlt. 

1. Nyisson meg egy új Azure Portal lapot. 

1. A megnyitott új lapon keresse meg az **SQL-adatbázisokat.**

1. Válassza ki az SQL-adatbázist (csak egy lehet az előfizetésben). Ügyeljen arra, hogy ne válassza ki az SQL Data Warehouse.Be careful to not select the SQL Data Warehouse. 

1. Lekérdezésszerkesztő kiválasztása **(előzetes verzió)**

1. A Lekérdezésszerkesztőbe való bejelentkezéshez használja az AAD-hitelesítést. 

1. Futtassa az adatmegosztásban megadott lekérdezést (a 14. lépésben a vágólapra másolva). 

    Ez a parancs lehetővé teszi, hogy az Azure Data Share szolgáltatás felügyelt identitások az Azure Services hitelesítéséhez az SQL Server, hogy képes adatokat másolni. 

1. Lépjen vissza az eredeti lapra, és válassza **a Megcélolni kívánt leképezés**lehetőséget.

1. Ezután válassza ki az Azure Data Lake Gen2 mappát, amely az adatkészlet része, és rendelje hozzá egy Azure Blob Storage-fiókhoz. 

    ![storage](media/lab-data-flow-data-share/storage-map.png)

    Az összes adatkészlet leképezésével most már készen áll az adatszolgáltatótól érkező adatok fogadására. 

    ![Leképezés](media/lab-data-flow-data-share/all-mapped.png)
    
1. Válassza a **Részletek lehetőséget.** 

    Figyelje meg, hogy **az Eseményindító pillanatkép** már nem szürkén jelenik meg, mivel az adatmegosztás most már rendelkezik célhelyekkel, amelyekbe másolni kell.

1. Válassza az Eseményindító -> teljes másolat lehetőséget. 

    ![Ravaszt](media/lab-data-flow-data-share/trigger-full.png)

    Ezzel megkezdődik az adatok másolása az új adatmegosztási fiókba. Egy valós forgatókönyv, ezek az adatok érkeznének egy harmadik féltől. 

    Körülbelül 3-5 percet vesz igénybe, amíg az adatok találkoznak. A folyamatot az **Előzmények** fülre kattintva figyelheti. 

    Várakozás közben keresse meg az eredeti adatmegosztást (DataProvider), és tekintse meg az **Előfizetések megosztása** és az **előzmények** lapot. 

1. Lépjen vissza az Adatfogyasztó adatmegosztására. Miután az eseményindító állapota sikeres, keresse meg a cél SQL-adatbázis és a Data Lake, hogy az adatok landolt a megfelelő tárolókban. 

Gratulálok, befejezted a labort!


