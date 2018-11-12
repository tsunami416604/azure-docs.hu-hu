---
title: Relatív késések megtekintése az Azure-régiókhoz való adott helyekről való |} A Microsoft Docs
description: Útmutató megtekintéséhez relatív késések internetszolgáltatók különböző Azure-régiókhoz való adott helyekről.
services: network-watcher
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 6ac37c3a53b0cc71bdab85fb86e0e85d998867aa
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300608"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Nézet relatív késés-Azure-régiókhoz való adott helyekről

Ebből az oktatóanyagból megtudhatja, hogyan használható az Azure [Network Watcher](network-watcher-monitoring-overview.md) szolgáltatás segítségével eldöntheti, hogy melyik Azure-régióban helyezze üzembe az alkalmazást vagy szolgáltatást, a felhasználó demográfiai alapján. Továbbá használhatja azt mérik a szolgáltatók kapcsolatok az Azure-bA.  
        
## <a name="create-a-network-watcher"></a>A network watcher létrehozásához

Ha már rendelkezik egy network watcher legalább egy Azure-ban [régió](https://azure.microsoft.com/regions), kihagyhatja a jelen szakaszban leírt műveletekhez. Hozzon létre egy erőforráscsoportot a hálózati figyelő. Ebben a példában az erőforráscsoport az USA keleti régiójában jön létre, de bármelyik Azure-régióban hozhat létre az erőforráscsoportot.

```powershell
New-AzureRmResourceGroup -Name NetworkWatcherRG -Location eastus
```

A network watcher létrehozásához. Rendelkeznie kell legalább egy Azure-régióban létrehozott network watchert. Ebben a példában a network watchert a keleti velünk a Kapcsolatot Azure-régióban jön létre.

```powershell
New-AzureRmNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Egyetlen Azure-régióba relatív hálózati késések összehasonlítani egy adott helyen

Szolgáltatók kiértékelése és a egy felhasználó problémát jelent, mint például az "a hely nem volt," hibakeresést egy adott helyről, ahol a szolgáltatás üzembe helyezése az azure-régióban. Például a következő parancsot a Washington állam az Egyesült Államokban és a 13. 15., 2017 December között West US 2 Azure-régió között az átlagos relatív internetes szolgáltatás szolgáltató késések adja vissza:

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> A régiót, adja meg, ha az előző parancs nem kell lennie, ugyanaz, mint a lekért a network watcher megadott régióban. Az előző parancs egyszerűen szükséges egy meglévő a network watcher adjon meg. A network watcher bármelyik régióban is lehetnek. Ha értékeket ad meg `-Country` és `-State`, érvényesnek kell lennie. Az értékek: kis-és nagybetűket. Adatok országok, államok és városok korlátozott számú érhető el. Futtassa a parancsokat [elérhető országok, államok, város és szolgáltatók megtekintése](#view-available) országban érhető el, város és az előző parancs használata Államokban listájának megtekintéséhez. 

> [!WARNING]
> Adjon meg egy dátumot, az elmúlt 30 napon belül `-StartTime` és `-EndTime`. Meghatároz egy korábbi dátumot a visszaadott adatok nem eredményez.

Az előző parancs kimenete a következőképpen:

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

A visszaadott értéke kimenetben **pontszám** régiók és szolgáltatók közötti relatív késési van. 1 pontszámot a legrosszabb (legmagasabb) késés, mivel a 100 a legkisebb késéssel. A relatív késési átlagolja az adott napra vonatkozóan. Az előző példában azt rendelkezik egyértelmű, hogy míg az késleltetések azonos mindkét napon és, hogy egy kisebb különbség a két szolgáltató késését között, akkor rendelkezik is törölje, amely az 1-100 skálázási alacsonyak legyenek-e az késleltetések mindkét szolgáltatók számára. Bár ez várható, mivel a Washington állam az Egyesült Államokban fizikailag közel a West US 2 Azure azon régióját, néha eredmények nem várt módon. Minél nagyobb a dátumtartomány adja meg, annál is átlagos késés idővel.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Egy adott helyről Azure-régiók közötti relatív hálózati késések összehasonlítása

Ha egy adott hely és a egy adott Azure-régió az között a relatív késési megadása helyett `-Location`, szeretne meghatározni, hogy a relatív késési az összes Azure-régióban egy adott fizikai helyen, akkor ezt is megteheti. Például a következő parancs segítségével kiértékelheti milyen azure-régiót, a szolgáltatás üzembe helyezése, ha az elsődleges felhasználók Washington államban Comcast számára:

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

>[!NOTE]
Ellentétben egyetlen helyen megadásakor, ha nem adja meg egy helyet, vagy adja meg a több helyen, például az "USA nyugati 2 régiója", "West US", meg kell adnia egy internetszolgáltató parancs futtatásakor. 

## <a name="view-available"></a>Rendelkezésre álló országok, államok, város és szolgáltatók megtekintése

Adatok bizonyos internetszolgáltatók, országok, államok és városok érhető el. Az összes elérhető internetszolgáltatók listájának megtekintéséhez országok, államok és városok, megtekintheti az adatait, adja meg a következő parancsot:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Adatok csak az országok, államok és városok az előző parancs által visszaadott számára érhető el. Az előző parancs megköveteli, hogy adjon meg egy meglévő hálózati figyelő. A megadott példa a *NetworkWatcher_eastus* network watcher nevű erőforráscsoportot a *NetworkWatcherRG*, de megadhat minden olyan meglévő hálózati figyelő. Ha egy meglévő a network watcher nincs, hozzon létre egyet a feladatok végrehajtásával [egy network watcher létrehozásához](#create-a-network-watcher). 

Az előző parancs futtatása után a kimenet adott vissza érvényes értékeinek megadásával szűrheti **ország**, **állapot**, és **Város**, ha szükséges.  Írja be például a Seattle, Washington, elérhető internetszolgáltatók listájának megtekintéséhez az Egyesült Államokban, a következő parancsot:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> A megadott **ország** kell lennie a kis- és nagybetűk. A megadott értékek **állapot** és **Város** kisbetűnek kell lennie. Az értékek szerepelnie kell a kimenetet ad vissza, miután a parancs nem a **ország**, **állapot**, és **Város**. Ha a helytelen esetben adja meg, vagy adjon meg egy értéket **ország**, **állapot**, vagy **Város** , amely nem szerepel a következő parancs futtatásával értékkel nem rendelkező ezek után kapott kimenetre Tulajdonságok, a visszaadott kimenetet je prázdná.
