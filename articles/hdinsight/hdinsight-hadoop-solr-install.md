---
title: A Solr telepítése a Hadoop-fürt – az Azure használatával
description: Útmutató a HDInsight-fürt testreszabása a Script actionnel Solr.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 749a599936825f5f69ae18affad0fa89a4f1118f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259631"
---
# <a name="install-and-use-apache-solr-on-windows-based-hdinsight-clusters"></a>Telepítse és Apache Solr használata Windows-alapú HDInsight-fürtökön

Ismerje meg a Windows-alapú HDInsight-fürt testreszabása a Script actionnel Apache Solr, és a Solr használata adatok kereséséhez.

> [!IMPORTANT]  
> A jelen dokumentumban leírt lépések csak a Windows-alapú HDInsight-fürtökkel működik. HDInsight csak akkor használható a Windows-verziók alacsonyabb, mint a HDInsight 3.4-es. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). A Solr használata a Linux-alapú fürt információkért lásd: [telepítése és az Apache Solr használata a HDinsight Hadoop-fürtök (Linux)](hdinsight-hadoop-solr-install-linux.md).


Telepítheti a Solr Azure HDInsight (Hadoop-, Storm, HBase, Spark-) fürt bármilyen típusú használatával *parancsfájlművelet*. Egy mintaszkriptet telepítéséhez a Soirt egy HDInsight-fürtön érhető el, csak olvasható Azure storage-blobból [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

A minta parancsfájl a csak a HDInsight-fürt verziója 3.1 működik. A HDInsight fürt verziókról további információkért lásd: [HDInsight fürtverziók](hdinsight-component-versioning.md).

A minta parancsfájl a jelen témakörben használt egyedi konfigurációs Solr Windows-alapú fürt hoz létre. Ha azt szeretné, a Solr fürt konfigurálására a különböző gyűjteményekhez, szegmensek, sémákat, replikák és az egyéb, módosítania kell a parancsfájlt és a Solr bináris ennek megfelelően.

**Kapcsolódó cikkek**

* [Telepítse és Apache Solr használata a HDinsight Hadoop-fürtök (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [A HDInsight Hadoop-fürtök létrehozása](hdinsight-provision-clusters.md): általános információk a HDInsight-fürtök létrehozása során.
* [Szkriptműveletek használatával HDInsight-fürt testreszabása][hdinsight-cluster-customize]: általános információk a Testreszabás szkriptműveletekkel HDInsight-fürtök.
* [Parancsfájlművelet-parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-solr"></a>Mi az a Solr?
<a href="https://lucene.apache.org/solr/features.html" target="_blank">Az Apache Solr</a> egy vállalati keresési platform, amely lehetővé teszi az adatok hatékony teljes szöveges keresés. Míg a Hadoop tárolására és kezelésére, az adatok óriási mennyiségben, Apache Solr biztosít a keresési funkciókkal gyorsan az adatok lekéréséhez.

## <a name="install-solr-using-portal"></a>Portallal Solr telepítése
1. Indítsa el a fürt létrehozása a **egyéni létrehozás** paraméterrel, ahogyan a [Apache Hadoop-fürtök létrehozása a HDInsight](hdinsight-provision-clusters.md).
2. A a **Parancsfájlműveletek** lapon kattintson a varázsló **parancsfájlművelet hozzáadása** , adja meg a parancsfájlművelet adatait, ahogy az alábbi:

    ![Fürt testreszabása Szkriptműveletek használatával](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Szkriptműveleteket használata a fürt testreszabása")

    <table border='1'>
        <tr><th>Tulajdonság</th><th>Érték</th></tr>
        <tr><td>Name (Név)</td>
            <td>Adja meg a parancsfájlművelet nevét. Ha például <b>Solr telepítése</b>.</td></tr>
        <tr><td>Szkript URI-ja</td>
            <td>Adja meg az egységes erőforrás-azonosító (URI) a parancsfájlt, amely a fürt testreszabásához meghívott. Ha például <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Csomóponttípus</td>
            <td>Adja meg a csomópontok, amelyen fut a testreszabási parancsfájlt. Választhat <b>minden csomópont</b>, <b>csak Átjárócsomópontokhoz</b>, vagy <b>csak a munkavégző csomópontok</b>.
        <tr><td>Paraméterek</td>
            <td>Adja meg a paramétereket, ha a parancsfájl által igényelt. A szkriptet a Solr telepítése nem szükséges paramétereket, így is ezt üresen hagyja.</td></tr>
    </table>

    Hozzáadhat több parancsfájlművelet több összetevők telepíthetők a fürtön. Miután hozzáadta a parancsfájlok, kattintson a pipa jelre a fürt létrehozásának megkezdéséhez.

## <a name="use-solr"></a>A Solr használata
A Solr indexelő bizonyos adatok fájlokkal kell elindítani. Ezután használhatja a Solr az indexelt adatok keresési lekérdezések futtatására. Hajtsa végre az alábbi lépéseket a Solr használata egy HDInsight-fürtön:

1. **Távoli asztal protokoll (RDP) való használható távoli a HDInsight-fürtbe a Solr telepítése**. Az Azure Portalról létrehozott Solr telepítve van, és ezután távolról, a fürt a fürt a távoli asztal engedélyezése. Útmutatásért lásd: [csatlakozhat a HDInsight-fürtök RDP-vel](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Index Solr adatfájlok feltöltésével**. Solr indexeli, amikor a dokumentumok elhelyezése, amelyek szükség lehet a kereséshez. Solr indexelni, a fürtbe való távoli RDP használja, az asztalon nyissa meg, nyissa meg a Hadoop parancssor és keresse meg a **C:\apps\dist\solr-4.7.2\example\exampledocs**. Futtassa az alábbi parancsot:

        java -jar post.jar solr.xml monitor.xml

    A következő kimenet jelenik meg a konzolon:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    A post.jar segédprogram Solr indexeli a két Mintadokumentumokat **solr.xml** és **monitor.xml**. A post.jar segédprogram és a Mintadokumentumokat Solr telepítése érhetők el.
3. **A Solr Irányítópult segítségével keresni az indexelt dokumentumok**. Az RDP-munkamenetet a HDInsight-fürthöz, nyissa meg az Internet Explorert, és a Solr irányítópult elindításához **http://headnodehost:8983/solr/#/**. A bal oldali ablaktáblán az a **Core választó** legördülő menüben válassza **collection1**, belül, kattintson a **lekérdezés**. Tegyük fel válassza ki, és adja vissza minden docs Solr, adja meg a következő értékeket:

   * Az a **q** szöveget adja meg  **\*:**\*. Ez a indexelt összes dokumentumot visszaadja a Solr. Ha szeretne keresni egy adott karakterláncot a dokumentumokon belül, tulajdonság: Itt adhatja meg.
   * Az a **wt** szöveg mezőben adja meg a kimeneti formátumot. Alapértelmezett érték a **json**. Kattintson a **lekérdezés végrehajtása**.

     ![Fürt testreszabása Szkriptműveletek használatával](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "-lekérdezést futtathat Solr irányítópult")

     A kimenet a két docs, amelyet is használt indexelő Solr adja vissza. A kimenet az alábbihoz hasonló:

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
4. **Ajánlott: Az Azure Blob storage a HDInsight-fürthöz társított Solr indexelt adatainak biztonsági mentése**. Jó megoldás készítsen biztonsági mentést az indexelt adatok Azure Blob storage-be a Solr fürtcsomópontok. A következő lépésekkel ehhez:

   1. Az RDP-munkamenetből nyissa meg az Internet Explorert, és a következő URL-címre mutasson:

           http://localhost:8983/solr/replication?command=backup

       A következőhöz hasonló választ kell megjelennie:
            
      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
          <response>
             <lst name="responseHeader">
               <int name="status">0</int>
               <int name="QTime">9</int>
             </lst>
            <str name="status">OK</str>
          </response>
      ```
      
   2. A távoli munkamenet, lépjen a {SOLR_HOME}\{gyűjtemény} \data. A parancsfájlpéldát létrehozott fürt, ez legyen `C:\apps\dist\solr-4.7.2\example\solr\collection1\data`. Ezen a helyen kell megjelennie egy hasonló nevű létrehozott pillanatképmappa **pillanatkép.* időbélyeg x.
   
   3. Zip-mappába, és töltse fel az Azure Blob storage. A Hadoop parancssori paranccsal futtathatja keresse meg a pillanatkép-mappa helye a következő paranccsal:

      ```
      hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data
      ```

   Ez a parancs másolja át a pillanatkép, /example/data/alapértelmezés szerint a fürthöz társított tárfiókban lévő azon tároló alatt.

## <a name="install-solr-using-azure-powershell"></a>Azure PowerShell-lel a Solr telepítése
Lásd: [testreszabása HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  A minta azt ismerteti, hogyan telepítheti az Apache Spark az Azure PowerShell-lel. Testre kell szabnia a használandó parancsfájlt [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>A Solr használata a .NET SDK telepítése
Lásd: [testreszabása HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). A minta azt ismerteti, hogyan telepítheti az Apache Spark, a .NET SDK használatával. Testre kell szabnia a használandó parancsfájlt [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="see-also"></a>Lásd még
* [Telepítse és Apache Solr használata a HDinsight Hadoop-fürtök (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [A HDInsight Apache Hadoop-fürtök létrehozása](hdinsight-provision-clusters.md): általános információk a HDInsight-fürtök létrehozása során.
* [Szkriptműveletek használatával HDInsight-fürt testreszabása][hdinsight-cluster-customize]: általános információk a Testreszabás szkriptműveletekkel HDInsight-fürtök.
* [Parancsfájlművelet-parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions.md).
* [Telepítse, és az Apache Spark használata a HDInsight-fürtökön][hdinsight-install-spark]: A művelet példaszkript Spark telepítésével kapcsolatban.
* [Az Apache Giraph telepítése HDInsight-fürtökön](hdinsight-hadoop-giraph-install.md): A művelet példaszkript Giraph telepítésével kapcsolatban.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
