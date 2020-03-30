---
title: Egyéni MapReduce programok futtatása - Azure HDInsight
description: Mikor és hogyan futtathatok egyéni Apache MapReduce programokat az Azure HDInsight-fürtökön.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 78623f738285e781cb561a3844db8fbf37226929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645021"
---
# <a name="run-custom-mapreduce-programs"></a>Egyéni MapReduce programok futtatása

Az Apache Hadoop-alapú big data-rendszerek, például a HDInsight lehetővé teszik az adatfeldolgozást az eszközök és technológiák széles körével. Az alábbi táblázat ismerteti az egyes előnyöket és szempontokat.

| Lekérdezési mechanizmus | Előnyök | Megfontolandó szempontok |
| --- | --- | --- |
| **Apache Hive használata HiveQL** | <ul><li>Kiváló megoldás nagy mennyiségű nem módosítható adat kötegelt feldolgozásához és elemzéséhez, adatösszegzéshez és igény szerinti lekérdezéshez. Ismerős SQL-szerű szintaxist használ.</li><li>Használható állandó adattáblák, amelyek könnyen particionálható és indexelhető.</li><li>Ugyanazon az adaton több külső tábla és nézet is létrehozható.</li><li>Támogatja az egyszerű adattárház-megvalósítást, amely hatalmas horizontális felskálázási és hibatűrési lehetőségeket biztosít az adattároláshoz és -feldolgozáshoz.</li></ul> | <ul><li>A forrásadatoknak legalább néhány azonosítható struktúrával kell rendelkezniük.</li><li>Nem alkalmas valós idejű lekérdezésekhez és sorszintű frissítésekhez. A legjobb, ha kötegelt feladatok nagy adathalmazok on-át.</li><li>Előfordulhat, hogy bizonyos típusú összetett feldolgozási feladatokat nem tud végrehajtani.</li></ul> |
| **Apache Pig használata Pig Latin** | <ul><li>Kiváló megoldás az adatok halmazként történő kezelésére, az adatkészletek egyesítésére és szűrésére, függvények rekordokra vagy rekordcsoportokra való alkalmazására, valamint az adatok átstruktolására oszlopok definiálásával, értékek csoportosításával vagy oszlopok sorokká alakításával.</li><li>A munkafolyamat-alapú megközelítést használhatja az adatokon végzett műveletek sorozataként.</li></ul> | <ul><li>Sql felhasználók előfordulhat, hogy pig latin kevésbé ismert és nehezebb használni, mint hiveQL.</li><li>Az alapértelmezett kimenet általában egy szövegfájl, így nehezebb encikbe tolni a vizualizációs eszközökkel, például az Excellel. Általában egy Hive-táblát rétegez a kimenetfölé.</li></ul> |
| **Egyéni térkép/kicsinyítés** | <ul><li>Teljes körű vezérlést biztosít a térkép felett, és csökkenti a fázisokat és a végrehajtást.</li><li>Lehetővé teszi a lekérdezések optimalizálását a fürt maximális teljesítményének elérése érdekében, vagy a kiszolgálók és a hálózat terhelésének minimalizálása érdekében.</li><li>Az összetevők számos jól ismert nyelven írhatók.</li></ul> | <ul><li>Ez nehezebb, mint a Pig vagy a Hive használata, mert létre kell hoznia a saját térképet, és csökkenti az összetevőket.</li><li>Az adathalmazok összekapcsolását igénylő folyamatok at nehezebb megvalósítani.</li><li>Annak ellenére, hogy vannak rendelkezésre álló tesztkeretrendszerek, a hibakeresési kód összetettebb, mint egy normál alkalmazás, mert a kód kötegelt feladatként fut a Hadoop feladat ütemező ellenőrzése alatt.</li></ul> |
| **Apache HKatalógus** | <ul><li>Kivonta a tárolás elérési útrészleteit, megkönnyítve az adminisztrációt, és szükségtelenül, hogy a felhasználók tudják, hol tárolják az adatokat.</li><li>Lehetővé teszi az események, például az adatok rendelkezésre állásának értesítését, lehetővé téve más eszközök, például az Oozie számára, hogy észleljék, ha műveletek történtek.</li><li>Felfedi az adatok relációs nézetét, beleértve a kulcsonkénti particionálást is, és megkönnyíti az adatok elérését.</li></ul> | <ul><li>Alapértelmezés szerint támogatja az RCFile, CSV szöveg, JSON szöveg, SequenceFile és ORC fájlformátumokat, de előfordulhat, hogy más formátumokhoz egyéni SerDe-t kell írnia.</li><li>A HCatalog nem szálbiztos.</li><li>Vannak bizonyos korlátozások az oszlopok adattípusaira vonatkozóan, amikor a HCatalog betöltőt pig parancsfájlokban használja. További információ: [HCatLoader data types](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) az Apache HCatalog dokumentációjában.</li></ul> |

