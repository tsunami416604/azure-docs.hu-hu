---
title: Örökölt Exchange-társ átalakítása Azure-erőforrásra a PowerShell használatával
titleSuffix: Azure
description: Örökölt Exchange-társ átalakítása Azure-erőforrásra a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775393"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Örökölt Exchange-társ átalakítása Azure-erőforrásra a PowerShell használatával

Ez a cikk bemutatja, hogyan alakíthat át egy meglévő örökölt Exchange-társítást az Azure-erőforrásra a PowerShell-parancsmagok használatával.

Ha szeretné, ezt az útmutatót a [portálon](howto-legacy-exchange-portal.md)végezheti el.

## <a name="before-you-begin"></a>Előzetes teendők
* A konfigurálás megkezdése előtt tekintse át az [Előfeltételek](prerequisites.md) és az [Exchange](walkthrough-exchange-all.md) -társítás áttekintése című témakört.

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Örökölt Exchange-társ átalakítása az Azure-erőforrásra

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>Örökölt Exchange-társítás beolvasása átalakításhoz
Alább látható a következő példa az örökölt Exchange-társítás beszerzésére a Seattle-beli peering Location helyen:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

A válasz az alábbi példához hasonlóan néz ki:
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>Örökölt társak konvertálása
Az alábbi parancs használatával konvertálhatja a régi Exchange-társat az Azure-erőforrásra:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Vegye figyelembe, hogy az örökölt peering Azure-erőforrásra konvertálásakor a módosítások nem támogatottak &nbsp;

Az alábbiakban egy példa választ talál a végpontok közötti kiépítés sikeres befejezésére:

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával érheti el:

```powershell
Get-Help Get-AzPeering -detailed
```
További információért látogasson el az internetes kereséssel kapcsolatos [Gyakori kérdések](faqs.md) oldalra.

## <a name="next-steps"></a>Következő lépések

* [Exchange-társ létrehozása vagy módosítása a PowerShell-lel](howto-exchange-powershell.md)
