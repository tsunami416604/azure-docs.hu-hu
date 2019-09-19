---
title: Apache TEZ-alkalmazás lefagy az Azure HDInsight
description: Apache TEZ-alkalmazás lefagy az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 9a7b28dd48be7bbd0c789749ee4a1fa01a17d85f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091348"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>Forgatókönyv: Apache TEZ-alkalmazás lefagy az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Apache Hive-feladatoknak a TEZ való elküldése után a feladatok állapota "fut", de úgy tűnik, hogy nem végez előrehaladást

## <a name="cause"></a>Ok

Túl sok feladat van elküldve; hosszú fonalas üzenetsor.

## <a name="resolution"></a>Megoldás:

Bővítse a fürtöt, vagy csak várjon, amíg a fonal-várólista el nem kerül.

Alapértelmezés `yarn.scheduler.capacity.maximum-applications` szerint a vagy függőben lévő alkalmazások maximális számát szabályozza, és alapértelmezés `10000`szerint a () értéket használja.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
