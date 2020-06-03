---
title: Azure Firewall üzembe helyezése Availability Zones a PowerShell használatával
description: Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy Azure Firewallt a Availability Zones használatával a Azure PowerShell használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ced5dc58edb5ee44a39b5afde268a290f33aedac
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84297717"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Azure Firewall üzembe helyezése az Availability Zonesszal az Azure PowerShell használatával

A Azure Firewall az üzembe helyezés során konfigurálható úgy, hogy a nagyobb rendelkezésre állás érdekében több Availability Zones is kiterjedhet.

Ez a szolgáltatás a következő forgatókönyveket teszi lehetővé:

- A rendelkezésre állást 99,99%-os üzemidőre növelheti. További információ: Azure Firewall [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Ha két vagy több Availability Zones van kiválasztva, a 99,99%-os rendelkezésre állási SLA-t ajánljuk.
- A szolgáltatáshoz a 99,95%-os SLA-t használó szabványos szolgáltatási szabványnak megfelelően a Azure Firewallt is hozzárendelheti egy adott zónához.

További információ a Azure Firewall Availability Zonesról: [Mi az Azure Firewall?](overview.md)

A következő Azure PowerShell példa azt szemlélteti, hogyan helyezhet üzembe egy Azure Firewall a Availability Zones használatával.

## <a name="create-a-firewall-with-availability-zones"></a>Tűzfal létrehozása Availability Zones

Ez a példa egy tűzfalat hoz létre az 1., 2. és 3. zónában.

A standard nyilvános IP-cím létrehozásakor nincs megadva adott zóna. Ez alapértelmezés szerint létrehoz egy zóna-redundáns IP-címet. A standard nyilvános IP-címek bármelyik zónában, vagy egyetlen zónában konfigurálhatók.

Fontos tudni, mert nem lehet tűzfal az 1. zónában és egy IP-cím a 2. zónában. Az 1. zónában és az IP-címen található tűzfal az összes zónában, illetve egy tűzfal és egy IP-cím is lehet ugyanabban a zónában a közelségi célokhoz.

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
  -PublicIpAddress @($pip1) `
  -Zone 1,2,3
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./tutorial-diagnostics.md)
