---
title: "R használata a HDInsight-fürtök - Azure testreszabása |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse az R parancsfájl művelet segítségével, és a HDInsight-fürtök R használhatja."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: be851270-afa5-4af0-a69e-2d343a4deeb7
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 5b9b793d49217acd9f0c6c518596a7afb5600d69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Az R környezet telepítése és használata HDInsight-beli Hadoop-fürtökön

Ismerje meg, hogyan szabhatja testre a Windows alapú HDInsight-fürt az R parancsfájl műveletével, és R használata a HDInsight-fürtök. A [HDInsight ajánlat](https://azure.microsoft.com/pricing/details/hdinsight/) tartalmaz R Server a HDInsight-fürt részeként. Ez lehetővé teszi az R parancsfájlok használata a MapReduce és Spark elosztott számítások futtatásához. További információk: [Get started using R Server on HDInsight](hdinsight-hadoop-r-server-get-started.md) (R Server on HDInsight – első lépések). Az R használatával egy Linux-alapú fürttel információkért lásd: [telepítése és R használata a HDinsight Hadoop-fürtök (Linux)](hdinsight-hadoop-r-scripts-linux.md).

Telepíthető R bármilyen típusú on Azure HDInsight (Hadoop-, Storm, HBase, Spark) fürt segítségével *parancsfájlművelet*. Egy minta parancsfájlt a HDInsight-fürtök R telepítéséhez érhető el, csak olvasható az Azure storage-blobból [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

**Kapcsolódó cikkek**

* [Telepítheti és használhatja R HDinsight Hadoop-fürtök (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Hdinsight Hadoop-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md): általános információk a HDInsight-fürtök létrehozása
* [Testre szabhatja a HDInsight-fürtjéhez parancsfájlművelet][hdinsight-cluster-customize]: általános információk a HDInsight-parancsfájlművelet fürtök testreszabása
* [A HDInsight parancsfájlművelet-parancsfájlok fejlesztése](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>Mi az az R?
A <a href="http://www.r-project.org/" target="_blank">R-projektjét, amely statisztikai számításokhoz</a> egy megnyitott adatforrás nyelvi és statisztikai számításokhoz környezet. R biztosít több száz beépített statisztikai függvények és a saját programozási nyelv, amely egyesíti a működési és objektumorientált programozási aspektusait. Nagy mennyiségű grafikus képességeket is tartalmaz. R a legtöbb szakmai statisztikusok és a mezők számos különböző kutatók az előnyben részesített programozási környezetet.

R rendszer kompatibilis Azure Blob Storage (WASB), így az ott tárolt adatok R használata a HDInsight dolgozhatók.  

## <a name="install-r"></a>R telepítéséhez
A [mintaparancsfájl](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) R telepítéséhez egy hdinsight fürt érhető el az egy csak olvasható az Azure Storage-blobból. Ez a szakasz leírja a minta-parancsprogram használatáról az Azure portál használata a fürt létrehozása során.

> [!NOTE]
> A parancsfájlpéldát a HDInsight-fürt verziószáma 3.1-ban jelent. A HDInsight fürt verzióival kapcsolatos további információkért lásd: [HDInsight-fürt verziókról](hdinsight-component-versioning.md).
>
>

1. Amikor HDInsight fürtöt hoz létre a portálról, kattintson **opcionális konfigurációs**, és kattintson a **Parancsfájlműveletek**.
2. Az a **Parancsfájlműveletek** lapján adja meg a következő értékeket:

    ![Parancsfájlművelet használni ahhoz, hogy a fürt](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "parancsfájlművelet használja a fürt testreszabása")

    <table border='1'>
        <tr><th>Tulajdonság</th><th>Érték</th></tr>
        <tr><td>Név</td>
            <td>Adja meg a parancsfájlművelet nevét, például <b>R telepítéséhez</b>.</td></tr>
        <tr><td>A parancsfájl URI azonosítója</td>
            <td>Adja meg az URI-t a parancsfájlt, amelyet a fürt, például testreszabásához <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Csomóponttípus</td>
            <td>Adja meg a csomópontok, amelyen fut a testreszabási parancsfájl. Választhat <b>csomópontjaihoz</b>, <b>csak Átjárócsomópontokat</b>, vagy <b>munkavégző csomópontokhoz</b> csak.
        <tr><td>Paraméterek</td>
            <td>Adja meg a paraméterek, ha a parancsfájl által igényelt. R telepítéséhez a parancsfájl azonban nem szükséges paramétereket, ezért üresen hagyhatja, ez.</td></tr>
    </table>

    Több összetevőinek telepítése a fürt több parancsfájlművelet adhat hozzá. Miután hozzáadta a parancsfájlok, kattintson a pipa jelre a fürt crating elindításához.

A parancsfájl segítségével R telepítése a HDInsight az Azure PowerShell vagy a HDInsight .NET SDK használatával. Ezek az eljárások az utasításokat a cikk későbbi részében.

## <a name="run-r-scripts"></a>R parancsfájlok futtatása
Ez a szakasz ismerteti a Hadoop-fürt hdinsightban az R-parancsfájl futtatása.

1. **Távoli asztali kapcsolat létrehozása a fürt**: a portál a, a távoli asztal engedélyezése a fürthöz létrehozott r telepítve, és csatlakozzon a fürthöz. Útmutatásért lásd: [csatlakozás RDP Funkciót használnak a HDInsight-fürtök](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Nyissa meg az R konzolt**: az R-telepítés helyezi egy hivatkozást az R-konzolra átjárócsomópontjához asztalán. Kattintson rá az R-konzol megnyitásához.
3. **Az R-parancsfájl futtatása**: az R parancsfájlt közvetlenül az R-konzolról illeszti, ha kiválasztja, és nyomja le az ENTER BILLENTYŰT. Ez egy egyszerű példa parancsfájlt, amely állít elő, 1 és 100 közötti számokat, majd 2 szorzatát.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

Az első két sorok hívható meg a telepített R. RHadoop könyvtárak A végső sor kiírja az eredmények a konzol. A kimeneti kell kinéznie:

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Segítségével a következőkre PowerShell R telepítéséhez
Lásd: [testreszabása HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  A Spark az Azure PowerShell telepítése mutatja be. Meg kell adnia, hogy a használandó parancsfájlt [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>.NET SDK használatával R telepítéséhez
Lásd: [testreszabása HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). A minta bemutatja, hogyan telepítse a .NET SDK használatával Spark. Meg kell adnia, hogy a használandó parancsfájlt [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).

## <a name="see-also"></a>Lásd még:
* [Telepítheti és használhatja R HDinsight Hadoop-fürtök (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Hdinsight Hadoop-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md): általános információk a HDInsight-fürtök létrehozása
* [Testre szabhatja a HDInsight-fürtjéhez parancsfájlművelet][hdinsight-cluster-customize]: általános információk a HDInsight-parancsfájlművelet fürtök testreszabása
* [A HDInsight parancsfájlművelet-parancsfájlok fejlesztése](hdinsight-hadoop-script-actions.md)
* [Telepítse, és válassza a Spark on HDInsight-fürtök][hdinsight-install-spark]: Spark telepítéséről parancsfájlművelet-minta
* [Giraph telepíthető a HDInsight-fürtök](hdinsight-hadoop-giraph-install.md): parancsfájlművelet minta Giraph telepítéséről
* [Solr telepíthető a HDInsight-fürtök](hdinsight-hadoop-solr-install-linux.md): parancsfájlművelet minta Solr telepítésével kapcsolatban.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md
