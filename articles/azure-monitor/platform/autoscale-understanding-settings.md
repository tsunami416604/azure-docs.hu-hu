---
title: Az automatikus skálázási beállítások ismertetése az Azure Monitorban
description: Az automatikus skálázási beállítások és azok működésének részletes bontása. Virtuális gépekre, felhőszolgáltatásokra, webalkalmazásokra vonatkozik
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: 9a2b94208de7ce490a0e7acfbb71175b4a7c846e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364305"
---
# <a name="understand-autoscale-settings"></a>Ismerkedés az automatikus méretezési beállításokkal
Az automatikus skálázási beállítások biztosítják, hogy az alkalmazás ingadozó terhelésének kezeléséhez megfelelő mennyiségű erőforrás fut. Beállíthatja, hogy az automatikus skálázási beállítások a terhelést vagy teljesítményt jelző, vagy egy ütemezett napon és időpontban aktivált metrikák alapján aktiválódjanak. Ez a cikk részletesen bemutatja az automatikus skálázási beállítások anatómiát. A cikk egy beállítás sémájával és tulajdonságaival kezdődik, majd végigvezeti a konfigurálható különböző profiltípusokon. Végül a cikk ismerteti, hogy az Automatikus skálázás funkció az Azure-ban kiértékeli, hogy melyik profilt kell végrehajtani egy adott időpontban.

## <a name="autoscale-setting-schema"></a>Automatikus méretezési beállítássémája
Az automatikus skálázási beállításséma szemléltetéséhez a következő automatikus skálázási beállítás jelenik meg. Fontos megjegyezni, hogy ez az automatikus skálázási beállítás a következőket:
- Egy profil. 
- Két metrikaszabályok ebben a profilban: az egyik a horizontális felskálázás, és egy a skálázás.
  - A horizontális felskálázási szabály akkor aktiválódik, ha a virtuálisgép-méretezési csoport átlagos százalékos CPU-metrikája nagyobb, mint 85 százalék az elmúlt 10 percben.
  - A horizontális felskálázási szabály akkor aktiválódik, ha a virtuálisgép-méretezési csoport átlaga kevesebb, mint 60 százalék az elmúlt percben.

