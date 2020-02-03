---
title: Hive táblák létrehozása, és betöltheti az adatokat a Blob storage - csoportos adatelemzési folyamat
description: Hive-lekérdezések használata a Hive táblákat hozhat létre, és betöltheti az adatokat az Azure blob storage-ból. Hive-táblák partíció, és használja az optimalizált sor Oszlopalapú (ORC) formázása a lekérdezés teljesítményének javítása érdekében.
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
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Hive táblák létrehozása és az adatok betöltése az Azure Blob Storage-ból

Ez a cikk bemutatja az általános Hive-lekérdezések, amelyek Hive táblákat hozhat létre, és az adatok betöltése az Azure blob storage-ból. Hive Táblák particionálása és az optimalizált sor Oszlopalapú (ORC) lekérdezési teljesítmény javításához formázás használatával útmutatást is tartalmaz.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Létrehozott egy Azure Storage-fiókot. Ha útmutatásra van szüksége, tekintse meg [Az Azure Storage-fiókok](../../storage/common/storage-introduction.md)című témakört.
* A HDInsight szolgáltatással egyéni Hadoop-fürt kiépítése.  Ha útmutatásra van szüksége, tekintse meg [a fürtök beállítása a HDInsight-ben](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)című témakört.
* A fürthöz engedélyezett távelérési jelentkezett be, és a Hadoop parancssori konzolt megnyitva. Ha útmutatásra van szüksége, tekintse meg a [Apache Hadoop-fürtök kezelése](../../hdinsight/hdinsight-administer-use-portal-linux.md)című témakört.

## <a name="upload-data-to-azure-blob-storage"></a>Adatfeltöltés az Azure blob storage-bA
Ha létrehozott egy Azure-beli virtuális gépet az Azure-beli [virtuális gépek speciális elemzéshez való beállítása](../../machine-learning/data-science-virtual-machine/overview.md)című részben leírtak szerint, a parancsfájlt le kell tölteni a *C:\\felhasználók\\\<Felhasználónév\>\\dokumentumok\\adatelemzési parancsfájlok* könyvtára a virtuális gépen. Ezek a kaptár-lekérdezések csak az Adatséma és az Azure Blob Storage konfigurációjának megadását igénylik a megfelelő mezőkben, hogy készen álljanak a beküldésre.

Feltételezzük, hogy a kaptárak táblái nem **tömörített** táblázatos formátumban jelennek meg, és az adatokat feltöltötte a Hadoop-fürt által használt alapértelmezett (vagy a további) tárolóba.

Ha azt szeretné, hogy a **New York-i taxival**kapcsolatos információkért a következőket kell elvégeznie:

