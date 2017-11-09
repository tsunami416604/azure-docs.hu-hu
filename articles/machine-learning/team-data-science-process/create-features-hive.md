---
title: "Hive-lekérdezésekkel Hadoop-fürtben lévő adatok funkciók létrehozása |} Microsoft Docs"
description: "Példák a szolgáltatások készítése az Azure HDInsight Hadoop-fürt tárolt adatokat a Hive-lekérdezéseket."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: e8a94c71-979b-4707-b8fd-85b47d309a30
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: a967a8fccfe0dc051a7cf3a4a2fcefad2a2f187f
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="create-features-for-data-in-an-hadoop-cluster-using-hive-queries"></a>Funkciók létrehozása az adatokhoz egy Hadoop-fürtben Hive-lekérdezések segítségével
Ez a dokumentum bemutatja, hogyan hozzon létre egy Azure HDInsight Hadoop-fürt Hive-lekérdezésekkel tárolt adatok funkciói. A Hive-lekérdezéseket beágyazott Hive felhasználó által megadott funkciókat (UDF), amelynek parancsfájlokat használja.

A szolgáltatások létrehozásához szükséges műveleteket memóriaigényes lehet. A Hive-lekérdezések teljesítményét kritikus fontosságú ebben az esetben lesz, és bizonyos paraméterek hangolása javítja. Ezek a paraméterek beállítása az utolsó szakaszban tárgyalt.

