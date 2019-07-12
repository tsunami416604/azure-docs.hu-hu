---
title: Üzembe helyezése az Azure-tűzfal több nyilvános IP-címekkel rendelkező Azure PowerShell-lel
description: Ebből a cikkből megismerheti, hogyan való üzembe helyezéséhez az Azure-tűzfalak több nyilvános IP-cím az Azure PowerShell használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: ce47612f18ee64caa3a053001deb5448f7c27bfd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703986"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Üzembe helyezés az Azure-tűzfalak több nyilvános IP-címek Azure PowerShell-lel

> [!IMPORTANT]
> Több nyilvános IP-címekkel rendelkező Azure-tűzfalon keresztül az Azure PowerShell, az Azure CLI-vel, a REST és a sablonok érhető el. A portál kezelőfelülete ad hozzá régiókat Növekményesen, és lesz elérhető az összes régióban a bevezetés befejezésekor.

Az Azure tűzfal legfeljebb 100 nyilvános IP-címmel is telepítheti.

Ez a funkció lehetővé teszi, hogy a következő esetekben:

- **DNAT** – több standard port példány lefordíttathatja a háttérkiszolgálókhoz. Például ha két nyilvános IP-címek, szerint lefordíttathatja a TCP-portját 3389 (RDP) IP-címe.
- **SNAT** – további portokat a kimenő SNAT-kapcsolatok, csökkentve az esetleges SNAT portfogyás érhető el. Jelenleg az Azure-tűzfal véletlenszerűen kiválaszt a nyilvános IP-forráscím-kapcsolathoz való használatra. Ha bármelyik alárendelt szűrés a hálózaton, lehetővé teszik a tűzfal társított összes nyilvános IP-címeket szeretne.

Az alábbi Azure PowerShell-példák bemutatják, hogyan konfigurálja, hozzáadhat és eltávolíthat a nyilvános IP-címek az Azure tűzfal.

> [!NOTE]
> Az első IP-konfiguráció nem távolítható el az Azure-tűzfal nyilvános IP cím konfigurációs lapon. Ha szeretné módosítani az IP-cím, használhatja az Azure PowerShell-lel.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Két vagy több nyilvános IP-címekkel rendelkező tűzfal létrehozása

Ez a példa létrehoz egy virtuális hálózathoz csatlakozó tűzfal *vnet* két nyilvános IP-címmel.

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

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Nyilvános IP-cím hozzáadása egy meglévő tűzfal

Ebben a példában a nyilvános IP-cím *azFwPublicIp1* csatolva van a tűzfal.

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

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Nyilvános IP-cím eltávolítása egy meglévő tűzfal

Ebben a példában a nyilvános IP-cím *azFwPublicIp1* a tűzfal leválasztani.

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

* [Oktatóanyag: A figyelő Azure tűzfal-naplókon](./tutorial-diagnostics.md)
