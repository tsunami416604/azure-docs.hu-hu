---
title: Az Apache Ambari TEZ View lassan töltődik be az Azure HDInsight
description: Előfordulhat, hogy az Apache Ambari TEZ-nézete lassan vagy egyáltalán nem töltődik be az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 2bad0389d03f2479e035dc6f563dad635403fb22
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539581"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Forgatókönyv: az Apache Ambari TEZ-nézete lassan töltődik be az Azure HDInsight

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Előfordulhat, hogy az Apache Ambari TEZ-nézete lassan vagy egyáltalán nem töltődik be. Ambari TEZ-nézet betöltésekor előfordulhat, hogy a Átjárócsomópontokkal folyamatai nem válaszolnak.

## <a name="cause"></a>Ok

A fonál ATS API-k elérése esetenként gyenge teljesítményt eredményezhet a 2017 TOT előtt létrehozott fürtökön, amikor a fürt nagy számú kaptár-feladatot futtat rajta.

## <a name="resolution"></a>Feloldás

Ez egy olyan probléma, amely a 2017-es TOT-ban lett kijavítva. Ha újra létrehozza a fürtöt, azzal nem fogja újból futtatni a problémát.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.