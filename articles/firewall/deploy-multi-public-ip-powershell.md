---
title: Azure Firewall üzembe helyezése több nyilvános IP-címmel a PowerShell használatával
description: Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy Azure Firewall több nyilvános IP-címmel a Azure PowerShell használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 21f645e64c9944ed102f538710ea6facc26c7e83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81314033"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Azure Firewall üzembe helyezése több nyilvános IP-címmel az Azure PowerShell használatával

Ez a szolgáltatás a következő forgatókönyveket teszi lehetővé:

- **DNAT** – a háttér-kiszolgálókra több szabványos port-példányt is lefordíthat. Ha például két nyilvános IP-címmel rendelkezik, akkor mindkét IP-cím esetében lefordíthatja a 3389-es TCP-portot.
- **SNAT** – további portok érhetők el a kimenő SNAT-kapcsolatokhoz, ami csökkenti a SNAT-portok kimerülésének lehetséges lehetőségét. Ekkor Azure Firewall véletlenszerűen kiválasztja a forrás nyilvános IP-címét, amelyet a rendszer a kapcsolódáshoz használ. Ha a hálózaton bármilyen alsóbb rétegbeli szűrés van, engedélyeznie kell a tűzfalhoz társított összes nyilvános IP-címet.
 
A több nyilvános IP-címmel rendelkező Azure Firewall a Azure Portal, a Azure PowerShell, az Azure CLI, a REST és a sablonok használatával érhető el. Akár 100 nyilvános IP-címmel rendelkező Azure Firewall is üzembe helyezhet.

Az alábbi Azure PowerShell példák bemutatják, hogyan konfigurálhatja, adhatja hozzá és távolíthatja el a Azure Firewall nyilvános IP-címeit.

> [!NOTE]
> Az első ipConfiguration nem távolítható el a Azure Firewall nyilvános IP-cím konfigurációjának oldaláról. Ha módosítani kívánja az IP-címet, használhatja a Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Hozzon létre két vagy több nyilvános IP-címmel rendelkező tűzfalat

Ez a példa két nyilvános IP-címmel rendelkező virtuális hálózati *vnet* csatolt tűzfalat hoz létre.

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

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Nyilvános IP-cím hozzáadása meglévő tűzfalhoz

Ebben a példában a nyilvános IP- *azFwPublicIp1* a tűzfalhoz van csatlakoztatva.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Nyilvános IP-cím eltávolítása meglévő tűzfallal

Ebben a példában a nyilvános IP-cím *azFwPublicIp1* le van választva a tűzfalból.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>További lépések

* [Gyors útmutató: Azure Firewall létrehozása több nyilvános IP-címmel – Resource Manager-sablon](quick-create-multiple-ip-template.md)
