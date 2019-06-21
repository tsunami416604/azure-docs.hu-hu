---
title: Üzembe helyezése az Azure-tűzfal rendelkezésre állási zónák az Azure PowerShell-lel
description: Ebből a cikkből megismerheti, hogyan helyezhet üzembe egy Azure tűzfalakat a rendelkezésre állási zónákkal az Azure PowerShell használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/20/2019
ms.author: victorh
ms.openlocfilehash: 2fa6a62a28a1536da83994cb07b7c5fa5d7bda9f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276911"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Üzembe helyezése az Azure tűzfal rendelkezésre állási zónák az Azure PowerShell-lel

> [!IMPORTANT]
> A rendelkezésre állási zónák Azure tűzfal jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
