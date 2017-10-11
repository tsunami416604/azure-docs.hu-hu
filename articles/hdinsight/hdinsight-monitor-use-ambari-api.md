---
title: "Az Ambari API-jával - Azure hdinsight Hadoop-fürtök figyelése |} Microsoft Docs"
description: "Az Apache Ambari API-k létrehozása, kezelése és figyelése a Hadoop-fürtök használja. Intuitív operátori eszközök és API-k elrejtése a Hadoop összetettségét."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
editor: cgronlun
manager: jhubbard
ms.assetid: 052135b3-d497-4acc-92ff-71cee49356ff
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: b6fc2098027690eb76b69b1427f0e9541b8a7a69
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>A HDInsight-beli Hadoop-fürtök figyelése az Ambari API használatával
Ismerje meg, hogy a HDInsight-fürtök figyelése az Ambari API-k használatával.

> [!NOTE]
> A cikkben szereplő információkat elsősorban a Windows-alapú HDInsight-fürtök az Ambari REST API egy csak olvasható verziója van. Linux-alapú fürtök esetében lásd: [kezelése Hadoop fürtök az Ambari segítségével](hdinsight-hadoop-manage-ambari.md).
> 
> 

## <a name="what-is-ambari"></a>Mi az az Ambari?
[Apache Ambari] [ ambari-home] kiépítésére, kezelésére és Apache Hadoop-fürtök ellenőrzésére szolgál. Operátori eszközök intuitív gyűjteményét és egy robusztus API-készletet foglal magában, amelyek elfedik a Hadoop összetettségét, és leegyszerűsítik a fürtök működését. Az API-k kapcsolatos további információkért lásd: [Ambari API-referencia][ambari-api-reference]. 

A HDInsight jelenleg csak az Ambari figyelési funkció támogatja. Ambari API 1.0 HDInsight 3.0-s és a 2.1-es verziójú fürtöket támogatja. Ez a cikk ismerteti a HDInsight-fürtökön 3.1 és 2.1 elérése során Ambari API-k. A legfontosabb különbség a kettő között, hogy egyes összetevői új képességeket nyújtanak (például előzmények feladatkiszolgáló) bevezetésével megváltoztak. 

**Előfeltételek**

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Munkaállomás Azure PowerShell-lel**.
* (Választható) [cURL][curl]. A telepítéshez lásd: [kiadások és letöltéseket tartalmazó oldalon cURL][curl-download].
  
  > [!NOTE]
  > Ha használja a cURL-parancsot a Windows rendszerben egyetlen idézőjelek beállítás értékek helyett használjon idézőjelek közé.
  > 
  > 
* **Egy Azure HDInsight fürt**. Fürtök kiépítése kapcsolatos útmutatásért lásd: [használatának megkezdésében a HDInsight] [ hdinsight-get-started] vagy [Provision HDInsight clusters][hdinsight-provision]. A következő adatokat az oktatóanyag teljesítéséhez szüksége:
  
  | Fürt tulajdonság | Az Azure PowerShell változó neve | Érték | Leírás |
  | --- | --- | --- | --- |
  |   A HDInsight-fürt neve |$clusterName | |A HDInsight-fürt neve. |
  |   Fürt felhasználónév |$clusterUsername | |Fürt felhasználónevet megadva, a fürt létrehozásakor. |
  |   Fürt jelszó |$clusterPassword | |Fürt felhasználói jelszavát. |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Feladat gyors végrehajtásának megkönnyítésére
Számos módon figyelheti a HDInsight-fürtök az Ambari használatával.

**Azure PowerShell használata**

A következő Azure PowerShell-parancsfájl lekérdezi a MapReduce feladatot követő *HDInsight 3.5 fürtben.*  A fő különbség, hogy azt le ezeket a részleteket a YARN szerviz (hanem MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

A következő PowerShell-parancsfájl lekérdezi a MapReduce-feladatok követő adatait *HDInsight 2.1-fürtben lévő*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

A kimenete:

![Jobtracker kimeneti][img-jobtracker-output]

**A cURL használata**

Az alábbi példa fürtinformációkat lekérdezi a cURL használatával:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

A kimenete:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**A 10/8/2014 kiadásban**:

Az Ambari végpont használatakor "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", a *gazdaszámítógép_neve* mező a gazdagép neve helyett a csomópont teljesen minősített tartománynevét (FQDN) adja vissza. A 10/8/2014 kiadásban előtt ebben a példában adott vissza egyszerűen "**headnode0**". 10/8/2014 megjelenése után az FQDN első "**headnode0. { ClusterDNS} .azurehdinsight .net**", az előző példában látható módon. Ez a módosítás lehetővé teszi a forgatókönyvekben, ahol több fürt típust (például a HBase és a Hadoop) is telepíthető egy virtuális hálózathoz (VNET) szükséges. Ez történik, például a HBase a háttér-platformként a Hadoop használatakor.

## <a name="ambari-monitoring-apis"></a>Ambari API-k figyelése
Az alábbi táblázatban a leggyakrabban használt Ambari API-hívások figyelési némelyike. A API-val kapcsolatos további információkért lásd: [Ambari API-referencia][ambari-api-reference].

| A figyelő API-hívás | URI | Leírás |
| --- | --- | --- |
| Fürtök beolvasása |`/api/v1/clusters` | |
| Fürt-adatok beolvasása. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |fürtök, a szolgáltatások, a gazdagépek |
| Szolgáltatások |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |Szolgáltatások közé tartoznak: hdfs, mapreduce |
| Szolgáltatások adatok beolvasása. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| Szolgáltatás-összetevők beszerzése |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Összetevő-adatok beolvasása. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo, gazdagép-összetevők, metrikák |
| Gazdagépek beolvasása |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0, workernode0 |
| Gazdagép-adatok beolvasása. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| Gazdagép-összetevők beszerzése |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode, erőforrás-kezelő |
| Összetevő-adatok beolvasása a gazdagépen. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles, összetevőt, a gazdagép, a metrikák |
| Konfigurációk beolvasása |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |Config típusok: core-webhely, hdfs-webhely, mapred-helyet, hive-hely |
| Konfigurációs adatok beolvasása. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |Config típusok: core-webhely, hdfs-webhely, mapred-helyet, hive-hely |

## <a name="next-steps"></a>Következő lépések
Most megtanulhatta, hogyan használható az Ambari API-hívások figyelése. További tudnivalókért lásd:

* [Az Azure portál használata a HDInsight-fürtök kezelése][hdinsight-admin-portal]
* [Az Azure PowerShell HDInsight-fürtök kezelése][hdinsight-admin-powershell]
* [Parancssori felület használata a HDInsight-fürtök kezelése][hdinsight-admin-cli]
* [HDInsight-dokumentáció][hdinsight-documentation]
* [Első lépései a hdinsight eszközzel][hdinsight-get-started]

[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: https://docs.microsoft.com/azure/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
