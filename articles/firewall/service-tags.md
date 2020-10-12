---
title: A Azure Firewall Service-címkék áttekintése
description: A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "74168689"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall szolgáltatás címkéi

A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem hozhat létre saját szolgáltatáscímkéket, és nem határozhatja meg, hogy melyik IP-címeket jelöljék az egyes címkék. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket.

Azure Firewall szolgáltatás címkéi a hálózati szabályok célhely mezőjében használhatók. Azokat meghatározott IP-címek helyett használhatja.

## <a name="supported-service-tags"></a>Támogatott szolgáltatási Címkék

Az Azure tűzfal hálózati szabályaiban használható szolgáltatási címkék listáját a [biztonsági csoportok](../virtual-network/security-overview.md#service-tags) részben tekintheti meg.

## <a name="next-steps"></a>További lépések

További információ a Azure Firewall szabályokról: [Azure Firewall szabály-feldolgozási logika](rule-processing.md).