Általában a legegyszerűbb ezeket a megközelítéseket használja, amelyek biztosítják a kívánt eredményeket. Előfordulhat például, hogy csak a Hive használatával érheti el ezeket az eredményeket, de összetettebb forgatókönyvek esetén előfordulhat, hogy a Pig-t kell használnia, vagy akár saját térképet is kell írnia, és csökkentenie kell az összetevőket. Dönthet úgy is, hogy a Hive vagy a Pig kísérletezésután az egyéni térkép és az összetevők csökkentése jobb teljesítményt nyújthat azáltal, hogy lehetővé teszi a feldolgozás finomhangolását és optimalizálását.

## <a name="custom-mapreduce-components"></a>Egyéni térképezés/összetevők csökkentése

A Térkép/kicsinyítő kód két külön funkcióból áll, **amelyeket térképként** valósítanak meg, és **csökkentik** az összetevőket. A **térkép-összetevő** párhuzamosan fut több fürtcsomóponton, és minden csomópont a leképezést alkalmazza a csomópont saját részhalmazára. A **reduce** komponens összegyűjti és összegzi az összes térképfüggvény eredményeit. A két összetevőről a [MapReduce használata a Hadoopban a HDInsight ban című témakörben talál](hdinsight-use-mapreduce.md)további információt.

A legtöbb HDInsight-feldolgozási forgatókönyvben egyszerűbb és hatékonyabb egy magasabb szintű absztrakciót, például a Pig vagy a Hive használatát. Egyéni leképezést is létrehozhat, és csökkentheti a Hive-parancsfájlokban használható összetevőket a kifinomultabb feldolgozáshoz.

Egyéni térkép/kicsinyítés alkatrészek általában java nyelven íródott. A Hadoop olyan streamelési felületet biztosít, amely lehetővé teszi más nyelveken, például a C#, F#, Visual Basic, Python és JavaScript nyelven kifejlesztett összetevők et is.

* Az egyéni Java MapReduce programok fejlesztéséről a [Java MapReduce programok fejlesztése a HDInsight-on című Hadoop-programok fejlesztése](apache-hadoop-develop-deploy-java-mapreduce-linux.md)című témakörben látható.

Fontolja meg saját térkép létrehozását, és csökkentse az összetevők et a következő feltételekhez:

* Az adatok elemzésével és az egyéni logika használatával fel kell dolgoznia a teljesen strukturálatlan adatokat.
* Olyan összetett feladatokat szeretne végrehajtani, amelyeket a Pig vagy a Hive összetettségében nehéz (vagy lehetetlen) kifejezni anélkül, hogy UDF-et kellene létrehoznia. Előfordulhat például, hogy külső geokódolási szolgáltatást kell használnia a forrásadatok szélességi és hosszúsági koordinátáinak vagy IP-címeinek földrajzi helynevekké alakításához.
* Szeretné újra felhasználni a meglévő .NET, Python vagy JavaScript-kódot a map/reduce összetevők a Hadoop streamelési felület használatával.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Az egyéni MapReduce program feltöltése és futtatása

A leggyakoribb MapReduce programok Java nyelven íródnak, és egy jar fájlba vannak fordítva.

1. Miután kifejlesztette, összeállította és tesztelte a MapReduce programot, a paranccsal feltöltheti a `scp` jar fájlt a fejnótérbe.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Cserélje le a CLUSTERNAME-t a fürtnevére. Ha jelszót használt az SSH-fiók védelméhez, a rendszer kéri a jelszó megadását. Ha tanúsítványt használt, előfordulhat, hogy `-i` a paramétert kell használnia a személyes kulcsfájl megadásához.

1. Az [ssh paranccsal](../hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetből hajtsa végre a MapReduce programot a YARN-on keresztül.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Ez a parancs elküldi a MapReduce feladatot a YARN-nak. A bemeneti `/example/data/sample.log`fájl , a `/example/data/logoutput`kimeneti könyvtár pedig a. A bemeneti fájl és a kimeneti fájlok a fürt alapértelmezett tárolójában tárolódnak.

## <a name="next-steps"></a>További lépések

* [C# használata a MapReduce streameléssel az Apache Hadoopon a HDInsightban](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Java MapReduce programok fejlesztése az Apache Hadoop számára a HDInsight on HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Az Azure Toolkit for Eclipse segítségével apache Spark-alkalmazásokat hozhat létre HDInsight-fürthöz](../spark/apache-spark-eclipse-tool-plugin.md)
* [Python-felhasználó által definiált függvények (UDF) használata Apache Hive-val és Apache Pig-rel a HDInsightban](python-udf-hdinsight.md)
