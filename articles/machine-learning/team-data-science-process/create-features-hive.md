---
title: Azure HDInsight Hadoop-fürtben lévő adatokhoz való funkciók létrehozása – Csapatadat-elemzési folyamat
description: Példák olyan Hive-lekérdezésekre, amelyek az Azure HDInsight Hadoop-fürtben tárolt adatokban hoznak létre funkciókat.
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
ms.openlocfilehash: c926aac3ea4360793ff52b616a55dc6198357c8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721778"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Hadoop-fürt adatainak létrehozása Hive-lekérdezésekkel
Ez a dokumentum bemutatja, hogyan hozhat létre funkciókat az Azure HDInsight Hadoop-fürtben tárolt adatokhoz Hive-lekérdezések használatával. Ezek a Hive-lekérdezések beágyazott Hive felhasználó által definiált függvényeket (UDF-eket) használnak, amelyek parancsfájljai vannak megadva.

A funkciók létrehozásához szükséges műveletek memóriaigényesek lehetnek. A Hive-lekérdezések teljesítménye kritikusabbá válik az ilyen esetekben, és bizonyos paraméterek finomhangolásával javítható. Ezeknek a paramétereknek a finomhangolását az utolsó szakasz tárgyalja.

A bemutatott lekérdezések példái a [New York-i Taxi-fuvaradatok](https://chriswhong.com/open-data/foil_nyc_taxi/) forgatókönyveire vonatkoznak, a [GitHub-tárházban](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)is találhatók. Ezek a lekérdezések már rendelkeznek megadott adatsémával, és készen állnak a futtatásra. Az utolsó szakaszban a felhasználók által hangolható paramétereket, hogy a Hive-lekérdezések teljesítményét javítani is tárgyalja.

Ez a feladat egy lépés a [Csapat adatelemzési folyamatában (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy a következőket feltételezi:

* Létrehozott egy Azure-tárfiókot. Ha utasításokra van szüksége, olvassa [el az Azure Storage-fiók létrehozása című témakört.](../../storage/common/storage-account-create.md)
* Kiépített egy testreszabott Hadoop-fürtöt a HDInsight szolgáltatással.  Ha utasításokra van szüksége, olvassa [el az Azure HDInsight Hadoop-fürtök testreszabása speciális elemzésekhez című](customize-hadoop-cluster.md)témakört.
* Az adatok feltöltve lettek a Hive-táblákaz Azure HDInsight Hadoop-fürtökben. Ha nem, kövesse [az adatok létrehozása és betöltése a Hive-táblák](move-hive-tables.md) adatok feltöltéséhez Hive táblák először.
* Engedélyezve van a fürt távoli elérése. Ha utasításokra van szüksége, [olvassa el a Hadoop-fürt főcsomópontjának elérése című témakört.](customize-hadoop-cluster.md)

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Szolgáltatás létrehozása
Ebben a szakaszban számos példa, amelyben a funkciók hozhat létre Hive-lekérdezések használatával ismertetik. Miután további szolgáltatásokat hozott létre, hozzáadhatja őket oszlopként a meglévő táblához, vagy létrehozhat egy új táblát a további szolgáltatásokkal és elsődleges kulccsal, amely ezután az eredeti táblához illeszthető. Itt vannak a bemutatott példák:

1. [Frekvenciaalapú szolgáltatásgenerálás](#hive-frequencyfeature)
2. [A kategorikus változók kockázatai a bináris osztályozásban](#hive-riskfeature)
3. [Funkciók kinyerése a Datetime mezőből](#hive-datefeatures)
4. [Szolgáltatások kinyerése a szövegmezőből](#hive-textfeatures)
5. [A GPS-koordináták közötti távolság kiszámítása](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Frekvenciaalapú szolgáltatásgenerálás
Gyakran hasznos kiszámítani egy kategorikus változó szintjeinek gyakoriságát, vagy a szintek bizonyos kombinációinak gyakoriságát több kategorikus változóból. A felhasználók a következő parancsfájl segítségével számíthatják ki ezeket a frekvenciákat:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>A kategorikus változók kockázatai a bináris osztályozásban
A bináris osztályozásban a nem numerikus kategorikus változókat numerikus jellemzőkké kell alakítani, ha a használt modellek csak numerikus jellemzőket vesznek fel. Ez az átalakítás úgy történik, hogy minden nem numerikus szintet numerikus kockázattal helyettesít. Ez a szakasz néhány általános Hive-lekérdezéseket jelenít meg, amelyek kiszámítják egy kategorikus változó kockázati értékeit (naplószorzóit).

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

Ebben a példában `smooth_param1` `smooth_param2` a változók, és úgy vannak beállítva, hogy simítsa az adatokból számított kockázati értékeket. A kockázatok -Inf és Inf közötti tartományban vannak. A 0 > kockázat azt jelzi, hogy annak valószínűsége, hogy a cél egyenlő 1-el, nagyobb, mint 0,5.

A kockázati tábla kiszámítása után a felhasználók kockázati értékeket rendelhetnek egy táblához, ha csatlakoznak a kockázati táblához. A Hive-illesztési lekérdezés az előző szakaszban lett megadva.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Funkciók kinyerése a datetime mezőkből
A Hive a datetime mezők feldolgozásához udf-ok készletét hozza létre. A Hive-ban az alapértelmezett datetime formátum például a "yyyy-MM-dd 00:00:00" ('1970-01-01 12:21:32' ) . Ez a szakasz példákat mutat be, amelyek kibontják a hónap napját, a hónapot egy datetime mezőből, és más példákat, amelyek a datetime karakterláncot az alapértelmezett formátumtól eltérő formátumban konvertálják datetime karakterláncra alapértelmezett formátumban.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Ez a Hive lekérdezés feltételezi, hogy a * \<>dátumidő-mezője* az alapértelmezett datetime formátumban van.

Ha egy datetime mező nem az alapértelmezett formátumban van, először a datetime mezőt Unix időbélyegzővé kell konvertálnia, majd a Unix időbélyegzőt az alapértelmezett formátumú datetime karakterláncra kell konvertálnia. Ha a datetime alapértelmezett formátumban van, a felhasználók alkalmazhatják a beágyazott datetime UDF-eket a szolgáltatások kinyerésére.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Ebben a * \<lekérdezésben,* ha a datetime mező>a minta, mint *a 03/26/2015 12:04:39*, * \<a datetime mező>" mintának* kell lennie. `'MM/dd/yyyy HH:mm:ss'` A teszteléshez a felhasználók futtathatják a

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

A *struktúráramintavételező* ebben a lekérdezésben jön előtelepítve az összes Azure HDInsight Hadoop-fürtök alapértelmezés szerint, ha a fürtök ki vannak építve.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Szolgáltatások kinyerése szövegmezőkből
Ha a Hive-táblában van egy szóközzel körülhatárolt szóköz-karakterláncot tartalmazó szövegmező, a következő lekérdezés kinyeri a karakterlánc hosszát és a karakterláncban lévő szavak számát.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>A GPS-koordináták készletei közötti távolságok kiszámítása
The query given in this section can be directly applied to the NYC Taxi Trip Data. A lekérdezés célja, hogy megmutassa, hogyan alkalmazhat egy beágyazott matematikai függvényt a Hive-ban a szolgáltatások létrehozásához.

A lekérdezésben használt mezők a felvételi és a leadási helyek GPS koordinátái, a *felvételi\_hosszúság,* *a felvételi\_szélesség,* a *lemorzsolódási\_hosszúság*és a *\_leadási szélesség*. A felvételi és a leadási koordináták közötti közvetlen távolságot kiszámító lekérdezések a következők:

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

A matematikai egyenletek, amelyek kiszámítják a távolságot két GPS koordináták megtalálható a <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a> oldalon, szerzője Peter Lapisu. Ebben a Javascript, `toRad()` a funkció csak *lat_or_lon*pi/180, amely átalakítja fok radián. Itt *lat_or_lon* a szélességi vagy hosszúsági fok. Mivel a Hive nem `atan2`biztosítja a `atan`funkciót `atan2` , hanem `atan` a függvényt, a függvényt a fenti Hive lekérdezés függvénye hajtja végre a <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipédiában</a>megadott definíció használatával.

![Munkaterület létrehozása](./media/create-features-hive/atan2new.png)

A Hive beágyazott **UDF-ek** teljes listája megtalálható az Apache Hive wiki Beépített függvények <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">szakaszában).</a>  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a>Speciális témakörök: A Hive-paraméterek finomhangolása a lekérdezési sebesség növeléséhez
A Hive-fürt alapértelmezett paraméterbeállításai nem feltétlenül alkalmasak a Hive-lekérdezésekhez és a lekérdezéseket feldolgozó adatokhoz. Ez a szakasz ismerteti néhány paramétert, hogy a felhasználók finomhangolása a Hive-lekérdezések teljesítményének javítása érdekében. A felhasználóknak hozzá kell adniuk a paraméterhangolási lekérdezéseket az adatok feldolgozásának lekérdezései előtt.

1. **Java halomterület:** Nagy adatkészletek összeillesztésével vagy hosszú rekordok feldolgozásával járó lekérdezések esetén a **halommemória-terület kifutása** az egyik gyakori hiba. Ez a hiba elkerülhető a *mapreduce.map.java.opts* paraméterek és *a mapreduce.task.io.sort.mb* paramétereinek a kívánt értékekre állításával. Például:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Ez a paraméter 4 GB memóriát foglal le a Java halomterület számára, és hatékonyabbá teszi a rendezést azáltal, hogy több memóriát foglal le számára. Érdemes játszani ezekkel a foglalásokkal, ha a halommemória-területtel kapcsolatos feladathibák vannak.

1. **DfS blokk mérete**: Ez a paraméter a fájlrendszer által tárolott legkisebb adategységet állítja be. Ha például az elosztott fájlrendszer blokkmérete 128 MB, akkor a legfeljebb 128 MB méretű adatok at egyetlen blokkban tárolják. A 128 MB-nál nagyobb adatok extra blokkokat osztottak ki. 
2. Kis blokkméret kiválasztása nagy általános költségeket okoz a Hadoopban, mivel a névcsomópontnak sokkal több kérést kell feldolgoznia a fájlhoz tartozó megfelelő blokk megkereséséhez. Gigabájt (vagy nagyobb) adatok kezelése esetén ajánlott beállítás:

        set dfs.block.size=128m;

2. **Illesztési művelet optimalizálása a Hive-ban:** Bár a térképen/csökkentési keretrendszerben a csatlakozási műveletek általában a csökkentési fázisban zajlanak, néha hatalmas nyereség érhető el a térképfázisillesztések ütemezésével (más néven "mapjoins"). Állítsa be ezt a beállítást:
   
       set hive.auto.convert.join=true;

3. **A Hive leképezők számának megadása:** Bár a Hadoop lehetővé teszi a felhasználó számára a szűkítők számának beállítását, a leképezők számát általában nem a felhasználó állítja be. Egy trükk, amely lehetővé teszi bizonyos fokú ellenőrzést ezen a számon, hogy válassza ki a Hadoop változók *mapred.min.split.size* és *mapred.max.split.size,* mint a méret az egyes térkép feladat határozza meg:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    A következők alapértelmezett értéke általában a következő:
    
   - *mapred.min.split.size* 0, hogy a
   - *mapred.max.split.size* a **Long.MAX** és a 
   - *a dfs.block.size fájl* 64 MB.

     Mint látjuk, mivel az adatok mérete, tuning ezeket a paramétereket a "beállítás" lehetővé teszi számunkra, hogy beállítsa a mappers használt száma.

4. Íme néhány további **speciális lehetőség** a Hive teljesítményének optimalizálására. Ezek a beállítások lehetővé teszik a feladatok leképezéséhez és csökkentéséhez lefoglalt memória beállítását, és hasznosak lehetnek a teljesítmény módosításában. Ne feledje, hogy a *mapreduce.reduce.memory.mb* nem lehet nagyobb, mint a Hadoop-fürt minden munkavégző csomópontjának fizikai memóriamérete.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

