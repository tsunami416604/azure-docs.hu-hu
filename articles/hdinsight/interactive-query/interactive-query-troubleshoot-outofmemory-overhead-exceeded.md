---
title: Az Apache Hive-ban való csatlakozás outOfmemory hibához vezet - Azure HDInsight
description: OutOfMemory hibák kezelése "A globális katalógus terhelési korlátja túllépte a hibát"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ab334dfb15044fd0734a107c12003ca2c1f86906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895174"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Eset: Az Apache Hive-ben való illesztés ek ként az Azure HDInsightban OutOfMemory-hibát eredményez

Ez a cikk az Azure HDInsight-fürtök interaktív lekérdezési összetevőinek használatával kapcsolatos problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache Hive-illesztések alapértelmezett viselkedése a tábla teljes tartalmának betöltése a memóriába, hogy az illesztés a Map/Reduce lépés végrehajtása nélkül is elvégezhető legyen. Ha a Hive-tábla túl nagy ahhoz, hogy elférjen a memóriában, a lekérdezés sikertelen lehet.

## <a name="cause"></a>Ok

Ha megfelelő méretű kaptárban fut illesztéseket, a következő hiba lép fel:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Megoldás:

Akadályozza meg, hogy a Hive a következő Hive konfigurációs érték beállításával betöltse a táblákat a memóriába az illesztések en (ahelyett, hogy egy Map/Reduce lépést hajtvégre).

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>További lépések

Ha ennek az értéknek a beállítása nem oldotta meg a problémát, látogasson el az alábbi...

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
