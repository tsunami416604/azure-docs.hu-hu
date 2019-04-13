---
title: Funkciók létrehozása az adatokhoz egy Hadoop-fürt – a csoportos adatelemzési folyamat
description: Funkciók létrehozása az Azure HDInsight Hadoop-fürtben tárolt adatok Hive-lekérdezések példái.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a491f923d7755513d84adfe765d595a3a7a80715
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524905"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Funkciók létrehozása az adatokhoz a Hive-lekérdezések segítségével Hadoop-fürt
Ez a dokumentum bemutatja, hogyan funkciók létrehozása az Azure HDInsight Hadoop-fürtben Hive-lekérdezések segítségével tárolt adatokat. A Hive-lekérdezések használata beágyazott Hive User-Defined funkciókat (UDF), a parancsfájlok, amelynek biztosított.

A szolgáltatások létrehozásához szükséges műveletek memóriaigényes is lehet. Hive-lekérdezések teljesítményének válik a kritikus fontosságú ezekben az esetekben, és javítani lehet bizonyos paraméterek beállításával. Ezek a paraméterek beállítása a következő cikkben az utolsó szakaszban.

A lekérdezések, amelyek bemutatják példák jellemző a [NYC Taxi Útadatok](https://chriswhong.com/open-data/foil_nyc_taxi/) forgatókönyveket is rendelkezésre állnak a [GitHub-adattár](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Ezeket a lekérdezéseket már rendelkezik az adatok séma van megadva, és készen áll hamarosan futtatásához. Paraméterek, amelyeket a felhasználók hangolhassa a Hive-lekérdezések teljesítményének javítása érdekében, hogy az utolsó szakaszban is ismertetik.

Ez a feladat Ez a lépés a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Létrehozott egy Azure storage-fiókot. Ha utasításokat van szüksége, tekintse meg [Azure Storage-fiók létrehozása](../../storage/common/storage-quickstart-create-account.md)
* A HDInsight szolgáltatással egyéni Hadoop-fürt kiépítése.  Ha utasításokat van szüksége, tekintse meg [testreszabása az Azure HDInsight Hadoop-fürtök Advanced Analytics](customize-hadoop-cluster.md).
* Az adatok Hive-táblák az Azure HDInsight Hadoop-fürtök lett feltöltve. Ha még nem, hajtsa végre a [létrehozása és az adatok betöltése Hive-tábláihoz](move-hive-tables.md) először feltölteni az adatokat a Hive-táblákat.
* Távoli hozzáférés a fürthöz engedélyezett. Ha utasításokat van szüksége, tekintse meg [a fő csomópont, Hadoop-fürt eléréséhez](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>A szolgáltatás létrehozása
Ez a szakasz néhány ötletet a, amelyben funkciókat is lehet létrehozása Hive-lekérdezések segítségével ismerteti. További funkciók hoztak létre, ha oszlopként, azokat hozzá a meglévő tábla, vagy hozzon létre egy új táblát a további funkciók és az elsődleges kulcsa, amelyet majd összekapcsolható az eredeti tábla a. Az alábbiakban bemutatott példák:

1. [Gyakoriság-alapú szolgáltatás létrehozása](#hive-frequencyfeature)
2. [A bináris osztályozási Kategorikus változók kockázatok](#hive-riskfeature)
3. [Jellemzők kinyerése Datetime mező](#hive-datefeatures)
4. [Jellemzők kinyerése szövegmező](#hive-textfeatures)
5. [Kiszámítja a GPS-koordinátáit közötti távolság](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Gyakoriság-alapú szolgáltatás létrehozása
Gyakran hasznos kiszámítása a kategorikus változó szintek gyakoriságát, vagy az egyes szintek több kategorikus változó kombinációi gyakoriságát. Felhasználók a következő parancsfájl segítségével kiszámíthatja az e:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>A bináris osztályozási kategorikus változók kockázatok
Bináris osztályozás, a nem numerikus kategorikus változók konvertálni kell numerikus funkciókat, ha a modell csak használt numerikus funkciók. Ez a konverzió nem numerikus szintenként cserélje le a numerikus kockázata végezhető el. Ez a szakasz bemutatja néhány általános Hive-lekérdezések, amelyek egy kategorikus változó (napló zajok) kockázati értékek kiszámítása.

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

Ebben a példában változókat `smooth_param1` és `smooth_param2` zökkenőmentes az adatokból számított kockázati értékek vannak beállítva. Kockázatok kapott -Inf és Inf között. A kockázati > 0 azt jelzi, hogy 0,5-nél nagyobb a valószínűsége, hogy a célként megadott egyenlő 1.

A kockázat után tábla számítható ki, felhasználókat rendelhet kockázati értékek egy táblát a kockázati tábla csatlakoztatásával. A csatlakozó Hive-lekérdezést az előző szakaszban lett megadva.

### <a name="hive-datefeatures"></a>Jellemzők kinyerése a datetime mezők
Hive tartalmaz egy UDF-EK a datetime mezők feldolgozás céljából. A Hive, az alapértelmezett dátum és idő formátumban van "éééé-hh-nn 00:00:00" ("1970-01-01 12:21:32: például). Ez a szakasz bemutatja, amely egy hónap, az a dátum/idő mezőt a hónap napját kinyerése példák és további példák, amelyek a dátum/idő karakterlánc formátuma nem az alapértelmezett formátum dátum/idő karakterlánc az alapértelmezett formázása.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

A Hive-lekérdezést feltételezi, hogy a  *\<datetime mező >* alapértelmezett dátum és idő formátumban.

Ha egy dátum/idő mezőt nem az alapértelmezett formátumban, először a datetime mező átalakítása Unix-időbélyegző, és majd a Unix-időbélyeg konvertálása dátum/idő karakterlánc, amely az alapértelmezett formátuma szüksége. Ha a dátumot/időt, az alapértelmezett formátum felhasználók alkalmazhat UDF szolgáltatások kinyerése beágyazott dátuma és időpontja.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Ebben a lekérdezésben Ha a  *\<datetime mező >* a következő mintának például *03-26-2015 12:04:39*, a  *\<minta a datetime mező > "* legyen `'MM/dd/yyyy HH:mm:ss'`. Tesztelje, hogy a felhasználók futtathatja

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

A *hivesampletable* ebben a lekérdezésben előtelepítve minden Azure HDInsight Hadoop-fürtökön alapértelmezés szerint amikor a fürtök kiépítése történik.

### <a name="hive-textfeatures"></a>Jellemzők kinyerése szöveges mezők
A Hive-táblában van egy szövegmező, hogy szóközök vannak elválasztva karakterláncot tartalmazó, amikor a következő lekérdezést a karakterláncot, és a karakterlánc szavak számát adja eredményül.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Kiszámítja a GPS-koordinátákat közötti távolság
Az ebben a szakaszban megadott lekérdezést közvetlenül a NYC Taxi Útadatok alkalmazhatók. Ez a lekérdezés az a célja, hogy szemléltetik a alkalmazni egy beágyazott matematikai függvény a Hive-szolgáltatások létrehozásához.

Ebben a lekérdezésben használt mezőket, a GPS-koordinátáit nevű begyűjtést és dropoff helyeket *begyűjtés\_hosszúsági*, *begyűjtés\_szélességi*,  *dropoff\_hosszúsági*, és *dropoff\_szélességi*. A lekérdezések, amelyek a közvetlen távolsága a begyűjtés és dropoff koordináták kiszámítása a következők:

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

A két GPS-koordinátáit közötti távolság számító matematikai egyenletek találhatók a <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">ingó típus parancsfájlok</a> hely Peter Lapisu által írt. A JavaScript, a függvény `toRad()` mindössze *lat_or_lon*radiánná konvertálja a fokban pi/180. Itt *lat_or_lon* a szélességi és hosszúsági van. Mivel Hive nem biztosít a függvény `atan2`, a funkciót biztosít, de `atan`, a `atan2` függvény implementálva lett által `atan` függvényt a fenti Hive-lekérdezést a megadott definíció használatával <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Munkaterület létrehozása](./media/create-features-hive/atan2new.png)

Beágyazott UDF-EK található Hive teljes listáját a **beépített függvények** szakaszában a <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="tuning"></a> Haladó témakörök: Hallgassa meg paramétereket Hive lekérdezés sebességének növelése
A Hive-fürt alapértelmezett paraméterbeállítások nem alkalmas a Hive-lekérdezések és a lekérdezések-e feldolgozni az adatokat. Ez a szakasz bemutatja néhány paraméter, amely a felhasználók hangolhassa a Hive-lekérdezések teljesítményének javítása érdekében. Felhasználók kell hozzáadnia a paramétert, a lekérdezések, az adatfeldolgozás előtt a Lekérdezések finomhangolása.

1. **Java halommemória terület**: Lekérdezések illesztése a nagyméretű adathalmazok, vagy hosszú rekordjának **elegendő szabad terület halommemória** a gyakori hibák egyike. Ez a hiba elkerülhető az paraméterek beállításával a *mapreduce.map.java.opts* és *mapreduce.task.io.sort.mb* a kívánt értékeket. Például:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Ez a paraméter 4GB memória, a Java halommemória helyet foglal le, és is használatával a rendezés hatékonyabb hozzá, illetve több memória. Érdemes tesztelni a megoldást, ha minden olyan feladat sikertelen hibák halommemória terület kapcsolódó.

1. **Az elosztott Fájlrendszerbeli blokkméret**: Ez a paraméter beállítása a legkisebb egység, amely a fájlrendszer tárolja az adatokat. Például ha az elosztott Fájlrendszerbeli blokkméret a 128 MB, majd bármilyen típusú és méretű kisebb, mint és legfeljebb 128 MB tárolódik egyetlen blokkot. 128 MB-nál nagyobb méretű adatok extra blokkok engedélyezett. 
2. Egy kis blokkméretet kiválasztása hatására nagy terhek Hadoop, mert a név csomópont található a releváns blokkot a fájlhoz tartozó számos további kérelmeket feldolgozni. A javasolt beállítás foglalkozó gigabájt (vagy nagyobb) adatok:

        set dfs.block.size=128m;

2. **Hive join művelet optimalizálása**: A map/reduce-keretrendszer összekapcsolási műveletek általában kerül sor a csökkentse fázisban vannak, amíg óriási nyereséget illesztések ütemezésével a térkép fázisban (más néven "mapjoins") érhető el. A közvetlen Hive ehhez, amikor csak lehetséges, állítsa be:
   
       set hive.auto.convert.join=true;

3. **A Hive-leképező számát**: Hadoop lehetővé teszi, hogy a felhasználó csökkentő száma, amíg a leképező száma általában van-e a felhasználó nem állítható be. Egy trükköt, amely lehetővé teszi bizonyos fokú felügyeletet ezen a számon, hogy válassza ki a Hadoop változók *mapred.min.split.size* és *mapred.max.split.size* minden leképezés méretének tevékenység határozza meg:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Általában az alapértelmezett érték:
    
   - *mapred.min.split.size* 0, a
   - *mapred.max.split.size* van **Long.MAX** és az 
   - *DFS.Block.size* 64 MB.

     Ahogy láthatjuk, az adatok mérete, adott ezeket a paramétereket "beállítás" finomhangolás őket lehetővé teszi számunkra finomhangolása használt leképező száma.

4. Az alábbiakban néhány egyéb információ **speciális beállítások** Hive teljesítményének optimalizálásához. Ezek lehetővé teszik a térkép, és csökkentheti a feladatok számára lefoglalt memória, és igen hasznos lehet a teljesítmény finomhangolásához. Vegye figyelembe, hogy a *mapreduce.reduce.memory.mb* nem lehet nagyobb, mint a Hadoop-fürt mindegyik feldolgozó csomópontja fizikai memória mérete.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

