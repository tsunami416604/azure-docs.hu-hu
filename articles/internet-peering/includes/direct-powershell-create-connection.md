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
ms.openlocfilehash: e1cb1bec6273fa79315b9439bec1412622ebfe28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774301"
---
A következő példa bemutatja, hogyan hozhat létre egy 10 Gbps közvetlen társviszony-létesítés seattle-i.

A PowerShell **Új-AzPeeringDirectConnectionObject** parancsmagjával hozzon létre közvetlen kapcsolatokat, amelyeket az új társviszony-létesítési kérelemben kell használni.

Az alábbi példa egy DirectConnection-kapcsolat létrehozására:

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
> A <index> fenti<index>példában a $peeringLocation[ ]-nak meg kell felelnie az Ön által választott társviszony-létesítési helynek.

Hozzon létre egy másik kapcsolatot arra az esetre, ha redundanciára lenne szüksége az adott társviszony-létesítési helyen:

```powershell
$connection2 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.33.0/31 `
    -SessionPrefixV6 fe01::3f:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

A PowerShell **Új-AzPeering** parancsmagjával hozzon létre egy új közvetlen társviszony-létesítést. Ez a parancs ASN erőforrás-azonosítót igényel, amely az alábbiak szerint beolvasható.


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

Az alábbiakban egy példa választ, ha a kérelem feldolgozása sikeresen:

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
Vegye figyelembe, hogy a fenti kimenetben a **{subscriptionId}** helyett a tényleges előfizetés-azonosító jelenik meg.
