---
title: Üzembe helyezése az Azure-tűzfal rendelkezésre állási zónák az Azure PowerShell-lel
description: Ebből a cikkből megismerheti, hogyan helyezhet üzembe egy Azure tűzfalakat a rendelkezésre állási zónákkal az Azure PowerShell használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: 56958eedceeb4602589d65d5e0eb7b10e8a9ff2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703999"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Üzembe helyezése az Azure tűzfal rendelkezésre állási zónák az Azure PowerShell-lel

Az Azure tűzfal span megnövelt rendelkezésre állás érdekében több rendelkezésre állási zónában való üzembe helyezéskor konfigurálható.

Ez a funkció lehetővé teszi, hogy a következő esetekben:

- 99,99 %-os rendelkezésre állást is növelheti. További információkért tekintse meg az Azure-tűzfal [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). A 99,99 %-os SLA-t érhető el, ha két vagy több rendelkezésre állási zónák vannak kijelölve.
- Is társíthat Azure tűzfal egy adott zóna csak a közelségi okok miatt a szolgáltatás standard 99,95 %-os SLA-t használja.

Azure tűzfal rendelkezésre állási zónákkal kapcsolatos további információkért lásd: [Mi az Azure-tűzfal?](overview.md)

Az alábbi Azure PowerShell-példa bemutatja, hogyan telepíthet egy Azure-tűzfal a rendelkezésre állási zónákban.

## <a name="create-a-firewall-with-availability-zones"></a>A rendelkezésre állási zónákban tűzfal létrehozása

Ebben a példában az 1, 2 és 3 zónákban hoz létre tűzfalat.

A standard nyilvános IP-cím létrehozásakor nem adott zóna van megadva. Ez alapértelmezés szerint létrehoz egy zónaredundáns IP-címet. A standard nyilvános IP-címek konfigurálhatók, az összes zóna vagy a zónában.

Fontos tudni, mert az 2. zóna nem lehet 1. zónába tűzfal és a egy IP-címet. De lehet 1. zónába egy tűzfal- és IP-címet minden zóna vagy a tűzfal és IP-cím közelségi célból egyetlen ugyanabban a zónában.

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

- [Oktatóanyag: A figyelő Azure tűzfal-naplókon](./tutorial-diagnostics.md)
