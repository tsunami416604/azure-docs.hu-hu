---
title: Az Apache Ambari Tez View lassan töltődik be az Azure HDInsightban
description: Előfordulhat, hogy az Apache Ambari Tez View lassan töltődik be, vagy egyáltalán nem töltődik be az Azure HDInsight ban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f4b1ffbc1e5b8147279d1e0320bd5f55aec90ea2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895093"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Forgatókönyv: Az Apache Ambari Tez View lassan töltődik be az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtök interaktív lekérdezési összetevőinek használatával kapcsolatos problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache Ambari Tez View lassan töltődhet be, vagy egyáltalán nem töltődhet be. Az Ambari Tez View betöltésekor előfordulhat, hogy a Headnodes folyamatai nem reagálnak.

## <a name="cause"></a>Ok

A yarn ATS API-k elérése néha gyenge teljesítményt eredményezhet a 2017 októbere előtt létrehozott fürtökön, amikor a fürt nagy számú Hive-feladat fut rajta.

## <a name="resolution"></a>Megoldás:

Ezt a problémát 2017 októberében javították. A fürt újbóli létrehozása miatt nem fut újra ez a probléma.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
