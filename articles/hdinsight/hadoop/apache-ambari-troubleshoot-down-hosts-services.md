---
title: Az Apache Ambari felhasználói felülete a gazdagépeket és szolgáltatásokat mutatja be az Azure HDInsight
description: Apache Ambari felhasználói felülettel kapcsolatos probléma elhárítása, amikor az Azure HDInsight gazdagépeit és szolgáltatásait mutatja be
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895635"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Forgatókönyv: az Apache Ambari felhasználói felülete a gazdagépeket és szolgáltatásokat mutatja be az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache Ambari felhasználói felülete elérhető, de a felhasználói felület szinte minden szolgáltatás leállt, a szívverést jelző összes gazdagép elveszett.

## <a name="cause"></a>Ok

A legtöbb esetben ez az a probléma, hogy a Ambari-kiszolgáló nem fut az aktív átjárócsomóponthoz. Ellenőrizze, hogy melyik átjárócsomóponthoz az aktív átjárócsomóponthoz, és győződjön meg arról, hogy a ambari-kiszolgáló fut a jobb oldalon. Ne indítsa el kézzel a ambari, hagyja, hogy a feladatátvételi vezérlő szolgáltatás felelős a ambari a megfelelő átjárócsomóponthoz való indításához. Indítsa újra az aktív átjárócsomóponthoz a feladatátvétel kényszerítéséhez.

A hálózati problémák is okozhatják ezt a problémát. A fürt minden csomópontján ellenőrizze, hogy tud-e pingelni `headnodehost` . Ritka helyzet van, ha egy fürtcsomópont nem tud csatlakozni a következőhöz `headnodehost` :

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Megoldás:

Általában az aktív átjárócsomóponthoz újraindítása csökkenti a problémát. Ha nem forduljon a HDInsight ügyfélszolgálatához.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
