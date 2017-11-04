---
title: "Hive táblák létrehozása és az adatok betöltése az Azure Blob Storage |} Microsoft Docs"
description: "Hive táblák létrehozása és a hive táblák blob adatainak betöltése"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: cff9280d-18ce-4b66-a54f-19f358d1ad90
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.openlocfilehash: c90c3d3c0effd68a4a5962d4d097fccbdc3fee56
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Hive táblák létrehozása és az adatok betöltése az Azure Blob Storage
Ebből a témakörből megismerheti, hogy a Hive táblák létrehozása és az adatok betöltése az Azure blob storage általános Hive-lekérdezéseket. Néhány is útmutatást a Hive Táblák particionálása és az optimalizált sor oszlopos (ORC) lekérdezés teljesítmény javítása érdekében formázás használatával.

Ez **menü** betöltik az adatokat tároló környezetekben, ahol az adatok is tárolhatók és feldolgozhatók, a csapat adatok tudományos folyamat (TDSP) során módját leíró témakörök hivatkozásait.

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Egy Azure storage-fiók létrehozása. Ha módosítania kell az utasításokat, lásd: [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md).
* A HDInsight szolgáltatásban egy testreszabott Hadoop-fürt üzembe helyezve.  Ha módosítania kell az utasításokat, lásd: [testreszabása az Azure HDInsight Hadoop-fürtök Speciális elemzésekre](customize-hadoop-cluster.md).
* A fürthöz engedélyezett távelérési jelentkezett be, és a Hadoop parancssori konzol megnyitása. Ha módosítania kell az utasításokat, lásd: [a Head csomópont a Hadoop-fürt eléréséhez](customize-hadoop-cluster.md#headnode).

## <a name="upload-data-to-azure-blob-storage"></a>Adatok feltöltése az Azure blob storage
Ha a megjelenő utasításokat követve létrehozott Azure virtuális gép [állítson be egy Azure virtuális gép speciális elemzésekre](../data-science-virtual-machine/setup-virtual-machine.md), a parancsfájl kell lettek töltve az *C:\\felhasználók \\ \<felhasználónév\>\\dokumentumok\\adatok tudományos parancsfájlok* könyvtárhoz, a virtuális gépen. A következő Hive-lekérdezések csak szükséges, csatlakoztassa a saját adatok séma és az Azure blob storage konfiguráció készen áll arra, hogy elküldhesse a megfelelő mezőket.

Feltételezzük, hogy a Hive táblák adatai van egy **tömörítetlen** táblázatos formátumban, és, hogy az adatok az alapértelmezett (vagy egy további) feltöltötte-e a tárfiók a Hadoop-fürt által használt tároló.

Ha meg szeretné gyakorlat a **NYC Taxi út adatok**, kell:

* **Töltse le** a 24 [NYC Taxi út adatok](http://www.andresmh.com/nyctaxitrips) (12 út fájlok és 12 jegy ára fájlok),
* **Csomagolja ki** .csv fájlokat, az összes fájl, majd
* **Töltse fel** őket az Azure storage-fiók ismertetett eljárással létrehozott alapértelmezett (vagy megfelelő tárolót) a [testreszabása az Azure HDInsight Hadoop-fürtök Advanced Analytics folyamat és a technológia](customize-hadoop-cluster.md)témakör. A folyamat a .csv fájlok feltöltése az alapértelmezett tároló a tárfiók itt található [lap](hive-walkthrough.md#upload).

## <a name="submit"></a>Hogyan lehet elküldeni a Hive-lekérdezések
Hive-lekérdezések segítségével küldheti el:

1. [A Hadoop-fürt headnode keresztül Hadoop parancssori Hive-lekérdezések elküldése](#headnode)
2. [A Hive szerkesztő Hive-lekérdezések elküldése](#hive-editor)
3. [Küldje el az Azure PowerShell-parancsokkal Hive-lekérdezések](#ps)

Hive-lekérdezések SQL-szerű. Ha ismeri az SQL, azt tapasztalhatja a [SQL felhasználók Cheat lap Hive](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) hasznos.

A Hive-lekérdezés elküldésekor azt is meghatározhatja, és Hive-lekérdezések eredményének a képernyőn vagy az átjárócsomópont helyi fájlba vagy egy Azure-blobba legyen.

### <a name="headnode"></a> 1. A Hadoop-fürt headnode keresztül Hadoop parancssori Hive-lekérdezések elküldése
A Hive lekérdezés túl összetett, ha közvetlenül a Hadoop átjárócsomópontjához a továbbítás fürt általában vezet, mint a Hive szerkesztő vagy az Azure PowerShell parancsfájlok továbbítás gyorsabban kapcsolja.

Jelentkezzen be a Hadoop-fürt átjárócsomópontjához, nyissa meg a Hadoop parancssort az átjárócsomópont asztalán, és írja be a parancs `cd %hive_home%\bin`.

Háromféleképpen elküldeni a Hive-lekérdezéseket a Hadoop parancssorban:

* közvetlenül
* .hql fájlok használata
* a Hive parancs konzol

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Küldje el közvetlenül a Hadoop parancssori Hive-lekérdezéseket.
Például a parancs futtatása `hive -e "<your hive query>;` egyszerű Hive-lekérdezéseket közvetlenül a Hadoop parancssori elküldeni. Íme egy példa, ahol a vörös téglalappal ismerteti a parancs, amellyel a Hive-lekérdezést, és a zöld lista ismerteti a Hive-lekérdezések eredményének.

![Munkaterület létrehozása](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Küldje el a Hive-lekérdezéseket a .hql fájlok
A Hive-lekérdezések bonyolultabb, és több vonal van, a parancssor vagy a Hive parancskonzolról lekérdezések szerkesztése esetén nem gyakorlati. A másik lehetőség a Hadoop-fürt átjárócsomópontjához egy szövegszerkesztő segítségével egy helyi könyvtárban, az átjárócsomópont .hql fájlba mentése a Hive-lekérdezéseket. A Hive-lekérdezést a .hql fájl segítségével küldheti el, majd a `-f` argumentum az alábbiak szerint:

    hive -f "<path to the .hql file>"

![Munkaterület létrehozása](./media/move-hive-tables/run-hive-queries-3.png)

**Ne jelenjen meg többé folyamat állapota képernyőn nyomtatás Hive-lekérdezések**

Alapértelmezés szerint a Hadoop parancssorban Hive-lekérdezés elküldése után a térkép vagy csökkentse a feladat előrehaladását nyomtatása a képernyőn. A képernyő Nyomtatás a térkép vagy csökkentse a feladat előrehaladását a mellőzése, argumentumot is használhat `-S` ("S" nagybetűvel) a parancsban sor az alábbiak szerint:

    hive -S -f "<path to the .hql file>"
.    Hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Küldje el a Hive parancskonzolról Hive-lekérdezéseket.
Először is megadhat a Hive parancskonzolról parancs futtatásával `hive` a Hadoop parancssor, és küldje el a Hive parancskonzolról Hive-lekérdezéseket. Íme egy példa. Ebben a példában a két piros mezőkbe írja be a Hive parancskonzolról használt parancsok, és a Hive-lekérdezés Hive parancskonzolról, illetve benyújtott jelöljön ki. A zöld mező a Hive-lekérdezések eredményének mutatja be.

![Munkaterület létrehozása](./media/move-hive-tables/run-hive-queries-2.png)

Az előző példákban közvetlenül kimeneti a Hive-lekérdezések eredményeit a képernyőn. Is kiírhatja a kimenetet egy helyi fájlba az átjárócsomóponthoz, vagy egy Azure-blobba. Más eszközök segítségével, majd további a Hive-lekérdezések kimenetének elemzése.

**A kimeneti Hive lekérdezés eredményei egy helyi fájlba.**
Kimeneti Hive lekérdezés eredményei az átjárócsomópont helyi könyvtárába, van a következőképpen elküldeni a Hive-lekérdezést a Hadoop parancssorban:

    hive -e "<hive query>" > <local path in the head node>

A következő példa a Hive-lekérdezések eredményének egy fájlba írása `hivequeryoutput.txt` könyvtárban `C:\apps\temp`.

![Munkaterület létrehozása](./media/move-hive-tables/output-hive-results-1.png)

**Kimeneti Hive lekérdezés eredményei egy Azure-blobba**

A Hive lekérdezés eredményeit az Azure-blobba, az alapértelmezett tároló, a Hadoop-fürt belül is készíthető. A Hive-lekérdezést a következőképpen történik:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

A következő példában a Hive-lekérdezések eredményének beíródik egy blob könyvtár `queryoutputdir` a Hadoop-fürt alapértelmezett tárolóban. Itt csak szüksége arra, hogy a könyvtár nevét, a blob neve nélkül. Hiba történt kívánja megadni a címtár és a blob neve, mint például `wasb:///queryoutputdir/queryoutput.txt`.

![Munkaterület létrehozása](./media/move-hive-tables/output-hive-results-2.png)

Ha az alapértelmezett tároló, a Hadoop-fürt használata Azure Tártallózó megnyitni, láthatja az alábbi ábrán látható módon a Hive-lekérdezések eredményének. Csak a nevek a megadott meghajtóbetűjellel rendelkező blob beolvasása a szűrő (vörös téglalappal által kiemelt) is alkalmazhat.

![Munkaterület létrehozása](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2. A Hive szerkesztő Hive-lekérdezések elküldése
A lekérdezés konzol (Hive szerkesztő) írja be egy URL-cím is használható *https://&#60; Hadoop-fürt neve >.azurehdinsight.net/Home/HiveEditor* egy webböngészőbe. Végezhetik el a további részletekért lásd a konzol bejelentkezett, és így a Hadoop fürthöz hitelesítő adatait itt kell.

### <a name="ps"></a> 3. Küldje el az Azure PowerShell-parancsokkal Hive-lekérdezések
PowerShell elküldeni a Hive-lekérdezéseket is használható. Útmutatásért lásd: [elküldeni a Hive feladatok PowerShell-lel](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Hive-adatbázis és tábla létrehozása
A Hive-lekérdezéseket is meg van osztva a [GitHub-tárházban](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) , és innen tölthető le.

Ez a Hive lekérdezés, amely egy Hive táblát hoz létre.

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

Az alábbiakban a mezőket, amelyeknek kell csatlakoztatni és egyéb beállításokra leírása:

* **& #60; adatbázis neve >**: a létrehozni kívánt adatbázis nevét. Ha szeretné használni az alapértelmezett adatbázis, a lekérdezés *adatbázis létrehozása... * kihagyható.
* **& #60; tábla neve >**: a táblázat, amely szeretne létrehozni a megadott adatbázis nevét. Ha szeretné használni az alapértelmezett adatbázis, a tábla is közvetlenül elé *& #60; tábla neve >* nélkül & #60; adatbázis neve >.
* **& #60; mező elválasztó >**: az elválasztó, amely az adatfájlban a Hive tábla feltölteni kívánt mezőket.
* **& #60; Sorelválasztó >**: az elválasztó, amely az adatfájl sorainak.
* **& #60; tárolási helye >**: az Azure storage-helyre menteni az adatokat a Hive táblák. Ha nincs megadva *hely & #60; a tárolási helye >*, az adatbázis és a táblázatok tárolja *hive/adatraktár/* könyvtárban lévő az alapértelmezett tároló alapértelmezés szerint a Hive-fürt. Ha meg szeretné határozni a tárolási helye, a tárolási hely nem lehet az adatbázis és a táblák alapértelmezett tárolóban. Ezen a helyen van, a fürt formátumban viszonyítva az alapértelmezett tároló helye elé *"wasb: / / / & #60; könyvtár 1 > /"* vagy *"wasb: / / / & #60; 1 könyvtár > / & #60; directory 2 > / "*stb. A lekérdezés végrehajtása után a relatív könyvtárak alapértelmezett tárolóban jönnek létre.
* **TBLPROPERTIES("Skip.Header.line.Count"="1")**: Ha az adatfájl fejléc, fel kell vennie a tulajdonság **végén** , a *tábla létrehozása* lekérdezés. Ellenkező esetben a fejlécsort a táblázathoz rekordként be van töltve. Ha az adatok fájlban nincs fejléc, ez a konfiguráció elhagyható a lekérdezésben.

## <a name="load-data"></a>Adatok betöltése a Hive táblák
Ez a Hive-lekérdezés, amely adatokat tölt be egy Hive tábla.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **& #60; blob adatok elérési útja >**: az alapértelmezett tárolóban, a HDInsight Hadoop-fürt, a blob-fájlt fel kell tölteni a Hive tábla esetén a *& #60; blob adatok elérési útja >* formátumban kell megadni *" wasb: / / / & #60; ebben a tárolóban directory > / & #60; blob fájl neve >'*. A blob fájl is lehet egy további tárolót a HDInsight Hadoop-fürt. Ebben az esetben *& #60; blob adatok elérési útja >* formátumban kell megadni *"wasb: / / & #60; a tároló neve > @& #60; tárfiók neve >.blob.core.windows.net/ & #60; a blob-fájl neve >'*.

  > [!NOTE]
  > A blobadatokat feltöltendő Hive táblát nem lehet az alapértelmezett vagy a tárfiók a Hadoop-fürt további tárolóban. Ellenkező esetben a *adatok betöltése* lekérdezés nem sikerült panaszos, hogy az adatok nem férhet hozzá.
  >
  >

## <a name="partition-orc"></a>Speciális témakörök: particionált tábla és a tároló Hive adatok ORC formátumban
Az adatok nagy, a tábla particionáló akkor hasznos, amelyeket csak a táblázat néhány partíciók vizsgálata lekérdezések. Például akkor ésszerű a naplózási adatokat egy webhely particionálásához dátuma alapján.

Hive Táblák particionálása mellett célszerű is a Hive adatokat tároló optimalizált sor oszlopos (ORC) formátumban. A formázás ORC további információkért lásd: <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">használatával ORC fájlokat javítja a teljesítményt, ha Hive olvasása, írása, és adatfeldolgozás</a>.

### <a name="partitioned-table"></a>Particionált tábla
Ez a Hive-lekérdezés, amely létrehoz egy particionált táblához, és adatokat tölt be.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Particionált táblák lekérdezésekor javasoljuk, hogy a partíció feltétel hozzáadása a **kezdete** , a `where` záradék, ez növeli a jelentősen keresés hatékonyságát.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Hive adattárolásra ORC formátumban
Ön nem közvetlenül adatok betöltése az blob-tároló az ORC formátumban tárolt Hive táblákat. Az alábbiakban a lépéseket, amelyek a kell venni a betöltése az Azure-ból adatokat blobok Hive táblákat ORC formátumban tárolja.

Létrehoz egy külső táblát **tárolt AS TEXTFILE** és az adatok betöltése az blob storage a táblába.

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

Hozzon létre egy belső tábla, mint a külső tábla 1. lépésben az azonos a mezőhatárolóval ugyanazon séma, és a Hive adatokat tároló ORC formátumban.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Válassza az 1. lépésben a külső tábla az adatok és az ORC táblázat beszúrása

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Ha a TEXTFILE tábla *& #60; adatbázis neve >. & #60; külső textfile táblanév >* partíciókkal rendelkezik, a 3. LÉPÉSBEN, a `SELECT * FROM <database name>.<external textfile table name>` parancsot választja a partíció változó a visszaadott adatkészlet mező. Beszúrása be azt a *& #60; adatbázis neve >. & #60; ORC táblanév >* óta sikertelen *& #60; adatbázis neve >. & #60; ORC táblanév >* nem rendelkezik a partíció változó mezőként az a következő tábla sémáját. Ebben az esetben kell kifejezetten válassza ki a mezőket a beszúrni *& #60; adatbázis neve >. & #60; ORC táblanév >* az alábbiak szerint:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Biztonságos dobja el a *& #60; külső textfile táblanév >* amikor után minden adat a következő lekérdezéssel e behelyezve *& #60; adatbázis neve >. & #60; ORC táblanév >*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

A következő eljárással, után készen áll a használatra ORC formátumú adatokat tartalmazó táblát kell rendelkeznie.  
