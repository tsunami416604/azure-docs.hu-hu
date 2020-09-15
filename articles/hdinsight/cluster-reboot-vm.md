---
title: Virtuális gépek újraindítása az Azure HDInsight-fürtökhöz
description: Ismerje meg, hogyan indíthatja újra a nem válaszoló virtuális gépeket az Azure HDInsight-fürtökön.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: 149a82526263f5e372db81b5a92a9ee90a2c76f3
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089972"
---
# <a name="reboot-vms-for-hdinsight-clusters"></a>Virtuális gépek újraindítása HDInsight-fürtökhöz

Az Azure HDInsight-fürtök virtuálisgép-csoportokat (VM-ket) tartalmaznak. A hosszú ideig futó fürtök esetében előfordulhat, hogy ezek a csomópontok különböző okok miatt nem válaszolnak. Ez a cikk azt ismerteti, hogyan lehet újraindítani a nem válaszoló virtuális gépeket egy HDInsight-fürtben.

## <a name="when-to-reboot"></a>Újraindítás időpontja

> [!WARNING]
> Ha egy fürtben újraindítja a virtuális gépeket, a csomópont nem érhető el, és a csomóponton lévő szolgáltatásoknak újra kell indítaniuk.

A csomópontok újraindításakor előfordulhat, hogy a fürt nem megfelelő állapotba kerül, és előfordulhat, hogy a feladatok lelassulnak vagy meghibásodnak. Ha az aktív fő csomópontot próbálja újraindítani, az összes futó feladat le lesz állítva. Addig nem fog tudni feladatokat elküldeni a fürtbe, amíg a szolgáltatások újra nem futnak. Ezen okok miatt csak szükség esetén indítsa újra a virtuális gépeket. A virtuális gépek újraindítását érdemes megfontolni:

- Az SSH használatával nem lehet bejutni a csomópontba, de a pingek megválaszolására is reagál.
- A munkavégző csomópont nem áll le szívverés nélkül a Ambari felhasználói felületén.
- A temp lemez megtelt a csomóponton.
- A virtuális gépen található folyamat-táblázat számos olyan bejegyzést tartalmaz, ahol a folyamat befejeződött, de "leállított állapottal" szerepel.

> [!WARNING]
> Ügyeljen arra, hogy a virtuális gépek újraindítása a **HBase** és a **Kafka** -fürtök esetében is megtörténjen, mert az újraindítással elvész az adatindítás.

## <a name="use-powershell-to-reboot-vms"></a>Virtuális gépek újraindítása a PowerShell használatával

Két lépés szükséges a csomópont-újraindítási művelet használatához: csomópontok listázása és csomópontok újraindítása.

1. Csomópontok listázása. A fürt csomópontjainak listáját a [Get-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost)címen érheti el.

      ```
      Get-AzHDInsightHost -ClusterName myclustername
      ```

1. Indítsa újra a gazdagépeket. Miután megszerezte az újraindítani kívánt csomópontok nevét, indítsa újra a csomópontokat a [restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)használatával.

      ```
      Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
      ```

## <a name="use-a-rest-api-to-reboot-vms"></a>Virtuális gépek újraindításának REST API használata

A HDInsight kérések küldéséhez használhatja az API doc **kipróbálás** funkcióját. Két lépés szükséges a csomópont-újraindítási művelet használatához: csomópontok listázása és csomópontok újraindítása.

1. Csomópontok listázása. A fürtcsomópontok listáját a REST API vagy a Ambari szerezheti be. További információ: HDInsight- [lista gazdagépek REST API művelet](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

1. Indítsa újra a gazdagépeket. Miután megszerezte az újraindítani kívánt csomópontok nevét, indítsa újra a csomópontokat a REST API használatával a csomópontok újraindításához. A csomópont neve a *NodeType (lefelé/HN/ZK/GW)*  +  *x*a  +  *fürt nevének első hat karakterét*követi. További információ: HDInsight- [Újraindítási gazdagépek REST API művelet](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts).

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

## <a name="next-steps"></a>Következő lépések

* [Újraindítás – AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [HDInsight virtuális gépek REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [HDInsight REST API](https://docs.microsoft.com/rest/api/hdinsight/)
