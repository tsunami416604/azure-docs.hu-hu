---
title: Virtuális gépek újraindítása az Azure HDInsight-fürtön
description: Megtudhatja, hogyan indíthatja újra a nem válaszoló virtuális gépeket a HDInsight-fürtön.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 46efccad60c06c73145bbf30c119f6a47cb856d5
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323447"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Virtuális gépek újraindítása a HDInsight-fürthöz

A HDInsight-fürtök virtuális gépek csoportjait tartalmazzák. A hosszú ideig futó fürtök esetében ezek a csomópontok különböző okok miatt nem válaszolnak. Ez a cikk azt ismerteti, hogyan lehet újraindítani a nem válaszoló virtuális gépeket egy HDInsight-fürtben.

## <a name="when-to-reboot"></a>Újraindítás időpontja

A fürtben lévő virtuális gépek újraindítása a csomópont leállását és a szolgáltatások újraindítását eredményezi a csomóponton. A csomópont újraindításakor előfordulhat, hogy a fürt nem megfelelő állapotba kerül, és előfordulhat, hogy a feladatok lelassulnak vagy sikertelenek lesznek. Ha az aktív fő csomópontot próbálja újraindítani, az összes futó feladat el lesz távolítva, és nem tud feladatokat beküldeni a fürtbe, amíg a szolgáltatások nem futnak újra. A virtuális gépeket csak szükség esetén érdemes újraindítani. Íme néhány útmutató a virtuális gépek újraindításához.

- Nem lehet SSH-t a csomópontba, de válaszol a pingelésekre.
- A Worker csomópont nem érhető el szívverés nélkül a Ambari felhasználói felületén.
- A temp lemez megtelt a csomóponton.
- A virtuális gépen található folyamat-táblázat számos olyan bejegyzést tartalmaz, ahol a folyamat befejeződött, de a "leállított állapottal" szerepel.

## <a name="use-rest-api-to-reboot-vms"></a>Virtuális gépek újraindításának REST API használata

A csomópontok újraindítása csak REST API jelenleg támogatott. A HDInsight kérések küldéséhez használhatja az API doc **kipróbálás** funkcióját. A csomópont-újraindítási művelet használatához két lépés szükséges: csomópontok listázása és csomópontok újraindítása.

1. Csomópontok listázása. A fürtcsomópontok listáját a REST API vagy a Ambari szerezheti be. További részletek a [HDInsight-lista gazdagépek REST API műveletben](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts) találhatók

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Indítsa újra a gazdagépeket. Az újraindítani kívánt csomópontok nevének lekérése után a csomópontok újraindításához használja a gazdagépek újraindítását REST API. További részletek a [HDInsight újraindítására szolgáló gazdagépek REST API műveletben](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts) találhatók.

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

Az újraindítani kívánt csomópontok tényleges nevei a kérelem törzsében egy JSON-tömbben vannak megadva.

```json
[
  "gateway1",
  "gateway3"
]
```

## <a name="next-steps"></a>További lépések

* [HDInsight virtuális gépek REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [HDInsight REST API](https://docs.microsoft.com/rest/api/hdinsight/)