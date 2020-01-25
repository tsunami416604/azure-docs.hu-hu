---
title: Struktúra-táblák létrehozása és adatok betöltése a blob Storage-ban – csoportos adatelemzési folyamat
description: Kaptár-lekérdezések használatával létrehozhat kaptár-táblákat, és betöltheti az Azure Blob Storage-ból. Partíciós struktúra táblái és a lekérdezési teljesítmény javítása érdekében használja az optimalizált sorok oszlopos (ORK) formátumát.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722526"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Struktúra-táblák létrehozása és adatok betöltése az Azure Blob Storage

Ez a cikk olyan általános struktúra-lekérdezéseket mutat be, amelyek struktúrát hoznak létre az Azure Blob Storage-ból, és az adatok betöltése. Bizonyos útmutatást a kaptár-táblák particionálásával és az optimalizált sor oszlopos (ORK) formázással is biztosít a lekérdezési teljesítmény javítása érdekében.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik a következővel:

* Létrehozott egy Azure Storage-fiókot. Ha útmutatásra van szüksége, tekintse meg [Az Azure Storage-fiókok](../../storage/common/storage-introduction.md)című témakört.
* Testreszabott Hadoop-fürt kiépítve a HDInsight szolgáltatással.  Ha útmutatásra van szüksége, tekintse meg [a fürtök beállítása a HDInsight-ben](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)című témakört.
* Engedélyezve van a távoli hozzáférés a fürthöz, bejelentkezve, és megnyitotta a Hadoop parancssori konzolt. Ha útmutatásra van szüksége, tekintse meg a [Apache Hadoop-fürtök kezelése](../../hdinsight/hdinsight-administer-use-portal-linux.md)című témakört.

## <a name="upload-data-to-azure-blob-storage"></a>Adatok feltöltése az Azure Blob Storage-ba
Ha létrehozott egy Azure-beli virtuális gépet az Azure-beli [virtuális gépek speciális elemzéshez való beállítása](../../machine-learning/data-science-virtual-machine/overview.md)című részben leírtak szerint, a parancsfájlt le kell tölteni a *C:\\felhasználók\\\<Felhasználónév\>\\dokumentumok\\adatelemzési parancsfájlok* könyvtára a virtuális gépen. Ezek a kaptár-lekérdezések csak az Adatséma és az Azure Blob Storage konfigurációjának megadását igénylik a megfelelő mezőkben, hogy készen álljanak a beküldésre.

Feltételezzük, hogy a kaptárak táblái nem **tömörített** táblázatos formátumban jelennek meg, és az adatokat feltöltötte a Hadoop-fürt által használt alapértelmezett (vagy a további) tárolóba.

Ha azt szeretné, hogy a **New York-i taxival**kapcsolatos információkért a következőket kell elvégeznie:

