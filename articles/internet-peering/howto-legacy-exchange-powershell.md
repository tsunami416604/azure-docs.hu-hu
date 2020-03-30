---
title: Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a PowerShell használatával
titleSuffix: Azure
description: Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775393"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a PowerShell használatával

Ez a cikk bemutatja, hogyan konvertálhatja a meglévő örökölt Exchange-társviszony-létesítést Az Azure-erőforrás PowerShell-parancsmagok használatával.

Ha szeretné, ezt az útmutatót a portál segítségével is [elvégezheti.](howto-legacy-exchange-portal.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át [az Előfeltételek](prerequisites.md) és [az Exchange-társviszony-létesítési forgatókönyvet.](walkthrough-exchange-all.md)

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Örökölt Exchange-társviszony konvertálása Azure-erőforrássá

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be Azure-fiókjába, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Örökölt Exchange-társviszony-létesítés a konvertáláshoz
Az alábbiakban a példa, hogy örökölt Exchange társviszony-létesítés seattle-i társviszony-létesítési hely:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

A válasz a következő példához hasonlóan néz ki:
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

### <a name="convert-legacy-peering"></a>Örökölt társviszony-létesítés konvertálása
Az alábbiakban a parancs segítségével konvertálhatja az örökölt Exchange-társviszony-létesítést az Azure-erőforrássá:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Vegye figyelembe, hogy az örökölt társviszony-létesítés azure-erőforrássá történő konvertálásakor a módosítások nem támogatottak&nbsp;

Az alábbiakban egy példa választ, ha a végpontok között kiépítése sikeresen befejeződött:

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
Az összes paraméter részletes leírását a következő parancs futtatásával kaphatja meg:

```powershell
Get-Help Get-AzPeering -detailed
```
További információkért látogasson el [az internetes társviszony-létesítési GYIK-be](faqs.md)

## <a name="next-steps"></a>További lépések

* [Exchange-társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával](howto-exchange-powershell.md)
