---
title: Egyéni MapReduce programok – Azure HDInsight futtatása
description: Mikor és hogyan lehet egyéni MapReduce programok futtatása a HDInsight.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: 87eb4c8380c0a542d52ffb4a77bcc317407ea545
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594474"
---
# <a name="run-custom-mapreduce-programs"></a>Egyéni MapReduce programok futtatása

Például a HDInsight Hadoop-alapú big data rendszerek engedélyezése adatfeldolgozás számos különféle eszközök és technológiák használatával. Az alábbi táblázat a fő és szempontjai, mindegyiknél ismerteti.

| Lekérdezés mechanizmus | Előnyök | Megfontolandó szempontok |
| --- | --- | --- |
| **Hive-HiveQL használatával** | <ul><li>Kötegelt feldolgozásra és elemzésre, nagy mennyiségű adat nem módosítható, az adatok összegzési és a egy kiváló megoldás az igény szerinti lekérdezése. Egy jól ismert SQL-szerű szintaxist használ.</li><li>Könnyedén particionált és indexelt adatok állandó táblák létrehozására használható.</li><li>Az ugyanazon adatokra hozható létre több külső táblák és nézetek.</li><li>Egy egyszerű adatraktár megvalósítása, amely jelentős horizontális felskálázást és hibatűrési képességeket biztosít az adattárolási és feldolgozási támogatja.</li></ul> | <ul><li>Legalább egy azonosításra alkalmas struktúrával rendelkeznek forrásadatokat van szükség.</li><li>Nem alkalmas a valós idejű lekérdezések és a sor szintű frissítéseket. Legjobb szolgál a batch-feladatok nagy adatkészleteken, keresztül.</li><li>Nem lehet képes bizonyos típusú összetett feldolgozási feladatok elvégzésére.</li></ul> |
| **A Pig Latin a Pig használatával** | <ul><li>Kiváló megoldás kezelésére szolgáló adatokat, mivel beállítja, egyesítése és szűrésének történő rekordon vagy bejegyzések,-adatkészletek és az adatok átalakítását, oszlopok definiálásával, csoportosítási értékek vagy oszlopokat sorokra átalakításával.</li><li>A munkafolyamat-alapú megközelítést használhat adatok műveletek sorozataként.</li></ul> | <ul><li>SQL-felhasználók is található, a Pig Latin kevésbé ismert és bonyolultabb, mint a HiveQL használatára.</li><li>Az alapértelmezett kimenet általában egy szövegfájlt, és így nehezebb a vizualizációs eszközök, például az Excel használata is lehet. Általában egy Hive-tábla lesz réteg át a kimenetet.</li></ul> |
| **Egyéni map/csökkentése** | <ul><li>A térkép teljes körűen biztosít, és csökkentheti a fázisok és végrehajtás.</li><li>Lehetővé teszi a lekérdezések optimalizálását a fürtből a maximális teljesítmény elérése érdekében, vagy a kiszolgálók és a hálózat terhelése minimalizálható legyen.</li><li>Az összetevők is írható a jól ismert nyelvek széles.</li></ul> | <ul><li>Nehezebb, mint a Pig- vagy Hive használata, mivel létre kell hoznia a saját térkép és csökkentse az összetevők.</li><li>Csatlakozás adatkészletek igénylő folyamatokat több nehezen megvalósításához.</li><li>Annak ellenére, hogy nincsenek elérhető tesztelési keretrendszereket, bonyolultabb, mint egy normál alkalmazást, mert a kód lefut egy kötegelt felügyelete alatt, a Hadoop Feladatütemező kód hibakeresése.</li></ul> |
| **HCatalog** | <ul><li>Ez kivonatolja a tárolót, a felügyelet megkönnyítése és a felhasználók számára az adatok tárolására szükségtelenné részletes elérési utat.</li><li>Lehetővé teszi az értesítési esemény, például az adatok rendelkezésre állását, lehetővé téve a más eszközökkel, például az Oozie operations történt észleléséhez.</li><li>Ez az adatokat, beleértve a kulcsot, a particionálás relációs nézetben mutatja, és megkönnyíti az adatok eléréséhez.</li></ul> | <ul><li>Támogatja a RCFile, CSV-szöveg, JSON-szöveget, SequenceFile és ORC fájlformátumok alapértelmezés szerint, de előfordulhat, hogy kell írnia egy egyéni SerDe más formátumokat.</li><li>HCatalog nem szálbiztos.</li><li>Az oszlopok adattípusai bizonyos korlátozások vonatkoznak a HCatalog-betöltőben az Pig-parancsfájlok használata során. További információkért lásd: [HCatLoader adattípusok](http://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) az Apache HCatalog dokumentációjában.</li></ul> |

Általában használhatja a legegyszerűbb módszer, amely képes biztosítani a szükséges eredmények. Például előfordulhat, hogy a érhet el az ilyen eredmények csak a Hive használatával, de összetett forgatókönyvek esetén szükség lehet, a Pig használata vagy még a saját térkép írása, és csökkentse az összetevők. Is előfordulhat, hogy döntse el, a Hive és Pig, hogy egyéni ábrázolásiterület kísérletezés után, és csökkentse összetevők finomhangolást és optimalizálhatja a feldolgozást, így jobb teljesítményt tud nyújtani.

## <a name="custom-mapreduce-components"></a>Összetevők egyéni map/csökkentése

Mapreduce/kód áll két különálló funkciók megvalósítása **térkép** és **csökkentheti** összetevőket. A **térkép** összetevő fut párhuzamosan több fürtcsomóponton a leképezés alkalmazása az adatok csomópont saját részét az egyes csomópontok. A **csökkentheti** összetevő sorrendbe állítja, és a térkép összes funkciójában az eredményről. Ez a két összetevő további információkért lásd: [MapReduce használata a Hadooppal a HDInsight](hdinsight-use-mapreduce.md).

A legtöbb feldolgozása a HDInsight-környezetben egyszerűbben és hatékonyabban egy magasabb szintű absztrakció, mint a Pig- vagy Hive használata. Hozhat létre egyéni ábrázolásiterület, és csökkentse az összetevők Hive-parancsprogramok hajtsa végre az egyre kifinomultabb feldolgozási belüli használatra.

Egyéni map/csökkentése összetevők általában Java nyelven írt. Hadoop, amely lehetővé teszi használt összetevők, például a C#, F #, Visual Basic, Python és JavaScript más nyelveken fejlesztett streamelési felületet biztosít.

* Egyéni Java MapReduce programok fejlesztése a bemutatóért lásd: [fejlesztés Java MapReduce programok hadoop on HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).
* Példa Python használatával, olvassa el [fejlesztés Python-streamprogramok for HDInsight MapReduce](apache-hadoop-streaming-python.md).

Érdemes lehet létrehozni a saját térkép, és csökkentse az összetevők a következő feltételeknek:

* Az adatok elemzése és strukturált információt lekérni egyéni logikát használó teljesen strukturált adatok feldolgozására van szüksége.
* Összetett feladatokat, amelyek a nehezen (vagy) szeretne a Pig Express vagy Hive-UDF létrehozása nélkül. Például előfordulhat, hogy szüksége külső geokódolási szolgáltatásokat szélességi és hosszúsági koordináták vagy IP-címek a forrásadatok átalakítása földrajzi hely nevét.
* Újból felhasználhatja a meglévő .NET, Python vagy JavaScript kód, a mapreduce és összetevők a Hadoop-tartalomközvetítő felület használatával szeretne.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Töltse fel, és futtassa a egyéni MapReduce-programot

A leggyakoribb MapReduce-programok Java nyelven íródtak, és zkompilováno do egy jar-fájlt.

1. Miután fejlesztett, összeállítani, és tesztelni a MapReduce-programot, használja a `scp` a jar-fájl feltöltése az átjárócsomóponthoz parancsot.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Cserélje le **felhasználónév** a fürt SSH-felhasználói fiókkal. Cserélje le **CLUSTERNAME** a fürt nevére. Ha az SSH-fiókhoz jelszót használt, a jelszó megadására kéri. Ha tanúsítványt használt, előfordulhat, hogy szeretné használni a `-i` paraméterrel adja meg a titkos kulcs fájlját.

2. A fürthöz történő csatlakozás [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Az SSH-munkamenetből hajtsa végre a MapReduce-programot YARNON keresztül.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Ez a parancs YARN a MapReduce feladatot küld el. A bemeneti fájl `/example/data/sample.log`, és a kimeneti könyvtár `/example/data/logoutput`. A bemeneti fájl és a kimeneti fájlokat, a a fürt alapértelmezett tárolója tárolja.

## <a name="next-steps"></a>További lépések

* [C# használata MapReduce a hadoop együttes használata a HDInsight streaming](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Java MapReduce programok fejlesztése hadoop on HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Python-streamelés HDInsight MapReduce-programok fejlesztése](apache-hadoop-streaming-python.md)
* [Eclipse-hez készült Azure eszközkészlet használata Spark-alkalmazások egy HDInsight-fürt létrehozása](../spark/apache-spark-eclipse-tool-plugin.md)
* [Használható Python felhasználói függvények (UDF) a Hive és a Piggel a HDInsight](python-udf-hdinsight.md)
