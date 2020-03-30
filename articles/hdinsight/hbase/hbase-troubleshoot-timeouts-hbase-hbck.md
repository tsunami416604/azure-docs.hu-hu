---
title: A hbase hbck parancs időtúllépése az Azure HDInsightban
description: Időtúlolása a "hbase hbck" paranccsal a régió-hozzárendelések javításakor
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887189"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Eset: Időtúltöltések a "hbase hbck" paranccsal az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Időmegtolások `hbase hbck` találkozása a paranccsal a régió-hozzárendelések rögzítésekénekén.

## <a name="cause"></a>Ok

A parancs használatakor az `hbck` időtúlváltási problémák egyik fő oka lehet, hogy több régió hosszú ideig "átmeneti" állapotban van. Ezeket a régiókat offline állapotban láthatja a HBase főkiszolgáló felhasználói felületén. Mivel nagy számú régió próbál áttérni, a HBase Master időtúljárhatja, és előfordulhat, hogy nem tudja újra online állapotba hozni ezeket a régiókat.

## <a name="resolution"></a>Megoldás:

1. Jelentkezzen be a HDInsight HBase fürtbe az SSH használatával.

1. Futtassa `hbase zkcli` a parancsot az Apache ZooKeeper rendszerhéjhoz való csatlakozáshoz.

1. Futtatás `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` vagy parancs.

1. Kilépés `hbase zkcli` a rendszerhéjból a parancs segítségével. `exit`

1. Az Apache Ambari felhasználói felületéről indítsa újra az Active HBase Master szolgáltatást.

1. Futtassa a következő parancsot: `hbase hbck -fixAssignments`.

1. Figyelje a HBase főkiszolgáló felhasználói felületének "átalakulóban lévő régióját", hogy megbizonyosodjon arról, hogy egyetlen régió sem ragad meg.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

- Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

- Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
