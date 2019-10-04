---
title: Azure Resource Manager-sablonminták virtuális hálózathoz | Microsoft Docs
description: Ismerje meg az Azure virtuális hálózatok üzembe helyezéséhez használható különféle Azure Resource Manager-sablonokat.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: c553e3f2de963abb4a3c23a4114eecfa33d0aa61
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735328"
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>Azure Resource Manager-sablonminták virtuális hálózathoz

Az alábbi táblázatban hivatkozásokat találhat az elérhető Azure Resource Manager-sablonmintákra. A sablonokat az Azure [Portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), az Azure [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy az Azure [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json) segítségével helyezheti üzembe. Saját sablonok létrehozásáról [Az első saját sablon létrehozása](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Az Azure Resource Manager-sablonok szerkezetének és szintaxisának megértése](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című cikkben tájékozódhat.

A JSON-szintaxist és a sablonok tulajdonságait: [Microsoft.Network erőforrás-típus](/azure/templates/microsoft.network/allversions).


| Tevékenység | Leírás |
|----|----|
|[Virtuális hálózat létrehozása két alhálózattal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| Létrehoz egy virtuális hálózatot két alhálózattal.|
|[Forgalom irányítása hálózati virtuális készüléken keresztül](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| Létrehoz egy virtuális hálózatot három alhálózattal. Üzembe helyez egy virtuális gépet minden egyes alhálózatban. Létrehoz egy útvonaltáblát, mely a forgalmat az egyik alhálózatból egy másikba irányítja a harmadik alhálózatban futó virtuális gépen keresztül. Hozzárendeli az útvonaltáblát az alhálózatok egyikéhez.|
|[Virtuális hálózati szolgáltatásvégpont létrehozása az Azure Storage szolgáltatáshoz](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|Létrehoz egy új virtuális hálózatot két alhálózattal, mindkét alhálózatban egy-egy hálózati adapterrel. Engedélyez egy Azure Storage-szolgáltatásvégpontot az egyik alhálózathoz, és új tárfiókot is biztosít az adott alhálózat számára.|
|[Két virtuális hálózat csatlakoztatása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| Létrehoz két virtuális hálózatot, illetve egy virtuális hálózati társviszonyt a két hálózat között.|
|[Virtuális gép létrehozása több IP-címmel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| Létrehoz egy Windows vagy Linux rendszerű virtuális gépet több IP-címmel.|
|[Konfigurálhatja IPv4 + IPv6 kettős verem virtuális hálózat](https://github.com/Azure/azure-quickstart-templates/tree/master/ipv6-in-vnet)|Kettős vermű (IPv4 + IPv6) két virtuális gépet, és az Azure alapszintű Load Balancerhez IPv4 és IPv6 típusú nyilvános IP-címekkel rendelkező virtuális hálózaton üzembe helyezi. |
