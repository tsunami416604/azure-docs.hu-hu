---
title: Nem lehet olvasni az Apache Yarn-naplót az Azure HDInsightban
description: Az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák lépéseinek és lehetséges megoldásai elhárítása.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776195"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Eset: Nem lehet olvasni az Apache Yarn-naplót az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A tárfiókból talált Apache Yarn naplók nem olvashatók emberre. A fájlelemző nem működik, és a következő hibaüzenetet adja elő:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Ok

Az Apache Yarn napló `IndexFile` formátumba van összesítve, amelyet a fájlelemző nem támogat.

## <a name="resolution"></a>Megoldás:

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net`meg `CLUSTERNAME` a , ahol a fürt neve.

1. Az Ambari felhasználói felületéről keresse meg a **YARN** > **Configs** > **Advanced** > Advanced**fonalwebhelyet.**

1. WASB-tároló esetén: Az `yarn.log-aggregation.file-formats` `IndexedFormat,TFile`alapértelmezett érték a. Módosítsa az `TFile`értéket .

1. ADLS-tároló esetén: Az `yarn.nodemanager.log-aggregation.compression-type` `gz`alapértelmezett érték a . Módosítsa az `none`értéket .

1. Mentse a módosítást, és indítsa újra az összes érintett szolgáltatást.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
