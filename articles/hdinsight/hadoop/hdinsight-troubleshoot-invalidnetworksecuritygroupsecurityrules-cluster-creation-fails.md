---
title: InvalidNetworkSecurityGroupSecurityRules – a fürt meghibásodik az Azure HDInsight
description: A fürt létrehozása nem sikerül a ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: a534ae54b6cb9ce20d0a1bc970ef575f88a09827
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735749"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Forgatókönyv: InvalidNetworkSecurityGroupSecurityRules – a fürt létrehozása nem sikerül az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az alhálózattal `InvalidNetworkSecurityGroupSecurityRules` konfigurált hálózati biztonsági csoport biztonsági szabályaihoz hasonló leírással kapott hibakód nem teszi lehetővé a szükséges bejövő és/vagy kimenő kapcsolatokat.

## <a name="cause"></a>Ok

Valószínűleg probléma van a fürthöz konfigurált bejövő [hálózati biztonsági csoport](../../virtual-network/virtual-network-vnet-plan-design-arm.md) szabályaival.

## <a name="resolution"></a>Megoldás:

Nyissa meg a Azure Portal, és keresse meg azt az alhálózathoz társított NSG, amelyben a fürtöt üzembe helyezi. A **bejövő biztonsági szabályok** szakaszban győződjön meg arról, hogy a szabályok engedélyezik a 443-es port bejövő hozzáférését az [itt](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)említett IP-címekhez.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
