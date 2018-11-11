---
title: Hive táblák létrehozása és az adatok betöltése az Azure Blob Storage-ból |} A Microsoft Docs
description: Hive táblákat hozhat létre és tölt be adatokat a hive-táblákban blob
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: cff9280d-18ce-4b66-a54f-19f358d1ad90
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: c1cbd523b8c74bf1221dc47b832c35e9119493a9
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346286"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Hive táblák létrehozása és az adatok betöltése az Azure Blob Storage-ból

Ez a cikk bemutatja az általános Hive-lekérdezések, amelyek Hive táblákat hozhat létre, és az adatok betöltése az Azure blob storage-ból. Hive Táblák particionálása és az optimalizált sor Oszlopalapú (ORC) lekérdezési teljesítmény javításához formázás használatával útmutatást is tartalmaz.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Létrehozott egy Azure storage-fiókot. Ha utasításokat van szüksége, tekintse meg [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md).
* A HDInsight szolgáltatással egyéni Hadoop-fürt kiépítése.  Ha utasításokat van szüksége, tekintse meg [testreszabása az Azure HDInsight Hadoop-fürtök fejlett analitikai](customize-hadoop-cluster.md).
* A fürthöz engedélyezett távelérési jelentkezett be, és a Hadoop parancssori konzolt megnyitva. Ha utasításokat van szüksége, tekintse meg [a fő csomópont, Hadoop-fürt eléréséhez](customize-hadoop-cluster.md).

## <a name="upload-data-to-azure-blob-storage"></a>Adatfeltöltés az Azure blob storage-bA
Ha létrehozott egy Azure virtuális gépen található utasításokat követve [állítsa be az Azure virtuális gép fejlett analitikai](../data-science-virtual-machine/setup-virtual-machine.md), a parancsfájl kell letöltötte a *C:\\felhasználók \\ \<felhasználónév\>\\dokumentumok\\Data Science parancsfájlok* könyvtárban a virtuális gépen. A Hive-lekérdezések csak akkor van szükség, hogy csatlakoztassa a saját adatok séma és az Azure blob storage konfigurációs beküldésre kell a megfelelő mezőkbe.

Feltételezzük, hogy az adatokat a Hive-táblákat szerepel egy **tömörítetlen** táblázatos formátumú, és az, hogy az adatokat fel lett töltve az alapértelmezett (vagy egy további) a tárfiók a Hadoop-fürt által használt tároló.

Ha meg szeretné gyakorlat a **NYC Taxi Útadatok**, kell tennie:

