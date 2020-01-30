---
title: Nem sikerült beolvasni az Apache-beli fonal-naplót az Azure HDInsight
description: Hibaelhárítási lépések és lehetséges megoldások az Azure HDInsight-fürtökkel való interakció során felmerülő problémákhoz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776195"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Forgatókönyv: nem sikerült beolvasni az Apache-beli fonal-naplót az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A Storage-fiókból talált Apache-alapú fonal-naplók nem emberi olvashatóságot mutatnak. A fájl-elemző nem működik, és a következő hibaüzenetet jeleníti meg:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Ok

Az Apache-beli fonalak naplója `IndexFile` formátumban van összesítve, amelyet a fájl-elemző nem támogat.

## <a name="resolution"></a>Felbontás

1. Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net`, ahol a `CLUSTERNAME` a fürt neve.

1. A Ambari felhasználói felületén navigáljon a következőhöz: **fonal** > **konfigurációk** > **Advanced** > **Advanced fonál-site**.

1. WASB tárolás esetén: az `yarn.log-aggregation.file-formats` alapértelmezett értéke `IndexedFormat,TFile`. Módosítsa az értéket `TFile`ra.

1. ADLS tárolás esetén: az `yarn.nodemanager.log-aggregation.compression-type` alapértelmezett értéke `gz`. Módosítsa az értéket `none`ra.

1. Mentse a módosítást, és indítsa újra az összes érintett szolgáltatást.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
