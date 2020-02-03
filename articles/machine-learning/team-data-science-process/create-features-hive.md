---
title: Szolgáltatások létrehozása egy Azure HDInsight Hadoop-fürtben lévő adatelemzési folyamathoz
description: Funkciók létrehozása az Azure HDInsight Hadoop-fürtben tárolt adatok Hive-lekérdezések példái.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721778"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Funkciók létrehozása az adatokhoz a Hive-lekérdezések segítségével Hadoop-fürt
Ez a dokumentum bemutatja, hogyan funkciók létrehozása az Azure HDInsight Hadoop-fürtben Hive-lekérdezések segítségével tárolt adatokat. A Hive-lekérdezések használata beágyazott Hive User-Defined funkciókat (UDF), a parancsfájlok, amelynek biztosított.

A szolgáltatások létrehozásához szükséges műveletek memóriaigényes is lehet. Hive-lekérdezések teljesítményének válik a kritikus fontosságú ezekben az esetekben, és javítani lehet bizonyos paraméterek beállításával. Ezek a paraméterek beállítása a következő cikkben az utolsó szakaszban.

A bemutatott lekérdezésekre jellemző példák a New York-i [taxi Trip](https://chriswhong.com/open-data/foil_nyc_taxi/) adatforgatókönyvek esetében is elérhetők a [GitHub-tárházban](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Ezeket a lekérdezéseket már rendelkezik az adatok séma van megadva, és készen áll hamarosan futtatásához. Paraméterek, amelyeket a felhasználók hangolhassa a Hive-lekérdezések teljesítményének javítása érdekében, hogy az utolsó szakaszban is ismertetik.

Ez a feladat a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)egyik lépése.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Létrehozott egy Azure storage-fiókot. Ha útmutatásra van szüksége, tekintse meg [Az Azure Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című témakört.
* A HDInsight szolgáltatással egyéni Hadoop-fürt kiépítése.  Ha útmutatásra van szüksége, tekintse meg [a Azure HDInsight Hadoop-fürtök testreszabása speciális elemzésekhez](customize-hadoop-cluster.md)című témakört.
* Az adatok Hive-táblák az Azure HDInsight Hadoop-fürtök lett feltöltve. Ha még nem tette meg, kövesse az [adatok létrehozása és betöltése a kaptár-táblákba](move-hive-tables.md) lehetőséget, hogy először töltse fel az adatok a kaptár-táblákba.
* Távoli hozzáférés a fürthöz engedélyezett. Ha útmutatásra van szüksége, tekintse meg [a Hadoop-fürt fő csomópontjának elérését](customize-hadoop-cluster.md)ismertető témakört.

## <a name="hive-featureengineering"></a>Szolgáltatás létrehozása
Ez a szakasz néhány ötletet a, amelyben funkciókat is lehet létrehozása Hive-lekérdezések segítségével ismerteti. További funkciók hoztak létre, ha oszlopként, azokat hozzá a meglévő tábla, vagy hozzon létre egy új táblát a további funkciók és az elsődleges kulcsa, amelyet majd összekapcsolható az eredeti tábla a. Az alábbiakban bemutatott példák:

1. [Gyakoriság-alapú szolgáltatás létrehozása](#hive-frequencyfeature)
2. [A kategorikus változók kockázatai a bináris besorolásban](#hive-riskfeature)
3. [Szolgáltatások kinyerése a DateTime mezőből](#hive-datefeatures)
4. [Szolgáltatások kinyerése a szövegmezőből](#hive-textfeatures)
5. [A GPS-koordináták közötti távolság kiszámítása](#hive-gpsdistance)

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


### <a name="hive-riskfeature"></a>A kategorikus változók kockázatai a bináris besorolásban
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

Ebben a példában a változók `smooth_param1` és `smooth_param2` az adatokból kiszámított kockázati értékek simítására vannak beállítva. Kockázatok kapott -Inf és Inf között. A kockázati > 0 azt jelzi, hogy 0,5-nél nagyobb a valószínűsége, hogy a célként megadott egyenlő 1.

A kockázat után tábla számítható ki, felhasználókat rendelhet kockázati értékek egy táblát a kockázati tábla csatlakoztatásával. A csatlakozó Hive-lekérdezést az előző szakaszban lett megadva.

### <a name="hive-datefeatures"></a>Szolgáltatások kinyerése a DateTime mezőkből
Hive tartalmaz egy UDF-EK a datetime mezők feldolgozás céljából. A Hive, az alapértelmezett dátum és idő formátumban van "éééé-hh-nn 00:00:00" ("1970-01-01 12:21:32: például). Ez a szakasz bemutatja, amely egy hónap, az a dátum/idő mezőt a hónap napját kinyerése példák és további példák, amelyek a dátum/idő karakterlánc formátuma nem az alapértelmezett formátum dátum/idő karakterlánc az alapértelmezett formázása.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Ez a kaptár-lekérdezés feltételezi, hogy a *\<datetime mező >* az alapértelmezett datetime formátumban van.

Ha egy dátum/idő mezőt nem az alapértelmezett formátumban, először a datetime mező átalakítása Unix-időbélyegző, és majd a Unix-időbélyeg konvertálása dátum/idő karakterlánc, amely az alapértelmezett formátuma szüksége. Ha a dátumot/időt, az alapértelmezett formátum felhasználók alkalmazhat UDF szolgáltatások kinyerése beágyazott dátuma és időpontja.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Ebben a lekérdezésben, ha a *\<datetime (dátum* és idő) mezőben > az *03/26/2015 12:04:39*-as mintával, a *datetime > mező\<mintáját* `'MM/dd/yyyy HH:mm:ss'`kell megadni. Tesztelje, hogy a felhasználók futtathatja

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

A lekérdezésben szereplő *hivesampletable* alapértelmezés szerint az összes Azure HDInsight Hadoop-fürtön előre telepítve van, amikor a fürtöket kiépítik.

### <a name="hive-textfeatures"></a>Szolgáltatások kinyerése szöveges mezőkből
A Hive-táblában van egy szövegmező, hogy szóközök vannak elválasztva karakterláncot tartalmazó, amikor a következő lekérdezést a karakterláncot, és a karakterlánc szavak számát adja eredményül.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Távolságok kiszámítása a GPS-koordináták készletei között
Az ebben a szakaszban megadott lekérdezést közvetlenül a NYC Taxi Útadatok alkalmazhatók. Ez a lekérdezés az a célja, hogy szemléltetik a alkalmazni egy beágyazott matematikai függvény a Hive-szolgáltatások létrehozásához.

A lekérdezésben használt mezők a felvételi és lemorzsolódási helyeinek GPS-koordinátái, a *pickup\_hosszúság*, a *pickup\_szélesség*, a *lemorzsolódási\_hosszúság*és a *lemorzsolódási\_szélesség*. A lekérdezések, amelyek a közvetlen távolsága a begyűjtés és dropoff koordináták kiszámítása a következők:

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

A két GPS-koordináták közötti távolságot kiszámító matematikai egyenletek megtalálhatók a (z) Peter Lapisu által készített <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a> webhelyen. Ebben a JavaScriptben a függvény `toRad()` csak *lat_or_lon*PI/180, amely a fok és a radián közötti értéket konvertálja. Itt *lat_or_lon* a szélesség vagy a hosszúság. Mivel a kaptár nem biztosítja a (`atan2`) függvényt, de a függvényt `atan`ja, a `atan2` függvényt a fenti kaptár-lekérdezés `atan` függvénye hajtja végre a <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">wikipedia</a>által megadott definíció használatával.

![Munkaterület létrehozása](./media/create-features-hive/atan2new.png)

A kaptár beágyazott UDF teljes listája megtalálható a <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a> **beépített függvények** szakaszában.  

## <a name="tuning"></a>Speciális témakörök: a struktúra paramétereinek finomhangolása a lekérdezési sebesség javítása érdekében
A Hive-fürt alapértelmezett paraméterbeállítások nem alkalmas a Hive-lekérdezések és a lekérdezések-e feldolgozni az adatokat. Ez a szakasz bemutatja néhány paraméter, amely a felhasználók hangolhassa a Hive-lekérdezések teljesítményének javítása érdekében. Felhasználók kell hozzáadnia a paramétert, a lekérdezések, az adatfeldolgozás előtt a Lekérdezések finomhangolása.

1. **Java-halom területe**: a nagyméretű adatkészletek csatlakoztatását, illetve a hosszú rekordok feldolgozását végző lekérdezések esetében az egyik gyakori hiba az, hogy kifogyott a **rendelkezésre álló terület** . Ezt a hibát el lehet kerülni a paraméterek *MapReduce. map. Java. dönt* és *MapReduce. Task. IO. sort. MB* és a kívánt értékek megadásával. Például:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Ez a paraméter 4 GB memóriát foglal le a Java kupac területéhez, és hatékonyabbá teszi a rendezést azáltal, hogy több memóriát foglal le. Érdemes tesztelni a megoldást, ha minden olyan feladat sikertelen hibák halommemória terület kapcsolódó.

1. **DFS-blokk mérete**: Ez a paraméter a fájlrendszer által tárolt adatmennyiség legkisebb egységét állítja be. Például ha az elosztott Fájlrendszerbeli blokkméret a 128 MB, majd bármilyen típusú és méretű kisebb, mint és legfeljebb 128 MB tárolódik egyetlen blokkot. 128 MB-nál nagyobb méretű adatok extra blokkok engedélyezett. 
2. Egy kis blokkméretet kiválasztása hatására nagy terhek Hadoop, mert a név csomópont található a releváns blokkot a fájlhoz tartozó számos további kérelmeket feldolgozni. A javasolt beállítás foglalkozó gigabájt (vagy nagyobb) adatok:

        set dfs.block.size=128m;

2. **Összekapcsolási művelet optimalizálása a kaptárban**: míg a Térkép/csökkentés keretrendszerben lévő csatlakozási műveletek jellemzően a csökkentési fázisban vannak, időnként a térképi fázisban (más néven "mapjoins") lévő összekapcsolások ütemezésével is nagy nyereség érhető el. Beállítás beállítása:
   
       set hive.auto.convert.join=true;

3. **A kaptárak számának megadása a struktúra számára**: míg a Hadoop lehetővé teszi a felhasználó számára a szűkítők számának megadását, a leképezések számát általában nem a felhasználó állítja be. Egy olyan trükk, amely lehetővé teszi, hogy a szám bizonyos fokú szabályozása kiválassza a Hadoop változókat *mapred. min. Split. size* és *mapred. max. Split. size* értékre, az egyes leképezési feladatok méretének meghatározásához:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Általában az alapértelmezett érték:
    
   - a *mapred. min. Split. size* értéke 0, a
   - a *mapred. max. Split. size* **hossza. max** és a 
   - az *elosztott fájlrendszer. Block. size* értéke 64 MB.

     Ahogy láthatjuk, az adatok mérete, adott ezeket a paramétereket "beállítás" finomhangolás őket lehetővé teszi számunkra finomhangolása használt leképező száma.

4. Íme néhány további **speciális lehetőség** a struktúra teljesítményének optimalizálásához. Ezekkel a beállításokkal megadhatja a leképezési és a feladatokhoz lefoglalt memóriát, és hasznos lehet a teljesítmény finomhangolásához. Ne feledje, hogy a *MapReduce. csökkentse a memóriát. MB* nem lehet nagyobb, mint a Hadoop-fürtben lévő egyes munkavégző csomópontok fizikai memóriájának mérete.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

