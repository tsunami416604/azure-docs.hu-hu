---
title: Hive-táblák létrehozása és adatok betöltése a Blob storage-ból - Csapatadat-elemzési folyamat
description: A Hive-lekérdezések használatával hive-táblákat hozhat létre, és adatokat tölthet be az Azure blob storage-ból. Partition Hive-táblákat, és az Optimalizált soroszlop (ORC) formázással javíthatja a lekérdezés teljesítményét.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 625d9d5c5ecf095d4acbff625754b2065f184536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251660"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Hive-táblák létrehozása és adatok betöltése az Azure Blob Storage-ból

Ez a cikk bemutatja az általános Hive-lekérdezéseket, amelyek hive-táblákat hoznak létre, és adatokat töltenek be az Azure blob storage-ból. Néhány útmutatást is mellékelt a Hive-táblák particionálása és az Optimalizált soroszlopos (ORC) formázás használata a lekérdezési teljesítmény javítása érdekében.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy a következőket feltételezi:

* Létrehozott egy Azure Storage-fiókot. Ha utasításokra van szüksége, olvassa el [az Azure Storage-fiókok – betekintés.](../../storage/common/storage-introduction.md)
* Kiépített egy testreszabott Hadoop-fürtöt a HDInsight szolgáltatással.  Ha utasításokra van szüksége, olvassa el [a Fürtök beállítása a HDInsight ban](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Engedélyezve van a fürt távoli eléréséhez, bejelentkezve és megnyitva a Hadoop parancssori konzolt. Ha utasításokra van szüksége, [olvassa el az Apache Hadoop-fürtök kezelése ..](../../hdinsight/hdinsight-administer-use-portal-linux.md)

## <a name="upload-data-to-azure-blob-storage"></a>Adatok feltöltése az Azure blob storage-ba
Ha létrehozott egy Azure virtuális gépet az [Azure virtuális gép beállítása fejlett elemzéshez](../../machine-learning/data-science-virtual-machine/overview.md)című dokumentumutasításait követve, ezt a parancsfájlt le kellett volna tölteni a *C:\\\\\<Users\>\\user name Documents\\Data Science Scripts* könyvtárba a virtuális gépen. Ezek a Hive-lekérdezések csak megkövetelik, hogy adatsémát és az Azure blob storage-konfiguráció a megfelelő mezőkben, hogy készen áll a beküldésre.

Feltételezzük, hogy a Hive-táblák adatai **tömörítetlen** táblázatos formátumban vannak, és hogy az adatok feltöltve lettek a Hadoop-fürt által használt tárfiók alapértelmezett (vagy további) tárolójára.

Ha azt szeretnénk, hogy a gyakorlatban a **NYC Taxi Trip Data**, meg kell:

* **töltse le** a 24 [NYC Taxi Trip Data](https://www.andresmh.com/nyctaxitrips) fájlokat (12 Trip fájlokés 12 Fare fájlok),
* **csomagolja ki az** összes fájlt .csv fájlokba, majd
* **töltse fel** őket az Azure Storage-fiók alapértelmezett (vagy megfelelő tárolójára) ; az ilyen fiók beállításai megjelennek az [Azure Storage használata az Azure HDInsight-fürtökkel](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) témakörben. Ezen a [lapon](hive-walkthrough.md#upload)található az a folyamat, amelyen a .csv fájlokat fel kell tölteni a tárfiók alapértelmezett tárolójára.

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Hive-lekérdezések küldése
A Hive-lekérdezések a következők kel küldhetők el:

* [Hive-lekérdezések küldése a Hadoop-fürt fejnokján keresztül a Hadoop-fürt ben](#headnode)
* [Hive-lekérdezések küldése a Hive-szerkesztővel](#hive-editor)
* [Hive-lekérdezések küldése az Azure PowerShell-parancsokkal](#ps)

Hive lekérdezések SQL-szerű. Ha ismeri az SQL, előfordulhat, hogy a [Hive az SQL-felhasználók Cheat Sheet](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) hasznos.

Hive-lekérdezés küldésekor is szabályozhatja a kimenet i. Hive-lekérdezések, függetlenül attól, hogy a képernyőn, vagy egy helyi fájlt a főcsomóponton, vagy egy Azure blob.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Hive-lekérdezések küldése a Hadoop-fürt fejnokján keresztül a Hadoop-fürt ben
Ha a Hive-lekérdezés összetett, közvetlenül a Hadoop-fürt fő csomópontjában történő elküldése általában gyorsabb megforduláshoz vezet, mint egy Hive-szerkesztővel vagy Az Azure PowerShell-parancsfájlokkal való elküldés.

Jelentkezzen be a Hadoop-fürt főcsomópontjára, nyissa meg a Hadoop parancssort a főcsomópont asztalán, és írja be a parancsot `cd %hive_home%\bin`.

A Hadoop parancssorában háromféleképpen küldhet hive-lekérdezéseket:

* Közvetlenül
* '.hql' fájlok használata
* a Hive parancskonzollal

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Hive-lekérdezések küldése közvetlenül a Hadoop parancssorában.
Futtathatja a `hive -e "<your hive query>;` parancsot, például egyszerű Hive-lekérdezéseket küldhet közvetlenül a Hadoop parancssorból. Íme egy példa, ahol a piros mező körvonalazza a hive lekérdezést küldő parancsot, a zöld mező pedig a Hive-lekérdezés kimenetét.

![Hive-lekérdezés küldése a Hive-lekérdezés kimenetével](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Hive-lekérdezések küldése a'.hql' fájlokban
Ha a Hive-lekérdezés bonyolultabb, és több sort is tartalmazó, a parancssori vagy hive parancskonzollekérdezéseinek szerkesztése nem praktikus. Egy másik lehetőség, hogy a Hadoop-fürt fő csomópontjában egy szövegszerkesztőt használ a Hive-lekérdezések egy ".hql" fájlba történő mentéséhez a fő csomópont helyi címtárába. Ezután a Hive lekérdezés a '.hql' fájlban `-f` az alábbi argumentum használatával küldhető el:

    hive -f "<path to the '.hql' file>"

![Hive-lekérdezés egy '.hql' fájlban](./media/move-hive-tables/run-hive-queries-3.png)

**A Hive-lekérdezések végrehajtási állapotképernyő-nyomtatásának letiltása**

Alapértelmezés szerint a Hive-lekérdezés Hadoop parancssorban történő elküldése után a Térkép/Csökkentés feladat előrehaladása kikerül a képernyőre. A Térkép/A feladat előrehaladásának csökkentése című képképernyős `-S` nyomtatás letiltásához a parancssorban az "S" nagybetűs) argumentumot használhatja az alábbiak szerint:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Hive-lekérdezések küldése a Hive parancskonzolon.
Először is beléphet a Hive `hive` parancskonzolba, ha a Hadoop parancssorában futtatja a parancsot, majd elküldi a Hive-lekérdezéseket a Hive parancskonzolon. Íme egy példa. Ebben a példában a két piros mező kiemeli a Hive parancskonzolba való belépéshez használt parancsokat, illetve a Hive parancskonzolon elküldött Hive-lekérdezést. A zöld mező kiemeli a Hive-lekérdezés kimenetét.

![Nyissa meg a Hive parancskonzolt, és írja be a parancsot, tekintse meg a Hive-lekérdezés kimenetét](./media/move-hive-tables/run-hive-queries-2.png)

Az előző példák közvetlenül kimeneti a Hive lekérdezés eredményeit a képernyőn. A kimenetet a főcsomóponton lévő helyi fájlba vagy egy Azure-blobba is írhatja. Ezután más eszközök kelthet a Hive-lekérdezések kimenetének további elemzéséhez.

**Kimeneti hive lekérdezés eredményeit egy helyi fájlba.**
A Hive-lekérdezés eredményeinek a főcsomópont helyi könyvtárába való kimenetéhez a Hadoop parancssorában kell elküldenie a Hive-lekérdezést az alábbiak szerint:

    hive -e "<hive query>" > <local path in the head node>

A következő példában a Hive-lekérdezés kimenete `hivequeryoutput.txt` egy `C:\apps\temp`könyvtárba írt fájlba kerül.

![Hive-lekérdezés kimenete](./media/move-hive-tables/output-hive-results-1.png)

**Kimeneti hive-lekérdezés eredményei egy Azure-blobba**

A Hive-lekérdezés eredményeit is kimenetelheti egy Azure blobba, a Hadoop-fürt alapértelmezett tárolóján belül. A Hive lekérdezés ehhez a következő:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

A következő példában a Hive-lekérdezés kimenete `queryoutputdir` a Hadoop-fürt alapértelmezett tárolójában lévő blobkönyvtárba kerül. Itt csak meg kell adnia a könyvtár nevét, a blob neve nélkül. Hiba történik, ha könyvtár- és blobneveket `wasb:///queryoutputdir/queryoutput.txt`is megad, például .

![Hive-lekérdezés kimenete](./media/move-hive-tables/output-hive-results-2.png)

Ha megnyitja a Hadoop-fürt alapértelmezett tárolóját az Azure Storage Explorer használatával, láthatja a Hive-lekérdezés kimenetét az alábbi ábrán látható módon. A szűrőt (piros négyzettel kiemelve) alkalmazhatja, hogy csak a megadott betűkkel rendelkező blobot olvassa be a nevekben.

![Az Azure Storage Explorer a Hive-lekérdezés kimenetét jeleníti meg](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Hive-lekérdezések küldése a Hive-szerkesztővel
A Query Console (Hive Editor) is használhatja az űrlap URL-címét *https:\//\<Hadoop fürtnév>.azurehdinsight.net/Home/HiveEditor* egy webböngészőben. Be kell jelentkeznie a konzol látja ezt a konzolt, és így szüksége van a Hadoop-fürt hitelesítő adatait itt.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Hive-lekérdezések küldése az Azure PowerShell-parancsokkal
A PowerShell használatával is küldhet Hive-lekérdezéseket. További információt a [Hive-feladatok küldése a PowerShell használatával .](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md)

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Hive-adatbázis és -táblák létrehozása
A Hive-lekérdezések meg vannak osztva a [GitHub-tárházban,](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) és onnan letölthetők.

Itt van a Hive-lekérdezés, amely létrehoz egy Hive-táblát.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

A bedugandó mezők és egyéb konfigurációk leírásai az alábbiak:

* **adatbázis\>neve : a létrehozni kívánt adatbázis neve. \<** Ha csak az alapértelmezett adatbázist szeretnénk használni, a *"create database...*" lekérdezés elhagyható.
* **táblanév\>: a megadott adatbázison belül létrehozni kívánt tábla \<** neve. Ha az alapértelmezett adatbázist szeretnénk használni, a tábla \<közvetlenül\>hivatkozható * \<táblanévvel\> * az adatbázis neve nélkül.
* **mezőelválasztó:\>az elválasztó, amely a Hive-táblába feltöltendő adatfájl mezőit határolja. \<**
* **vonalelválasztó:\>az adatfájl sorait határolja elválasztó elválasztó. \<**
* **tárolási\>hely: az Azure Storage helyét a Hive-táblák adatainak mentéséhez. \<** Ha nem adja meg *a HELYTÁROLÓ \<helyét,\>* az adatbázis és a táblák alapértelmezés szerint a Hive-fürt alapértelmezett tárolójában tárolódnak a *struktúra/raktár/* könyvtárban. Ha meg szeretné adni a tárolási helyet, a tárolóhelynek az adatbázis és a táblák alapértelmezett tárolón belül kell lennie. Ezt a helyet a fürt alapértelmezett tárolójához viszonyítva *"wasb:///\<directory 1>/"* vagy *\<"wasb:///\<directory 1>/ könyvtár 2>/"* stb. A lekérdezés végrehajtása után a relatív könyvtárak jönnek létre az alapértelmezett tárolón belül.
* **TBLPROPERTIES("skip.header.line.count"="1")**: Ha az adatfájl fejlécsora van, akkor ezt a tulajdonságot hozzá kell adnia a *create table* query **végén.** Ellenkező esetben a fejlécsor rekordként töltődik be a táblába. Ha az adatfájlnem rendelkezik fejlécszóval, ez a konfiguráció elhagyható a lekérdezésben.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Adatok betöltése A Kaptár-táblákba
Itt van a Hive-lekérdezés, amely betölti az adatokat egy Hive-táblába.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* * \<\> * **\>blobadatok elérési útja: Ha a Hive-táblába feltöltendő blobfájl a HDInsight Hadoop-fürt alapértelmezett tárolójában található, a blobadatok elérési útjának "wasb:// könyvtár ebben a tárolóban>/ blobfájlnév>" formátumban \<** kell lennie. *\<\<* A blobfájl a HDInsight Hadoop-fürt egy további tárolójában is lehet. Ebben az esetben a * \<\> blobadatok elérési útjának* *"wasb://\<tárolónév>tárfiók név>.blob.core.windows.net/ \<\<blob fájlnév>"* formátumúnak kell lennie.

  > [!NOTE]
  > A Hive-táblába feltöltendő blobadatoknak a Hadoop-fürt tárfiókának alapértelmezett vagy további tárolójában kell lenniük. Ellenkező esetben a *LOAD DATA* lekérdezés sikertelen lesz arra panaszkodni, hogy nem tud hozzáférni az adatokhoz.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Speciális témakörök: particionált tábla és Hive-adatok orc formátumban való tárolása
Ha az adatok nagyok, a tábla particionálása előnyös a lekérdezések, amelyek csak a tábla néhány partíciójának vizsgálata szükséges. Például ésszerű egy webhely naplóadatainak dátum szerint való felosztása.

A Hive-táblák particionálása mellett a Hive-adatok at optimalizált soroszlopos (ORC) formátumban is tárolhatja. Az ORC formázásáról az <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">ORC-fájlok használata javítja a teljesítményt, amikor a Hive adatokat olvas, ír és dolgoz fel.</a>

### <a name="partitioned-table"></a>Particionált tábla
Itt van a Hive-lekérdezés, amely létrehoz egy particionált táblát, és betölti az adatokat.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Particionált táblák lekérdezésekénél ajánlott a partíciófeltételhozzáadása `where` a záradék **elején,** ami javítja a keresési hatékonyságot.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Hive-adatok tárolása ORC formátumban
Közvetlenül nem tölthet be adatokat a blobstorage-ból az ORC formátumban tárolt Hive-táblákba. Az alábbiakban azokat a lépéseket, amelyeket az Azure blobokból az ORC formátumban tárolt Hive-táblákba való betöltéséhez kell tennie.

Hozzon létre egy külső táblát **TÁROLT SZÖVEGFÁJL KÉNT,** és töltse be az adatokat a blob storage-ból a táblába.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Hozzon létre egy belső táblát ugyanazzal a sémával, mint az 1.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Az 1.

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Ha a TEXTFILE * \<táblaadatbázis neve\>.\< a külső szövegfájl-tábla\> névpartíciókat* tartalmaz, a `SELECT * FROM <database name>.<external textfile table name>` 3. Beszúrása az * \<adatbázis\>nevébe .\< Az ORC\> tábla neve* sikertelen az * \<adatbázis\>neve óta.\< Az ORC\> táblaneve* nem tartalmazza a partícióváltozót a táblaséma mezőjeként. Ebben az esetben külön ki kell jelölnie az * \<adatbázis\>nevébe beszúrandó mezőket.\< ORC tábla\> neve* az alábbiak szerint:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

A * \<külső szövegfájltábla nevét\> * biztonságosan eldobni, ha a következő lekérdezést használja, miután az összes adatot beillesztette az adatbázis * \<nevébe.\>\< ORC tábla\>neve:*

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Az eljárás követése után egy használatra kész, ORC formátumú adatokat felszolgáló táblázatnak kell lennie.  