A lekérdezések, amelyek bemutatják példák jellemzőek a [NYC Taxi út adatok](http://chriswhong.com/open-data/foil_nyc_taxi/) forgatókönyvek is szerepelnek [GitHub-tárházban](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Ezeket a lekérdezéseket már rendelkezik az adatok séma van megadva, és készen áll elküldésre váró futtatásához. A végső szakaszban paramétereket, így növelhető a Hive-lekérdezések teljesítményét észlelheti a felhasználók is ismerteti.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Ez **menü** szolgáltatások adatok létrehozása a különböző környezetek leíró témakörök hivatkozásait. Ez a feladat Ez a lépés a [Team adatok tudományos folyamat (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Egy Azure storage-fiók létrehozása. Ha módosítania kell az utasításokat, lásd: [egy Azure Storage-fiók létrehozása](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* A HDInsight szolgáltatásban egy testreszabott Hadoop-fürt üzembe helyezve.  Ha módosítania kell az utasításokat, lásd: [testreszabása Azure HDInsight Hadoop-fürtök az Advanced Analytics](customize-hadoop-cluster.md).
* Az adatok az Azure HDInsight Hadoop-fürtök Hive táblák fel lett töltve. Ha még nem, kövesse [létrehozása és az adatok betöltése a Hive táblák](move-hive-tables.md) feltölteni az adatokat a Hive táblák először.
* Engedélyezve van a fürt távoli eléréséhez. Ha módosítania kell az utasításokat, lásd: [a Head csomópont a Hadoop-fürt eléréséhez](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Szolgáltatás létrehozása
Ez a szakasz néhány példa a módszereket, amelyben funkciókat is kell generálása Hive-lekérdezésekkel ismerteti. Miután létrehozta a további szolgáltatásokat, oszlopokként vegye fel a meglévő tábla, vagy hozzon létre egy új táblázat további funkciók és elsődleges kulcs, majd az eredeti táblázatban lehetne illeszteni. Az alábbiakban bemutatott példák:

1. [Gyakoriság alapú szolgáltatás létrehozása](#hive-frequencyfeature)
2. [A bináris osztályozási Kategorikus változók kockázata](#hive-riskfeature)
3. [Bontsa ki a szolgáltatásokat a DateTime típusú mező](#hive-datefeatures)
4. [Szolgáltatások kinyerése szövegmező](#hive-textfeatures)
5. [Kiszámíthatja GPS-koordináták közötti távolság](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Gyakoriság alapú szolgáltatás létrehozása
Érdemes gyakran kategorikus változó szintjének gyakoriságot, vagy több kategorikus változók szintjét egyes kombinációi gyakoriságát kiszámításához. Felhasználók a következő parancsfájl segítségével az e számítása:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>A bináris osztályozási Kategorikus változók kockázata
A bináris osztályozási igazolnia kell a nem numerikus kategorikus változók átalakítása numerikus szolgáltatások, a modellek használt csak numerikus szolgáltatások kerül. Ez történik, azáltal, hogy minden nem numerikus szinthez a numerikus kockázata. Ez a szakasz bemutatja néhány általános kockázati értékek (napló valószínűleg) kategorikus változó kiszámításához Hive-lekérdezéseket.

        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

Ebben a példában, változók `smooth_param1` és `smooth_param2` a kockázati értékek az adatokból számított sima értékre van beállítva. Kockázatok hogy a tartomány -Inf és Inf között. A kockázat > 0 azt jelzi, hogy a valószínűsége, hogy a cél 1-nél nagyobb, mint 0,5.

A kockázat után tábla kiszámítása, felhasználókat rendelhet kockázati értékek tábla csatlakoztatná a kockázat táblával. Az előző szakaszban megadott csatlakozó Hive-lekérdezést.

### <a name="hive-datefeatures"></a>Bontsa ki a szolgáltatásokat a Datetime mezők
Hive tartalmaz egy felhasználó által megadott függvények a datetime mezők feldolgozásához. A Hive, az alapértelmezett dátum és idő formátuma: "éééé-hh-nn 00:00:00" ("1970-01-01 12:21:32" például). Ez a szakasz bemutatja, bontsa ki a hónap, a DateTime típusú mező a hónap napját példákat és egyéb példák, amelyek a dátum/idő karakterlánc formátuma nem az alapértelmezett formátum dátum/idő karakterláncot az alapértelmezett formázása.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

A Hive-lekérdezést feltételezi, hogy a *&#60; DateTime típusú mező >* az alapértelmezett dátum és idő formátumban.

Egy DateTime típusú mező nem az alapértelmezett formátumban van, ha először a DateTime típusú mező átalakítása Unix időbélyegzőjét, és majd alakíthatja át a Unix időbélyeg dátum/idő karakterlánc, amely az alapértelmezett formátumban van szüksége. A dátum és idő formátuma alapértelmezett, ha felhasználók alkalmazhatja a beágyazott datetime felhasználó által megadott függvények szolgáltatások kibontásához.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Ebben a lekérdezésben Ha a *&#60; DateTime típusú mező >* rendelkezik a minta like *2015-03/26 12:04:39*, a *"&#60; a mintában a DateTime típusú mező >"* kell `'MM/dd/yyyy HH:mm:ss'`. Tesztelheti, hogy a felhasználók futtathatják

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

A *hivesampletable* ebben a lekérdezésben előre telepítve van az összes Azure HDInsight Hadoop-fürtök alapértelmezés szerint a fürtök kiosztásakor.

### <a name="hive-textfeatures"></a>Szolgáltatások kinyerése szövegmezők tartalma
A Hive tábla egy szövegmezőben, hogy szóközök határolja karakterláncot tartalmazó rendelkezik, a következő lekérdezés kicsomagolja a karakterláncot, és szót a karakterlánc hosszát.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Kiszámíthatja GPS koordinátákat közötti távolság
A jelen szakaszban megadott lekérdezést közvetlenül a következőt: Taxi út adatok alkalmazhatók. A lekérdezés célja egy beágyazott matematikai függvény a szolgáltatások létrehozásához Hive alkalmazásáról megjelenítése.

Az ebben a lekérdezésben használt mezőket a felvételi és dropoff helyeket nevű GPS koordinátáit *a felvételi\_hosszúság*, *a felvételi\_szélesség*, *dropoff\_hosszúság*, és *dropoff\_szélesség*. A felvétel és dropoff koordináták közvetlen távolságát számító lekérdezéseket a következők:

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

A két GPS-koordináták közötti távolság számító Matematikai egyenleteket található meg a <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type parancsfájlok</a> hely Peter Lapisu által létrehozott. A Javascript, a függvény a `toRad()` csak van *lat_or_lon*pi/180 *, amely radiánban megadott szög fok alakítja át. Itt *lat_or_lon* a szélesség vagy hosszúság. Mivel struktúra nem ad a függvény `atan2`, de a függvény `atan`, a `atan2` függvény megvalósítja `atan` a fenti Hive-lekérdezés segítségével a definícióját a megadott függvény <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Munkaterület létrehozása](./media/create-features-hive/atan2new.png)

A beágyazott felhasználó által megadott függvények található Hive teljes listáját a **beépített funkciók** a szakasz a <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="tuning"></a>Speciális témakörök: hangolási Hive paraméterek lekérdezési teljesítmény javítása
Előfordulhat, hogy az alapértelmezett paraméterbeállítások Hive fürt nem alkalmas a Hive-lekérdezéseket és az adatokat, amelyek a lekérdezések feldolgozás alatt. Ebben a szakaszban arról lesz szó néhány paraméter, amely észlelheti a felhasználók, amelyek javítják a Hive-lekérdezések teljesítményét. Felhasználók kell hozzáadnia a lekérdezések a lekérdezéseket az adatok feldolgozása előtt hangolása paraméter.

1. **Java halommemória terület**: lekérdezések nagy adatkészletek csatlakozni, vagy hosszú rekordjának feldolgozásáért **elegendő szabad terület halommemória** egyik előforduló hibákat. Ez a paraméter beállításával szabályozható *mapreduce.map.java.opts* és *mapreduce.task.io.sort.mb* kívánt értékekre. Például:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Ez a paraméter Java halommemória területre 4GB memóriát foglal le, majd is teszi rendezés hatékonyabb több memóriát oszt ki azt. Célszerű lejátszás a megoldást, ha bármely halommemória terület kapcsolatos hiba hibák állnak fenn.

1. **Az elosztott Fájlrendszerbeli blokkméret**: Ez a paraméter állandóként állítja be, a fájlrendszer által tárolt adatokat a legkisebb egysége. Például ha az elosztott Fájlrendszerbeli blokkméret 128MB, majd mérete adatot legalább és legfeljebb 128MB tárolódik egyetlen blokkot tartalmaz, amely nagyobb, mint 128MB számára engedélyezett a felesleges blokkok adatainak közben. Egy nagyon kis blokkméretet kiválasztása hatására nagy terhek Hadoop, mert a név csomópont található a megfelelő blokkot, a fájl vonatkozó számos további kérelmeket. A javasolt beállítás foglalkozó gigabájt (vagy nagyobb) adat:
   
        set dfs.block.size=128m;
2. **Hive join művelet optimalizálása**: közben összekapcsolási műveletek térkép/csökkentse keretében általában kerül sor a csökkentse fázisban, egyes esetekben hatalmas növekedését elérhető illesztések ütemezésével a térkép fázisban (más néven "mapjoins"). Közvetlen Hive ehhez, amikor csak lehetséges, állítsa be:
   
        set hive.auto.convert.join=true;
3. **A Hive mappers számát**: közben Hadoop lehetővé teszi a felhasználónak szűkítő adjon meg, a száma mappers általában a felhasználó nem állítható be. Amely lehetővé teszi, hogy ez a szám a vezérlő bizonyos fokú körben, hogy válassza körültekintően a Hadoop változók *mapred.min.split.size* és *mapred.max.split.size* minden leképezés méretének feladat határozza meg:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Általában az alapértelmezett érték *mapred.min.split.size* 0, az *mapred.max.split.size* van **Long.MAX** és az *dfs.block.size* 64 MB. Ahogyan azt láthatja, megadott adatok mérete hangolása ezeket a paramétereket "beállítása" őket lehetővé teszi hangolására használható mappers száma.
4. Még más néhány **speciális beállítások** Hive optimalizálási teljesítmény alatt szerepelnek. Ezek lehetővé teszik rendelve, és csökkentheti a feladatok számára fenntartott memória mérete, és elősegíti a teljesítmény tökéletesítse. Ellenőrizze a következőket kell figyelembe venni, hogy a *mapreduce.reduce.memory.mb* nem lehet nagyobb, mint a Hadoop-fürt egyes feldolgozó csomópontok fizikai memória méretét.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

