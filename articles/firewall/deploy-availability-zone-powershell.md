---
title: Az Azure tűzfal üzembe helyezése rendelkezésre állási zónákkal a PowerShell használatával
description: Ebben a cikkben megtudhatja, hogyan telepíthet egy Azure-tűzfal rendelkezésre állási zónák az Azure PowerShell használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195927"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Azure Firewall üzembe helyezése az Availability Zonesszal az Azure PowerShell használatával

Az Azure Firewall konfigurálható a központi telepítés során, hogy több rendelkezésre állási zónára is kiterjedjen a nagyobb rendelkezésre állás érdekében.

Ez a szolgáltatás a következő eseteket teszi lehetővé:

- A rendelkezésre állást 99,99%-ra növelheti. További információt az Azure firewall [szolgáltatásiszint-szerződésében (SLA) talál.](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) A 99,99%-os rendelkezésre állási SLA két vagy több rendelkezésre állási zóna kiválasztásakor érhető el.
- Az Azure Firewall-t is társíthatja egy adott zónához csak közelségi okokból, a 99.95%-os SLA szolgáltatás használatával.

Az Azure tűzfal rendelkezésre állási zónáiról a [Mi az Azure tűzfal?](overview.md)

A következő Azure PowerShell-példa bemutatja, hogyan telepítheti az Azure tűzfal rendelkezésre állási zónákkal.

## <a name="create-a-firewall-with-availability-zones"></a>Tűzfal létrehozása rendelkezésre állási zónákkal

Ez a példa tűzfalat hoz létre az 1., 2.

A szabványos nyilvános IP-cím létrehozásakor nincs megadva adott zóna. Ez alapértelmezés szerint zónaredundáns IP-címet hoz létre. A szabványos nyilvános IP-címek konfigurálhatók az összes zónában vagy egyetlen zónában.

Fontos tudni, mert nem lehet tűzfal az 1-es zónában és ip-cím a 2-es zónában. De lehet tűzfal az 1.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./tutorial-diagnostics.md)
