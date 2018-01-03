---
title: "Automatikus skálázás beállításainak ismertetése |} Microsoft Docs"
description: "Részletes információkat a automatikus skálázási beállításokat, és hogyan működnek."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ce2930aa-fc41-4b81-b0cb-e7ea922467e1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: ancav
ms.openlocfilehash: cff2be1818417a19f36da08d8c2eaa227bb945ec
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="understand-autoscale-settings"></a>Automatikus skálázás beállításai
Automatikus skálázás beállításai lehetővé teszik, hogy ellenőrizze, hogy a megfelelő mennyiségű erőforrást fut az alkalmazás változó terhelés kezelésére. Automatikus skálázási beállításokat aktiválására, amely jelzi a load vagy a teljesítmény, vagy egy ütemezett dátummal és időponttal eseményindítót mérőszámok alapján konfigurálhatja. Ez a cikk részletes tekintse meg az automatikus skálázási beállítás leírása vesz igénybe. A cikk kezdődik megismerni a séma- és a beállítás tulajdonságait, majd végigvezeti a másik profil típusokat, amelyek konfigurálhatók, és végül ismerteti, hogyan automatikus skálázás kiértékeli melyik profil egy adott időpontban végrehajtásához.

## <a name="autoscale-setting-schema"></a>Automatikus skálázási beállítás séma
Az automatikus skálázási beállítás séma mutatja be, a következő automatikus skálázási beállítás szolgál. Fontos megjegyezni, hogy rendelkezik-e az automatikus skálázási beállításhoz:
- Egy profil 
- Rendelkezik két mérőszám szabályok ebben a profilban; egy kibővített, egy, a skála.
- A kibővített szabály akkor lesz kiváltva, amikor a virtuális gép méretezési átlagos százalékos CPU metrika érték nagyobb, mint a 85 % az elmúlt 10 perc.
- A skála a szabály akkor lesz kiváltva, ha a virtuális gép méretezési átlaga kisebb, mint 60 % az elmúlt percben.

