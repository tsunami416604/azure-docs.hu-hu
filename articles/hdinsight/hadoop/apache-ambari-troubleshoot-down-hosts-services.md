---
title: Az Apache Ambari felhasználói felülete megjeleníti a gazdagépeket és szolgáltatásokat az Azure HDInsightban
description: Az Apache Ambari felhasználói felületével kapcsolatos probléma elhárítása, amikor az az Azure HDInsightban a gazdagépeket és szolgáltatásokat jeleníti meg
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895635"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Eset: Az Apache Ambari felhasználói felülete megmutatja a gazdagépeket és szolgáltatásokat az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache Ambari felhasználói felülete elérhető, de a felhasználói felület azt mutatja, hogy szinte az összes szolgáltatás nem működik, az összes gazdagép szívverést mutat.

## <a name="cause"></a>Ok

A legtöbb esetben ez a probléma az Ambari kiszolgáló nem fut az aktív headnode. Ellenőrizze, hogy melyik headnode az aktív headnode, és győződjön meg róla, hogy az ambari-szerver fut a megfelelő. Ne indítsa el manuálisan az ambari-kiszolgálót, hagyja, hogy a feladatátvevő vezérlő szolgáltatás felelős az ambari-kiszolgáló elindításáért a jobb oldali csomóponton. Indítsa újra az aktív headnode feladatátvétel kényszerítéséhez.

A hálózati problémák is okozhatják ezt a problémát. Az egyes fürtcsomópontokból nézze meg, hogy tud-e pingelni. `headnodehost` Ritka olyan helyzet áll fenn, amikor `headnodehost`egyetlen fürtcsomópont sem tud csatlakozni:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Megoldás:

Az aktív headnode újraindítása általában enyhíti ezt a problémát. Ha nem, kérjük, forduljon a HDInsight támogatási csapatához.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
