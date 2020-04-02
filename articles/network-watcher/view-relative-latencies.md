---
title: Az Azure-régiók relatív késéseinek megtekintése adott helyekről
description: Ismerje meg, hogyan tekintheti meg az internetszolgáltatók relatív késéseit az Azure-régiókba adott helyekről.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 39f81731f20566d1a39f3f0931ff52c4e8b43ec0
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521382"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Adott helyek és Azure-régiók közötti relatív késés megtekintése

> [!WARNING]
> Ez a funkció jelenleg előzetes verzióban érhető el, és még mindig tesztelik a stabilitást.

Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure [Network Watcher](network-watcher-monitoring-overview.md) szolgáltatást annak eldöntéséhez, hogy milyen Azure-régióban telepítse az alkalmazást vagy szolgáltatást a felhasználó demográfiai adatai alapján. Emellett használhatja a szolgáltatók Azure-hoz fűződő kapcsolatainak kiértékelésére.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Hálózati figyelő létrehozása

Ha már rendelkezik egy hálózati figyelővel legalább egy [Azure-régióban,](https://azure.microsoft.com/regions)kihagyhatja a szakaszokban szereplő feladatokat. Hozzon létre egy erőforráscsoportot a hálózatfigyelő számára. Ebben a példában az erőforráscsoport az USA keleti régiójában jön létre, de az erőforráscsoportot bármely Azure-régióban létrehozhatja.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Hozzon létre egy hálózati figyelőt. Legalább egy Azure-régióban létre kell hoznia egy hálózati figyelőt. Ebben a példában egy hálózati figyelő jön létre az USA keleti régiójában az Azure régióban.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Relatív hálózati késések összehasonlítása egyetlen Azure-régióval egy adott helyről

Kiértékelheti a szolgáltatókat, vagy elháríthatja a problémát , például a "a hely lassú" problémát jelző problémát egy adott helyről az azure-régióba, ahol egy szolgáltatás telepítve van. A következő parancs például az Egyesült Államokban Washington állam és az USA nyugati régiója 2017.

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Az előző parancsban megadott területnek nem kell megegyeznie a hálózati figyelő beolvasásakor megadott területével. Az előző parancs egyszerűen meg kell adnia egy meglévő hálózati figyelőt. A hálózat figyelője bármely régióban lehet. Ha a és `-Country` `-State`a értékét adja meg, azoknak érvényesnek kell lenniük. Az értékek ben a kis- és nagybetűk et nem lehet figyelembe. Az adatok korlátozott számú országra/régióra, államra és városra vonatkozóan érhetők el. Futtassa a parancsokat [a Rendelkezésre álló országok/régiók, államok, városok és szolgáltatók nézetében](#view-available) az előző paranccsal használható országok/régiók, városok és állapotok listájának megtekintéséhez. 

> [!WARNING]
> Meg kell adnia egy dátumot `-StartTime` az `-EndTime`elmúlt 30 napban a és a területen. Ha megad egy korábbi dátumot, a rendszer nem ad vissza adatokat.

Az előző parancs kimenete a következő:

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

A visszaadott kimenetben a **Score** értéke a régiók és szolgáltatók relatív késése. Az 1-es pontszám a legrosszabb (legmagasabb) késés, míg a 100 a legalacsonyabb késés. A relatív késések átlagolója a nap. Az előző példában, bár egyértelmű, hogy a késések voltak azonosak mindkét nap, és hogy van egy kis különbség a késés a két szolgáltató, az is egyértelmű, hogy a késések mindkét szolgáltató alacsony az 1-100 skálán. Bár ez várható, mivel az Egyesült Államokwashington állam fizikailag közel van az USA 2 nyugat-azure-régióhoz, néha az eredmények nem a várt módon. Minél nagyobb a megadott dátumtartomány, annál többet átlagolhat az idő múlásával.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Relatív hálózati késések összehasonlítása az Azure-régiók között egy adott helyről

Ha ahelyett, hogy egy adott hely és egy adott Azure-régió között relatív késéseket ad meg, `-Location`meg szeretné határozni az összes Azure-régió relatív késéseit egy adott fizikai helyről, ezt is megteheti. A következő parancs például segít kiértékelni, hogy milyen azure-régióban telepítsen egy szolgáltatást, ha az elsődleges felhasználók A Comcast-felhasználók Washington államban találhatók:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Ellentétben akkor, ha egyetlen helyet ad meg, ha nem ad meg helyet, vagy több helyet ad meg, például "USA nyugati része", a parancs futtatásakor meg kell adnia egy internetszolgáltatót. 

## <a name="view-available-countriesregions-states-cities-and-providers"></a><a name="view-available"></a>Az elérhető országok/régiók, államok, városok és szolgáltatók megtekintése

Az adatok meghatározott internetszolgáltatók, országok/régiók, államok és városok számára érhetők el. Az összes elérhető internetszolgáltató, ország/régió, állam és város listájának megtekintéséhez adja meg a következő parancsot:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Az adatok csak az előző parancs által visszaküldött országokra/régiókra, állapotokra és városokra vonatkozóan érhetők el. Az előző parancs megköveteli, hogy adjon meg egy meglévő hálózati figyelőt. A példa a *NetworkWatcherRG*nevű *erőforráscsoportban* adta meg a NetworkWatcher_eastus a hálózati figyelőt, de bármely meglévő hálózati figyelőt megadhat. Ha nem rendelkezik meglévő hálózati figyelővel, hozzon létre egyet a [Hálózati figyelő létrehozása](#create-a-network-watcher)című területen végzett feladatok elvégzésével. 

Az előző parancs futtatása után szükség esetén az **Ország,** **az Állam**és a **Város**érvényes értékeinek megadásával szűrheti a visszaadott kimenetet.  Ha például meg szeretné tekinteni a Washington állambeli Seattle-ben(Washington) elérhető internetszolgáltatók listáját, írja be a következő parancsot:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> **Az Ország** megadott értéknek kis- és nagybetűsnek kell lennie. **Az Állam** és **város** megadott értékeknek kisbetűseknek kell lenniük. Az értékeket az **Ország**, **Állam**és **Város**érték nélküli parancs futtatása után visszaadott kimenetben kell felsorolni. Ha a helytelen kis- és nagybetűket adja meg, vagy olyan értéket ad meg **az Ország,** **az Állam**vagy a **Város** értékhez, amely nem szerepel a parancs futtatása után visszaadott kimenetben, és ezeknek a tulajdonságoknak nincs értéke, a visszaadott kimenet üres lesz.
