---
title: "Solr telepítése Linux-alapú hdinsight - Azure a parancsfájlművelet használatával |} Microsoft Docs"
description: "Ismerje meg, Solr Parancsfájlműveletek Linux-alapú HDInsight Hadoop-fürtök telepítése."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cc93ed5c-a358-456a-91a4-f179185c0e98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: larryfr
ms.openlocfilehash: c7a911474d6fb90f45565c90a72bfd407898ceba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Telepítheti és használhatja Solr HDInsight Hadoop-fürtök

Útmutató: Azure HDInsight Solr telepítése parancsfájlművelet segítségével. Solr egy hatékony keresési platform, és a Hadoop által kezelt adatok vállalati szintű keresési funkciókat biztosít.

> [!IMPORTANT]
    > A jelen dokumentumban leírt lépések egy HDInsight-fürt által használt Linux igényelnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!IMPORTANT]
> A dokumentumban használt parancsfájlpélda Solr 4.9 egy adott konfigurációval telepíti. Ha azt szeretné, a Solr fürt konfigurálásához a különböző gyűjteményekhez, szilánkok, sémákat, replikákat, stb., módosítania kell a parancsfájl és Solr bináris fájljait.

## <a name="whatis"></a>Mi az a Solr

[Apache Solr](http://lucene.apache.org/solr/features.html) egy vállalati keresési platform, amely lehetővé teszi az adatok hatékony teljes szöveges keresés. Hadoop lehetővé teszi a tárolásához és kezeléséhez az adatok óriási mennyiségben, Apache Solr biztosít a keresési képességek gyorsan az adatok lekéréséhez.

> [!WARNING]
> A HDInsight-fürt összetevői Microsoft teljes mértékben támogatja.
>
> Egyéni összetevők, például Solr, minden üzleti szempontból ésszerű terméktámogatási segítséget nyújtanak a probléma további hibaelhárításához. Előfordulhat, hogy a Microsoft támogatási nem egyéni összetevőkkel kapcsolatos problémák megoldásához. Szükség lehet a nyílt forráskódú Közösségek szólítsa meg, ha segítségre van szüksége. Például nincsenek sok közösségi webhelyek használható, például: [MSDN fórum hdinsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Is Apache projektek rendelkezik projekt helyek [http://apache.org](http://apache.org), például: [Hadoop](http://hadoop.apache.org/).

## <a name="what-the-script-does"></a>A parancsfájl funkciója

Ezt a parancsfájlt a HDInsight-fürt hajt végre a következő módosításokat:

* 4.9 Solr történő telepítése`/usr/hdp/current/solr`
* Létrehoz egy felhasználói **solrusr**, amely a Solr szolgáltatás futtatására szolgál
* Készletek **solruser** tulajdonosa`/usr/hdp/current/solr`
* Hozzáad egy [Upstart](http://upstart.ubuntu.com/) konfigurációjához, amely Solr automatikusan elindul.

## <a name="install"></a>A Parancsfájlműveletek segítségével Solr telepítése

Egy HDInsight-fürtök Solr telepítendő parancsfájlt a következő helyen érhető el:

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Hozzon létre egy fürtöt, amely rendelkezik a telepített Solr, kövesse a lépéseket a a [HDInsight-fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-portal.md) dokumentum. A létrehozási folyamat során a következő lépések segítségével Solr telepítése:

1. Az a __fürt összefoglaló__ szakaszban, select__Advanced settings__, majd __parancsfájl-műveletek__. Az űrlap feltöltéséhez használja az alábbi információkat:

   * **NÉV**: Adja meg a parancsfájlművelet rövid nevét.
   * **PARANCSFÁJL URI azonosítója**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **HEAD**: ezt a beállítást
   * **MUNKAVÉGZŐ**: ezt a beállítást
   * **ZOOKEEPER**: ezt a beállítást a Zookeeper csomóponton telepítése
   * **PARAMÉTEREK**: ezt a mezőt hagyja üresen

2. Alján a **parancsfájl-műveletek** szakaszban a **válasszon** gombra kattintva mentse a konfigurációt. Végül a **következő** gombra kattintva visszatérhet a __fürt összegzése__

3. Az a __fürt összefoglaló__ lapon jelölje be __létrehozása__ a fürt létrehozásához.

## <a name="usesolr"></a>Solr használata a Hdinsightban

> [!IMPORTANT]
> Ebben a szakaszban a lépések bemutatják, Solr alapvető funkciókat. Solr használatával kapcsolatos további információkért lásd: a [Apache Solr hely](http://lucene.apache.org/solr/).

### <a name="index-data"></a>Index adatok

Az alábbi lépések segítségével Példa adatok hozzáadása a Solr, és majd lekérdezése:

1. Csatlakozzon SSH-val a HDInsight-fürthöz:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

     > [!IMPORTANT]
     > A jelen dokumentum lépéseit az SSL-protokollbújtatás való csatlakozáskor használandó a Solr webes felhasználói felületen. Ezen lépések használatához, az SSL-protokollbújtatás létrehozására és a böngésző használják majd beállítása.
     >
     > További információkért lásd: a [használata SSH Tunneling hdinsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentum.

2. Az alábbi parancsokkal Solr index mintaadatok rendelkezik:

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

    A `post.jar` segédprogram hozzáadja a **solr.xml** és **monitor.xml** az index a dokumentumokat.
  
3. Használja a következő parancsot lekérdezéséhez Solr REST API:

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    Ez a parancs keres **collection1** megfelelő dokumentumokhoz  **\*:\***  (kódolva \*% 3A\* a lekérdezési karakterláncban). A következő JSON-dokumentum a válasz példája:

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

### <a name="using-the-solr-dashboard"></a>A Solr irányítópult használata

A Solr irányítópult a webes felhasználói felület, amely lehetővé teszi, hogy a webböngészőn keresztül Solr alkalmazásában. A Solr irányítópult nincs felfedve, közvetlenül a HDInsight-fürtök az interneten. Az SSH-alagút segítségével-e férni. További információ az SSH-alagút használatával, lásd: a [használata SSH Tunneling hdinsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentum.

Az SSH-alagút létesítése, tegye a következőket a Solr irányítópult használata:

1. Határozza meg az elsődleges headnode a host name:

   1. SSH használatával csatlakozhat az átjárócsomóponthoz. Például: `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

       További információ az SSH használatával, tekintse meg a [az SSH a Hdinsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

   2. Az alábbi parancs segítségével a teljesen minősített nevének beolvasása:

        ```bash
        hostname -f
        ```

        Ez a parancs értéket ad vissza a következő állomás neve hasonlít:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        Az érték lett visszaadva, akkor akkor menteni, mert a rendszer később.

2. A böngészőben csatlakozzon a **http://HOSTNAME:8983/solr / #/**, ahol **ÁLLOMÁSNÉV** az előző lépésben maghatározott neve.

    A kérelem az SSH-alagút, a fürt Solr webes felhasználói felületén keresztül történik. A lap jelenik meg az alábbi képen hasonlít:

    ![Solr irányítópult képe](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. A bal oldali ablaktáblán, használja a **Core választó** legördülő listán válassza ki a **collection1**. Több bejegyzést kell őket alatt jelennek meg **collection1**.

4. Az alábbi bejegyzései **collection1**, jelölje be **lekérdezés**. Töltse fel adatokkal a lapon a következő értékeket használja:

   * Az a **q** szöveget adja meg a  **\*:**\*. Ez a lekérdezés a indexelt dokumentumok Solr adja vissza. Ha szeretne keresni egy adott karakterláncot belül a dokumentumokhoz, karakterláncokat Itt adhatja meg.
   * Az a **wt** szöveg mezőben adja meg a kimeneti formátum. Alapértelmezett érték a **json**.

     Végül válassza ki a **lekérdezés végrehajtása** gombra a keresési pate alján.

     ![Parancsfájlművelet használni ahhoz, hogy a fürt](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     A kimenet a korábban hozzáadott az indexhez két dokumentumot ad vissza. A kimenet a következő JSON-dokumentum hasonlít:

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

### <a name="starting-and-stopping-solr"></a>Indítása és leállítása Solr

A következő parancsok segítségével manuálisan állítsa le és indítsa el a Solr:

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>Biztonsági mentési indexelt adatokat

Adatok biztonsági mentésének Solr az alapértelmezett tárhelyre, a fürt tegye a következőket:

1. Csatlakozzon a fürthöz SSH használatával, akkor a gazdagép nevét, az átjárócsomópont a következő paranccsal:

    ```bash
    hostname -f
    ```

2. Az alábbi parancs segítségével készít pillanatképet az indexelt adatokat. Cserélje le **ÁLLOMÁSNÉV** az előző parancs által visszaadott nevű:

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    A válasz a következő XML hasonlít:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. Módosítsa a könyvtárat a `/usr/hdp/current/solr/example/solr`. Nincs gyűjtemény itt alkönyvtárat. Minden gyűjtemény könyvtár neve tartalmazza a `data` ahhoz a gyűjteményhez a pillanatképet tartalmazó könyvtárat.

4. A pillanatkép mappája a tömörített archívum létrehozása, használja a következő parancsot:

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    Cserélje le a `snapshot.20150806185338855` a nevet a gyűjtemény a pillanatkép értékeket.

    Ezzel a paranccsal létrejön az archívum neve **snapshot.20150806185338855.tgz**, amely tartalmazza a tartalmát a **snapshot.20150806185338855** directory.

5. Majd tárolhatja az archívum a fürt elsődleges Storage a következő parancsot:

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Solr biztonsági mentés és visszaállítás használatához további információkért lásd: [https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups).

## <a name="next-steps"></a>Következő lépések

* [Giraph telepíthető a HDInsight-fürtök](hdinsight-hadoop-giraph-install-linux.md). A HDInsight Hadoop-fürtök Giraph telepítése a fürt testreszabási használatával. Giraph lehetővé teszi a végrehajtását diagramfeldolgozás Hadoop használatával, és az Azure HDInsight használható.

* [A HDInsight-fürtökön Hue telepítése](hdinsight-hadoop-hue-linux.md). HDInsight Hadoop-fürtök a Hue telepítése fürt testreszabási használata Hue olyan fürtökkel a Hadoop fürtök folytatott kommunikációhoz használható webalkalmazás.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
