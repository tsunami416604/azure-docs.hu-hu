---
title: InvalidNetworkSecurityGroupSecurityRules-hiba – Azure HDInsight
description: A fürt létrehozása nem sikerül a ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 749cfbb3aeb0d82a81a8383919b9a6568419e967
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044758"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Forgatókönyv: InvalidNetworkSecurityGroupSecurityRules – a fürt létrehozása nem sikerül az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A hibakód `InvalidNetworkSecurityGroupSecurityRules` az alhálózattal konfigurált hálózati biztonsági csoport biztonsági szabályaihoz hasonló leírással, nem engedélyezi a szükséges bejövő és/vagy kimenő kapcsolatokat. "

## <a name="cause"></a>Ok

Valószínűleg probléma van a fürthöz konfigurált bejövő [hálózati biztonsági csoport](../../virtual-network/virtual-network-vnet-plan-design-arm.md) szabályaival.

## <a name="resolution"></a>Felbontás

Nyissa meg a Azure Portal, és keresse meg azt az alhálózathoz társított NSG, amelyben a fürtöt üzembe helyezi. A **bejövő biztonsági szabályok** szakaszban győződjön meg arról, hogy a szabályok engedélyezik a 443-es port bejövő hozzáférését az [itt](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)említett IP-címekhez.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiók a felhasználói élmény javításához az Azure-Közösség és a megfelelő erőforrások összekapcsolásával: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