> [!NOTE]
> A beállítás is több profil áll, Ugrás a [profilok](#autoscale-profiles) további szakasz.
> Egy profilt is lehet több kibővített szabályokat és megadott, skálázási szabályok Ugrás a [értékelési szakasz](#autoscale-evaluation) hogyan kiértékelésük megjelenítéséhez

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
| properties | célerőforrás URI azonosítóját tartalmazó | Az erőforrás-azonosító az erőforrás méretezése folyamatban. Csak akkor is erőforrásonként egy automatikus skálázási beállítás. |
| properties | Profilok | Az automatikus skálázási beállítás egy vagy több profilok tevődik össze. Minden egyes futásakor az automatikus skálázás motor egy profil végrehajtja. |
| Profil | név | A profil nevét, kiválaszthatja a neve, amely segít a profil azonosításához. |
| Profil | Capacity.maximum | Az engedélyezett maximális kapacitását. Biztosítja, hogy automatikus skálázási profil végrehajtásakor nem méretezhető a fenti Ez a szám erőforrás. |
| Profil | Capacity.minimum | Az engedélyezett minimális kapacitást. Biztosítja, hogy automatikus skálázási profil végrehajtásakor nem méretezhető a erőforrás e szám alá. |
| Profil | Capacity.default | Ha az erőforrás metrika (esetünkben a "vmss1" cpu) olvasása, és a jelenlegi kapacitásnál nem éri el az alapértelmezett kapacitásértéket, majd az erőforrás rendelkezésre állásának biztosításához, automatikus skálázás méretezik ki az alapértelmezett. Ha a jelenlegi kapacitásnál már nagyobb, mint az alapértelmezett kapacitásértéket, automatikus skálázás fog nem skálázási be. |
| Profil | szabályok | Automatikus skálázás automatikusan méretezi a maximális és minimális kapacitás, a szabályok alkalmazásával a profil között. A profil több szabály is lehet. Az alapvető forgatókönyv, két szabályt, egyet, hogy a kibővített és egyéb, hogy a skála. |
| A szabály | metricTrigger | A metrika a szabály állapota határozza meg. |
| metricTrigger | metricName | A mérték neve. |
| metricTrigger |  metricResourceUri | Az erőforrás-azonosító bocsát ki a metrika erőforrás. A legtöbb esetben ez megegyezik az erőforrás méretezése folyamatban. Bizonyos esetekben eltérő lehet, például egy virtuálisgép-méretezési csoport egy tárolási várólistában lévő üzenetek száma alapján is méretezhető. |
| metricTrigger | Időkeretben vannak | A metrika mintavételi időtartamot. Például időkeretben vannak = "PT1M" azt jelenti, hogy legyen a metrikák összesítése minden 1 perces "statisztika." a megadott összesítő metódus |
| metricTrigger | statisztika | Az összesítési módszer a időkeretben vannak időszakon belül. Például statisztika = "Átlagos" és a időkeretben vannak "PT1M" azt jelenti, hogy a metrikák kell = 1 percenként összesítve átlaga. Ez a tulajdonság szabja meg, hogyan az a mérték mintát venni. |
| metricTrigger | Az időtartomány értékének | A metrikák vissza keres időtartam. Például az időtartomány értékének = "PT10M" azt jelenti, hogy minden alkalommal, amikor az automatikus skálázás fut, az lekérdezi metrikák az elmúlt 10 perc. Az időszak lehetővé teszi, hogy a mérni kívánt normalizálható, és elkerülhetők a átmeneti igényeiben jelentkező reagálnak. |
| metricTrigger | timeAggregation | Az összesítési módszer használatával a mintában szereplő metrikák összesítése. Például TimeAggregation = "Átlagos" kell összesíteni a mintában szereplő metrikák átlaga. A fenti esetben a tíz 1 perces mintát venni, és átlagos őket. |
| A szabály | scaleAction | A szabály metricTrigger kezdeményezése esetén végrehajtandó műveletet. |
| scaleAction | irány | "" Horizontális növeléséhez "Csökkentése" méretezési az|
| scaleAction | érték | Mennyi növeléséhez vagy csökkentéséhez az erőforrás kapacitása |
| scaleAction | cooldown | A skálázási művelet előtt skálázás megismételni után várakozási idő mennyiségét. Például ha cooldown = "PT10M", majd miután megtörtént a skálázási művelet, automatikus skálázás nem kísérli meg újra a méretezési egy másik 10 perc. A cooldown, a mérni kívánt után hozzáadását és eltávolítását példányok stabilizálását engedélyezéséhez. |

## <a name="autoscale-profiles"></a>Automatikus skálázási profilok

Az automatikus skálázási profilok három típusa van:

1. **Rendszeres profil:** leggyakrabban használt profil. Ha nincs szüksége a menübeállításoktól függően a hét napját, vagy egy adott napon erőforrás méretezése, majd csak szeretné az automatikus skálázási beállításban rendszeres profil beállítása. Ez a profil majd metrika szabályok határozzák meg, mikor érdemes kibővített és mikor kell a méretezési konfigurálható. Csak akkor kell definiált egy rendszeres profil.

    Az ebben a cikkben használt példa-profilt egy rendszeres profil példája. Nem célszerű is beállítható méretezési profil az erőforrás statikus példányszám tegye.

2. **Dátum profilt:** definiált rendszeres profillal tegyük fel, a 2017 December 26 (csendes-óceáni TÉLI) közeledik fontos eseményt rendelkezik, és azt szeretné, hogy az erőforrás térhet el azon a napon, de továbbra is méretezni a metrikák a minimuma vagy maximuma kapacitások . Ebben az esetben meg kell rögzített dátum profil hozzáadása a beállítás profilok listájára. A profil használatára van konfigurálva, csak az esemény napon futtatásához. Minden nap a rendszeres profil végrehajtása.

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
    
3. **Ismétlődés profil:** ilyen típusú profilok segítségével győződjön meg arról, hogy a hét egy bizonyos napon nem mindig használja ezt a profilt. Ismétlődés profilok csak a kezdő időpont van, emiatt a következő ismétlődési profil addig futnak, vagy rögzített dátum profil indításra van beállítva. Az automatikus skálázási beállítás a csak egy ismétlődési profil hajtja végre, hogy a profil, még akkor is, ha egy beállítás a meghatározott reguláris profil van. A következő két példa azt mutatják be, az ehhez a profilhoz használatát:

    **Példa: 1 - és a hét napja. Hétvégéket** tegyük fel, hogy a hétvégekre 4 lennie a maximális kapacitás kívánt, de hétköznapokon várt nagyobb terhelést, mivel azt szeretné, a maximális kapacitás 10. Ebben az esetben a beállítás tartalmazná két ismétlődési profilok, a másik pedig a hétvégéket és a másik a létrehozását.
    A beállítás néz ki:

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

    Ha az előző beállítás megnézi, láthatja, hogy minden egyes ismétlődési profil rendelkezik-e olyan ütemezést, a ütemezése határozza meg, ha a profil végrehajtása kezdődik-e. A profil nem végrehajtása, ha a másik profil végrehajtási idő.

    Például az előző beállítás "weekdayProfile" értéke 12 órakor, amely azt jelenti, hogy a profil kezdődik 12.am hétfőn végrehajtása hétfőn elindításához. Folyamatosan végrehajtás alatt, amíg szombat 12a.m. amikor "weekendProfile" végrehajtása történő futásra van ütemezve.

    **2 – példa munkaidő** vessen egy másik példa, lehet, hogy szeretné metrika küszöbérték = "x" munkaidőben, 9 a.m. 5 p.m. közötti, majd a délután 5 óra között a 9 a.m. a következő napon, érdemes a metrika küszöbértéket, "y" lehet.
    A beállítás néz ki:
    
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
    
    Az előző beállítás megtekintésével "businessHoursProfile" hétfő reggel 9 feldolgozás alatt álló megkezdése és délután 5 óráig végrehajtása a memóriában tárolja mivel az a mikor "nonBusinessHoursProfile" végrehajtásának elkezdése. A "nonBusinessHoursProfile" hajt végre, amíg a 9 a.m. Kedd és a "businessHoursProfile" élvez majd. Ez ismétlődik, amíg 5 p.m. közötti, ezen a ponton péntek "nonBusinessHoursProfile" végrehajtja egészen a 9 a.m. hétfő mivel a "businessHoursProfile" nem indul el, vége: hétfő reggel 9 végrehajtása
    
> [!Note]
> Az Azure-portálon az automatikus skálázás UX kikényszeríti a befejező időpontok ismétlődési profilokhoz, és az automatikus skálázási beállítás alapértelmezett profilként Between ismétlődési profilok végrehajtása kezdődik.
    
## <a name="autoscale-evaluation"></a>Automatikus skálázás kiértékelése
Megadott, hogy az automatikus skálázási beállításokat lehet több automatikus skálázási profil, és az egyes profilok rendelkezhet több metrika szabályok fontos tudni, hogyan történik az automatikus skálázási beállítás. Minden alkalommal, amikor az automatikus skálázási feladat fut, válassza ki a profilt, amely esetben automatikus skálázási profil kiválasztása után kezdődik kiértékeli a minimális és maximális értékek és a profil metrika szabályok, és úgy dönt, hogy a skálázási művelet szükséges.

### <a name="which-profile-will-autoscale-pick"></a>Melyik profil átveszi automatikus skálázás?
- Automatikus skálázás először megkeresi az összes rögzített dátum-profilt, amely futtatásra van beállítva, ha nincs, automatikus skálázás hajt végre azt. Ha futtatásához kellene több rögzített dátum portprofilokat, automatikus skálázási kiválasztja az elsőt.
- Ha nem rögzített dátum profil, automatikus skálázás ellenőrzi, hogy az ismétlődési profilok, ha található, majd azt futtatja azt.
- Ha nem rögzített vagy ismétlődési profilok, majd automatikus skálázás hajtsa végre a rendszeres profil.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Hogyan automatikus skálázás több szabály értékelése?

Automatikus skálázás határozza meg, melyik profil kellene hajtható végre, ha a profil a kibővített szabály kiértékelésével kezdődik (irányú szabályok = "Növelése").
- Egy vagy több kibővített szabály által kiváltott, ha az automatikus skálázás határozza meg az egyes ezeket a szabályokat scaleAction új kapacitást számítja ki. Majd azt méretezik ki a maximális számát ezen kapacitások szolgáltatás rendelkezésre állásának biztosításához.
- Példa: Ha egy virtuálisgép-méretezési csoport 10 aktuális kapacitással rendelkező átjáróeszközt, és két kibővített szabály; egy, a kapacitás 10 %-kal növekszik, és egy, a kapacitás növeli a 3. Az első szabály 11 új kapacitású eredményezne, és a második szabály 13 kapacitású eredményezne. Szolgáltatás rendelkezésre állásának biztosításához az automatikus skálázási úgy dönt, a művelet a maximális kapacitás, ezért a második szabály van kiválasztva.

Nincs kibővített szabály által kiváltott, ha automatikus skálázás kiértékeli az összes méretezési a szabály (irányú szabályok = "Csökkentése"). Automatikus skálázási a méretezési művelet csak akkor, ha összes méretezési a szabály által kiváltott.
- Automatikus skálázás új kapacitást határozza meg az egyes ezeket a szabályokat scaleAction számítja ki. Ezután azt úgy dönt, a skálázási művelet, amely a maximális számát ezen kapacitások szolgáltatás rendelkezésre állásának biztosításához eredményez.
- Példa: Ha egy virtuálisgép-méretezési csoport 10 aktuális kapacitással rendelkező átjáróeszközt, és két méretezési a szabály; egy, a kapacitás 50 %-kal csökken, és egy 3 csökkenti a kapacitást. Az első szabály 5 új kapacitású eredményezne, és a második szabály 7 kapacitású eredményezne. Szolgáltatás rendelkezésre állásának biztosításához az automatikus skálázási úgy dönt, a művelet a maximális kapacitás, ezért a második szabály van kiválasztva.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogy további információ az automatikus skálázási tekintse meg a következőket:

* [Az automatikus méretezés áttekintése](monitoring-overview-autoscale.md)
* [Az Azure a figyelő automatikus skálázás közös metrikák](insights-autoscale-common-metrics.md)
* [Ajánlott eljárások az Azure a figyelő automatikus skálázás](insights-autoscale-best-practices.md)
* [A automatikus skálázási műveletek is elküldhetik e-mailek és a webhook riasztási értesítésekre](insights-autoscale-to-webhook-email.md)
* [Automatikus skálázás REST API-n](https://msdn.microsoft.com/library/dn931953.aspx)
