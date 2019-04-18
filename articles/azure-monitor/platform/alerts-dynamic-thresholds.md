---
title: Riasztások létrehozása az Azure monitorban dinamikus küszöbértékekkel
description: Riasztások létrehozása a machine learning-alapú dinamikus küszöbértékekkel
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 772401c286a50774d201703cefcbbc12f0fcf88f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678887"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor-public-preview"></a>Metrikákhoz kapcsolódó riasztások dinamikus küszöbértékekkel az Azure monitorban (nyilvános előzetes verzió)

Metrikariasztás dinamikus küszöbértékek észlelése a fejlett gépi tanulási (ML) további metrikák általi korábbi viselkedés, mintákat és rendellenességeket, amelyek jelzik a lehetséges szolgáltatással kapcsolatos problémák azonosítására használ. Egy egyszerű felhasználói felület és a méretezett operations támogatást biztosít a felhasználók olyan teljesen automatizált módon konfigurálhatja a riasztási szabályok az Azure Resource Manager API-n keresztül.

Riasztási szabály létrehozása után fog aktiválódni csak amikor a figyelt metrika nem viselkednek, várt, a testre szabott küszöbértékek alapján.

Örömmel vennénk visszajelzéseit, legyen hamarosan <azurealertsfeedback@microsoft.com>.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Miért és mikor ajánlott a dinamikus feltétel típusa használ?

1. **Méretezhető riasztási** – riasztások szabályokat hozhat létre dinamikus küszöbértékek metrika sorozat több száz küszöbértékek szabott egyszerre. Riasztási szabály meghatározása csak egyetlen mértéket a ugyanolyan könnyű még biztosítása. Használata kezelheti a felhasználói felületen vagy az Azure Resource Manager API-eredmények kevesebb riasztási szabályok az. A méretezhető módszer különösen hasznos, metrika méretek esetén, vagy több erőforrásokhoz, például az összes előfizetés erőforrások alkalmazása esetén. Amely fordítja le a felügyeleti és a riasztásokat szabályok létrehozását mentése jelentős mennyiségű időt. [További tudnivalók a metrika riasztások dinamikus küszöbértékekkel sablonokkal konfigurálása](alerts-metric-create-templates.md).

1. **A metrika mintafelismerést intelligens** – az egyedi gépi Tanulási technológiával képesek vagyunk metrikamintákhoz észlelése és tudjon alkalmazkodni metrika idővel, amely gyakran szezonalitás (óránként / naponta / hetente). Idő és a riasztási gyakorlattól eltérő minta alapján a metrikák általi viselkedéséhez gyakorlatainak mentesíti az, hogy mindegyik metrikát "megfelelő" küszöbértéke terhe. A dinamikus küszöbértékek használt gépi Tanulási algoritmus úgy tervezték, hogy megakadályozza a zajos (kis pontosságú) és az adott várt mintának nem rendelkező wide (alacsony visszaírási) küszöbértékeket.

1. **Intuitív konfigurációs** – dinamikus küszöbértékek engedélyezése kell rendelkeznie a metrika tartalmazó tartományhoz ismerete enyhítése fogalmait, használatával metrikákhoz kapcsolódó riasztások beállítását.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Hogyan konfigurálhatók a riasztásokat szabályok dinamikus küszöbértékekkel?

