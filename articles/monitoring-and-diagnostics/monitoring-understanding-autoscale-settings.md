---
title: Az Azure a figyelő automatikus skálázás beállításainak ismertetése
description: Részletes információkat a automatikus skálázási beállításokat, és hogyan működnek. Virtuális gépek, a Felhőszolgáltatásokat, a webalkalmazások vonatkozik
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 982bc43fd86a808da07833d77bde17e17789b2d6
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264996"
---
# <a name="understand-autoscale-settings"></a>Ismerkedés az automatikus méretezési beállításokkal
Automatikus skálázási beállításokat biztosíthatja, hogy rendelkezik-e a megfelelő mennyiségű erőforrást fut az alkalmazás változó terhelés kezelésére. Konfigurálhatja az automatikus skálázási beállításokat ütemezett dátum és idő alapján, amelyek jelzik a load vagy a teljesítmény metrikák vagy kiváltott aktiválására. Ez a cikk részletes tekintse meg az automatikus skálázási beállítás leírása vesz igénybe. A cikk a séma- és a beállítás tulajdonságait kezdődik, és majd végigvezeti a másik profil típusokat, amelyek konfigurálhatók. Végül a cikk azt ismerteti, hogyan értékeli ki az automatikus skálázási funkciót, az Azure-ban a melyik profil egy adott időpontban végrehajtásához.

## <a name="autoscale-setting-schema"></a>Automatikus skálázási beállítás séma
Az automatikus skálázási beállítás séma mutatja be, a következő automatikus skálázási beállítás szolgál. Fontos megjegyezni, hogy rendelkezik-e az automatikus skálázási beállításhoz:
- Egy profil. 
- Ebben a profilban két mérőszám szabályok: egy bővített és egy, a skála.
  - A kibővített szabály akkor lesz kiváltva, ha a virtuális gép méretezési átlagos CPU metrika 85 százaléknál nagyobb az elmúlt 10 perc.
  - A skála a szabály akkor lesz kiváltva, ha a virtuális gép méretezési átlaga kisebb, mint 60 % az elmúlt percben.

