---
title: Ismerje meg, hogyan működnek a metrikariasztások az Azure Monitorban.
description: Áttekintést kaphat arról, hogy mit tehet a metrikariasztásokkal, és hogyan működnek az Azure Monitorban.
ms.date: 03/17/2020
ms.topic: conceptual
ms.subservice: alerts
ms.openlocfilehash: a6860cad077b597df923274f8971f5652d4ba9e3
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397977"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>A metrikariasztások működése az Azure Monitorban

Metrikariasztások az Azure Monitorban többdimenziós metrikákon dolgoznak. Ezek a metrikák lehetnek [platform metrikák](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [egyéni metrikák](../../azure-monitor/platform/metrics-custom-overview.md), [népszerű naplók az Azure Monitor metrikák és](../../azure-monitor/platform/alerts-metric-logs.md) Application Insights metrikák. Metrikariasztások rendszeres időközönként kiértékeli, hogy ellenőrizze, hogy egy vagy több metrika-idősorozat feltételei teljesülnek-e, és értesítik, ha a kiértékelések teljesülnek. Metrika riasztások állapotalapú, azaz csak akkor küld értesítést, ha az állapot megváltozik.

## <a name="how-do-metric-alerts-work"></a>Hogyan működnek a metrikariasztások?

Metrikariasztási szabályt úgy határozhat meg, hogy megadja a figyelni kívánt célerőforrást, a metrika nevét, a feltétel típusát (statikus vagy dinamikus), valamint a feltételt (operátor és küszöbérték/érzékenység) és egy műveletcsoportot, amelyet a riasztási szabály aktiválódásakor aktivál. A feltételtípusok befolyásolják a küszöbértékek meghatározási módját. [További információ a dinamikus küszöbértékek feltételtípusáról és érzékenységi beállításairól.](alerts-dynamic-thresholds.md)

### <a name="alert-rule-with-static-condition-type"></a>Statikus feltételtípussal rendelkező riasztási szabály

Tegyük fel, hogy létrehozott egy egyszerű statikus küszöbértéket metrika riasztási szabályt az alábbiak szerint:

- Célerőforrás (a figyelni kívánt Azure-erőforrás): myVM
- Metrika: Százalékos CPU
- Feltétel típusa: Statikus
- Idő összesítése (A nyers metrikaértékeken futó statisztika. A támogatott időösszesítések: Min, Max, Átlagos, Összes, Darabszám): Átlag
- Időszak (Az a visszatekintési ablak, amelyen a mérőszámok értékei be vannak jelölve): Az elmúlt 5 mins
- Gyakoriság (A metrikariasztás gyakorisága ellenőrzi, hogy a feltételek teljesülnek-e): 1 min
- Operátor: Nagyobb, mint
- Küszöbérték: 70

A riasztási szabály létrehozásától kezdve a figyelő 1 percenként fut, és megvizsgálja az elmúlt 5 perc metrikaértékeit, és ellenőrzi, hogy ezen értékek átlaga meghaladja-e a 70-et. Ha a feltétel teljesül, azaz az átlagos százalékos CPU az elmúlt 5 percben meghaladja a 70, a riasztási szabály aktiválódik egy aktivált értesítés. Ha beállított egy e-mailt vagy egy webhook-műveletet a riasztási szabályhoz társított műveletcsoportban, mindkettőről aktivált értesítést fog kapni.

Ha több feltételt használ egy szabályban, az "ands" szabály együtt.  Ez azt, hogy a riasztás akkor aktiválódik, ha a riasztás minden feltétele igaznak számít, és megoldja, ha az egyik feltétel már nem teljesül. Az ilyen típusú riasztások például akkor lehet riasztást, ha "a 90%-nál magasabb PROCESSZOR" és a "várólista hossza meghaladja a 300 elemet". 

### <a name="alert-rule-with-dynamic-condition-type"></a>Riasztási szabály dinamikus feltételtípussal

Tegyük fel, hogy létrehozott egy egyszerű dinamikus küszöbértékek metrika riasztási szabályt az alábbiak szerint:

- Célerőforrás (a figyelni kívánt Azure-erőforrás): myVM
- Metrika: Százalékos CPU
- Feltétel típusa: Dinamikus
- Idő összesítése (A nyers metrikaértékeken futó statisztika. A támogatott időösszesítések: Min, Max, Átlagos, Összes, Darabszám): Átlag
- Időszak (Az a visszatekintési ablak, amelyen a mérőszámok értékei be vannak jelölve): Az elmúlt 5 mins
- Gyakoriság (A metrikariasztás gyakorisága ellenőrzi, hogy a feltételek teljesülnek-e): 1 min
- Operátor: Nagyobb, mint
- Érzékenység: Közepes
- Visszatekintés időszakok: 4
- Szabálysértések száma: 4

A riasztási szabály létrehozása után a dinamikus küszöbértékek gépi tanulási algoritmus beszerzi a rendelkezésre álló előzményadatokat, kiszámítja a metrikasorozat viselkedési mintájához leginkább illeszkedő küszöbértéket, és folyamatosan megtanulja az új adatok alapján a küszöbérték et, hogy pontosabb legyen.

A riasztási szabály létrehozásától kezdve a figyelő 1 percenként fut, és az elmúlt 20 perc metrikaértékeit 5 percidőszakokba csoportosítva megvizsgálja, és ellenőrzi, hogy a 4 időszak időszakértékeinek átlaga meghaladja-e a várt küszöbértéket. Ha a feltétel teljesül, azaz az átlagos százalékos CPU az elmúlt 20 percben (négy 5 perc időszakok) eltért a várható viselkedés négyszer, a riasztási szabály aktiválódik egy aktivált értesítés. Ha beállított egy e-mailt vagy egy webhook-műveletet a riasztási szabályhoz társított műveletcsoportban, mindkettőről aktivált értesítést fog kapni.

### <a name="view-and-resolution-of-fired-alerts"></a>A kilőtt riasztások megtekintése és feloldása

A fenti példák a riasztási szabályok tüzelési is megtekinthető kérhető az Azure Portalon az **Összes riasztás** panelen.

Tegyük fel, hogy a "myVM" használata továbbra is a küszöbérték felett van a későbbi ellenőrzések során, a riasztási szabály nem fog újra elsülni, amíg a feltételek meg nem oldódnak.

Egy idő után, a használat a "myVM" jön vissza a normál (megy a küszöbérték alatt). A riasztási szabály figyeli a feltétel még két alkalommal, hogy küldjön egy feloldott értesítést. A riasztási szabály egy feloldott/inaktivált üzenetet küld, ha a riasztási feltétel három egymást követő időszakban nem teljesül, hogy csökkentse a zajt csapkodó körülmények esetén.

A megoldott értesítés webes horgokon vagy e-mailen keresztül történő küldése kor, a riasztási példány (monitor állapot) állapota az Azure Portalon is megoldódott.

### <a name="using-dimensions"></a>Dimenziók használata

Metrikariasztások az Azure Monitorban is támogatja a figyelés több dimenziós érték kombinációk egy szabálysal. Nézzük meg, miért használhat több dimenziókombinációt egy példa segítségével.

Tegyük fel, hogy rendelkezik egy App Service-csomaggal a webhelyéhez. A webhelyet/alkalmazást futtató több példányban is figyelni szeretné a processzorhasználatot. Ezt metrikariasztási szabály használatával a következőképpen teheti meg:

- Célerőforrás: myAppServicePlan
- Metrika: Százalékos CPU
- Feltétel típusa: Statikus
- Dimenziók
  - Instance = InstanceName1, InstanceName2
- Összesítés i.aggregáció: Átlag
- Időszak: Az elmúlt 5 évben
- Frekvencia: 1 min
- Operátor: GreaterThan
- Küszöbérték: 70

A korábhoz hasonlóan ez a szabály figyeli, hogy az átlagos CPU-használat az elmúlt 5 percben meghaladja-e a 70%-ot. Ugyanazzal a szabállyal azonban két, a webhelyet futtató példányt figyelhet. Minden példány külön-külön lesz figyelve, és külön-külön kap értesítéseket.

Tegyük fel, hogy van egy webes alkalmazás, amely látja a hatalmas kereslet, és szüksége lesz, hogy adjunk további példányok. A fenti szabály továbbra is csak két példányt figyel. A szabályokat azonban a következőképpen is létrehozhatja:

- Célerőforrás: myAppServicePlan
- Metrika: Százalékos CPU
- Feltétel típusa: Statikus
- Dimenziók
  - Példány = *
- Összesítés i.aggregáció: Átlag
- Időszak: Az elmúlt 5 évben
- Frekvencia: 1 min
- Operátor: GreaterThan
- Küszöbérték: 70

Ez a szabály automatikusan figyeli a példány összes értékét, azaz figyelheti a példányokat, ahogy jönnek létre anélkül, hogy módosítsa a metrika riasztási szabály újra.

Több dimenzió figyelése esetén a dinamikus küszöbértékek riasztási szabály egyszerre több száz metrikasorozathoz hozhat létre testreszabott küszöbértékeket. A dinamikus küszöbértékek kevesebb riasztási szabályt eredményeznek, és jelentős időmegtakarítást eredményeznek a felügyeleti és riasztási szabályok létrehozása korban.

Tegyük fel, hogy van egy webalkalmazás, sok példányban, és nem tudja, mi a legmegfelelőbb küszöbértéket. A fenti szabályok mindig 70%-os küszöbértéket alkalmaznak. A szabályokat azonban a következőképpen is létrehozhatja:

- Célerőforrás: myAppServicePlan
- Metrika: Százalékos CPU
- Feltétel típusa: Dinamikus
- Dimenziók
  - Példány = *
- Összesítés i.aggregáció: Átlag
- Időszak: Az elmúlt 5 évben
- Frekvencia: 1 min
- Operátor: GreaterThan
- Érzékenység: Közepes
- Visszatekintés iderei: 1
- Szabálysértések száma: 1

Ez a szabály figyeli, ha az átlagos CPU-használat az elmúlt 5 percben meghaladja az egyes példányok várt viselkedését. Ugyanazt a szabályt figyelheti a példányok, ahogy jönnek létre anélkül, hogy módosítsa a metrika riasztási szabály újra. Minden példány kap egy küszöbértéket, amely illeszkedik a metrikasorozat viselkedési minta, és folyamatosan változik az új adatok alapján, hogy a küszöbérték pontosabb. Mint korábban, minden példány külön-külön lesz figyelve, és értesítést kap külön-külön.

A visszatekintési időszakok és a szabálysértések számának növelése azt is lehetővé teheti, hogy a szűrési riasztások csak a jelentős eltérés definíciója alapján jelenjenek meg. [További információ a dinamikus küszöbértékek speciális beállításairól.](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean)

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Figyelés skálán metrikariasztások használatával az Azure Monitorban

Eddig azt tapasztalta, hogy egyetlen metrikariasztás használható egy vagy több metrika-idősorozat egyetlen Azure-erőforráshoz kapcsolódó figyelésére. Sokszor előfordulhat, hogy ugyanazt a riasztási szabályt szeretné alkalmazni számos erőforrásra. Az Azure Monitor is támogatja a figyeléstöbb erőforrás (azonos típusú) egy metrikariasztási szabály, az erőforrások, amelyek ugyanabban az Azure-régióban léteznek. 

Ez a funkció jelenleg támogatott platform metrikák (nem egyéni metrikák) a következő szolgáltatások a következő Azure-felhők:

| Szolgáltatás | Nyilvános Azure | Government | Kína |
|:--------|:--------|:--------|:--------|
| Virtual machines (Virtuális gépek)  | **Igen** | Nem | Nem |
| SQL-kiszolgáló-adatbázisok | **Igen** | **Igen** | Nem |
| SQL-kiszolgáló rugalmas készletei | **Igen** | **Igen** | Nem |
| Adatdoboz széléről lévő eszközök | **Igen** | **Igen** | Nem |

Megadhatja a figyelés hatókörét egyetlen metrika riasztási szabály három módon. A virtuális gépek esetében például a következőképpen adhatja meg a hatókört:  

- virtuális gépek listája egy Azure-régióban egy előfizetésen belül
- az összes virtuális gép (egy Azure-régióban) egy vagy több erőforráscsoportban egy előfizetésben
- az összes virtuális gép (egy Azure-régióban) egyetlen előfizetésben

Metrikariasztási szabályok létrehozása, amelyek több erőforrást figyelnek, olyan, mint [bármely más metrikariasztás létrehozása,](alerts-metric.md) amely egyetlen erőforrást figyel. Az egyetlen különbség az, hogy ki kell választania az összes figyelni kívánt erőforrást. Ezeket a szabályokat [az Azure Resource Manager-sablonokon](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources)keresztül is létrehozhatja. Minden figyelt erőforrásról külön értesítéseket fog kapni.

> [!NOTE]
>
> Egy metrika riasztási szabály, amely több erőforrást figyel, csak egy feltétel engedélyezett.

## <a name="typical-latency"></a>Tipikus késleltetés

Metrikariasztások esetén általában 5 percen belül értesítést kap, ha a riasztási szabály gyakoriságát 1 percre állítja be. Az értesítési rendszerek nagy terhelése esetén előfordulhat, hogy hosszabb késés jelenik meg.

## <a name="supported-resource-types-for-metric-alerts"></a>Támogatott erőforrástípusok metrikariasztásokhoz

A támogatott erőforrástípusok teljes listáját ebben a [cikkben](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)találja.


## <a name="next-steps"></a>További lépések

- [Ismerje meg, hogyan hozhat létre, tekinthet meg és kezelhet metrikariasztásokat az Azure-ban](alerts-metric.md)
- [Ismerje meg, hogyan helyezhető üzembe metrikariasztások az Azure Resource Manager-sablonok használatával](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [További információ a műveletcsoportokról](action-groups.md)
- [További információ a dinamikus küszöbértékek feltételtípusról](alerts-dynamic-thresholds.md)