* **töltse le** a 24 [New York-i taxi Trip](https://www.andresmh.com/nyctaxitrips) -adatfájlt (12 utazási fájl és 12 viteldíj-fájl),
* **bontsa** ki az összes fájlt. csv-fájlba, majd
* **töltse fel** őket az Azure Storage-fiók alapértelmezett (vagy megfelelő) tárolójába; az ilyen fiókra vonatkozó beállítások az [Azure Storage és az Azure HDInsight-fürtök használata](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) című témakörben jelennek meg. Ezen a [lapon](hive-walkthrough.md#upload)megtalálhatók a. CSV fájlok a Storage-fiók alapértelmezett tárolójába való feltöltésének folyamata.

## <a name="submit"></a>Struktúra-lekérdezések beküldése
A struktúra lekérdezéseit a következő használatával lehet elküldeni:

* [Kaptár-lekérdezések elküldése a Hadoop parancssorból a Hadoop-fürt átjárócsomóponthoz](#headnode)
* [Kaptár-lekérdezések elküldése a kaptár-szerkesztővel](#hive-editor)
* [Kaptár-lekérdezések elküldése Azure PowerShell parancsokkal](#ps)

A kaptár-lekérdezések SQL-szerűek. Ha ismeri az SQL-t, hasznosnak találhatja az [SQL-felhasználók Cheat adatlapjának struktúráját](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) .

A kaptár-lekérdezések elküldésekor azt is megadhatja, hogy a rendszer a struktúra lekérdezéseit a képernyőn, vagy a fő csomóponton vagy egy Azure-blobon lévő helyi fájlra irányítsa.

### <a name="headnode"></a>Kaptár-lekérdezések elküldése a Hadoop parancssorból a Hadoop-fürt átjárócsomóponthoz
Ha a kaptár-lekérdezés összetett, akkor közvetlenül a Hadoop-fürt fő csomópontján küldi el, hogy a rendszer általában gyorsabban bekapcsolja, mint a kaptár-szerkesztővel vagy Azure PowerShell parancsfájlokkal való elküldés.

Jelentkezzen be a Hadoop-fürt fő csomópontjára, nyissa meg a Hadoop parancssort a fő csomópont asztalán, és írja be a következő parancsot: `cd %hive_home%\bin`.

A Hadoop parancssorában háromféle módon lehet beküldeni a kaptár-lekérdezéseket:

* közvetlenül
* a ". HQL" fájl használata
* a kaptár parancssori konzollal

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>A Hadoop parancssorában közvetlenül elküldheti a kaptár-lekérdezéseket.
Az egyszerű kaptár-lekérdezéseket közvetlenül a Hadoop parancssorból is futtathatja, például `hive -e "<your hive query>;`. Íme egy példa, ahol a vörös mező a kaptár-lekérdezést elküldő parancsot írja le, a zöld mező pedig a kaptár lekérdezés kimenetét ismerteti.

![A kaptár-lekérdezés kimenetből való elküldésére szolgáló parancs](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Kaptár-lekérdezések küldése a ". HQL" fájlokban
Ha a kaptár-lekérdezés bonyolultabb, és több sorral rendelkezik, a parancssorban vagy a kaptár-konzolon végzett szerkesztési lekérdezések nem praktikusak. Egy másik lehetőség, hogy egy szövegszerkesztőt használ a Hadoop-fürt fő csomópontjában, hogy mentse a kaptár lekérdezéseit egy ". HQL" fájlba a fő csomópont helyi könyvtárában. Ezután a ". HQL" fájlban lévő kaptár-lekérdezést a `-f` argumentummal lehet elküldeni a következő módon:

    hive -f "<path to the '.hql' file>"

![Struktúra lekérdezése egy ". HQL" fájlban](./media/move-hive-tables/run-hive-queries-3.png)

**A folyamat állapotának kihagyása a kaptár-lekérdezések nyomtatásakor**

Alapértelmezés szerint a Hadoop parancssorban a kaptár-lekérdezés elküldése után a rendszer kinyomtatja a Térkép/csökkentés feladatot a képernyőn. Ha el szeretné tiltani a Térkép/a feladatok csökkentésének folyamatát, a parancssorban egy `-S` ("S") argumentumot is használhat a parancssorban az alábbiak szerint:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Kaptár-lekérdezések elküldése a kaptár parancssori konzolon.
Azt is megteheti, hogy először a Hadoop parancssorban `hive` futtatja a kaptár parancssori konzolt, majd elküldi a kaptár-lekérdezéseket a kaptár-konzolon. Íme egy példa. Ebben a példában a két piros mező kiemeli a kaptár-konzol megadásához használt parancsokat, valamint a kaptár parancssori konzolon küldött struktúra-lekérdezést. A zöld mező kiemeli a kaptár lekérdezés kimenetét.

![Nyissa meg a kaptár-parancssori konzolt, és írja be a parancsot, és tekintse meg a struktúra lekérdezés](./media/move-hive-tables/run-hive-queries-2.png)

Az előző példákban közvetlenül a kaptár-lekérdezés eredményei láthatók a képernyőn. A kimenetet egy helyi fájlba is írhatja a fő csomóponton vagy egy Azure-blobon. Ezután más eszközökkel is elemezheti a kaptár-lekérdezések kimenetét.

**A kimeneti struktúra lekérdezési eredményei helyi fájlba kerülnek.**
A kaptár lekérdezési eredményeinek a fő csomóponton egy helyi könyvtárába való kimenetéhez a következő módon el kell küldenie a kaptár-lekérdezést a Hadoop parancssorba:

    hive -e "<hive query>" > <local path in the head node>

A következő példában a kaptár-lekérdezés kimenete egy fájlba `hivequeryoutput.txt` a könyvtárban `C:\apps\temp`.

![A kaptár-lekérdezés kimenete](./media/move-hive-tables/output-hive-results-1.png)

**Kimeneti struktúra lekérdezési eredményei egy Azure-blobhoz**

A kaptár lekérdezési eredményeit egy Azure-blobba is kiválaszthatja a Hadoop-fürt alapértelmezett tárolóján belül. A kaptár lekérdezése a következő:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

A következő példában a kaptár-lekérdezés kimenete egy blob könyvtárba kerül, `queryoutputdir` a Hadoop-fürt alapértelmezett tárolóján belül. Itt csak a könyvtár nevét kell megadnia a blob neve nélkül. A rendszer hibát jelez, ha a címtár-és a Blobok nevét is megadja, például `wasb:///queryoutputdir/queryoutput.txt`.

![A kaptár-lekérdezés kimenete](./media/move-hive-tables/output-hive-results-2.png)

Ha a Hadoop-fürt alapértelmezett tárolóját Azure Storage Explorer használatával nyitja meg, a struktúra-lekérdezés kimenete a következő ábrán látható módon jelenik meg. Alkalmazhatja a szűrőt (a piros mezőben kiemelve), hogy csak a megadott betűkkel rendelkező blobot kérje le a nevekben.

![Azure Storage Explorer a kaptár-lekérdezés kimenetét mutatja](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a>Kaptár-lekérdezések elküldése a kaptár-szerkesztővel
A lekérdezési konzolt (kaptár-szerkesztő) is használhatja a *https:\//\<HADOOP-fürt neve >. azurehdinsight. net/Home/HiveEditor* webböngészőbe való beírásával. Be kell jelentkeznie a jelen konzolon, ezért szüksége lesz a Hadoop-fürt hitelesítő adataira.

### <a name="ps"></a>Kaptár-lekérdezések elküldése Azure PowerShell parancsokkal
A PowerShell használatával is elküldheti a kaptár-lekérdezéseket. Útmutatásért lásd a [kaptár-feladatok elküldése a PowerShell használatával](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md)című témakört.

## <a name="create-tables"></a>Struktúra-adatbázis és-táblák létrehozása
A kaptár-lekérdezések meg vannak osztva a [GitHub-tárházban](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) , és onnan tölthetők le.

Itt látható az a kaptár-lekérdezés, amely létrehoz egy struktúra-táblázatot.

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

Itt láthatók a bekapcsolni és egyéb konfigurációkhoz szükséges mezők leírása:

* **\<adatbázis neve\>** : a létrehozni kívánt adatbázis neve. Ha csak az alapértelmezett adatbázist szeretné használni, akkor az "*adatbázis létrehozása..* ." lekérdezés elhagyható.
* **\<táblanév\>** : a megadott adatbázison belül létrehozni kívánt tábla neve. Ha az alapértelmezett adatbázist szeretné használni, a tábla *\<táblanév* közvetlenül is megadható,\>\<adatbázis neve nélkül\>.
* **\<mező elválasztó\>** : az adatfájlban a kaptár táblába feltöltendő mezőket határoló elválasztó.
* **\<line elválasztó\>** : az adatfájl sorait határoló elválasztó.
* **\<tárolási hely\>** : az Azure Storage-hely, amely a kaptárak adatfájljainak mentésére szolgáló helyet. Ha nem ad meg *helyet \<tárolási hely\>* , a rendszer alapértelmezés szerint az adatbázist és a táblákat a kaptár-fürt alapértelmezett tárolójában, a kaptárban/ *raktárban/* könyvtárban tárolja. Ha meg szeretné adni a tárolási helyet, a tárolási helynek az adatbázis és a táblák alapértelmezett tárolójában kell lennie. Ezt a helyet a fürt alapértelmezett tárolójához viszonyítva kell megadni, a következő formátumban: *"wasb:///\<Directory 1 >/"* vagy *"wasb:///\<Directory 1 >/\<Directory 2 >/* " stb. A lekérdezés végrehajtása után a relatív könyvtárak az alapértelmezett tárolón belül jönnek létre.
* **TBLPROPERTIES ("skip. header. line. Count" = "1")** : Ha az adatfájlnak van fejléce, akkor a *tábla létrehozása* lekérdezés **végén** hozzá kell adnia ezt a tulajdonságot. Ellenkező esetben a fejlécsor rekordként van betöltve a táblába. Ha az adatfájlnak nincs fejléce, akkor ez a konfiguráció kihagyható a lekérdezésben.

## <a name="load-data"></a>Adatgyűjtés a kaptár tábláiba
Itt látható a kaptár-lekérdezés, amely betölti az adathalmazt egy struktúra-táblába.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **\<blob-adatelérési út\>** : Ha a kaptárba feltöltendő blob-fájl a HDInsight Hadoop-fürt alapértelmezett tárolójában van, akkor a *blob-adat\>ek\<i elérési útja* *"wasb://\<directory ebben a tárolóban >/\<blob fájlnév >"* . A blob fájl a HDInsight Hadoop-fürt további tárolójában is szerepelhet. Ebben az esetben *\<blob-adat\>elérési útját* *"wasb://\<tároló neve >\<Storage-fiók neve >. blob. Core. Windows. net/\<blob file Name >"* .

  > [!NOTE]
  > A kaptár táblába feltöltendő blob-adatmennyiségnek a Hadoop-fürthöz tartozó Storage-fiók alapértelmezett vagy további tárolójában kell lennie. Ellenkező esetben a *betöltési adat* lekérdezése sikertelenül panaszkodik, hogy nem fér hozzá az adathoz.
  >
  >

## <a name="partition-orc"></a>Speciális témakörök: particionált tábla és a kaptárak információinak tárolása ork formátumban
Ha az adat nagy méretű, a tábla particionálása olyan lekérdezések esetében hasznos, amelyek csak a tábla néhány partíciójának vizsgálatához szükségesek. Például érdemes lehet egy webhely naplózási adatokát dátumokkal particionálni.

A kaptár-táblázatok particionálásán kívül hasznos lehet a kaptár-adatok tárolására is az optimalizált sorok oszlopos (ORK) formátumában. Az ork formázásával kapcsolatos további információkért lásd: az <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">ork-fájlok használata javítja a teljesítményt, ha a struktúra adatokat olvas, ír és dolgoz</a>fel.

### <a name="partitioned-table"></a>Particionált tábla
Itt látható a kaptár-lekérdezés, amely létrehoz egy particionált táblát, és betölti az betöltést.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Particionált táblák lekérdezésekor ajánlott a `where` záradék **elejére** hozzáadni a partíciós feltételt, ami javítja a keresési hatékonyságot.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Struktúra-adattárolók az ork formátumban
Az adatok nem tölthetők be közvetlenül a blob Storage-ból olyan struktúra-táblákba, amelyek az ork formátumban vannak tárolva. Az alábbi lépésekkel végezheti el az adatok Azure-blobokból való betöltését az ork-formátumban tárolt struktúra-táblákba.

Hozzon létre egy **TEXTFILE tárolt** külső táblázatot, és töltse be az adatok a blob Storage-ból a táblába.

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

Hozzon létre egy belső táblázatot ugyanazzal a sémával, mint az 1. lépésben szereplő külső táblával, ugyanazzal a mezővel, és tárolja a kaptár-adatforrást az ork formátumban.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Válassza ki az 1. lépésben szereplő külső tábla adatait, és illessze be az ork táblázatba

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Ha a TEXTFILE tábla *\<adatbázis neve\>.\<külső TEXTFILE táblanév\>* rendelkezik partíciókkal, a 3. lépésben a `SELECT * FROM <database name>.<external textfile table name>` parancs kiválasztja a partíció változót a visszaadott adatkészletben mezőként. Szúrja be azt a *\<adatbázisnév\>.\<ork-tábla neve\>* sikertelen, mert *\<adatbázisnév\>.\<az ork-tábla neve\>* nem rendelkezik a partíciós változóval a tábla sémája mezőjében. Ebben az esetben kifejezetten ki kell választania a beszúrandó mezőket *\<az adatbázis neve\>.\<ork-táblázat neve\>* a következőképpen:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

A *\<külső szövegfájl neve\>* , ha a következő lekérdezést használja, miután az összes adattal beszúrta *\<adatbázisnév\>.\<az ork-tábla neve\>* :

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Az eljárás követése után egy, az ork formátumú, használatra kész táblát tartalmazó táblával kell rendelkeznie.  
