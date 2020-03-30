---
title: InvalidNetworkSecurityGroupSecurityRules hiba - Azure HDInsight
description: A fürt létrehozása sikertelen az InvalidNetworkSecurityGroupSecurityRules hibakóddal
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: a73e1e9f7a9c017dd29b627a24c25ae2e064c0a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894149"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Eset: InvalidNetworkSecurityGroupSecurityRules – a fürt létrehozása sikertelen az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

`InvalidNetworkSecurityGroupSecurityRules` "Az alhálózattal konfigurált hálózati biztonsági csoport biztonsági szabályai nem engedélyezik a szükséges bejövő és/vagy kimenő kapcsolatot."

## <a name="cause"></a>Ok

Valószínűleg a fürthöz konfigurált bejövő [hálózati biztonságicsoport-szabályokkal](../../virtual-network/virtual-network-vnet-plan-design-arm.md) kapcsolatos probléma.

## <a name="resolution"></a>Megoldás:

Nyissa meg az Azure Portalon, és azonosítsa az nsg, amely társítva van az alhálózat, ahol a fürt üzembe helyezése. A **Bejövő biztonsági szabályok szakaszban** győződjön meg arról, hogy a szabályok engedélyezik a 443-as porthoz való bejövő hozzáférést az [itt](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)említett IP-címekhez.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