* **Töltse le** az 24 [NYC Taxi Útadatok](http://www.andresmh.com/nyctaxitrips) (12 Útadatokat tartalmazó fájlt, és 12 diszkont fájlok),
* **Csomagolja ki** tartalmazó .csv fájlt, az összes fájlt, majd
* **Töltse fel** őket az Azure storage-fiók leírt eljárást által létrehozott alapértelmezett (vagy megfelelő tárolót) a [testreszabása az Azure HDInsight Hadoop-fürtök fejlett analitikai folyamat és technológia](customize-hadoop-cluster.md)témakör. A folyamat a .csv-fájlok feltöltése az alapértelmezett tároló a tárfiókban található ezen [oldal](hive-walkthrough.md#upload).

## <a name="submit"></a>Hogyan lehet elküldeni a Hive-lekérdezések
Hive-lekérdezések használatával küldheti:

1. [A Hadoop-fürt átjárócsomópontjával Hadoop parancssor használatával Hive-lekérdezések elküldéséhez](#headnode)
2. [A Hive szerkesztőben a Hive-lekérdezések elküldéséhez](#hive-editor)
3. [Azure PowerShell-parancsokkal Hive-lekérdezések elküldéséhez](#ps)

Hive-lekérdezések a következő SQL-szerű. Ha ismeri az SQL, hasznosnak találhatja a [Hive SQL-felhasználók Adatlap lap](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) hasznos.

Hive-lekérdezés elküldésekor is szabályozhatja és Hive-lekérdezések eredményének legyen szó a képernyőn, vagy egy helyi fájlt a fő csomópontot vagy az Azure-blobba.

### <a name="headnode"></a> 1. A Hadoop-fürt átjárócsomópontjával Hadoop parancssor használatával Hive-lekérdezések elküldéséhez
Ha a Hive-lekérdezés túl összetett, elküldené azokat közvetlenül a fő csomópont a Hadoop a fürt általában vezet gyorsabban kapcsolja, mint a Hive szerkesztőben, vagy az Azure PowerShell-szkriptekkel elküldené azokat.

Jelentkezzen be a Hadoop-fürt fő csomópontjának, nyissa meg a Hadoop parancssor az asztalon, a fő csomópontot, és adja meg a parancs `cd %hive_home%\bin`.

Küldje el a Hive-lekérdezések a Hadoop parancssor három módja van:

* közvetlenül
* .hql fájlok használata
* a Hive-parancs konzollal

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Közvetlenül a Hadoop parancssor Hive-lekérdezések elküldéséhez.
Például a parancs futtatásával `hive -e "<your hive query>;` egyszerű Hive-lekérdezéseket közvetlenül a Hadoop parancssor elküldéséhez. Íme egy példa, ahol a vörös ismerteti, amelyek a Hive-lekérdezést küldi el a parancsot, és a zöld mezőt felvázolja a Hive-lekérdezés kimenete.

![Munkaterület létrehozása](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>.Hql fájlokat a Hive-lekérdezések elküldéséhez
Ha a Hive-lekérdezés bonyolultabb, és több sor tartozik, a parancssor vagy a Hive parancskonzol lekérdezések szerkesztése nem célszerű. Alternatív, hogy a Hadoop-fürt fő csomópontjának egy szövegszerkesztő segítségével egy helyi könyvtárban, az átjárócsomópont .hql fájlba mentése a Hive-lekérdezéseket. A Hive-lekérdezést a .hql fájlban beküldhető használatával, majd a `-f` argumentum az alábbiak szerint:

    hive -f "<path to the .hql file>"

![Munkaterület létrehozása](./media/move-hive-tables/run-hive-queries-3.png)

**Folyamat állapota képernyő nyomtatása Hive-lekérdezések elrejtése**

Alapértelmezés szerint a Hadoop parancssor, Hive-lekérdezés elküldése után a Map/Reduce feladat előrehaladását a kinyomtatni a képernyőn. Le a képernyő nyomtatása a Map/Reduce-feladatok előrehaladásának, használhatja a argumentumként `-S` ("S" nagybetűvel) a parancs a sor a következő:

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Hive-parancs konzolon Hive-lekérdezések elküldéséhez.
Először is adhatja meg a Hive parancskonzolról parancs futtatásával `hive` a Hadoop parancssor, majd küldje el a Hive-lekérdezések Hive parancs konzolon. Íme egy példa. Ebben a példában a két piros mezőkben adja meg a Hive parancskonzolról használt parancsok és a Hive-lekérdezés Hive parancskonzolról, illetve elküldött jelöljön ki. A zöld mezőt a Hive-lekérdezés kimenete emeli ki.

![Munkaterület létrehozása](./media/move-hive-tables/run-hive-queries-2.png)

Az előző példák közvetlenül kimeneti a Hive-lekérdezés eredményeit a képernyőn. Is kiírhatja a kimenetet egy helyi fájlba a fő csomópont, vagy Azure-blobba. Más eszközök segítségével, majd tovább a Hive-lekérdezések kimenetének elemzése.

**A kimeneti Hive-lekérdezés eredményeit egy helyi fájlba.**
A kimenet egy helyi könyvtárba, a fő csomópontot a Hive-lekérdezés eredményeit, küldje el a Hive-lekérdezést a Hadoop parancssor a következőképpen kell:

    hive -e "<hive query>" > <local path in the head node>

A következő példa a Hive-lekérdezés kimenete egy fájlba írt `hivequeryoutput.txt` címtárban `C:\apps\temp`.

![Munkaterület létrehozása](./media/move-hive-tables/output-hive-results-1.png)

**Azure-blobba kimeneti Hive-lekérdezés eredményei**

A Hive-lekérdezés eredményeit az Azure-blobba, az alapértelmezett tároló, a Hadoop-fürtön belül is készíthető. A Hive-lekérdezés a következőképpen történik:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

A következő példa a Hive-lekérdezés kimenete egy blob könyvtár írt `queryoutputdir` belül a a Hadoop-fürt alapértelmezett tárolója. Itt csak kell a címtár nevét, a blob neve nélkül. Hiba lépett fel, ha könyvtárat és a blob nevét is, mint például biztosít `wasb:///queryoutputdir/queryoutput.txt`.

![Munkaterület létrehozása](./media/move-hive-tables/output-hive-results-2.png)

A Hadoop-fürt Azure Storage Explorer használatával az alapértelmezett tároló megnyitása után megjelenik az alábbi ábrán látható módon a Hive-lekérdezés kimenete. A szűrő (vörös kiemelt) és lekérdezheti csak a megadott nevében szereplő betűket a blob alkalmazhat.

![Munkaterület létrehozása](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2. A Hive szerkesztőben a Hive-lekérdezések elküldéséhez
A Lekérdezéskonzol (Hive szerkesztő) is használhatja az űrlap egy URL-cím megadásával *https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor* kifejezés webböngészőbe. Kell lennie a lásd: Ez a konzol bejelentkezett, és ezért szüksége a Hadoop fürt hitelesítő adatait.

### <a name="ps"></a> 3. Azure PowerShell-parancsokkal Hive-lekérdezések elküldéséhez
PowerShell használatával Hive-lekérdezések elküldéséhez. Útmutatásért lásd: [elküldése Hive-feladatok PowerShell-lel](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Hive-adatbázis és tábla létrehozása
A Hive-lekérdezések található meg vannak osztva a [GitHub-adattár](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) és letölthető innen.

Itt van a Hive-lekérdezést, amely létrehoz egy Hive-táblába.

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

Az alábbiakban a leírásokat a mezőket, amelyek kell csatlakoztatni és más konfigurációkat:

* **<database name>**: a létrehozni kívánt adatbázis nevét. Ha csak át szeretné használni az alapértelmezett adatbázis, a lekérdezés *adatbázis létrehozása...*  elhagyható.
* **<table name>**: a tábla, amely a megadott adatbázison belül létrehozni kívánt nevét. Ha szeretné használni az alapértelmezett adatbázis, a tábla lehet közvetlenül hivatkozni által *<table name>* nélkül <database name>.
* **<field separator>**: az elválasztó, amely az adatfájlban, fel kell tölteni a Hive-táblában a mezők.
* **<line separator>**: az elválasztó, amely az adatfájl sorainak.
* **<storage location>**: az Azure tárolási hely menteni az adatokat a Hive-táblákat. Ha nincs megadva *hely <storage location>* , az adatbázis és a táblák *hive/adatraktár/* könyvtárat a az alapértelmezett tároló alapértelmezés szerint a Hive-fürt. Ha azt szeretné, adja meg a tárolási helyét, a tárolási hely nem lehet belül az adatbázis és a táblák az alapértelmezett tároló. Ezen a helyen van, a fürt formátumban az alapértelmezett tároló viszonyított helyét kell nevezett *"wasb: / / / < 1 könyvtár > /"* vagy *"wasb: / / / < 1 könyvtár > / < 2. könyvtár > /"* stb. A lekérdezés végrehajtása, miután a relatív könyvtárak hoz létre a rendszer az alapértelmezett tároló.
* **TBLPROPERTIES("Skip.Header.line.Count"="1")**: Ha a fájlt egy fejlécsort, fel kell vennie, ez a tulajdonság **végén** , a *tábla létrehozása* lekérdezés. Ellenkező esetben a fejlécsort betöltése a táblába egy rekord formájában. Az adatfájl nem rendelkezik egy fejlécsort, ha ezt a konfigurációt a lekérdezésben elhagyható.

## <a name="load-data"></a>Adatok betöltése a Hive-táblák
Itt van a Hive-lekérdezést, amely adatokat tölt be egy Hive-táblába.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **<path to blob data>**: Ha a blob fájl a Hive-táblában való feltöltésre az alapértelmezett tárolóban a HDInsight Hadoop-fürt a *<path to blob data>* formátumúnak kell lennie *"wasb: / / /<directory in this container> / <blob file name>'*. A blob-fájlt egy további tárolóban a HDInsight Hadoop-fürt is lehet. Ebben az esetben *<path to blob data>* formátumúnak kell lennie *"wasb: / /<container name><storage account name>.blob.core.windows.net/<blob file name>"*.

  > [!NOTE]
  > A Hive-táblába való feltöltésre Blobadatok nem az alapértelmezett vagy a storage-fiók, a Hadoop-fürt kiegészítő tároló lehet. Ellenkező esetben a *adatok betöltése* lekérdezés nem sikerült panaszkodik, hogy az adatok nem férhet hozzá.
  >
  >

## <a name="partition-orc"></a>Speciális témakörök: particionált tábla- és tároló Hive adatok ORC formátumban
Az adatok mérete nagy, ha a tábla particionálása akkor előnyös, a lekérdezések csak a táblázat néhány partíciók vizsgálata. Például célszerű, a webhely a naplóadatok particionálásához dátuma alapján.

Hive Táblák particionálása, mellett emellett akkor is előnyös, ha a Hive-adatok tárolására a optimalizált sor Oszlopalapú (ORC) formátumban. További információ a ORC formázásáról lásd <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">használatával ORC-fájlokat javítja a teljesítményt, ha Hive olvasása, írása, és adatokat feldolgozó</a>.

### <a name="partitioned-table"></a>Particionált tábla
Itt van a Hive-lekérdezést, amely létrehoz egy particionált táblához, és adatokat tölt be azt.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

A particionált táblákat kérdezi le, amikor javasoljuk, hogy a partíció feltétel hozzáadása a **kezdete** , a `where` záradék, ez növeli a jelentősen keresés hatékonyságát.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Hive-adatok Store ORC formátumban
Nem lehet közvetlenül betöltse a blob storage-ból a ORC formátumban tárolt Hive-táblákat. A következő lépéseket, amelyek a kell tennie betölteni adatokat Azure-blobok Hive-tábláihoz ORC formátumban tárolja.

Külső tábla létrehozása **tárolt AS TEXTFILE** , és betöltheti az adatokat blob storage-ból a táblába.

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

Egy belső tábla ugyanazzal a sémával, mint az azonos a mezőhatárolóval 1. lépésben a külső tábla létrehozása, és a Hive-adatok tárolása az ORC formátum.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Válassza az 1. lépésben a külső tábla az adatok és az ORC-táblába beszúrandó

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Ha a TEXTFILE tábla  *<database name>.<external textfile table name>* rendelkezik a partíciókat, a 3. LÉPÉSBEN a `SELECT * FROM <database name>.<external textfile table name>` parancs kiválasztja a partíció változó a visszaadott adatkészlet mezőként. Szúr be őket a  *<database name>.<ORC table name>* nem sikerül óta  *<database name>.<ORC table name>* a partíció változó nem rendelkezik a következő tábla sémáját mezőként. Ebben az esetben kell kifejezetten válassza ki a mezőket a beszúrásával  *<database name>.<ORC table name>* az alábbiak szerint:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Biztonságos dobja el a *<external textfile table name>* Ha használja a következő lekérdezést az összes adat után már be van szúrva *<database name>.<ORC table name>*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Jelen eljárás után készen áll a használatra ORC formátumban-adatokat tartalmazó táblát kell rendelkeznie.  
