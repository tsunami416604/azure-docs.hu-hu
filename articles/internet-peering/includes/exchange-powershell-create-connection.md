---
title: fájl belefoglalása
titleSuffix: Azure
description: fájl belefoglalása
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 0014b67443797f45de51ec1bc459f71bde55cdc9
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774431"
---
Az alábbi példa bemutatja, hogyan hozhat létre Exchange-kapcsolatot a Equinix internetes Exchange-ben Seattle-ben. Ha más szolgáltatót használ, és különböző beállításokkal rendelkezik, akkor a kérés végrehajtásakor cserélje le ezt az információt.

A **New-AzPeeringExchangeConnectionObject PowerShell-** parancsmaggal hozzon létre PowerShell-kapcsolódási objektumokat, amelyeket a rendszer az új egyenrangú kérelem létrehozásához fog használni:

Az alábbi példa egy Exchange-alapú kapcsolatok létrehozását mutatja be:

```powershell
$connection1 = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `
```

Hozzon létre egy másik kapcsolatot abban az esetben, ha redundancia szükséges a megadott egyenrangú helyen:

```powershell
$connection2 = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.23 `
    -PeerSessionIPv6Address  2001:504:12::23 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `
```

A **New-AzPeering PowerShell-** parancsmag használatával új Exchange-társítás hozható létre:

```powershell
$asn = Get-AzPeerAsn
New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup" `
    -PeerAsnResourceId $asn.Id `
    -PeeringLocation  $exchangeLocation[1].PeeringLocation `
    -ExchangeConnection $connection1[, $connection2]
```
&nbsp;

Az alábbi példa erre a válaszra mutat, ha a kérést egy-kapcsolatban futtatták:

```powershell

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

> [!IMPORTANT]
> A Microsoft elkezdi kiépíteni a kért társ-összevonást, és a `ConnectionState` a folyamat előrehaladását fogja tükrözni.
> A kapcsolódó lépések kilétesítéséhez tekintse meg az [Exchange-társi útmutató](../walkthrough-exchange-all.md) dokumentumát.

A ConnectionState az alább látható módon tekintheti meg:

```powershell

$peering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"
$peering.Connections

PeeringDBFacilityId         : 11
PeerSessionIPv4Address      : 198.32.134.22
PeerSessionIPv6Address      : 2001:504:12::22
SessionStateV4              : PendingAdd
SessionStateV6              : PendingAdd
MaxPrefixesAdvertisedV4     : 2000
MaxPrefixesAdvertisedV6     : 2000
MicrosoftSessionIPv4Address : 198.32.134.152
MicrosoftSessionIPv4Address : 2001:504:12::15
Md5AuthenticationKey        :

```