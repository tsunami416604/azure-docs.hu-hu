---
title: Az Azure Firewall szolgáltatáscímkéinek áttekintése
description: A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168689"
---
# <a name="azure-firewall-service-tags"></a>Az Azure Tűzfal szolgáltatáscímkéi

A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem hozhat létre saját szolgáltatáscímkéket, és nem határozhatja meg, hogy melyik IP-címeket jelöljék az egyes címkék. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket.

Az Azure Firewall szolgáltatáscímkék a hálózati szabályok célmezőjében használhatók. Ezeket használhatja az adott IP-címek helyett.

## <a name="supported-service-tags"></a>Támogatott szolgáltatáscímkék

Az Azure tűzfalhálózati szabályokban használható szolgáltatáscímkék listáját a [Biztonsági csoportok](../virtual-network/security-overview.md#service-tags) tekintse meg.

## <a name="next-steps"></a>További lépések

Az Azure Tűzfal-szabályokról az [Azure Firewall szabályfeldolgozási logikája.](rule-processing.md)