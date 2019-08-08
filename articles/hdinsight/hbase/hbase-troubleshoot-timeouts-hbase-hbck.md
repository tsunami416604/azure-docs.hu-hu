---
title: Időtúllépések az Azure HDInsight "hbase hbck" parancsával
description: Időtúllépési hiba az "hbase hbck" paranccsal a régió hozzárendeléseinek kijavításakor
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 4f3f1c22fa1dc05a66a8b6bf0179903a44cef9b6
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737926"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Forgatókönyv: Időtúllépések az Azure HDInsight "hbase hbck" parancsával

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Időtúllépéseket észlel a `hbase hbck` régió-hozzárendelések kijavítására szolgáló paranccsal.

## <a name="cause"></a>Ok

Ennek lehetséges oka az lehet, hogy hosszú idő alatt több régió is az "átmenet" állapotban van. Ezek a régiók az Apache HBase Master felhasználói felületéről offline állapotba helyezhetők. Az átállást igénylő régiók nagy száma miatt előfordulhat, hogy a HBase Master időtúllépést okoz, és nem fogja tudni visszahozni ezeket a régiókat online állapotba.

## <a name="resolution"></a>Megoldás:

1. Jelentkezzen be a HDInsight HBase-fürtbe SSH használatával.

1. Futtassa `hbase zkcli` a parancsot a Zookeeper-rendszerhéjhoz való kapcsolódáshoz.

1. Futtatás `rmr /hbase/regions-in-transition` vagy`rmr /hbase-unsecure/regions-in-transition` parancs.

1. Kilépés a `hbase zkcli` rendszerhéjból parancs `exit` használatával.

1. Nyissa meg a Ambari felhasználói felületét, és indítsa újra az aktív HBase Master szolgáltatást a Ambari.

1. Figyelje meg, hogy a "régió az átmenetben" HBase Master felhasználói felülete nem ragadja meg a régiót.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
