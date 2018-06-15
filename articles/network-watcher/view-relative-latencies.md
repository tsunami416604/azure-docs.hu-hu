---
title: Azure-régiók relatív késések nézet a megadott helyeken lévő |} Microsoft Docs
description: Útmutató megtekintéséhez relatív késések közötti internetszolgáltató az Azure-régiók adott helyről.
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
ms.openlocfilehash: a6c2ffa619eeff8b455df8a8b2157525af12c640
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
ms.locfileid: "27600956"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Nézet relatív késése Azure-régiók adott helyekről

Ebből az oktatóanyagból megtudhatja, hogyan használható az Azure [hálózati figyelőt](network-watcher-monitoring-overview.md) szolgáltatás pedig segít eldönteni, hogy melyik Azure-régió, az alkalmazás telepítéséhez, vagy a szolgáltatás, a felhasználó demográfiai alapján. Továbbá használhatja azt mérik szolgáltatók kapcsolatok az Azure-bA.  
        
## <a name="create-a-network-watcher"></a>Hozzon létre egy hálózati figyelőt

Ha már rendelkezik egy hálózati figyelőt legalább egy Azure-ban [régió](https://azure.microsoft.com/regions), kihagyhatja a feladatok ebben a szakaszban. Hozzon létre egy erőforráscsoportot a hálózati figyelőt. Ebben a példában az erőforráscsoport létrejön az USA keleti régiójában, de az erőforráscsoport bármely Azure régióban létrehozhat is.

```powershell
New-AzureRmResourceGroup -Name NetworkWatcherRG -Location eastus
```

Hozzon létre egy hálózati figyelőt. Rendelkeznie kell egy legalább egy Azure-régióban létrehozott hálózati figyelőt. Ebben a példában egy hálózati figyelőt jön létre a keleti Velünk Azure azon régióját.

```powershell
New-AzureRmNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Hasonlítsa össze relatív hálózati késések fordulnak elő egy egyetlen Azure-régió, egy adott helyen

Értékelje ki a szolgáltatók és a hibakeresést jelentéskészítő problémát, például a "a hely lassú volt," a felhasználó egy adott helyről az azure-régió, egy szolgáltatás telepítési helyét. Például a következő parancs Washington állam az Amerikai Egyesült Államokban és a nyugati Velünk 2 Azure azon régióját közötti December 13 – 15, 2017 között az átlagos relatív Internet service provider késések adja vissza:

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
> A régió, adja meg, ha az előző parancsban nem kell azonos hálózati figyelőt beolvasásakor megadott területet. Az előző parancsot egyszerűen igényel, meg kell adnia egy meglévő hálózati figyelőt. A hálózati figyelőt lehet bármely régióban. Ha értékeket ad meg `-Country` és `-State`, érvényesnek kell lennie. Az értékek: kis-és nagybetűket. Adatok országok, állapotok és városokat korlátozott számú érhető el. Futtassa a parancsokat [megtekintheti a rendelkezésre álló országok, állapotok, városokat és szolgáltatók](#view-available) elérhető országok, városokat és-állapotot, és az előző paranccsal listájának megtekintéséhez. 

> [!WARNING]
> 2017. November 14. a utáni dátumot adjon meg `-StartTime` és `-EndTime`. Adatok megadása előtt 2017. November 14. dátumot adja vissza. 

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

A visszaadott kimenet értéke **pontszám** régiókban és szolgáltatók a relatív késés van. Egy 1 érték a legrosszabb (legmagasabb) késés, mivel a 100 a legkisebb mértékű késleltetést. A relatív késések program átlagolja napjára. Az előző példában azt rendelkezik egyértelmű, hogy közben a késések azonos volt mindkét nap, és, hogy egy kis különbség a két szolgáltató késését között, akkor rendelkezik is törlése, amely a mindkét szolgáltatók a 1-100 skálán alacsony. Amíg ez várható, mivel az Amerikai Egyesült Államokban Washington állam fizikailag közel a nyugati Velünk 2 Azure azon régióját, néha eredmények nem várt módon. Minél nagyobb a dátumtartomány adja meg, annál átlagos a késés adott idő alatt.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Egy adott helyről Azure-régiók közötti relatív hálózati késések összehasonlítása

Ha egy adott helyen, és egy adott Azure-régiót használatával közötti relatív késések megadása helyett `-Location`, szeretné meghatározni, hogy a relatív késések fordulnak elő az összes Azure-régiók egy adott fizikai helyen, azt is megteheti. Például az alábbi parancs segítségével kiértékelheti a szolgáltatás telepítése, ha a elsődleges felhasználók is található, Washington államban Comcast felhasználók milyen az azure-régió:

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
Eltérően Amikor megad egy helyet, ha nem adja meg egy helyet, vagy adjon meg több helyen, például a "Nyugati US2", "US nyugati", meg kell adnia egy internetszolgáltató a parancs futtatásakor. 

## <a name="view-available"></a>Megtekintheti a rendelkezésre álló országok, állapotok, városokat és szolgáltatók

Adatok bizonyos internetszolgáltatók, országok, állapotok és városokat érhető el. Az összes rendelkezésre álló Internet-szolgáltatók listájának megtekintéséhez, országok, állapotok és várost, megtekintheti az adatokat, írja be a következő parancsot:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Adatok csak érhető el a országok, állapotok és az előző parancs által visszaadott város tartozik. Az előző parancs megköveteli, hogy adjon meg egy meglévő hálózati figyelőt. A megadott példa a *NetworkWatcher_eastus* hálózati figyelőt nevű erőforráscsoportban *NetworkWatcherRG*, de megadhat bármilyen meglévő hálózati figyelőt. Ha még nem rendelkezik egy meglévő hálózati figyelőt, hozzon létre egyet a feladatok végrehajtásával [hozzon létre egy hálózati figyelőt](#create-a-network-watcher). 

Az előző parancs futtatása után a kimeneti visszaadott értékek megadásával szűrheti **ország**, **állapot**, és **Város**, ha szükséges.  Az Amerikai Egyesült Államokban elérhető budapesti, Washington, az internetszolgáltatók listájának megtekintéséhez írja be például a következő parancsot:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> A megadott **ország** kis- és nagybetűk kell lennie. A megadott értékek **állapot** és **Város** kisbetűnek kell lennie. Az értékek szerepelnie kell a kimenet nem értékekkel a parancs futtatása után visszaadott **ország**, **állapot**, és **Város**. Ha helytelen megadásához, vagy adjon meg egy értéket **ország**, **állapot**, vagy **Város** a rendszer a parancs futtatása értékek nélküli ezek után visszaadott kimenet Tulajdonságok, a visszaadott eredménye üres.