Riasztások dinamikus küszöbértékekkel metrika riasztások az Azure Monitor használatával konfigurálható. [További információ a metrika-riasztások konfigurálása](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Hogyan számítják ki a küszöbértékek?

Dinamikus küszöbérték folyamatosan tanul az adatokat a metrika sorozat harmadik része, és algoritmusok és módszerek használatával modellezheti megpróbálja. Minták észleli az adatok, például a szezonalitás (óránként / naponta / hetente), és képesek zajos metrikákat (például a gép CPU és memória) kezelésére és metrikák alacsony diszperziós (például a rendelkezésre állás és a hiba mellett).

A küszöbértékek oly módon, hogy ezeket a küszöbértékeket eltérést jelöli a metrikákkal kapcsolatos viselkedést anomáliát ki van jelölve.

> [!NOTE]
> Szezonális észlelési óra, napi vagy heti időköz beállítása. Ez azt jelenti, hogy más bihourly mintája hasonló minták vagy semiweekly előfordulhat, hogy nem észlelhető.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Dinamikus küszöbérték középérték "Érzékenysége" beállítás mire?

Riasztási küszöbérték érzékenységét egy magas szintű fogalom, amely szabályozza a metrikákkal kapcsolatos viselkedés riasztást kiváltó szükséges eltérés.
Ezt a beállítást nem szükséges a mérőszám, például a statikus küszöbérték kapcsolatos adatait. Az elérhető lehetőségek:

- Magas – a küszöbértékeket a rövid és a metrika sorozat minta közeli lesz. Riasztási szabály akkor aktiválódik, a legkisebb értékét, a további riasztások eredményez.
- Közepes – kevesebb szoros és több elosztott terhelésű küszöbértékek, kevesebb riasztást, mint a magas érzékenységi (alapértelmezett).
- Alacsony – a küszöbértékek lesz metrika sorozat minta további távolsága a laza. Riasztási szabály csak aktiválják a nagy eltérés, kevesebb riasztást eredményez.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Mik a dinamikus küszöbértékek "Kezelő" beállítás lehetőségei?

A szabály riasztásokat hozhat létre dinamikus küszöbértékek szabott küszöbértékek mindkét felső a metrikákkal kapcsolatos viselkedést és dolní meze használatával ugyanaz a riasztási szabály alapján.
A riasztás a következő három feltétel egyikének aktiválását közül választhat:

- A felső küszöbértéknél nagyobb vagy kisebb, mint az alsó küszöbértéket (alapértelmezett)
- Nagyobb, mint a felső küszöbérték
- Az alsó küszöbérték alatti.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Mit dinamikus küszöbértékek középérték speciális beállításait?

**Sikertelen időszakok** -dinamikus küszöbértékek is lehetővé teszi, hogy "A riasztást kiváltó szabálysértések száma", konfigurálhatja a minimálisan szükséges (az időkeret alapértelmezett értéke négy riasztást a rendszer egy adott időtartományon belül szorzataként eltérések 20 percig). A felhasználó sikertelen időszakok konfigurálhatja, és válassza ki a, a sikertelen és a időtartomány módosításával riasztást kap. A riasztási zaj átmeneti által generált csökkenti. Példa:

Riasztást kiváltó, ha a probléma folyamatos 20 perc, 5 perc elteltével egy adott időszak csoportosítása 4 egymást követő alkalommal használja a következő beállításokat:

![Folyamatos probléma időszakok beállításainak 20 perc, 5 perces időszak adott csoportja 4 egymást követő alkalommal sikertelen](media/alerts-dynamic-thresholds/0008.png)

Ha hiba történt a dinamikus küszöbérték megsértését az elmúlt 30 perc, 5 perces időtartammal 20 perce a riasztást kiváltó, használja a következő beállításokat:

![Sikertelen időszakok beállításait a probléma az elmúlt 30 perc, 5 perces időszak csoportosítása a 20 perce](media/alerts-dynamic-thresholds/0009.png)

**Időpontnál régebbi adatok mellőzése** -felhasználók is adhat meg, amelyből a rendszer kell kezdődnie kiszámítása a küszöbértékeket a kezdő dátumot. Egy tipikus használati eset akkor fordulhat elő, ha egy erőforrást egy tesztelési módban futó volt, és a egy éles környezetbeli számítási feladatokra kiszolgálása érdekében mostantól hitelesítenie kelljen magát, és ezért a tesztelési fázis során bármilyen mérőszám viselkedését kell figyelmen kívül kell hagyni.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Hogyan találja meg, miért lett elindítva egy dinamikus küszöbérték-értesítés?

Megismerheti a riasztások nézetben aktivált riasztás példányok vagy kattintson a hivatkozásra az e-mail vagy szöveges üzenet, vagy a böngészőben a riasztások megtekintése az Azure Portalon. [További információ a riasztások nézetben](alerts-overview.md#alerts-experience).

A riasztás nézetet jelenít meg:

- Jelenleg minden metrika részletei a dinamikus küszöbértékek riasztás aktiválva.
- Az időszak, amelyben a riasztás-trigger, amely magában foglalja a dinamikus küszöbértékeket, abban az időpontban használt, egy diagram.
- Szeretne visszajelzést adni dinamikus küszöbértékek riasztás és a riasztások nézet élményt, amely javíthatja a jövőbeli észlelések képessége.

## <a name="will-slow-behavior-change-in-the-metric-trigger-an-alert"></a>Lassú viselkedés módosítani fogja a metrikaindító metrikanevét riasztást?

Valószínűleg nem. Dinamikus küszöbérték jók jelentős eltérések észlelése lassan fejlődő problémák helyett.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Mennyi adatot szolgál, és ezután kiszámíthatja a küszöbértékeket?

A küszöbértékeket a diagramon megjelenő előtt egy riasztási szabályt a rendszer létrehozza a mérőszám a rendszer kiszámolta elég előzményadatok alapján számítja ki órás vagy napi szezonális minták (10 nap) alapján. Riasztási szabály létrehozása után a dinamikus küszöbértékeket és fogja használni, amely érhető el, és folyamatosan ismerje meg valamennyi szükséges előzményadatok adept új adatok alapján, hogy a küszöbértékek pontosabb. Ez azt jelenti, hogy miután a számítási diagram megjeleníti heti mintákat is.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Mennyi adatot a riasztást kiváltó van szükség?

Ha egy új erőforrást vagy hiányzó metrikaadatok, dinamikus küszöbérték nem aktiválja riasztások a előtt az adatok három nap biztosítására pontosan küszöbértékek érhetők el.

## <a name="dynamic-thresholds-best-practices"></a>Dinamikus küszöbérték ajánlott eljárások

Dinamikus küszöbérték alkalmazhatók bármely platformra és az Azure monitorban egyéni metrika, és azt is hangolt volt a közös alkalmazás- és infrastruktúra metrikákat.
A következő elemeket a gyakorlati tanácsok a riasztások konfigurálása az egyes ezeket a metrikákat használó dinamikus küszöbérték.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>A virtuális gép Processzor-százalékos metrikák dinamikus küszöbérték

1. A [az Azure portal](https://portal.azure.com), kattintson a **figyelő**. A figyelő nézet az összes figyelési beállítást és adatokat egyetlen nézetben egyesíti.

2. Kattintson a **riasztások** kattintson **+ Új riasztási szabály**.

    > [!TIP]
    > A legtöbb erőforráspanelek is, hogy **riasztások** azok erőforrás menüben alatt **figyelés**, létrehozhat riasztásokat onnan.

3. Kattintson a **cél kiválasztása**, környezet, amely betölti panelén válassza a célként megadott erőforrás-riasztás kívánt. Használja **előfizetés** és **"Virtual Machines" erőforrástípus** legördülő listákból a figyelni kívánt erőforrás található. A Keresősáv használatával keresse meg az erőforrást.

4. Miután kiválasztotta a célként megadott erőforrás, kattintson a **feltétel hozzáadása**.

5. Válassza ki a **"CPU Percentage"**.

6. Szükség esetén pontosíthatja a metrika módosításával **időszak** és **összesítési**. Metrikus típus használata "Maximum" összesítés típusa, kevésbé tükrözik a működését, mert nem ajánlott. Az "Maximális" összesítési típus statikus küszöbérték esetleg több megfelelő.

7. Az elmúlt 6 óra látni fogja a metrika egy diagram. Adja meg a riasztási paraméterekkel:
    1. **A feltétel típusát** – válassza ki a "Dinamikus" beállítást.
    1. **Érzékenységi** – válassza a közepes vagy alacsony érzékenységi riasztási zaj csökkentéséhez.
    1. **Operátor** – kivéve, ha viselkedés jelöli az Alkalmazáshasználat "Nagyobb mint" válassza.
    1. **Gyakoriság** -fontolja meg a riasztás üzletmenetre gyakorolt hatás csökkentése alapján.
    1. **Sikertelen időszakok** (speciális lehetőség) – a hely vissza ablak legalább 15 percet kell lennie. Például ha öt perc alatt az időszak van beállítva, majd az időszakok sikertelen kell lennie legalább három vagy több.

8. A metrikadiagram jelenik meg a legújabb adatok alapján számított küszöbértékeket.

9. Kattintson a **Done** (Kész) gombra.

10. Töltse ki **riasztás részletei** például **riasztási szabály neve**, **leírás**, és **súlyossági**.

11. Műveletcsoport hozzáadása a riasztást, vagy egy új műveletcsoport létrehozása vagy meglévő műveletcsoport kiválasztása.

12. Kattintson a **kész** a metrikaalapú riasztási szabály mentéséhez.

> [!NOTE]
> Metrikariasztás portálon keresztül létrehozott szabályok jönnek létre a célként megadott erőforrás azonos erőforráscsoportban.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Dinamikus küszöbérték Application Insights HTTP-kérelem végrehajtási ideje

1. A [az Azure portal](https://portal.azure.com), kattintson a **figyelő**. A figyelő nézet az összes figyelési beállítást és adatokat egyetlen nézetben egyesíti.

2. Kattintson a **riasztások** kattintson **+ Új riasztási szabály**.

    > [!TIP]
    > A legtöbb erőforráspanelek is, hogy **riasztások** azok erőforrás menüben alatt **figyelés**, létrehozhat riasztásokat onnan.

3. Kattintson a **cél kiválasztása**, környezet, amely betölti panelén válassza a célként megadott erőforrás-riasztás kívánt. Használja **előfizetés** és **"Application Insights" erőforrástípus** legördülő listákból a figyelni kívánt erőforrás található. A Keresősáv használatával keresse meg az erőforrást.

4. Miután kiválasztotta a célként megadott erőforrás, kattintson a **feltétel hozzáadása**.

5. Válassza ki a **"HTTP kérelem végrehajtási idő"**.

6. Szükség esetén pontosíthatja a metrika módosításával **időszak** és **összesítési**. Metrikus típus használata "Maximum" összesítés típusa, kevésbé tükrözik a működését, mert nem ajánlott. Az "Maximális" összesítési típus statikus küszöbérték esetleg több megfelelő.

7. Az elmúlt 6 óra látni fogja a metrika egy diagram. Adja meg a riasztási paraméterekkel:
    1. **A feltétel típusát** – válassza ki a "Dinamikus" beállítást.
    1. **Operátor** – válassza a "Nagyobb, mint" az az időtartam fokozása aktivált riasztások csökkentése érdekében.
    1. **Gyakoriság** -fontolja meg a riasztás üzletmenetre gyakorolt hatás csökkentése alapján.

8. A metrikadiagram jelenik meg a legújabb adatok alapján számított küszöbértékeket.

9. Kattintson a **Done** (Kész) gombra.

10. Töltse ki **riasztás részletei** például **riasztási szabály neve**, **leírás**, és **súlyossági**.

11. Műveletcsoport hozzáadása a riasztást, vagy egy új műveletcsoport létrehozása vagy meglévő műveletcsoport kiválasztása.

12. Kattintson a **kész** a metrikaalapú riasztási szabály mentéséhez.

> [!NOTE]
> Metrikariasztás portálon keresztül létrehozott szabályok jönnek létre a célként megadott erőforrás azonos erőforráscsoportban.
