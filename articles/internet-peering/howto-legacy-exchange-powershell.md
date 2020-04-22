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
ms.openlocfilehash: eedf87548d62e05d4940911ed3dcd821077acb27
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686787"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a PowerShell használatával

Ez a cikk bemutatja, hogyan konvertálhatja a meglévő örökölt Exchange-társviszony-létesítés egy Azure-erőforrás powershell-parancsmagok használatával.

Ha szeretné, ezt az útmutatót az Azure Portal használatával is [elvégezheti.](howto-legacy-exchange-portal.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és az [Exchange-társviszony-létesítési forgatókönyvet.](walkthrough-exchange-all.md)

### <a name="work-with-azure-powershell"></a>Az Azure PowerShell munkája
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be Azure-fiókjába, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Örökölt Exchange-társviszony-létesítés beszerezni a konvertálást
Ez a példa bemutatja, hogyan juthat el a régi Exchange-társviszony-létesítéshez a seattle-i társviszony-létesítési helyen:

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
Ezzel a paranccsal konvertálhatja az örökölt Exchange-társviszony-létesítést egy Azure-erőforrássá:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Ha az örökölt társviszony-létesítést Azure-erőforrássá alakítja, a módosítások nem támogatottak.
&nbsp;

Ez a példaválasz azt mutatja, ha a végpontok között létesítés sikeresen befejeződött:

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
További információt az [Internetes társviszony-létesítés – gyakori kérdések című témakörben talál.](faqs.md)

## <a name="next-steps"></a>További lépések

* [Exchange-társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával](howto-exchange-powershell.md)
