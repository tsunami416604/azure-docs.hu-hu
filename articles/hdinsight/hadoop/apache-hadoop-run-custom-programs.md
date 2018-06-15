---
title: Egyéni MapReduce programok - futtatásához Azure HDInsight |} Microsoft Docs
description: Mikor és hogyan HDInsight egyéni MapReduce-programok futtatását.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: 94d199642b409a2fd087ec1543651031a907d09f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31403032"
---
# <a name="run-custom-mapreduce-programs"></a>Egyéni MapReduce programok futtatása

Például a HDInsight Hadoop-alapú big Data típusú adatok rendszerek engedélyezése az adatfeldolgozás számos különféle eszközök és technológiák használata. A következő táblázat a fő előnyeit és mindegyik szempontokat ismerteti.

| Lekérdezés mechanizmus | Előnyei | Megfontolandó szempontok |
| --- | --- | --- |
| **Hive HiveQL használatával** | <ul><li>Kötegfeldolgozási és nagy mennyiségű nem módosítható adatok elemzésére, adatainak összefoglalója és a kiváló megoldás az igény szerinti lekérdezése. A megszokott SQL-szerű szintaxist használja azt.</li><li>Könnyen particionálta és indexelt adatok állandó táblák létrehozására használható.</li><li>Több külső táblák és nézetek ugyanazokat az adatokat is létrejöhet.</li><li>Támogatja a egyszerű adatok adatraktár megvalósítása, amely jelentős kibővített és hibatűrő képességeket biztosít adatokat tárolás és feldolgozás céljából.</li></ul> | <ul><li>Az adatforrás rendelkezik legalább egy azonosítható struktúra igényel.</li><li>Nincs valós idejű lekérdezéseket és a sor szintű frissítések alkalmas. Legjobb szolgál a kötegelt feladatok nagy adatkészleteken keresztül.</li><li>Nem lehet elvégezni az összetett feladatokat bizonyos típusú képes.</li></ul> |
| **A Pig Latin a Pig használatával** | <ul><li>Kiváló megoldás szerint állítja be adatok kezelésére, az egyesítés és szűrés adatkészletek esetében a funkciók alkalmazása és olyan csoportokra, és az adatok átalakítása oszlopok meghatározásával, csoportosítási értékek vagy oszlopokat sorokra átalakításával.</li><li>Egy munkafolyamat-alapú módszert használni tudja adatok műveletek sorozatot.</li></ul> | <ul><li>Az SQL-felhasználók is található, a Pig Latin kevésbé ismert és nehezebb használható, mint a HiveQL.</li><li>Az alapértelmezett kimenet általában egy szövegfájlt, és így is több használata is nehézkes lehet a képi megjelenítés eszközökkel, például az Excel. Általában olyan Hive táblát fog réteg a kimeneti keresztül.</li></ul> |
| **Egyéni térkép vagy csökkentése** | <ul><li>A térkép teljes ellenőrzést biztosít, és csökkentheti a fázisok és végrehajtási.</li><li>Ez lehetővé teszi, hogy a lekérdezések optimalizálását a fürtből a legnagyobb teljesítmény eléréséhez, vagy a kiszolgálók és a hálózat terhelése minimalizálása érdekében.</li><li>Az összetevők a jól ismert nyelvek széles is beírhatók.</li></ul> | <ul><li>Azonban nehezebb, mint a Pig- vagy Hive használata, mert létre kell hoznia a saját térkép és csökkentse az összetevők.</li><li>Adatok csatlakoztatása igénylő folyamatok nehezebb megvalósításához.</li><li>Annak ellenére, hogy nincsenek elérhető teszt keretrendszerek, kód hibakereséséhez bonyolultabb, mint egy normál alkalmazást, mert a kód lefut egy kötegelt a Hadoop Feladatütemező felügyelete alatt.</li></ul> |
| **HCatalog** | <ul><li>Az útvonal adatai a tárolás, felügyelet könnyítve és a felhasználók számára az adatok tárolására szükség kivonatolja azt.</li><li>Értesítési események, például az adatok rendelkezésre állását, így más eszközökkel, például műveletek történt észlelése Oozie lehetővé teszi.</li><li>Az adatok, beleértve a gombot, particionálás relációs nézetben mutatja, és megkönnyíti, az adatok eléréséhez.</li></ul> | <ul><li>Az támogatja-e RCFile, CSV-szöveg, JSON-szöveg, SequenceFile és ORC fájlformátumok alapértelmezés szerint, de szükség lehet egy egyéni SerDe más formátumairól írni.</li><li>HCatalog nincs szálbiztos.</li><li>Nincsenek bizonyos korlátozások vonatkoznak az oszlopok adattípusai az HCatalog-betöltő a Pig-parancsfájlok használatával. További információkért lásd: [HCatLoader adattípusok](http://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) az Apache HCatalog dokumentációjában.</li></ul> |

Általában akkor használják a legegyszerűbb megoldás, amely képes biztosítani az eredmények van szüksége. Például előfordulhat, hogy csak a Hive eszközzel ilyen eredmények érhet el, de összetett forgatókönyvek esetén szükség lehet használni a Pig, vagy akár saját térkép írásához és csökkentse az összetevők. Is előfordulhat, hogy a Hive vagy a Pig, hogy egyéni ábrázolásiterület kísérletezés után döntse el, és csökkentse összetevők jobb teljesítményt tud nyújtani azáltal, hogy finomhangolásához, és optimalizálja a feldolgozása.

## <a name="custom-mapreduce-components"></a>Egyéni térkép/csökkentése összetevők

Térkép/csökkentése kód áll valósul meg két külön funkció **térkép** és **csökkentése** összetevőket. A **térkép** összetevő fut párhuzamosan több fürtcsomóponton, minden csomópont, a hozzárendelés alkalmazása az adatok csomópont saját részét. A **csökkentése** összetevő sorrendbe állítja és összefoglalja a térkép összes funkciójában eredményeit. A két összetevő további információkért lásd: [használata MapReduce a Hadoop on HDInsight](hdinsight-use-mapreduce.md).

A legtöbb forgatókönyveket HDInsight esetében egyszerűbben és hatékonyabban egy magasabb szintű absztrakció, például a Pig- vagy Hive használatára. Hozhat létre egyéni ábrázolásiterület, és csökkentse az összetevők Hive parancsfájlok kifinomultabb feldolgozás elvégzéséhez belüli használatra.

Egyéni térkép/csökkentése összetevők általában Java nyelven írt. Hadoop egy adatfolyam-továbbítási felületet biztosít, amely lehetővé teszi használandó összetevők, például a C#, F #, Visual Basic, Python, és a JavaScript más nyelveken fejlesztett.

* Az egyéni Java MapReduce programok bemutatóért lásd: [programok Java MapReduce fejlesztése a HDInsight Hadoop](apache-hadoop-develop-deploy-java-mapreduce-linux.md).
* Egy példa, Python használatával, olvassa el [MapReduce programok streaming hdinsight fejlesztése Python](apache-hadoop-streaming-python.md).

Érdemes lehet létrehozni a saját leképezés, és csökkentse az összetevők a következő feltételeket:

* Az adatok elemzése és egyéni logika használatával strukturált adatok lekérését teljesen strukturálatlan adatok feldolgozásához szüksége.
* Szeretne végezni az összetett feladatok, amelyek a nehezen (vagy lehetetlen) a Pig Express vagy Hive egy UDF létrehozása nélkül. Szükség lehet például, hogy egy külső geokódolás szolgáltatással szélességi és hosszúsági koordinátákkal vagy IP-címek a forrásadatok átalakítása földrajzi helyek neve.
* Újra fel kívánja a meglévő .NET, Python vagy JavaScript kódot az összetevők térkép vagy csökkentse a Hadoop-Stream felület használatával.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Töltse fel, és az egyéni MapReduce program futtatása

A leggyakoribb MapReduce programok Java nyelven írt, és fordítva a jar-fájlra.

1. Miután fejlesztett, lefordítva, és a MapReduce-program tesztelése, használja a `scp` feltölteni a jar-fájlra a headnode parancsot.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Cserélje le **felhasználónév** a fürthöz SSH felhasználói fiókkal. Cserélje le **CLUSTERNAME** a fürt nevéhez. Ha a jelszót biztonságos SSH-fiókjának biztonságát, a jelszó megadására kéri. Ha a tanúsítványt használja, előfordulhat, hogy szüksége a `-i` paraméterrel adhatja meg a titkos kulcs fájlja.

2. A fürthöz történő csatlakozás [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Az SSH-munkamenetből keresztül YARN a MapReduce programot végrehajtani.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Ez a parancs elküldi a YARN a MapReduce feladatot. A bemeneti fájl `/example/data/sample.log`, és a kimeneti könyvtár `/example/data/logoutput`. A bemeneti és kimeneti fájlokat tároló a fürt alapértelmezett tárolóhelyét.

## <a name="next-steps"></a>További lépések

* [C# használata a hdinsight Hadoop streamelési MapReduce](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [A hdinsight Hadoop Java MapReduce programok fejlesztése](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [A HDInsight MapReduce programok streaming Python fejlesztése](apache-hadoop-streaming-python.md)
* [Azure eszköztára Eclipse használata Spark-alkalmazások a HDInsight-fürtök létrehozása](../spark/apache-spark-eclipse-tool-plugin.md)
* [Használjon Python felhasználói függvény (UDF), a Hive és a Pig a Hdinsightban](python-udf-hdinsight.md)
