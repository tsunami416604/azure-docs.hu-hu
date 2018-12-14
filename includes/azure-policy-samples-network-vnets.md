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
ms.openlocfilehash: f93e22012a4855257f5372c1fc1dbc05ad29a6cd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318216"
---
### <a name="virtual-networks"></a>Virtuális hálózatok

|  |  |
|---------|---------|
| [Engedélyezett Application Gateway termékváltozatok](../articles/governance/policy/samples/allowed-app-gateway-sku.md) | Ehhez az alkalmazásátjáróknak jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy jóváhagyott termékváltozatokat tartalmazó tömböt. |
| [Virtuális hálózati átjárók engedélyezett termékváltozatai](../articles/governance/policy/samples/allowed-vnet-gateway-sku.md) | Ehhez a virtuális hálózati átjáróknak egy jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy jóváhagyott termékváltozatokat tartalmazó tömböt. |
| [Engedélyezett termékváltozatok terheléselosztókhoz](../articles/governance/policy/samples/allowed-load-balancer-skus.md) | Ehhez a virtuális hálózat terheléselosztóinak egy jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy jóváhagyott termékváltozatokat tartalmazó tömböt. |
| [Társhálózat-létesítés tiltása ExpressRoute-hálózattal](../articles/governance/policy/samples/no-peering-express-route-network.md) | Megtiltja hálózati társviszony társítását egy megadott erőforráscsoportban lévő hálózattal. Segít meggátolni a központi felügyelt hálózati infrastruktúrához való kapcsolódást. Meg kell adnia annak az erőforráscsoportnak a nevét, amelyben meg kívánja gátolni a társítást. |
| [Felhasználó által megadott útválasztási táblázatok tiltása](../articles/governance/policy/samples/no-user-defined-route-table.md)  | Megtiltja a virtuális hálózatok felhasználó által megadott útválasztási táblázattal való üzembe helyezését. |
| [NSG X minden alhálózaton](../articles/governance/policy/samples/nsg-on-subnet.md) | Előírja, hogy minden virtuális alhálózathoz egy adott hálózati biztonsági csoportot kell használni. A használandó hálózati biztonsági csoport azonosítóját Ön határozza meg. |
| [Jóváhagyott alhálózat használata a virtuális gépek hálózati adaptereihez](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Előírja, hogy a hálózati adaptereknek egy jóváhagyott alhálózatot kell használniuk. A jóváhagyott alhálózat azonosítóját Ön határozza meg. |
| [Jóváhagyott virtuális hálózat használata a virtuális gépek hálózati adaptereihez](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Előírja, hogy a hálózati adaptereknek egy jóváhagyott virtuális hálózatot kell használniuk. A jóváhagyott virtuális hálózat azonosítóját Ön határozza meg. |