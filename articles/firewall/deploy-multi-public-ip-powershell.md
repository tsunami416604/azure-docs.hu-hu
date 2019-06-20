---
title: Üzembe helyezése az Azure-tűzfal több nyilvános IP-címekkel rendelkező Azure PowerShell-lel
description: Ebből a cikkből megismerheti, hogyan való üzembe helyezéséhez az Azure-tűzfalak több nyilvános IP-cím az Azure PowerShell használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/15/2019
ms.author: victorh
ms.openlocfilehash: a7dd35212a573fc3e94dadea4365f150122e1b5a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276742"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Üzembe helyezés az Azure-tűzfalak több nyilvános IP-címek Azure PowerShell-lel

> [!IMPORTANT]
> Több nyilvános IP-címekkel rendelkező Azure tűzfal jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure tűzfal legfeljebb 600 nyilvános IP-címmel is telepítheti.

Ez a funkció lehetővé teszi, hogy a következő esetekben:

- **DNAT** – több standard port példány lefordíttathatja a háttérkiszolgálókhoz. Például ha két nyilvános IP-címek, szerint lefordíttathatja a TCP-portját 3389 (RDP) IP-címe.
- **SNAT** – további portokat a kimenő SNAT-kapcsolatok, csökkentve az esetleges SNAT portfogyás érhető el. Jelenleg az Azure-tűzfal véletlenszerűen kiválaszt a nyilvános IP-forráscím-kapcsolathoz való használatra. Ha bármelyik alárendelt szűrés a hálózaton, lehetővé teszik a tűzfal társított összes nyilvános IP-címeket szeretne.

Az alábbi Azure PowerShell-példák bemutatják, hogyan hozzáadása, eltávolítása és nyilvános IP-címek konfigurálása az Azure tűzfal.

> [!NOTE]
> Hozzáadásakor, vagy távolítsa el a futó tűzfal egy nyilvános IP-címet, meglévő bejövő kapcsolat DNAT-szabályok használatával nem működnek a 40-120 másodperc. Ez az egy korlátozás ennek a funkciónak a nyilvános előzetes verzióját.

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Nyilvános IP-cím hozzáadása egy meglévő tűzfal

Ebben a példában a nyilvános IP-cím *azFwPublicIp1* , a tűzfal csatlakozik.

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

Ebben a példában a nyilvános IP-cím *azFwPublicIp1* , a tűzfal leválasztani.

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

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: A figyelő Azure tűzfal-naplókon](./tutorial-diagnostics.md)
