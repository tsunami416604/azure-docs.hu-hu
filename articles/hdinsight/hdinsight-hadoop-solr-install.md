---
title: Hadoop-fürt - Azure Solr telepítendő parancsfájl művelettel |} Microsoft Docs
description: Megtudhatja, hogyan Solr parancsfájlművelet használata a HDInsight-fürtök testreszabása.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b1e6f338-8ac1-4b38-bbb5-2f7388b9de3b
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: caabe0fea6286c9439e8929b054d771868dcb6f1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="install-and-use-solr-on-windows-based-hdinsight-clusters"></a>Telepítheti és használhatja a Windows-alapú HDInsight-fürtök Solr

Útmutató: Windows-alapú HDInsight-fürtök testreszabása a parancsfájl műveletével Solr, és Solr segítségével történő keresés adatokat keressen.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések csak a Windows-alapú HDInsight-fürtök dolgozhat. HDInsight csak érhető el a Windows korábbi, mint a HDInsight 3.4-es verziójához. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). Egy Linux-alapú fürttel Solr használatáról információkért lásd: [telepítése és használata Solr a HDinsight Hadoop-fürtök (Linux)](hdinsight-hadoop-solr-install-linux.md).


Telepíthető Solr bármilyen típusú on Azure HDInsight (Hadoop-, Storm, HBase, Spark) fürt segítségével *parancsfájlművelet*. Egy minta parancsfájlt a HDInsight-fürtök Solr telepítendő érhető el, csak olvasható az Azure storage-blobból [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

A parancsfájlpéldát csak HDInsight-fürt verziószáma 3.1-es verziójával működik. A HDInsight-fürt verziókról további információkért lásd: [HDInsight-fürt verziókról](hdinsight-component-versioning.md).

A jelen témakörben használt minta parancsfájlt hoz létre a Windows-alapú Solr fürt adott konfigurációval. Ha azt szeretné, a Solr fürt konfigurálásához a különböző gyűjteményekhez, szilánkok, sémákat, replikákat, stb., módosítania kell a parancsfájl és Solr bináris ennek megfelelően.

**Kapcsolódó cikkek**

* [Telepítheti és használhatja Solr HDinsight Hadoop-fürtök (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Hdinsight Hadoop-fürtök létrehozása](hdinsight-provision-clusters.md): általános információk a HDInsight-fürtök létrehozása.
* [Testre szabhatja a HDInsight-fürtjéhez parancsfájlművelet][hdinsight-cluster-customize]: parancsfájlművelet HDInsight-fürtök testreszabása általános tájékoztatást.
* [Parancsfájlművelet-parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-solr"></a>Mi az a Solr?
<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> egy vállalati keresési platform, amely lehetővé teszi az adatok hatékony teljes szöveges keresés. Hadoop lehetővé teszi a tárolásához és kezeléséhez az adatok óriási mennyiségben, Apache Solr biztosít a keresési képességek gyorsan az adatok lekéréséhez.

## <a name="install-solr-using-portal"></a>Telepítse a portál használatával Solr
1. Indítsa el a fürt létrehozása a **egyéni létrehozás** beállítást, a részben ismertetett módon [Hadoop létrehozása a HDInsight-fürtök](hdinsight-provision-clusters.md).
2. A a **Parancsfájlműveletek** lapon kattintson a varázsló **parancsfájlművelet hozzáadása** adni a parancsfájlművelet alább látható módon:

    ![Parancsfájlművelet használni ahhoz, hogy a fürt](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "parancsfájlművelet használja a fürt testreszabása")

    <table border='1'>
        <tr><th>Tulajdonság</th><th>Érték</th></tr>
        <tr><td>Name (Név)</td>
            <td>Adja meg a parancsfájlművelet nevét. Például <b>telepítése Solr</b>.</td></tr>
        <tr><td>A parancsfájl URI azonosítója</td>
            <td>Adja meg az egységes erőforrás-azonosító (URI) a parancsfájlt, amelyet a fürt testreszabásához. Például <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Csomóponttípus</td>
            <td>Adja meg a csomópontok, amelyen fut a testreszabási parancsfájl. Választhat <b>csomópontjaihoz</b>, <b>csak Átjárócsomópontokat</b>, vagy <b>csak a feldolgozó csomópontok</b>.
        <tr><td>Paraméterek</td>
            <td>Adja meg a paraméterek, ha a parancsfájl által igényelt. Solr telepítéséhez a parancsfájlt nem szükséges paramétereket, ezért üresen hagyhatja, ez.</td></tr>
    </table>

    Több összetevőinek telepítése a fürt több parancsfájlművelet adhat hozzá. Miután hozzáadta a parancsfájlok, kattintson a pipára a fürt létrehozásához.

## <a name="use-solr"></a>A Solr használata
Az indexelő Solr bizonyos adatok fájlokkal kell elindítani. Solr az indexelt adatokat keresési lekérdezések futtatásához használhatja. A következő lépésekkel Solr használata a HDInsight-fürtöt:

1. **Segítségével Remote Desktop Protocol (RDP) távoli a HDInsight-fürthöz a telepített Solr**. Azure-portálról a távoli asztal engedélyezése a fürthöz létrehozott Solr telepítve, és ezután távolról, a fürt számára. Útmutatásért lásd: [csatlakozás RDP Funkciót használnak a HDInsight-fürtök](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Fájlok feltöltésével index Solr**. Index Solr, amikor a dokumentumok elhelyezése, amely a keresni szeretne. Index Solr, segítségével RDP távoli a fürthöz, keresse meg az asztalon, nyissa meg a Hadoop parancssort, és keresse meg a **C:\apps\dist\solr-4.7.2\example\exampledocs**. Futtassa az alábbi parancsot:

        java -jar post.jar solr.xml monitor.xml

    A következő eredmény jelenik meg a konzolon:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    A post.jar segédprogram Solr indexeli a két minta dokumentumok **solr.xml** és **monitor.xml**. A post.jar segédprogram és a minta dokumentumok Solr telepítés érhetők el.
3. **Az indexelt dokumentumok keresni a Solr Irányítópult segítségével**. A HDInsight-fürthöz RDP-munkamenetet, nyissa meg az Internet Explorert, és indítsa el a következő Solr irányítópult **http://headnodehost:8983/solr/#/**. A bal oldali ablaktáblán az a **Core választó** legördülő listából válassza **collection1**, belül, amely, kattintson **lekérdezés**. Tegyük fel válassza ki, és adja vissza a dokumentumok Solr, adja meg a következő értékeket:

   * Az a **q** szöveget adja meg a  **\*:**\*. Ezzel visszatér a indexelt dokumentumok a Solr. Ha szeretne keresni egy adott karakterláncot belül a dokumentumokhoz, karakterláncokat Itt adhatja meg.
   * Az a **wt** szöveg mezőben adja meg a kimeneti formátum. Alapértelmezett érték a **json**. Kattintson a **lekérdezés végrehajtása**.

     ![Parancsfájlművelet használni ahhoz, hogy a fürt](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Solr irányítópult-lekérdezés futtatható")

     A kimenet az indexelés Solr használt két docs adja vissza. A kimenet az alábbihoz hasonló:

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
4. **Ajánlott: Az adatok biztonsági mentésének indexelt Solr az Azure Blob Storage a HDInsight-fürthöz társított**. Jó gyakorlat készítsen biztonsági másolatot az indexelt adatokat a Solr fürtcsomópontok alakzatot Azure Blob Storage tárolóban. A következő lépésekkel ehhez:

   1. Az RDP-munkamenetet nyissa meg az Internet Explorert, és mutasson a következő URL-címe:

           http://localhost:8983/solr/replication?command=backup

       A következőhöz hasonló választ kell megjelennie:

           <?xml version="1.0" encoding="UTF-8"?>
           <response>
             <lst name="responseHeader">
               <int name="status">0</int>
               <int name="QTime">9</int>
             </lst>
             <str name="status">OK</str>
           </response>
   2. A távoli munkamenet, lépjen a {SOLR_HOME}\{gyűjtemény} \data. A parancsfájlpéldát létrehozott fürt, amely legyen **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. Ezen a helyen kell megjelennie a pillanatkép mappája létrehozott hasonló nevű **pillanatkép.* Timestamp típusú x.
   3. A ZIP-a pillanatkép mappája, majd töltse fel az Azure Blob Storage tárolóban. A Hadoop parancssorból keresse meg a pillanatkép-mappa helye a következő paranccsal:

             hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

       Ez a parancs másolja át a pillanatkép /example/data/belül az alapértelmezett tárfiók a fürthöz tartozó tárolóban.

## <a name="install-solr-using-aure-powershell"></a>Telepítse a következőkre PowerShell-lel Solr
Lásd: [testreszabása HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  A Spark az Azure PowerShell telepítése mutatja be. Meg kell adnia, hogy a használandó parancsfájlt [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Telepítse a .NET SDK használatával Solr
Lásd: [testreszabása HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). A minta bemutatja, hogyan telepítse a .NET SDK használatával Spark. Meg kell adnia, hogy a használandó parancsfájlt [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="see-also"></a>Lásd még
* [Telepítheti és használhatja Solr HDinsight Hadoop-fürtök (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Hdinsight Hadoop-fürtök létrehozása](hdinsight-provision-clusters.md): általános információk a HDInsight-fürtök létrehozása.
* [Testre szabhatja a HDInsight-fürtjéhez parancsfájlművelet][hdinsight-cluster-customize]: parancsfájlművelet HDInsight-fürtök testreszabása általános tájékoztatást.
* [Parancsfájlművelet-parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions.md).
* [Telepítse, és válassza a Spark on HDInsight-fürtök][hdinsight-install-spark]: Spark telepítéséről parancsfájlművelet-mintát.
* [Giraph telepíthető a HDInsight-fürtök](hdinsight-hadoop-giraph-install.md): parancsfájlművelet minta Giraph telepítésével kapcsolatban.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
