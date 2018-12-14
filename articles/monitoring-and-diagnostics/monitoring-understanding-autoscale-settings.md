---
title: Az Azure Monitor automatikus skálázási beállítások ismertetése
description: Az automatikus méretezési beállítások és azok működéséről részletes áttekintését. Virtual Machines, Cloud Services, Web Apps vonatkozik
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 2347d82b8c2f5a08b944577e5b06cde3b68617b3
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53385006"
---
# <a name="understand-autoscale-settings"></a>Ismerkedés az automatikus méretezési beállításokkal
Az automatikus méretezési beállítások segítségével, győződjön meg arról, hogy a megfelelő mennyiségű erőforrást fut az alkalmazás változó terhelés kezelésére. Konfigurálhatja az automatikus méretezési beállítások betöltése vagy a teljesítmény jelző mérőszámok alapján, vagy aktivált, ütemezett dátum és idő aktiválását. Ez a cikk részletes tekintse meg az automatikus skálázási beállítás felhőtámadások vesz igénybe. A cikk a séma és a egy beállítás tulajdonságait kezdődik, és ezután bemutatja a konfigurálható különböző típusaival. Végül a cikk azt ismerteti, hogyan értékeli ki az automatikus skálázási funkció az Azure-ban a profilt egy adott időpontban végrehajtásához.

## <a name="autoscale-setting-schema"></a>Automatikus skálázási beállítás séma
Az automatikus skálázási beállítás séma mutatja be, a következő automatikus skálázási beállítás szolgál. Fontos megjegyezni, hogy rendelkezik-e az automatikus méretezési beállítás:
- Egy profilt. 
- Ebben a profilban két mérőszám szabályokat: egy kibővítési és egy, a méretezési csoport.
  - A horizontális felskálázási szabály akkor aktiválódik, ha a virtuális gép méretezési csoportjának átlagos százalékos Processzorhasználat metrika értéke nagyobb, mint 85 %-os, az elmúlt 10 percre.
  - A horizontális leskálázási szabály akkor aktiválódik, ha a virtuális gép méretezési átlaga kisebb, mint 60 %-os az elmúlt percben.

