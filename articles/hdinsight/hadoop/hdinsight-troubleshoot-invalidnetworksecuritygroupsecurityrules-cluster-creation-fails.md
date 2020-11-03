---
title: InvalidNetworkSecurityGroupSecurityRules-hiba – Azure HDInsight
description: A fürt létrehozása nem sikerül a ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b1423dc965a3169a5f615ccc371849cc177be244
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289092"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Forgatókönyv: InvalidNetworkSecurityGroupSecurityRules – a fürt létrehozása nem sikerül az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az `InvalidNetworkSecurityGroupSecurityRules` alhálózattal konfigurált hálózati biztonsági csoport biztonsági szabályaihoz hasonló leírással kapott hibakód nem teszi lehetővé a szükséges bejövő és/vagy kimenő kapcsolatokat.

## <a name="cause"></a>Ok

Valószínűleg probléma van a fürthöz konfigurált bejövő [hálózati biztonsági csoport](../../virtual-network/virtual-network-vnet-plan-design-arm.md) szabályaival.

## <a name="resolution"></a>Feloldás

Nyissa meg a Azure Portal, és keresse meg azt az alhálózathoz társított NSG, amelyben a fürtöt üzembe helyezi. A **bejövő biztonsági szabályok** szakaszban győződjön meg arról, hogy a szabályok engedélyezik a 443-es port bejövő hozzáférését az [itt](../control-network-traffic.md)említett IP-címekhez.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]