* **töltse le** a 24 [New York-i taxi Trip](https://www.andresmh.com/nyctaxitrips) -adatfájlt (12 utazási fájl és 12 viteldíj-fájl),
* **bontsa** ki az összes fájlt. csv-fájlba, majd
* **töltse fel** őket az Azure Storage-fiók alapértelmezett (vagy megfelelő) tárolójába; az ilyen fiókra vonatkozó beállítások az [Azure Storage és az Azure HDInsight-fürtök használata](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) című témakörben jelennek meg. Ezen a [lapon](hive-walkthrough.md#upload)megtalálhatók a. CSV fájlok a Storage-fiók alapértelmezett tárolójába való feltöltésének folyamata.

## <a name="submit"></a>Struktúra-lekérdezések beküldése
Hive-lekérdezések használatával küldheti:

* [Kaptár-lekérdezések elküldése a Hadoop parancssorból a Hadoop-fürt átjárócsomóponthoz](#headnode)
* [Kaptár-lekérdezések elküldése a kaptár-szerkesztővel](#hive-editor)
* [Kaptár-lekérdezések elküldése Azure PowerShell parancsokkal](#ps)

Hive-lekérdezések a következő SQL-szerű. Ha ismeri az SQL-t, hasznosnak találhatja az [SQL-felhasználók Cheat adatlapjának struktúráját](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) .

Hive-lekérdezés elküldésekor is szabályozhatja és Hive-lekérdezések eredményének legyen szó a képernyőn, vagy egy helyi fájlt a fő csomópontot vagy az Azure-blobba.

### <a name="headnode"></a>Kaptár-lekérdezések elküldése a Hadoop parancssorból a Hadoop-fürt átjárócsomóponthoz
Ha a Hive-lekérdezés túl összetett, elküldené azokat közvetlenül a fő csomópont a Hadoop a fürt általában vezet gyorsabban kapcsolja, mint a Hive szerkesztőben, vagy az Azure PowerShell-szkriptekkel elküldené azokat.

Jelentkezzen be a Hadoop-fürt fő csomópontjára, nyissa meg a Hadoop parancssort a fő csomópont asztalán, és írja be a következő parancsot: `cd %hive_home%\bin`.

Küldje el a Hive-lekérdezések a Hadoop parancssor három módja van:

* közvetlenül
* a ". HQL" fájl használata
* a Hive-parancs konzollal

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Közvetlenül a Hadoop parancssor Hive-lekérdezések elküldéséhez.
Az egyszerű kaptár-lekérdezéseket közvetlenül a Hadoop parancssorból is futtathatja, például `hive -e "<your hive query>;`. Íme egy példa, ahol a vörös ismerteti, amelyek a Hive-lekérdezést küldi el a parancsot, és a zöld mezőt felvázolja a Hive-lekérdezés kimenete.

![Hive-lekérdezést a Hive-lekérdezés kimenete a parancs](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Kaptár-lekérdezések küldése a ". HQL" fájlokban
Ha a Hive-lekérdezés bonyolultabb, és több sor tartozik, a parancssor vagy a Hive parancskonzol lekérdezések szerkesztése nem célszerű. Egy másik lehetőség, hogy egy szövegszerkesztőt használ a Hadoop-fürt fő csomópontjában, hogy mentse a kaptár lekérdezéseit egy ". HQL" fájlba a fő csomópont helyi könyvtárában. Ezután a ". HQL" fájlban lévő kaptár-lekérdezést a `-f` argumentummal lehet elküldeni a következő módon:

    hive -f "<path to the '.hql' file>"

![Struktúra lekérdezése egy ". HQL" fájlban](./media/move-hive-tables/run-hive-queries-3.png)

**A folyamat állapotának kihagyása a kaptár-lekérdezések nyomtatásakor**

Alapértelmezés szerint a Hadoop parancssor, Hive-lekérdezés elküldése után a Map/Reduce feladat előrehaladását a kinyomtatni a képernyőn. Ha el szeretné tiltani a Térkép/a feladatok csökkentésének folyamatát, a parancssorban egy `-S` ("S") argumentumot is használhat a parancssorban az alábbiak szerint:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Hive-parancs konzolon Hive-lekérdezések elküldéséhez.
Azt is megteheti, hogy először a Hadoop parancssorban `hive` futtatja a kaptár parancssori konzolt, majd elküldi a kaptár-lekérdezéseket a kaptár-konzolon. Íme egy példa. Ebben a példában a két piros mezőkben adja meg a Hive parancskonzolról használt parancsok és a Hive-lekérdezés Hive parancskonzolról, illetve elküldött jelöljön ki. A zöld mezőt a Hive-lekérdezés kimenete emeli ki.

![Nyissa meg a Hive parancssori konzolt és adja meg a parancsot, Hive-lekérdezés kimenetének megtekintéséhez](./media/move-hive-tables/run-hive-queries-2.png)

Az előző példák közvetlenül kimeneti a Hive-lekérdezés eredményeit a képernyőn. Is kiírhatja a kimenetet egy helyi fájlba a fő csomópont, vagy Azure-blobba. Más eszközök segítségével, majd tovább a Hive-lekérdezések kimenetének elemzése.

**A kimeneti struktúra lekérdezési eredményei helyi fájlba kerülnek.**
A kimenet egy helyi könyvtárba, a fő csomópontot a Hive-lekérdezés eredményeit, küldje el a Hive-lekérdezést a Hadoop parancssor a következőképpen kell:

    hive -e "<hive query>" > <local path in the head node>

A következő példában a kaptár-lekérdezés kimenete egy fájlba `hivequeryoutput.txt` a könyvtárban `C:\apps\temp`.

![Hive-lekérdezés kimenete](./media/move-hive-tables/output-hive-results-1.png)

**Kimeneti struktúra lekérdezési eredményei egy Azure-blobhoz**

A Hive-lekérdezés eredményeit az Azure-blobba, az alapértelmezett tároló, a Hadoop-fürtön belül is készíthető. A Hive-lekérdezés a következőképpen történik:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

A következő példában a kaptár-lekérdezés kimenete egy blob könyvtárba kerül, `queryoutputdir` a Hadoop-fürt alapértelmezett tárolóján belül. Itt csak kell a címtár nevét, a blob neve nélkül. A rendszer hibát jelez, ha a címtár-és a Blobok nevét is megadja, például `wasb:///queryoutputdir/queryoutput.txt`.

![Hive-lekérdezés kimenete](./media/move-hive-tables/output-hive-results-2.png)

A Hadoop-fürt Azure Storage Explorer használatával az alapértelmezett tároló megnyitása után megjelenik az alábbi ábrán látható módon a Hive-lekérdezés kimenete. A szűrő (vörös kiemelt) és lekérdezheti csak a megadott nevében szereplő betűket a blob alkalmazhat.

![A Hive-lekérdezés kimenete bemutató az Azure Storage Explorerrel](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a>Kaptár-lekérdezések elküldése a kaptár-szerkesztővel
A lekérdezési konzolt (kaptár-szerkesztő) is használhatja a *https:\//\<HADOOP-fürt neve >. azurehdinsight. net/Home/HiveEditor* webböngészőbe való beírásával. Kell lennie a lásd: Ez a konzol bejelentkezett, és ezért szüksége a Hadoop fürt hitelesítő adatait.

### <a name="ps"></a>Kaptár-lekérdezések elküldése Azure PowerShell parancsokkal
PowerShell használatával Hive-lekérdezések elküldéséhez. Útmutatásért lásd a [kaptár-feladatok elküldése a PowerShell használatával](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md)című témakört.

## <a name="create-tables"></a>Struktúra-adatbázis és-táblák létrehozása
A kaptár-lekérdezések meg vannak osztva a [GitHub-tárházban](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) , és onnan tölthetők le.

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

* **\<adatbázis neve\>** : a létrehozni kívánt adatbázis neve. Ha csak az alapértelmezett adatbázist szeretné használni, akkor az "*adatbázis létrehozása..* ." lekérdezés elhagyható.
* **\<táblanév\>** : a megadott adatbázison belül létrehozni kívánt tábla neve. Ha az alapértelmezett adatbázist szeretné használni, a tábla *\<táblanév* közvetlenül is megadható,\>\<adatbázis neve nélkül\>.
* **\<mező elválasztó\>** : az adatfájlban a kaptár táblába feltöltendő mezőket határoló elválasztó.
* **\<line elválasztó\>** : az adatfájl sorait határoló elválasztó.
* **\<tárolási hely\>** : az Azure Storage-hely, amely a kaptárak adatfájljainak mentésére szolgáló helyet. Ha nem ad meg *helyet \<tárolási hely\>* , a rendszer alapértelmezés szerint az adatbázist és a táblákat a kaptár-fürt alapértelmezett tárolójában, a kaptárban/ *raktárban/* könyvtárban tárolja. Ha azt szeretné, adja meg a tárolási helyét, a tárolási hely nem lehet belül az adatbázis és a táblák az alapértelmezett tároló. Ezt a helyet a fürt alapértelmezett tárolójához viszonyítva kell megadni, a következő formátumban: *"wasb:///\<Directory 1 >/"* vagy *"wasb:///\<Directory 1 >/\<Directory 2 >/* " stb. A lekérdezés végrehajtása után a relatív könyvtárak az alapértelmezett tárolón belül jönnek létre.
* **TBLPROPERTIES ("skip. header. line. Count" = "1")** : Ha az adatfájlnak van fejléce, akkor a *tábla létrehozása* lekérdezés **végén** hozzá kell adnia ezt a tulajdonságot. Ellenkező esetben a fejlécsort betöltése a táblába egy rekord formájában. Az adatfájl nem rendelkezik egy fejlécsort, ha ezt a konfigurációt a lekérdezésben elhagyható.

## <a name="load-data"></a>Adatgyűjtés a kaptár tábláiba
Itt van a Hive-lekérdezést, amely adatokat tölt be egy Hive-táblába.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **\<blob-adatelérési út\>** : Ha a kaptárba feltöltendő blob-fájl a HDInsight Hadoop-fürt alapértelmezett tárolójában van, akkor a *blob-adat\>ek\<i elérési útja* *"wasb://\<directory ebben a tárolóban >/\<blob fájlnév >"* . A blob-fájlt egy további tárolóban a HDInsight Hadoop-fürt is lehet. Ebben az esetben *\<blob-adat\>elérési útját* *"wasb://\<tároló neve >\<Storage-fiók neve >. blob. Core. Windows. net/\<blob file Name >"* .

  > [!NOTE]
  > A Hive-táblába való feltöltésre Blobadatok nem az alapértelmezett vagy a storage-fiók, a Hadoop-fürt kiegészítő tároló lehet. Ellenkező esetben a *betöltési adat* lekérdezése sikertelenül panaszkodik, hogy nem fér hozzá az adathoz.
  >
  >

## <a name="partition-orc"></a>Speciális témakörök: particionált tábla és a kaptárak információinak tárolása ork formátumban
Az adatok mérete nagy, ha a tábla particionálása akkor előnyös, a lekérdezések csak a táblázat néhány partíciók vizsgálata. Például célszerű, a webhely a naplóadatok particionálásához dátuma alapján.

Hive Táblák particionálása, mellett emellett akkor is előnyös, ha a Hive-adatok tárolására a optimalizált sor Oszlopalapú (ORC) formátumban. Az ork formázásával kapcsolatos további információkért lásd: az <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">ork-fájlok használata javítja a teljesítményt, ha a struktúra adatokat olvas, ír és dolgoz</a>fel.

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

Particionált táblák lekérdezésekor ajánlott a `where` záradék **elejére** hozzáadni a partíciós feltételt, ami javítja a keresési hatékonyságot.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Struktúra-adattárolók az ork formátumban
Nem lehet közvetlenül betöltse a blob storage-ból a ORC formátumban tárolt Hive-táblákat. A következő lépéseket, amelyek a kell tennie betölteni adatokat Azure-blobok Hive-tábláihoz ORC formátumban tárolja.

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
> Ha a TEXTFILE tábla *\<adatbázis neve\>.\<külső TEXTFILE táblanév\>* rendelkezik partíciókkal, a 3. lépésben a `SELECT * FROM <database name>.<external textfile table name>` parancs kiválasztja a partíció változót a visszaadott adatkészletben mezőként. Szúrja be azt a *\<adatbázisnév\>.\<ork-tábla neve\>* sikertelen, mert *\<adatbázisnév\>.\<az ork-tábla neve\>* nem rendelkezik a partíciós változóval a tábla sémája mezőjében. Ebben az esetben kifejezetten ki kell választania a beszúrandó mezőket *\<az adatbázis neve\>.\<ork-táblázat neve\>* a következőképpen:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

A *\<külső szövegfájl neve\>* , ha a következő lekérdezést használja, miután az összes adattal beszúrta *\<adatbázisnév\>.\<az ork-tábla neve\>* :

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Jelen eljárás után készen áll a használatra ORC formátumban-adatokat tartalmazó táblát kell rendelkeznie.  
