---
title: Az Azure Tűzfal telepítése több nyilvános IP-címmel a PowerShell használatával
description: Ebben a cikkben megtudhatja, hogyan telepíthet egy Azure Tűzfalat több nyilvános IP-címmel az Azure PowerShell használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ad54b60d8f15e36636f887015d97967740123669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195860"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Azure Firewall üzembe helyezése több nyilvános IP-címmel az Azure PowerShell használatával

Ez a szolgáltatás a következő eseteket teszi lehetővé:

- **DNST** – Több szabványos portpéldányt is lefordíthat a háttérkiszolgálókra. Ha például két nyilvános IP-címmel rendelkezik, a 3389-es TCP-portot (RDP) mindkét IP-címhez lefordíthatja.
- **SNAT** - További portok állnak rendelkezésre a kimenő SNAT-kapcsolatokhoz, csökkentve az SNAT-port okának kimerülését. Ebben az időben az Azure Firewall véletlenszerűen kiválasztja a forrás nyilvános IP-címet használni a kapcsolatot. Ha a hálózaton van-e alsóbb rétegbeli szűrés, engedélyeznie kell a tűzfalhoz társított összes nyilvános IP-címet.
 
A több nyilvános IP-címmel rendelkező Azure Firewall az Azure Portalon, az Azure PowerShellen, az Azure CLI-n, a REST-en és a sablonokon keresztül érhető el. Legfeljebb 100 nyilvános IP-címmel rendelkező Azure tűzfalat helyezhet üzembe.

A következő Azure PowerShell-példák bemutatják, hogyan konfigurálhatja, adhat hozzá és távolíthatja el az Azure Firewall nyilvános IP-címeit.

> [!NOTE]
> Az első ipConfiguration az Azure Firewall nyilvános IP-cím konfigurációs lapjáról nem távolítható el. Ha módosítani szeretné az IP-címet, használhatja az Azure PowerShellt.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Tűzfal létrehozása két vagy több nyilvános IP-címmel

Ebben a példában két nyilvános IP-címmel rendelkező virtuális hálózati *virtuális hálózathoz* csatlakoztatott tűzfalat hoz létre.

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

Ebben a példában az *azFwPublicIp1* nyilvános IP-cím csatlakozik a tűzfalhoz.

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

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Nyilvános IP-cím eltávolítása meglévő tűzfalról

Ebben a példában az *azFwPublicIp1* nyilvános IP-cím levan választva a tűzfalról.

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

* [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./tutorial-diagnostics.md)
