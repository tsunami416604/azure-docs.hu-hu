---
title: Virtuális gépek újraindítása az Azure HDInsight-fürtön
description: Megtudhatja, hogyan indíthatja újra a nem válaszoló virtuális gépeket a HDInsight-fürtön.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: c0f0bd9eb423b3de6a602647dff93fd9fce6e13e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077014"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Virtuális gépek újraindítása a HDInsight-fürthöz

A HDInsight-fürtök virtuális gépek csoportjait tartalmazzák. A hosszú ideig futó fürtök esetében ezek a csomópontok különböző okok miatt nem válaszolnak. Ez a cikk azt ismerteti, hogyan lehet újraindítani a nem válaszoló virtuális gépeket egy HDInsight-fürtben.

## <a name="when-to-reboot"></a>Újraindítás időpontja

> [!WARNING]  
> A fürtben lévő virtuális gépek újraindítása a csomópont leállását és a szolgáltatások újraindítását eredményezi a csomóponton. 

A csomópont újraindításakor előfordulhat, hogy a fürt nem megfelelő állapotba kerül, és előfordulhat, hogy a feladatok lelassulnak vagy sikertelenek lesznek. Ha az aktív fő csomópontot próbálja újraindítani, az összes futó feladat el lesz távolítva, és nem tud feladatokat beküldeni a fürtbe, amíg a szolgáltatások nem futnak újra. A virtuális gépeket csak szükség esetén érdemes újraindítani. Íme néhány útmutató a virtuális gépek újraindításához.

- Nem lehet SSH-t a csomópontba, de válaszol a pingelésekre.
- A Worker csomópont nem érhető el szívverés nélkül a Ambari felhasználói felületén.
- A temp lemez megtelt a csomóponton.
- A virtuális gépen a folyamat táblázata számos olyan bejegyzést tartalmaz, amelyeken a folyamat befejeződött, de "leállított állapottal" szerepel.

> [!WARNING]  
> Ügyeljen arra, hogy a virtuális gépek újraindítása **HBase** és **Kafka** -clustes, mivel az adatvesztést okozhat.

## <a name="use-powershell-to-reboot-vms"></a>Virtuális gépek újraindítása a PowerShell használatával

A csomópont-újraindítási művelet használatához két lépés szükséges: csomópontok listázása és csomópontok újraindítása.

1. Csomópontok listázása. A fürt csomópontjainak listáját a [Get-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost)használatával szerezheti be. 

  ```
  Get-AzHDInsightHost -ClusterName myclustername
  ```

2. Indítsa újra a gazdagépeket. Az újraindítani kívánt csomópontok neveinek beolvasását követően indítsa újra a csomópontokat a [restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)használatával.

  ```
  Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
  ```

## <a name="use-rest-api-to-reboot-vms"></a>Virtuális gépek újraindításának REST API használata

A HDInsight kérések küldéséhez használhatja az API doc **kipróbálás** funkcióját. A csomópont-újraindítási művelet használatához két lépés szükséges: csomópontok listázása és csomópontok újraindítása.

1. Csomópontok listázása. A fürtcsomópontok listáját a REST API vagy a Ambari szerezheti be. További részletek a [HDInsight-lista gazdagépek REST API műveletben](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts)találhatók.

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Indítsa újra a gazdagépeket. Az újraindítani kívánt csomópontok nevének lekérése után a csomópontok újraindításához használja a REST API újraindítása csomópontot. A csomópont neve követi a **"NodeType (lefelé/HN/ZK/GW)" + "x" + "a fürt nevének első 6 karaktere" nevű**mintát. További részletek a [HDInsight újraindítására szolgáló gazdagépek REST API műveletben](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts)találhatók.

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

Az újraindítani kívánt csomópontok tényleges nevei a kérelem törzsében egy JSON-tömbben vannak megadva.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>További lépések

* [Újraindítás – AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [HDInsight virtuális gépek REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [HDInsight REST API](https://docs.microsoft.com/rest/api/hdinsight/)
