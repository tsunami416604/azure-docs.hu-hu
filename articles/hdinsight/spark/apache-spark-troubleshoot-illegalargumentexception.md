---
title: IllegalArgumentException hiba az Apache Sparkhoz – Azure HDInsight
description: IllegalArgumentException az Apache Spark-tevékenységhez az Azure HDInsight for Azure Data Factory szolgáltatásban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: df62dbd8db7d41eb11207c7741aed76cec0ac7a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894381"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Eset: IllegalArgumentException az Apache Spark-tevékenységhez az Azure HDInsightban

Ez a cikk az Apache Spark-összetevők Azure HDInsight-fürtökben való használata során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A következő kivétel tetsző, amikor spark-tevékenységet próbál végrehajtani egy Azure Data Factory-folyamatban:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Ok

A Spark-feladat sikertelen lesz, ha az alkalmazásjar-fájl nem található a Spark-fürt alapértelmezett/elsődleges tároló.

Ez egy ismert probléma a Spark nyílt forráskódú keretrendszer nyomon ebben a hibában: [Spark feladat sikertelen, ha fs.defaultFS és alkalmazás jar különböző url](https://issues.apache.org/jira/browse/SPARK-22587).

A probléma megoldódott a Spark 2.3.0-s számban.

## <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy az alkalmazásjar a HDInsight-fürt alapértelmezett/elsődleges tárolóján van tárolva. Az Azure Data Factory esetén győződjön meg arról, hogy az ADF-kapcsolt szolgáltatás a HDInsight alapértelmezett tárolójára mutat, nem pedig egy másodlagos tárolóra.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
