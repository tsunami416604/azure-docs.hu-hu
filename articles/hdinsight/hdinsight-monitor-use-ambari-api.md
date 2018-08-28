---
title: A használatával az Ambari API – Azure HDInsight Hadoop-fürtök monitorozása
description: Az Apache Ambari API-k létrehozása, kezelése és figyelése a Hadoop-fürtök használja. Intuitív kezelőeszközöket és API-k elrejtése a Hadoop összetettségét.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/07/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 23da477fc24eaea4a6f987f13ad055022d2ba032
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43108637"
---
# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>A HDInsight-beli Hadoop-fürtök figyelése az Ambari API használatával
Útmutató a HDInsight-fürtök figyelése az Ambari API-k használatával.

> [!NOTE]
> Ebben a cikkben található információk az elsősorban a Windows-alapú HDInsight-fürtök az Ambari REST API egyik csak olvasható verzióját. A Linux-alapú fürtök esetén lásd: [kezelése Hadoop-fürtök az Ambari](hdinsight-hadoop-manage-ambari.md).
> 
> 

## <a name="what-is-ambari"></a>Mi az Ambari?
[Az Apache Ambari] [ ambari-home] kiépítésére, kezelésére és figyelése Apache Hadoop-fürtök szolgál. Operátori eszközök intuitív gyűjteményét és egy robusztus API-készletet foglal magában, amelyek elfedik a Hadoop összetettségét, és leegyszerűsítik a fürtök működését. Az API-k kapcsolatos további információkért lásd: [Ambari API-referencia][ambari-api-reference]. 

HDInsight jelenleg csak az Ambari figyelési funkció támogatja. HDInsight 3.0-s és a 2.1-es verziójú fürtök az Ambari API 1.0-t támogatja. Ez a cikk ismerteti a HDInsight 3.1-et és a 2.1-es verziójú fürtökön hozzáférni az Ambari API-k. A fő különbség a kettő között, hogy egyes összetevői új funkciókat (például a feladat Előzménykiszolgáló) bevezetésével megváltozott. 

**Előfeltételek**

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Munkaállomás Azure PowerShell-lel**.
* (Nem kötelező) [cURL][curl]. A telepítéshez lásd: [cURL-kiadásainak listáját és a letöltéseket][curl-download].
  
  > [!NOTE]
  > Ha használja a cURL-parancsot a Windows, egyszeres idézőjelek lehetőség az értékek helyett használja idézőjelek közé.
  > 
  > 
* **Egy Azure HDInsight-fürt**. Útmutatás a fürtök kiépítése: [HDInsight használatának első lépései] [ hdinsight-get-started] vagy [Provision HDInsight clusters][hdinsight-provision]. A következő adatokat az oktatóanyagot lesz szüksége:
  
  | Fürt tulajdonság | Az Azure PowerShell-változó neve | Érték | Leírás |
  | --- | --- | --- | --- |
  |   HDInsight-fürt neve |$clusterName | |A HDInsight-fürt nevére. |
  |   Fürt felhasználóneve |$clusterUsername | |Fürthöz tartozó felhasználónevet megadva, a fürt létrehozásakor. |
  |   Fürt jelszava |$clusterPassword | |Fürt felhasználói jelszót. |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Indítsa be
Többféleképpen is lehet HDInsight-fürtök figyelése az Ambari használatával.

**Azure PowerShell használata**

A következő Azure PowerShell-parancsfájl beolvassa a MapReduce-feladatok tracker adatait *HDInsight 3.5-fürtben.*  A legfontosabb különbség, hogy mi lekéréses ezeket az adatokat a YARN-szolgáltatás (helyett a MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

A következő PowerShell-parancsfájl beolvassa a MapReduce-feladatok tracker adatait *HDInsight 2.1-fürtben*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

A kimenet a következő:

![Jobtracker kimenet][img-jobtracker-output]

**A cURL használata**

Az alábbi példa lekéri a fürt adatait a cURL használatával:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

A kimenet a következő:

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

Az Ambari végpont használatakor "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", a *gazdaszámítógép_neve* mező a gazdagép neve helyett a csomópont teljesen minősített tartománynevét (FQDN) adja vissza. A 10/8/2014 kiadás előtt ebben a példában adja vissza egyszerűen "**headnode0**". A 10/8/2014 kiadása után az FQDN első "**headnode0. { ClusterDNS} .azurehdinsight .net**", az előző példában látható módon. Ez a változás megkönnyítése érdekében a forgatókönyvek, ahol több fürttípusok (például HBase és Hadoop) is telepíthető egy virtuális hálózat (VNET) szükséges. Ez történik, például HBase a háttér-platformként a Hadoop használatakor.

## <a name="ambari-monitoring-apis"></a>Az Ambari API-k figyelése
Az alábbi táblázat a leggyakoribb Ambari API-hívások figyelése néhányat. Az API-val kapcsolatos további információkért lásd: [Ambari API-referencia][ambari-api-reference].

| A figyelő API-hívás | URI | Leírás |
| --- | --- | --- |
| Fürtök lekérése |`/api/v1/clusters` | |
| Fürt adatainak beolvasása. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |fürtök esetén a szolgáltatások, a gazdagépek |
| Szolgáltatások beolvasása |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |A szolgáltatások az alábbiak: hdfs, a mapreduce |
| Szolgáltatások adatainak beolvasása. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| Szolgáltatás-összetevők lekérése |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Összetevő-adatainak beolvasása. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo, gazdagép-összetevők, a metrikák |
| Gazdagépek beolvasása |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0, workernode0 |
| A gazdagép adatainak beolvasása. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| Gazdagép-összetevők lekérése |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode, az erőforrás-kezelő |
| Gazdagép összetevő adatainak beolvasása. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles, összetevő, a gazdagép, a metrikák |
| Konfiguráció beolvasása |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |Config típusok: hely, a hdfs-hely, a mapred-hely, a hive-hely |
| Konfigurációs adatainak beolvasása. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |Config típusok: hely, a hdfs-hely, a mapred-hely, a hive-hely |

## <a name="next-steps"></a>További lépések
Most már megtanulhatta, hogyan használhatja az Ambari API-hívások figyelése. További tudnivalókért lásd:

* [Az Azure portal használatával HDInsight-fürtök kezelése][hdinsight-admin-portal]
* [Azure PowerShell használatával HDInsight-fürtök kezelése][hdinsight-admin-powershell]
* [Parancssori felület használatával HDInsight-fürtök kezelése][hdinsight-admin-cli]
* [HDInsight-dokumentáció][hdinsight-documentation]
* [HDInsight – első lépések][hdinsight-get-started]

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
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