> [!NOTE]
> Egy beállítás több profilt is rendelkezhet. További tudnivalókért tekintse meg a [profilok](#autoscale-profiles) szakaszban. Több horizontális felskálázási szabályok és leskálázási szabályokat meghatározott profilt is lehet. Hogyan értékeli, olvassa el a [értékelési](#autoscale-evaluation) szakaszban.

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
| Beállítás | ID (Azonosító) | Az automatikus skálázási beállítás erőforrás-azonosítója. Automatikus skálázási beállítások egy Azure Resource Manager-erőforrást is. |
| Beállítás | név | Az automatikus skálázási beállítás neve. |
| Beállítás | location | Az automatikus skálázási beállítás helyét. Ezen a helyen az erőforrás méretezése folyamatban helye eltérő lehet. |
| properties | targetResourceUri | Az erőforrás-azonosító az erőforrás méretezése folyamatban van. Egy automatikus skálázási beállítás-erőforrásonként legfeljebb. |
| properties | Profilok | Az automatikus skálázási beállítás egy vagy több profilt tevődik össze. Minden alkalommal, amikor az automatikus skálázási motor fut, végrehajtása egy profilt. |
| profil | név | A profil neve. Kiválaszthatja, hogy bármely név, amely segítséget nyújt a profil azonosításához. |
| profil | Capacity.maximum | Az engedélyezett maximális kapacitását. Biztosítja, hogy automatikus skálázási profil az végrehajtása közben nem méretezhetők az erőforrás felett ezt a számot. |
| profil | Capacity.minimum | Az engedélyezett minimális kapacitást. Ez biztosítja, hogy az automatikus skálázási profil az végrehajtása közben nem méretezhetők az erőforrás e szám alá. |
| profil | Capacity.default | Ha az erőforrás-metrika (az ebben az esetben a "vmss1" CPU) olvasása során, és a jelenlegi kapacitás nem éri el az alapértelmezett, automatikus méretezés elvégzi a horizontális felskálázást, az alapértelmezett. Ez az erőforrás rendelkezésre állásának biztosításához. Ha a jelenlegi kapacitás még magasabb, mint az alapértelmezett kapacitásértéket, automatikus méretezés nem méretezhetők a. |
| profil | szabályok | Automatikus skálázási automatikusan méretezi magát, a maximális és minimális kapacitás, a szabályok segítségével a profil között. Egy profil több szabály is lehet. Általában két szabályok vonatkoznak: egyet, hogy mikor horizontális felskálázás, a másik pedig, hogy mikor alatt. |
| szabály | metricTrigger | A metrikai feltétel teljesülésekor a szabály határozza meg. |
| metricTrigger | MetricName | A metrika neve. |
| metricTrigger |  metricResourceUri | Az erőforrás, amely bocsát ki a metrika erőforrás-Azonosítóját. A legtöbb esetben ez megegyezik az erőforrás méretezése folyamatban van. Bizonyos esetekben ez eltérő lehet. Például egy virtuálisgép-méretezési egy storage-üzenetsorban található üzenetek száma szerint méretezheti. |
| metricTrigger | timeGrain | A metrika-mintavételezés időtartama. Ha például **TimeGrain = "PT1M"** azt jelenti, hogy a metrikák legyenek összesítve percenként, a statisztika elemben megadott összesítő metódus használatával. |
| metricTrigger | statisztika | A timeGrain időszakban használt összesítési módszer. Ha például **statisztika = "Average"** és **timeGrain = "PT1M"** azt jelenti, hogy a metrikák legyenek összesítve 1 percenként átlaga. Ez a tulajdonság szabja meg, hogyan mintavételezés a metrikát. |
| metricTrigger | timeWindow | A metrikák visszakeresnünk időtartama. Ha például **timeWindow = "PT10M"** azt jelenti, hogy minden alkalommal, amikor az automatikus skálázási funkció az lekérdezi metrikák az elmúlt 10 percben. Az időtartomány lehetővé teszi, hogy a metrikák lesz normalizálva, és elkerülhető az átmeneti csúcsterhelésekre való reagálás. |
| metricTrigger | timeAggregation | Az összesítési módszer összesíthetők a mintavételezett metrikák. Ha például **TimeAggregation = "Average"** kell összesíteni a mintavételezett metrikák átlaga. Az előző esetben a tíz 1 perces mintát venni, és az átlagos őket. |
| szabály | scaleAction | A szabály a metricTrigger kezdeményezése esetén végrehajtandó műveletet. |
| scaleAction | irány | "Növelni" horizontális felskálázás, vagy "Csökkentéséhez" méret esetén.|
| scaleAction | érték | Mennyi növelése vagy csökkentése érdekében az erőforrás kapacitását. |
| scaleAction | utáni | Mennyi ideig eltelte után a skálázási művelet újbóli skálázás előtt. Például ha **utáni = "PT10M"**, automatikus méretezés nem kísérli meg méretezését, ezáltal újra egy másik 10 perc. A utáni, hogy hozzáadását és eltávolítását a példányok után stabilizálódhatnak a metrikák engedélyezése. |

## <a name="autoscale-profiles"></a>Automatikus méretezési profilok

Automatikus méretezési profilok három típusa van:

- **Rendszeres profil:** A leggyakrabban használt profil. Ha nincs szüksége a hét napját, vagy egy adott nap alapján az erőforrás méretezése, használhatja a szokásos profilt. Ez a profil majd metrika szabályok határozzák meg, mikor horizontális felskálázás, és mikor érdemes méretezni a is konfigurálhatók. Meghatározott egy szokásos profilt kell rendelkeznie.

    A cikk korábbi részeiben használt példa profil, amelyek rendszeres profil. Fontos megjegyezni, hogy is lehet beállítani a profil statikus példányszámra az erőforrás méretezése.

- **Rögzített dátum profil:** Ez a profil olyan speciális esetekben. Például tegyük fel, hogy 26, 2017 December (PST) hamarosan fontos eseményt. Azt szeretné, hogy a minimális és maximális kapacitását az erőforrás kell lennie az adott nap, de továbbra is a metrikák a méretezési csoport. Ebben az esetben egy rögzített dátumon profilt kell hozzáadni a beállítás a profilok listáját. A profil csak az esemény naponta fusson van konfigurálva. Minden nap az automatikus méretezés a szokásos profilt használja.

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
    
- **Ismétlődési profil:** Az ilyen típusú profil segítségével győződjön meg arról, hogy ezt a profilt minden esetben használja a hét egy adott napon. Ismétlődési profil csak a kezdési időt kell. Futnak, amíg a következő ismétlődési profil vagy rögzített dátum profil indításra van állítva. Az automatikus skálázási beállítás csak egy ismétlődési profil használatával, hogy a profil fut, akkor is, ha egy beállítás a meghatározott reguláris profil van. Az alábbi két példák bemutatják, hogyan használja ezt a profilt:

    **1. példa: És a hétvégékre hétköznap**
    
    Tegyük fel, hétvégén, amelyet az 4 lennie a maximális kapacitását. Hétköznapokon mert további terhelés várható azt szeretné, a maximális kapacitása 10. A beállítás ebben az esetben két ismétlődési profil, a másik pedig a hétvégéket és a egy másik hétköznapokon tartalmaz.
    A beállítás a következőhöz hasonló:

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

    Az előző beállítás látható, hogy minden egyes ismétlődési profil rendelkezik-e az ütemezés szerint. Ez az ütemezés határozza meg a profil indításakor fut. A profil leáll, ha egy másik profilhoz futási idejét.

    Ha például az előző beállításai "weekdayProfile" indításra van beállítva, hétfőn 12:00 órakor. Ez azt jelenti, hogy ez a profil elindítja a futó hétfő, 12:00 órakor. 12:00-kor, ha "weekendProfile" történő futásra van ütemezve fut, amíg szombat folytatja.

    **2. példa: Munkaidő**
    
    Tegyük fel, egy metrika küszöbértéke munkaidőben (9:00-kor, 5:00 óra), és a egy másik a minden más időszakban legyen. A beállítás a következő lenne:
    
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
    
    Az előző beállítás látható, hogy a "businessHoursProfile" hétfő futó 9:00 órakor kezdődik, és 17:00-kor továbbra is. Ez mikor "nonBusinessHoursProfile" elindult. A "nonBusinessHoursProfile" 9:00-kor kedd amíg fut, és majd a "businessHoursProfile" átveszi újra. Ez ismétlődik amíg péntek, délután 5:00-kor. Ezen a ponton "nonBusinessHoursProfile" futtatásával egészen hétfő 9:00 órakor.
    
> [!Note]
> Az automatikus skálázási felhasználói felület az Azure Portalon az ismétlődési profil befejezési kikényszeríti, és megkezdi az automatikus skálázási beállítás alapértelmezett profil között ismétlődési profilok futtató.
    
## <a name="autoscale-evaluation"></a>Automatikus skálázás kiértékelési
Tekintve, hogy az automatikus méretezési beállítások több profilt is rendelkezik, és az egyes profilok rendelkezhet több metrika szabály, fontos tudni, hogyan értékeli ki az automatikus skálázási beállítás. Minden alkalommal, amikor az automatikus skálázási feladat fut, az megkezdi a profil érvényes kiválasztásával. Automatikus skálázási Ezután kiértékeli a minimális és maximális értékeket és a profil metrika szabályok, és úgy dönt, hogy ha szükség egy skálázási műveletet.

### <a name="which-profile-will-autoscale-pick"></a>Melyik profilt fogja választani az automatikus méretezés?

Automatikus skálázási választja ki a profilt a következő folyamat használja:
1. Bármely rögzített dátum profilt, amely konfigurálva van a Futtatás most először keresi. Ha nincs, az automatikus skálázási futtatja. Ha több rögzített dátum profilt, amely futtatni kell menteniük, automatikus méretezés kiválasztja az elsőt.
2. Ha nincsenek nem rögzített dátum profil, automatikus méretezés ismétlődési profilok megvizsgálja. Ha egy ismétlődési profil, futtatja azt.
3. Ha nincsenek rögzített dátum vagy ismétlődési profilok, az automatikus méretezés fut. a szokásos profilt

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Hogyan az automatikus méretezés több szabály kiértékeléséhez?

Automatikus skálázási profilt futtatásához határozza meg, miután értékeli a profil a kibővített szabályokat (ezek a szabályok a **iránya = "Növelése"**).

Legalább egy horizontális felskálázási szabályok esetén, ha az automatikus méretezés számítja ki az új kapacitást határozza meg a **scaleAction** az egyes ezeket a szabályokat. Majd méretezhetőségének köszönhetően a maximális számát ezen a kapacitás, a szolgáltatás rendelkezésre állásának biztosításához.

Például tegyük fel, van egy virtuálisgép-méretezési csoportot 10 aktuális kapacitás beállítása. Két horizontális felskálázási szabály: egyet, amely 10 %-kal növeli a kapacitást, a másikat, amely növeli a kapacitást 3 száma szerint. Az első szabály 11 egy új kapacitást eredményez, és a második szabálynak 13 kapacitást eredményez. Ahhoz, hogy a szolgáltatás rendelkezésre állása, az automatikus skálázási úgy dönt, az eseményekből eredő műveleteket a maximális kapacitást, így a második szabálynak van kiválasztva.

Horizontális felskálázási szabályok esetén, ha az automatikus méretezés kiértékeli az összes horizontális leskálázási szabály (a szabályok **irány "Csökkentéséhez" =**). Az automatikus méretezés csak akkor kerül a horizontális leskálázási művelet, ha aktivált összes horizontális leskálázási szabály.

Automatikus skálázási számítja ki az új kapacitást határozza meg a **scaleAction** az egyes ezeket a szabályokat. Ezután azt úgy dönt, a skálázási művelet, amely a szolgáltatás rendelkezésre állásának biztosításához ezeket kapacitások maximális eredményez.

Például tegyük fel, van egy virtuálisgép-méretezési csoportot 10 aktuális kapacitás beállítása. Két horizontális leskálázási szabály:, amely 50 %-kal csökkenti a kapacitást, a másikat, amely csökkenti a kapacitást 3 száma szerint. Az első szabály az 5-ből egy új kapacitást eredményez, és a második szabálynak 7 kapacitást eredményez. Ahhoz, hogy a szolgáltatás rendelkezésre állása, az automatikus skálázási úgy dönt, az eseményekből eredő műveleteket a maximális kapacitást, így a második szabálynak van kiválasztva.

## <a name="next-steps"></a>További lépések
További információ az automatikus méretezés lépésként tekintse át a következőket:

* [Az automatikus méretezés áttekintése](../azure-monitor/platform/autoscale-overview.md)
* [Gyakori metrikák az Azure Monitor automatikus méretezés](../azure-monitor/platform/autoscale-common-metrics.md)
* [Ajánlott eljárások az Azure Monitor automatikus skálázása](../azure-monitor/platform/autoscale-best-practices.md)
* [Automatikus skálázási műveletek használatával küldjön e-mailt és webhookot riasztási értesítések](../azure-monitor/platform/autoscale-webhook-email.md)
* [Az automatikus méretezés – REST API](https://msdn.microsoft.com/library/dn931953.aspx)
