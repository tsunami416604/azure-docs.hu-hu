---
title: A Azure Firewall Service-címkék áttekintése
description: A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 47377817b62d33e8af79e4a0d2dceb68ba9dbdc5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658647"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall szolgáltatás címkéi

A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem hozhat létre saját szolgáltatáscímkéket, és nem határozhatja meg, hogy melyik IP-címeket jelöljék az egyes címkék. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket.

Azure Firewall szolgáltatás címkéi a hálózati szabályok célhely mezőjében használhatók. Azokat meghatározott IP-címek helyett használhatja.

## <a name="supported-service-tags"></a>Támogatott szolgáltatási Címkék

Az Azure tűzfal hálózati szabályaiban használható szolgáltatási címkék listáját a [biztonsági csoportok](../virtual-network/network-security-groups-overview.md#service-tags) részben tekintheti meg.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Firewall szabályokról: [Azure Firewall szabály-feldolgozási logika](rule-processing.md).