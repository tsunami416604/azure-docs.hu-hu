---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: df9c291136fc6a48effb08cd59eeb66486eb641c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003861"
---
### <a name="virtual-networks"></a>Virtuális hálózatok

|  |  |
|---------|---------|
| [Engedélyezett Application Gateway termékváltozatok](../articles/governance/policy/samples/allowed-app-gate-sku.md) | Ehhez az alkalmazásátjáróknak jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy jóváhagyott termékváltozatokat tartalmazó tömböt. |
| [Hálózati társviszony tiltása egy erőforráscsoportban lévő hálózatban](../articles/governance/policy/samples/no-peering-er-net.md) | Megtiltja hálózati társviszony társítását egy megadott erőforráscsoportban lévő hálózattal. Segít meggátolni a központi felügyelt hálózati infrastruktúrához való kapcsolódást. Meg kell adnia annak az erőforráscsoportnak a nevét, amelyben meg kívánja gátolni a társítást. |
| [Felhasználó által megadott útválasztási táblázatok tiltása](../articles/governance/policy/samples/no-user-def-route-table.md)  |Megtiltja a virtuális hálózatok felhasználó által megadott útválasztási táblázattal való üzembe helyezését. |
| [NSG X minden alhálózaton](../articles/governance/policy/samples/nsg-on-subnet.md) | Előírja, hogy minden virtuális alhálózathoz egy adott hálózati biztonsági csoportot kell használni. A használandó hálózati biztonsági csoport azonosítóját Ön határozza meg. |
| [Jóváhagyott alhálózat használata a virtuális gépek hálózati adaptereihez](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Előírja, hogy a hálózati adaptereknek egy jóváhagyott alhálózatot kell használniuk. A jóváhagyott alhálózat azonosítóját Ön határozza meg. |
| [Jóváhagyott virtuális hálózat használata a virtuális gépek hálózati adaptereihez](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Előírja, hogy a hálózati adaptereknek egy jóváhagyott virtuális hálózatot kell használniuk. A jóváhagyott virtuális hálózat azonosítóját Ön határozza meg. |