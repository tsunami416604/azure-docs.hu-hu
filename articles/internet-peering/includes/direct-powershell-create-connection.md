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
ms.openlocfilehash: 610bac5c08d7f3aa3c93e273bc6573a08ca1239f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680748"
---
A következő példa bemutatja, hogyan hozhat létre egy 10 Gbit/s-os közvetlen társviszony-létesítés seattle-i.

A PowerShell **Új-AzPeeringDirectConnectionObject** parancsmagjával hozzon létre directconnection-objektumokat, amelyeket az új társviszony-létesítési kérelemben kell használni.

Ez a példa bemutatja, hogyan hozhat létre DirectConnection objektumot.

```powershell
$connection1 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.31.0/31 `
    -SessionPrefixV6 fe01::3e:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

> [!NOTE]
> Az előző <index>példában **$peeringLocation[]** <index> értéke meg kell egyeznie az Ön által választott társviszony-létesítési helynek.

Hozzon létre egy másik kapcsolatot arra az esetre, ha redundanciára lenne szüksége egy adott társviszony-létesítési helyen.

```powershell
$connection2 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.33.0/31 `
    -SessionPrefixV6 fe01::3f:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

A PowerShell új **társviszony-létesítési** parancsmagjával hozzon létre egy új közvetlen társviszony-létesítést. Ehhez a parancshoz ASN-erőforrás-azonosító szükséges, amely az itt látható módon beolvasható.


```powershell
$asn = Get-AzPeerAsn
New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `
    -PeeringLocation  $peeringLocation[0].PeeringLocation `
    -PeerAsnResourceId $asn.Id `
    -DirectConnection $connection1 [, $connection2]
```
&nbsp;

Ez a példa a kérés sikeres feldolgozásának válaszát mutatja be.

```powershell

    Name                 : SeattleDirectPeering
    Sku.Name             : Basic_Direct_Free
    Kind                 : Direct
    Connections          : 71
    PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/SeattleDirectPeering
    UseForPeeringService : False
    PeeringLocation      : Seattle
    ProvisioningState    : Succeeded
    Location             : centralus
    Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
    Type                 : Microsoft.Peering/peerings
    Tags                 : {}

```
Vegye figyelembe, hogy a **kimenet {subscriptionId}** helyett a tényleges előfizetés-azonosító jelenik meg.