> [!NOTE]
> Egy beállítástöbb profillal is rendelkezhet. További információ: [Profiles](#autoscale-profiles) szakasz. Egy profil is rendelkezhet több horizontális felskálázási szabályok és a horizontális felskálázási szabályok definiálva. A kiértékelés módját a [kiértékelésről](#autoscale-evaluation) című szakaszban láthatja.

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
| Beállítás | ID (Azonosító) | Az automatikus skálázási beállítás erőforrás-azonosítója. Az automatikus skálázási beállítások egy Azure Resource Manager-erőforrás.Autoscale settings are a Azure Resource Manager resource. |
| Beállítás | név | Az Automatikus skálázás beállítás neve. |
| Beállítás | location | Az automatikus skálázási beállítás helye. Ez a hely eltérhet a méretezett erőforrás helyétől. |
| properties | targetResourceUri | A méretezett erőforrás erőforrásazonosítója. Erőforrásonként csak egy automatikus skálázási beállítás lehet. |
| properties | Profilok | Az automatikus skálázási beállítás egy vagy több profilból áll. Minden alkalommal, amikor az automatikus skálázási motor fut, egy profilt hajt végre. |
| profil | név | A profil neve. Bármilyen nevet választhat, amely segít a profil azonosításában. |
| profil | Kapacitás.maximum | A maximális kapacitás megengedett. Ez biztosítja, hogy a profil végrehajtásakor az automatikus skálázás nem méretezi az erőforrást ezen a szám felett. |
| profil | Kapacitás.minimum | A minimális kapacitás engedélyezett. Ez biztosítja, hogy a profil végrehajtásakor az automatikus skálázás nem méretezi az erőforrást ezen a szám alatt. |
| profil | Kapacitás.alapértelmezett | Ha probléma merül fel az erőforrás-metrika olvasása (ebben az esetben a "vmss1" PROCESSZORa), és az aktuális kapacitás az alapértelmezett alatt van, az automatikus skálázás az alapértelmezettre skálázódik. Ez biztosítja az erőforrás rendelkezésre állását. Ha az aktuális kapacitás már nagyobb, mint az alapértelmezett kapacitás, az automatikus skálázás nem méretezhető be. |
| profil | szabályok | Az automatikus skálázás automatikusan átméretezi a maximális és a minimális kapacitást a profilban lévő szabályok használatával. Egy profilban több szabály is lehet. Általában két szabály van: az egyik határozza meg, hogy mikor kell horizontális felskálázni, a másik pedig annak meghatározására, hogy mikor kell skálázni. |
| Szabály | metricTrigger | A szabály metrikafeltételét határozza meg. |
| metricTrigger | metricName | A mérőszám neve. |
| metricTrigger |  metricResourceUri | A metrikát kibocsátó erőforrás erőforrásazonosítója. A legtöbb esetben ugyanaz, mint a méretezett erőforrás. Bizonyos esetekben, ez eltérő lehet. Például skálázhatja a virtuálisgép-méretezési készletet a tárolóvárólistában lévő üzenetek száma alapján. |
| metricTrigger | timeGrain | A metrika mintavételi időtartama. Például **a TimeGrain = "PT1M"** azt jelenti, hogy a mérőszámokat 1 percenként összesíteni kell a statisztikai elemben megadott összesítési módszer rel. |
| metricTrigger | Statisztika | Az összesítési módszer az időfizetési határidőn belül. Például a **statisztika = "Átlag"** és **timeGrain = "PT1M"** azt jelenti, hogy a metrikákat 1 percenként összesíteni kell az átlag figyelembevételével. Ez a tulajdonság határozza meg, hogyan mintavételezi a metrika. |
| metricTrigger | timeWindow | A metrikák visszatekintéséhez. Például **timeWindow = "PT10M"** azt jelenti, hogy minden alkalommal, amikor automatikus skálázás fut, lekérdezi metrikák az elmúlt 10 perc. Az időablak lehetővé teszi a metrikák normalizálása, és elkerüli az átmeneti csúcsok reagál. |
| metricTrigger | timeAggregation | A mintavételezett metrikák összesítéséhez használt összesítési módszer. Például **timeaggregation = "Átlagos"** kell összesíteni a mintavételezett metrikák az átlag. Az előző esetben vegye ki a tíz 1 perces mintákat, és átlagára. |
| Szabály | scaleAction (művelet) | A művelet, amelyet a szabály metrikatriggeraktiválása esetén kell végrehajtani. |
| scaleAction (művelet) | irány | "Növelés" a horizontális felskálázáshoz, vagy "Csökkentés" a méretezéshez.|
| scaleAction (művelet) | érték | Mennyit kell növelni vagy csökkenteni az erőforrás kapacitását. |
| scaleAction (művelet) | cooldown | A méretezési művelet után a méretezési művelet után várakoznivaló idő. Ha például **újratöltődési idő = "PT10M",** az automatikus skálázás nem kísérli meg a méretezést további 10 percig. A újratöltődési idő lehetővé teszi, hogy a metrikák stabilizálódjanak a példányok hozzáadása vagy eltávolítása után. |

## <a name="autoscale-profiles"></a>Automatikus skálázási profilok

Az automatikus skálázási profiloknak három típusa van:

- **Rendszeres profil:** A leggyakoribb profil. Ha nem kell az erőforrást a hét napja vagy egy adott nap alapján méreteznie, használhat egy normál profilt. Ez a profil ezután konfigurálható metrikaszabályok, amelyek meghatározzák, hogy mikor kell horizontális felskálázás és mikor kell skálázni. Csak egy normál profilt kell definiálni.

    A cikk korábbi részében használt példaprofil egy példa a normál profilra. Vegye figyelembe, hogy az is lehetséges, hogy egy profilt az erőforrás statikus példányszámra méretezése.

- **Rögzített dátum profil:** Ez a profil különleges esetekre van. Tegyük fel például, hogy 2017. Azt szeretné, hogy az erőforrás minimális és maximális kapacitása eltérő legyen az adott napon, de továbbra is ugyanazon a metrikákon méretezve. Ebben az esetben hozzá kell adnia egy rögzített dátumprofilt a beállítás profillistájához. A profil úgy van beállítva, hogy csak az esemény napján fusson. Bármely más napon automatikus skálázás a normál profilt használja.

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
    
- **Ismétlődési profil:** Ez a profiltípus lehetővé teszi annak biztosítását, hogy ez a profil mindig a hét egy adott napján legyen használva. Az ismétlődési profiloknak csak kezdési időpontja van. Addig futnak, amíg a következő ismétlődési profil vagy a rögzített dátumprofil el nem indul. A csak egy ismétlődési profillal rendelkező automatikus skálázási beállítás akkor is futtatja a profilt, ha ugyanabban a beállításban van definiálva egy normál profil. A következő két példa bemutatja, hogyan használja ezt a profilt:

    **1. példa: Hétköznapok és hétvégék**
    
    Tegyük fel, hogy hétvégén azt szeretné, hogy a maximális kapacitás a 4 legyen. Hétköznap, mivel több terhelést vár, a maximális kapacitás 10 legyen. Ebben az esetben a beállítás két ismétlődési profilt tartalmaz, az egyik hétvégén, a másik hétköznap.
    A beállítás így néz ki:

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

    Az előző beállítás azt mutatja, hogy minden ismétlődési profilnak van ütemezése. Ez az ütemezés határozza meg, hogy a profil mikor kezd futni. A profil leáll, ha itt az ideje egy másik profil futtatásának.

    Az előző beállításban például a "weekdayProfile" hétfőn 12:00 órakor kezdődik. Ez azt jelenti, hogy ez a profil hétfőn 12:00-kor kezdődik. Szombat12:00 óráig folytatódik, amikor a "weekendProfile" futása a tervek szerint megkezdődik.

    **2. példa: Munkaidő**
    
    Tegyük fel, hogy munkaidőben (9:00 és 17:00 óra között) egy metrikaküszöbértéket szeretne, és egy másikat az összes többi időponthoz. A beállítás így nézne ki:
    
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
    
    Az előző beállítás azt mutatja, hogy a "businessHoursProfile" hétfőn 9:00 órakor kezdődik, és 17:00 óráig tart. Ekkor kezdődik a "nonBusinessHoursProfile" futása. A "nonBusinessHoursProfile" kedd 9:00 óráig tart, majd a "businessHoursProfile" újra átveszi az irányítást. Ez péntek17:00-ig ismétlődik. Ezen a ponton a "nonBusinessHoursProfile" egészen hétfőig 9:00-ig fut.
    
> [!Note]
> Az Automatikus skálázás ú felhasználói felület az Azure Portalon kényszeríti az ismétlődési profilok befejezési idejét, és megkezdi az automatikus skálázási beállítás alapértelmezett profiljának futtatását az ismétlődési profilok között.
    
## <a name="autoscale-evaluation"></a>Automatikus skálázás kiértékelése
Mivel az automatikus skálázási beállítások több profillal is rendelkezhetnek, és minden profilnak több metrikaszabálya is lehet, fontos megérteni, hogyan történik az automatikus skálázási beállítások kiértékelése. Minden alkalommal, amikor az automatikus skálázási feladat fut, akkor kezdődik kiválasztásával a profilt, amely alkalmazható. Ezután az automatikus skálázás kiértékeli a minimális és maximális értékeket, valamint a profilban lévő metrikaszabályokat, és eldönti, hogy szükség van-e méretezési műveletre.

### <a name="which-profile-will-autoscale-pick"></a>Melyik profilt választja az automatikus skálázás?

Az automatikus skálázás a következő sorrendben választja ki a profilt:
1. Először megkeresi a rögzített dátumú profilt, amely most fut. Ha van, az automatikus skálázás futtatja. Ha több rögzített dátumprofil van, amelyeknek futniuk kell, az automatikus skálázás kiválasztja az elsőt.
2. Ha nincsenek rögzített dátumprofilok, az automatikus skálázás az ismétlődési profilokat vizsgálja. Ha ismétlődési profil található, futtatja azt.
3. Ha nincsenek rögzített dátumú vagy ismétlődési profilok, az automatikus skálázás futtatja a normál profilt.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Hogyan értékeli ki az automatikus skálázás több szabályt?

Miután az automatikus skálázás meghatározza, hogy melyik profilt kell futtatni, kiértékeli a profil összes horizontális felskálázási szabályát (ezek a szabályok irány = **"Növelés" ).**

Ha egy vagy több horizontális felskálázási szabályok aktiválódnak, az automatikus skálázás kiszámítja az új kapacitást, amelyet az egyes szabályok **skálázása** határoz meg. Ezután a maximális kapacitásra skálázódik, hogy biztosítsa a szolgáltatás rendelkezésre állását.

Tegyük fel például, hogy van egy 10-es aktuális kapacitású virtuálisgép-méretezési készlet. Két horizontális felskálázási szabály létezik: az egyik 10 százalékkal növeli a kapacitást, a másik pedig 3-mal növeli a kapacitást. Az első szabály 11 új kapacitást eredményezne, a második pedig 13-as kapacitást. A szolgáltatás rendelkezésre állásának biztosítása érdekében az automatikus skálázás kiválasztja a maximális kapacitást eredményező műveletet, így a második szabály kerül kiválasztásra.

Ha nem lép nek ki horizontális felskálázási szabályok, az automatikus skálázás kiértékeli az összes horizontális felskálázási szabályt (irány = **"Csökkentés"**) . Az automatikus skálázás csak akkor lép életbe, ha az összes horizontális felskálázási szabály aktiválódik.

Az automatikus skálázás kiszámítja az új kapacitást, amelyet az egyes szabályok **skálázása** határoz meg. Ezután kiválasztja a skálázási műveletet, amely a maximális kapacitást eredményezi a szolgáltatás rendelkezésre állásának biztosítása érdekében.

Tegyük fel például, hogy van egy 10-es aktuális kapacitású virtuálisgép-méretezési készlet. Két horizontális felskálázási szabály létezik: az egyik 50 százalékkal csökkenti a kapacitást, a másik pedig 3 számmal csökkenti a kapacitást. Az első szabály 5 új kapacitást eredményezne, a második pedig 7-es kapacitást. A szolgáltatás rendelkezésre állásának biztosítása érdekében az automatikus skálázás kiválasztja a maximális kapacitást eredményező műveletet, így a második szabály kerül kiválasztásra.

## <a name="next-steps"></a>További lépések
Az automatikus skálázásról az alábbiakra hivatkozva olvashat bővebben:

* [Az automatikus méretezés áttekintése](../../azure-monitor/platform/autoscale-overview.md)
* [Az Azure Monitor automatikus skálázási gyakori metrikák](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Ajánlott eljárások az Azure Monitor automatikus skálázásához](../../azure-monitor/platform/autoscale-best-practices.md)
* [Automatikus skálázási műveletek használata e-mailek és webhook-értesítési értesítések küldéséhez](../../azure-monitor/platform/autoscale-webhook-email.md)
* [REST AUTOMATIKUS SKÁLÁZÁSI REST API](https://msdn.microsoft.com/library/dn931953.aspx)

