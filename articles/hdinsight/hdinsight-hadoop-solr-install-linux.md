---
title: A Solr telepítése Linux-alapú HDInsight - Azure Szkriptműveleteket használata
description: Ismerje meg, a Solr telepítése Linux-alapú HDInsight Hadoop-fürtökön parancsfájlműveletekkel.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: 35a7410a5a30e248069ba31ad4213eff58680dcc
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597769"
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Soir telepítése és használata a HDInsight Hadoop-fürtök

Ismerje meg, a Solr telepítése Azure HDInsight Script Action használatával. A Solr egy hatékony keresési platform, és a vállalati szintű keresési funkciókat biztosít a Hadoop által kezelt adatok.

> [!IMPORTANT]
    > A dokumentum lépéseinek elvégzéséhez egy Linux-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!IMPORTANT]
> Az itt bemutatott minta parancsfájl Solr 4.9 adott konfigurációval telepíti. Ha azt szeretné, a Solr fürt konfigurálására a különböző gyűjteményekhez, szegmensek, sémákat, replikák és az egyéb, módosítania kell a parancsfájlt és a Solr bináris fájlokat.

## <a name="whatis"></a>Mi az a Solr

[Az Apache Solr](http://lucene.apache.org/solr/features.html) egy vállalati keresési platform, amely lehetővé teszi az adatok hatékony teljes szöveges keresés. Míg a Hadoop tárolására és kezelésére, az adatok óriási mennyiségben, Apache Solr biztosít a keresési funkciókkal gyorsan az adatok lekéréséhez.

> [!WARNING]
> A HDInsight-fürthöz megadott összetevők teljes mértékben támogatja a Microsoft által.
>
> Egyéni összetevők, például a Solr, annak érdekében, hogy a probléma további hibaelhárításához üzletileg ésszerű támogatást kapnak. Nem lehet egyéni összetevőkkel kapcsolatos problémák megoldásához a Microsoft ügyfélszolgálatához. Szükség lehet a nyílt forráskódú Közösségek végezhetnek segítségért. Számos, használható, például közösségi helyek vannak, például: [HDInsight az MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Is Apache projektek rendelkeznek projekt helyek [ http://apache.org ](http://apache.org), például: [Hadoop](http://hadoop.apache.org/).

## <a name="what-the-script-does"></a>A parancsfájl leírása

Ez a szkript az alábbi módosításokat végzi, a HDInsight-fürthöz:

* 4.9 a be telepíti Solr `/usr/hdp/current/solr`
* Létrehoz egy felhasználót **solrusr**, amely a Solr szolgáltatás futtatásához használt
* Csoportok **solruser** tulajdonosként `/usr/hdp/current/solr`
* Hozzáad egy [Upstart](http://upstart.ubuntu.com/) konfigurációt, amely automatikusan elindul, a Solr.

## <a name="install"></a>A Parancsfájlműveletek segítségével Solr telepítése

Egy mintaszkriptet telepítéséhez a Soirt egy HDInsight-fürtön a következő helyen érhető el:

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Szereplő lépések segítségével hozzon létre egy fürtöt, amely a Solr telepítve van, a [létre HDInsight-fürtök](hdinsight-hadoop-create-linux-clusters-portal.md) dokumentumot. A létrehozási folyamat során a következő lépések segítségével a Solr telepítése:

1. Az a __fürt összegzése__ szakasz, select__Advanced settings__, majd __Szkriptműveletek__. Az alábbi információk segítségével töltse ki az űrlapot:

   * **NÉV**: Adjon egy rövid nevet a parancsprogram-művelet.
   * **SZKRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **A fő**: ezt a beállítást választva
   * **FELDOLGOZÓ**: ezt a beállítást választva
   * **ZOOKEEPER**: ezt a beállítást választva a Zookeeper-csomópont telepítése
   * **PARAMÉTEREK**: hagyja üresen a mezőt

2. Alsó részén a **Szkriptműveletek** szakaszban a **kiválasztása** gombra a konfiguráció mentéséhez. Végül a **tovább** gombra kattintva visszatérhet a __fürt összegzése__

3. Az a __fürt összegzése__ lapon jelölje be __létrehozás__ a fürt létrehozásához.

## <a name="usesolr"></a>A Solr használata a HDInsight

> [!IMPORTANT]
> A jelen szakaszban ismertetett lépések bemutatják, Solr alapvető funkciói. A Solr használata további információkért lásd: a [Apache Solr hely](http://lucene.apache.org/solr/).

### <a name="index-data"></a>Adatok indexelése

Solr példa adatok hozzáadása a következő lépések segítségével, és ezután lekérdezéseket futtathat rajta:

1. Csatlakozzon SSH-val a HDInsight-fürthöz:

    > [!NOTE]
    > Cserélje le `sshuser` és a fürthöz az SSH-felhasználót. Cserélje le `clustername` a fürt nevére.

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

     > [!IMPORTANT]
     > Később a jelen dokumentumban leírt lépések az SSH-alagút használatával csatlakozni a Solr webes felhasználói felületen. Kövesse az alábbi lépéseket, egy SSH-alagutat létesíteni, és majd a böngészőben konfigurálni kell használni.
     >
     > További információkért lásd: a [SSH-bújtatással való HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentumot.

2. A következő parancsok használatával Solr index mintaadatok rendelkezik:

    ```bash
    cd /usr/hdp/current/solr/example/exampledocs
    java -jar post.jar solr.xml monitor.xml
    ```

    A következő kimenetet visszaadja a konzolhoz:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    A `post.jar` segédprogram hozzáadja a **solr.xml** és **monitor.xml** dokumentumok az indexbe.
  
3. Használja a következő parancsot a Solr REST API lekérdezéséhez:

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    Ez a parancs keres **collection1** az egyező dokumentumok **\*:\*** (kódolásban \*% 3A\* a lekérdezési karakterlánc). A következő JSON-dokumentum a válasz egy példát:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>A Solr irányítópultjának használata

A Solr irányítópult egy webes felhasználói felület, amely lehetővé teszi, hogy a Solr a webböngészőn keresztül. A Solr irányítópult nem közvetlenül az interneten a HDInsight-fürtből származó lesz közzétéve. Az SSH-alagút segítségével-e férni. SSH-alagút használatával kapcsolatos további információkért lásd: a [SSH-bújtatással való HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentumot.

Létesítése után SSH-alagút, a következő lépések segítségével a Solr irányítópult:

1. Határozza meg az állomásnév az elsődleges átjárócsomóponthoz:

   1. Az SSH használatával a fürt átjárócsomópontjához csatlakozik. Például: `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

       SSH használatával kapcsolatos további információkért lásd: a [az SSH használata a HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

   2. Használja a következő parancsot a teljesen minősített állomásnév beolvasásához:

        ```bash
        hostname -f
        ```

        Ez a parancs hasonló ad vissza értéket a következő állomásnév:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        Mentse az értéket ad vissza, később szolgál.

2. Csatlakozás a böngészőben, **http://HOSTNAME:8983/solr/#/**, ahol **ÁLLOMÁSNÉV** az előző lépésben maghatározott neve.

    A kérelem a Solr webes felhasználói felület a fürtön SSH-alagúton keresztül van átirányítva. Az oldal jelenik meg az alábbi képhez hasonló:

    ![A Solr irányítópult képe](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. A bal oldali ablaktáblán, használja a **Core választó** válassza ki a legördülő **collection1**. Több bejegyzést kell őket alatt jelennek meg **collection1**.

4. Az alábbi bejegyzések **collection1**válassza **lekérdezés**. A lapon feltöltéséhez használja a következő értékeket:

   * Az a **q** szöveget adja meg  **\*:**\*. Ez a lekérdezés a indexelt dokumentumok Solr adja vissza. Ha szeretne keresni egy adott karakterláncot a dokumentumokon belül, tulajdonság: Itt adhatja meg.
   * Az a **wt** szöveg mezőben adja meg a kimeneti formátumot. Alapértelmezett érték a **json**.

     Végül válassza ki a **lekérdezés végrehajtása** alján, a keresési illessze be a gombra.

     ![Fürt testreszabása Szkriptműveletek használatával](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     A kimenet az indexhez korábban hozzáadott két dokumentumot ad vissza. A kimenet a következő JSON-dokumentum hasonlít:

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }

### <a name="starting-and-stopping-solr"></a>Indítása és leállítása a Solr

A következő parancsok használatával manuálisan állítsa le és indítsa el a Solr:

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>Biztonsági mentési adatok indexelése

A következő lépések segítségével Solr adatok biztonsági mentésére a a fürt alapértelmezett tárolója:

1. A fürthöz SSH használatával csatlakozhat, majd a következő paranccsal kérje le a gazdagép nevét, az átjárócsomópont:

    ```bash
    hostname -f
    ```

2. A következő paranccsal hozzon létre egy indexelt adatairól készült pillanatfelvételre. Cserélje le **ÁLLOMÁSNÉV** az előző parancs által visszaadott nevét:

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    A válasz a következő XML formátumú hasonlít:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. Módosítsa a könyvtárakat a `/usr/hdp/current/solr/example/solr`. Az egyes gyűjtemények itt alkönyvtárban van. Minden gyűjtemény könyvtár tartalmaz egy `data` ahhoz a gyűjteményhez a pillanatképet tartalmazó könyvtárra.

4. Hozzon létre egy tömörített archívumot a pillanatképek mappájához, használja a következő parancsot:

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    Cserélje le a `snapshot.20150806185338855` értékeket a gyűjtemény a pillanatkép neve.

    Ez a parancs létrehoz egy nevű archívum **snapshot.20150806185338855.tgz**, a tartalmát, amely tartalmazza a **snapshot.20150806185338855** könyvtár.

5. Ezután tárolhatja az archívum a fürt elsődleges Storage a következő paranccsal:

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

A Solr biztonsági mentés és visszaállítás való használatáról további információkért lásd: [ https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups ](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups).

## <a name="next-steps"></a>További lépések

* [A Giraph telepítése HDInsight-fürtökön](hdinsight-hadoop-giraph-install-linux.md). Fürt testreszabása használatával a Giraph telepítése HDInsight Hadoop-fürtökön. A Giraph lehetővé teszi a Hadoop használatával diagramfeldolgozási végrehajtásához, és használható az Azure HDInsight.

* [A Hue telepítése HDInsight-fürtökön](hdinsight-hadoop-hue-linux.md). Fürt testreszabása használatával a Hue telepítése HDInsight Hadoop-fürtökön. A hue egy Hadoop-fürtökkel folytatott kommunikációhoz használható webalkalmazás.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