> [!NOTE]
> Egy beállítás több profil rendelkezhet. További tudnivalókért tekintse meg a [profilok](#autoscale-profiles) szakasz. A profil több kibővített szabályokat és megadott skálázási szabályok is. Hogyan kiértékelésük, olvassa el a [értékelési](#autoscale-evaluation) szakasz.

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| Section | Elem neve | Leírás |
| --- | --- | --- |
| Beállítás | ID (Azonosító) | Az automatikus skálázási beállítás erőforrás-azonosító. Automatikus skálázási beállításokat és az Azure Resource Manager szerinti erőforrás. |
| Beállítás | név | Az automatikus skálázási beállítás neve. |
| Beállítás | location | Az automatikus skálázási beállítás helye. Ezen a helyen méretezve, hogy az erőforrás helye eltérő lehet. |
| properties | targetResourceUri | Az erőforrás-azonosító az erőforrás méretezése folyamatban. Csak akkor is erőforrásonként egy automatikus skálázási beállítás. |
| properties | Profilok | Az automatikus skálázási beállítás egy vagy több profilok tevődik össze. Minden egyes futásakor az automatikus skálázás motor egy profil végrehajtja. |
| profil | név | A profil nevét. Lehetősége van a neve, amely segít a profil azonosításához. |
| profil | Capacity.maximum | Az engedélyezett maximális kapacitását. Ez biztosítja, hogy automatikus skálázás, ehhez a profilhoz végrehajtásakor nem méretezhető fent ezt a számot az erőforrás. |
| profil | Capacity.minimum | Az engedélyezett minimális kapacitást. Ez biztosítja, hogy az automatikus skálázás, ehhez a profilhoz végrehajtásakor nem méretezhető e szám alá az erőforrás. |
| profil | Capacity.default | Ha az erőforrás metrika (esetünkben a "vmss1" CPU) olvasása, és a jelenlegi kapacitásnál nem éri el az alapértelmezett, automatikus skálázási ki az alapértelmezett arányosan. Ez az erőforráscsoport rendelkezésre állásának biztosításához. Ha a jelenlegi kapacitásnál már nagyobb, mint az alapértelmezett kapacitásértéket, az automatikus skálázás nem méretezhető a. |
| profil | szabályok | Automatikus skálázás automatikusan méretezi a maximális és minimális kapacitás, a szabályok segítségével a profil között. A profil több szabály is lehet. Általában két szabály van: egy, hogy ki és mikor érdemes méretezni a meghatározására. |
| szabály | metricTrigger | A metrika a szabály állapota határozza meg. |
| metricTrigger | metricName | A mérték neve. |
| metricTrigger |  metricResourceUri | Az erőforrás-azonosító bocsát ki a metrika erőforrás. A legtöbb esetben ez megegyezik az erőforrás méretezése folyamatban. Néhány esetben ez eltérő lehet. Például méretezhető egy virtuálisgép-méretezési csoport egy tárolási várólistában lévő üzenetek száma alapján. |
| metricTrigger | Időkeretben vannak | A metrika mintavételi időtartamot. Például **időkeretben vannak = "PT1M"** azt jelenti, hogy a metrikák kell összesíteni minden 1 perc, a statisztika elemben megadott összesítési módszer használatával. |
| metricTrigger | statisztika | Az összesítési módszer a időkeretben vannak időszakon belül. Például **statisztika = "Átlagos"** és **időkeretben vannak = "PT1M"** azt jelenti, hogy a metrikák kell összesítse minden 1 perc, átlaga. Ez a tulajdonság szabja meg, hogyan az a mérték mintát venni. |
| metricTrigger | Az időtartomány értékének | A metrikák vissza keres időtartam. Például **az időtartomány értékének = "PT10M"** azt jelenti, hogy minden alkalommal, amikor az automatikus skálázás fut, az lekérdezi metrikák az elmúlt 10 perc. Az időszak lehetővé teszi, hogy a mérni kívánt normalizálható, és elkerülhetők a reagál a átmeneti teljesítményt. |
| metricTrigger | timeAggregation | Az összesítési módszer használatával a mintában szereplő metrikák összesítése. Például **TimeAggregation = "Átlagos"** átlaga a mintában szereplő metrikák kell összesíteni. Az előző esetben a tíz 1 perces mintát venni, és átlagos őket. |
| szabály | scaleAction | A szabály metricTrigger kezdeményezése esetén végrehajtandó műveletet. |
| scaleAction | irány | "Növelje" kiterjesztése, vagy "Csökkentéséhez" skála.|
| scaleAction | érték | Mennyi növelheti vagy csökkentheti a kapacitás az erőforrás. |
| scaleAction | cooldown | A skálázási művelet előtt skálázás megismételni után várakozási idő mennyiségét. Például ha **cooldown = "PT10M"**, automatikus skálázás nem kísérli meg újra a méretezési egy másik 10 perc. A cooldown, a mérni kívánt után hozzáadását és eltávolítását példányok stabilizálását engedélyezéséhez. |

## <a name="autoscale-profiles"></a>Automatikus skálázási profilok

Az automatikus skálázási profilok három típusa van:

- **Rendszeres profil:** a leggyakrabban használt profil. Ha nincs szüksége az erőforrás, a hét napját, vagy egy adott napon alapuló méretezési, egy rendszeres profilt is használhatja. Ez a profil majd metrika szabályok határozzák meg, mikor érdemes méretezni ki és mikor érdemes méretezni a konfigurálható. Csak akkor kell definiált egy rendszeres profil.

    Az ebben a cikkben használt példa-profilt egy rendszeres profil példája. Vegye figyelembe, hogy az is beállítható méretezési profil az erőforrás statikus példányszámot.

- **Dátum profilt:** speciális esetekben van ehhez a profilhoz. Például tegyük fel, a 2017 December 26 (csendes-óceáni TÉLI) közeledik fontos eseményt rendelkezik. Azt szeretné, hogy a minimális és maximális kapacitás az erőforrás kell lennie az adott nap, de továbbra is a metrikák a skála. Ebben az esetben egy rögzített dátum profilt kell hozzá profilok az beállításainak listája. A profil használatára van konfigurálva, csak az esemény napon futtatásához. Bármely más napi automatikus skálázás használ a rendszeres profilt.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
- **Ismétlődés profil:** ilyen típusú profilok segítségével győződjön meg arról, hogy a hét egy bizonyos napon nem mindig használja ezt a profilt. Ismétlődés profilok csak a kezdő időpont van. A következő ismétlődési profil addig futnak, vagy rögzített dátum profil indításra van beállítva. Az automatikus skálázási beállítás csak egy ismétlődési profillal, hogy a profil futtatja, akkor is, ha egy beállítás a meghatározott reguláris profil van. A következő két példa azt mutatják be, hogyan használja fel ezt a profilt:

    **1. példa: Létrehozását és hétvégéket összehasonlítása**
    
    Tegyük fel, a hétvégekre, amelyet a maximális kapacitásnak kell lennie 4. Létrehozását mert nagyobb terhelést, várt szeretné a maximális kapacitás 10. Ebben az esetben a beállítás tartalmazná két ismétlődési profilok, a másik pedig a hétvégéket és a másik a létrehozását.
    A beállítást így néz ki:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    Az előző beállítás jeleníti meg, hogy minden egyes ismétlődési profil rendelkezik-e az ütemezés szerint. Az ütemezés határozza meg a profil indításakor fut. A profil leáll, amikor egy másik profil futtatásához.

    Például az előző beállítás "weekdayProfile" érték: 00:00 hétfőn elindításához. Ez azt jelenti, hogy ez a profil elindul hétfőn: 00:00. 12:00 órakor, ha a "weekendProfile" ütemezve van arra, hogy elindítsa szombat addig folytatja.

    **2. példa: munkaidő**
    
    Tegyük fel kíván használni egy mérték küszöbérték munkaidőben (9:00-kor délután 5:00 óra) és egy másik a többi időszakban. A beállítás néz ki:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    Az előző beállítás látható, hogy a "businessHoursProfile" kezdődik, hétfőn 9:00 órakor fut, és Délután 5 továbbra is. Ha ez "nonBusinessHoursProfile" elindul. A "nonBusinessHoursProfile" csak 9:00 óra kedd fut, és majd a "businessHoursProfile" veszi át újra. Ez ismétlődik amíg péntek, 5:00 du. Ezen a ponton "nonBusinessHoursProfile" fut a hétfő 9:00 órakor.
    
> [!Note]
> Az automatikus skálázás felhasználói felület az Azure portálon kikényszeríti a befejező időpontok ismétlődési profilokhoz, és megkezdődik az automatikus skálázási beállítás alapértelmezett profilként Between ismétlődési profilok futtató.
    
## <a name="autoscale-evaluation"></a>Automatikus skálázás kiértékelése
Fényében, hogy automatikus skálázási beállításokat lehet több profilt, és az egyes profilok rendelkezhet több metrika szabály, fontos megérteni, hogyan történik az automatikus skálázási beállítás. Minden alkalommal, amikor az automatikus skálázási feladat fut, az megkezdi a profilt, amely megfelelő kiválasztásával. Automatikus skálázás Ezután kiértékeli a minimális és maximális értékek és a profil metrika szabályok, és úgy dönt, hogy a skálázási művelet szükséges.

### <a name="which-profile-will-autoscale-pick"></a>Melyik profil átveszi automatikus skálázás?

Automatikus skálázási a profil kiválasztásához a következő folyamat használja:
1. Először keresi a rögzített dátum profilt, amely most futtatásra van beállítva. Ha nincs, az automatikus skálázás futtatja. Ha futtatásához kellene több rögzített dátum portprofilokat, automatikus skálázás kiválasztja az elsőt.
2. Ha nem rögzített dátum profil, automatikus skálázás ismétlődési profilok megvizsgálja. Ha egy ismétlődési profil, futtatja azt.
3. Nincsenek rögzített dátum vagy ismétlődési profilok, ha az automatikus skálázási futtatja a rendszeres profil.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Hogyan automatikus skálázás több szabály értékelése?

Automatikus skálázás futtassa mely profil határozza meg, miután a profil a kibővített szabályok értékel (ezek a szabályok **iránya = "Növelése"**).

Ha egy vagy több kibővített szabály által kiváltott, automatikus skálázási kiszámítja új kapacitása határozza meg a **scaleAction** egyes ezeket a szabályokat. Majd méretezés ki ezeket a kapacitás, a szolgáltatás rendelkezésre állásának biztosításához legfeljebb.

Például tételezzük fel hiba van a virtuálisgép-méretezési 10 aktuális kapacitása. Két kibővített szabályok vonatkoznak: egy, a kapacitás 10 %-kal növekszik, és egy, a kapacitás 3 száma nő. Az első szabály 11 új kapacitású eredményezne, és a második szabály 13 kapacitású eredményezne. Ahhoz, hogy a szolgáltatás rendelkezésre állása, automatikus skálázás úgy dönt, a művelet a maximális kapacitás, ezért a második szabály van kiválasztva.

Nincs kibővített szabály által kiváltott, ha automatikus skálázás kiértékeli az összes méretezési a szabály (a szabályok **iránya = "Csökkentése"**). Automatikus skálázási a méretezési művelet csak akkor, ha összes méretezési a szabály által kiváltott.

Automatikus skálázás kiszámítja új kapacitása határozza meg a **scaleAction** egyes ezeket a szabályokat. Ezután azt úgy dönt, a skálázási művelet, amely a maximális számát ezen kapacitások szolgáltatás rendelkezésre állásának biztosításához eredményez.

Például tételezzük fel hiba van a virtuálisgép-méretezési 10 aktuális kapacitása. Két skálázási szabályok vonatkoznak: egy, a kapacitás 50 %-kal csökken, és egy, amely csökkenti a kapacitást 3 száma szerint. Az első szabály 5 új kapacitású eredményezne, és a második szabály 7 kapacitású eredményezne. Ahhoz, hogy a szolgáltatás rendelkezésre állása, automatikus skálázás úgy dönt, a művelet a maximális kapacitás, ezért a második szabály van kiválasztva.

## <a name="next-steps"></a>További lépések
További információ az automatikus skálázás hivatkozással a következőhöz:

* [Az automatikus méretezés áttekintése](monitoring-overview-autoscale.md)
* [Az Azure a figyelő automatikus skálázás közös metrikák](insights-autoscale-common-metrics.md)
* [Ajánlott eljárások az Azure a figyelő automatikus skálázás](insights-autoscale-best-practices.md)
* [A automatikus skálázási műveletek is elküldhetik e-mailek és a webhook riasztási értesítésekre](insights-autoscale-to-webhook-email.md)
* [Automatikus skálázás REST API-n](https://msdn.microsoft.com/library/dn931953.aspx)
