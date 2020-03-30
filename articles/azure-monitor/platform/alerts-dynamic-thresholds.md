---
title: Riasztások létrehozása dinamikus küszöbértékekkel az Azure Monitorban
description: Riasztások létrehozása gépi tanuláson alapuló dinamikus küszöbértékekkel
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 9345138e948d84e0ea3c804dbd7a4b3c21daca2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668145"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Metrikariasztások dinamikus küszöbértékekkel az Azure Monitorban

A dinamikus küszöbértékek detektálásával rendelkező metrikariasztás a fejlett gépi tanulás (ML) segítségével ismerteti a metrikák korábbi viselkedését, azonosíthatja a lehetséges szolgáltatási problémákat jelző mintákat és rendellenességeket. Támogatja mind az egyszerű felhasználói felületet, mind a nagy méretű műveleteket azáltal, hogy lehetővé teszi a felhasználók számára a riasztási szabályok konfigurálását az Azure Resource Manager API-n keresztül, teljesen automatizált módon.

Egy riasztási szabály létrehozása után csak akkor fog elsülni, ha a figyelt metrika nem a várt módon viselkedik, a személyre szabott küszöbértékek alapján.

Szeretnénk hallani a visszajelzést, tartsa <azurealertsfeedback@microsoft.com>jön a .

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Miért és mikor ajánlott dinamikus feltételtípushasználata?

1. **Skálázható riasztás –** Dinamikus küszöbértékek riasztási szabályok hozhat létre szabott küszöbértékeket több száz metrikasorozatok egy időben, de ugyanolyan könnyű definiálása riasztási szabály egyetlen metrika. Kevesebb riasztást adnak a létrehozáshoz és a kezeléshez. Az Azure Portal vagy az Azure Resource Manager API használatával létrehozhatja őket. A méretezhető megközelítés különösen hasznos metrikadimenziók kezelésekor, vagy ha több erőforrásra, például az összes előfizetés-erőforrásra vonatkozik.  [További információ arról, hogyan konfigurálhatja a metrikariasztásokat dinamikus küszöbértékekkel a sablonok használatával.](alerts-metric-create-templates.md)

1. **Intelligens metrikaminta-felismerés** – A mi ML technológia, képesek vagyunk automatikusan felismerni metrikus minták és alkalmazkodni metrikus változások idővel, amelyek gyakran magukban foglalják a szezonalitás (óránkénti / napi / heti). A metrikák viselkedéséhez való alkalmazkodás és a minta eltérései alapján történő riasztás enyhíti az egyes metrikák "helyes" küszöbértékének ismeretében lévő terhét. A dinamikus küszöbértékekben használt gépi tanulási algoritmus célja, hogy megakadályozza a zajos (alacsony pontosságú) vagy széles (alacsony visszahívási) küszöbértékeket, amelyek nem rendelkeznek a várt mintával.

1. **Intuitív konfiguráció** – A dinamikus küszöbértékek lehetővé teszi a metrikariasztások magas szintű fogalmak használatával történő beállítását, így enyhítve a metrikával kapcsolatos kiterjedt tartományi ismeretek kel.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Hogyan konfigurálható a riasztási szabályok dinamikus küszöbértékekkel?

A dinamikus küszöbértékekkel rendelkező riasztások az Azure Monitor metrikariasztások on keresztül konfigurálhatók. [További információ a metrikariasztások konfigurálásáról.](alerts-metric.md)

## <a name="how-are-the-thresholds-calculated"></a>Hogyan számítják ki a küszöbértékeket?

A dinamikus küszöbértékek folyamatosan megtanulja a metrikasorozat adatait, és algoritmusok és metódusok segítségével próbálja modellezni. Észleli az adatok mintáit, például a szezonalitást (óránkénti / napi / heti), és képes kezelni a zajos metrikákat (például a gép CPU-ját vagy a memóriát), valamint az alacsony diszperziós (például rendelkezésre állási és hibaarányú) mutatókat.

A küszöbértékek úgy vannak kiválasztva, hogy a küszöbértékektől való eltérés a metrika viselkedésében anomáliát jelez.

> [!NOTE]
> A szezonális mintafelismerés egy óra, nap vagy heti intervallumra van beállítva. Ez azt jelenti, hogy más minták, például a kétóránkénti minta vagy a félheti nem észlelhető.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Mit jelent a dinamikus küszöbértékek "Érzékenység" beállítása?

Riasztási küszöbérték érzékenysége egy magas szintű koncepció, amely szabályozza a riasztás aktiválásához szükséges metrikaviselkedéstől való eltérés mértékét.
Ez a beállítás nem igényel tartományi ismereteket a metrika, például a statikus küszöbértéket. Az elérhető lehetőségek:

- Magas – A küszöbértékek szorosak lesznek, és közel vannak a metrikasorozat-mintához. A riasztási szabály a legkisebb eltérés, ami több riasztást eredményez.
- Közepes – Kevésbé szűk és kiegyensúlyozottabb küszöbértékek, kevesebb riasztást, mint a nagy érzékenység (alapértelmezett).
- Alacsony – A küszöbértékek laza lesz, nagyobb távolságra metrikus sorozat mintát. A riasztási szabály csak akkor aktiválódik, a nagy eltérések, ami kevesebb riasztást eredményez.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Melyek az "Operátor" beállítási beállítások a dinamikus küszöbértékekben?

A dinamikus küszöbértékek riasztások szabály hozhat létre testre szabott küszöbértékek alapján metrika viselkedése mind a felső és alsó határok ugyanazt a riasztási szabályt.
Kiválaszthatja a riasztást, hogy az alábbi három feltétel valamelyikén aktiválódjon:

- Nagyobb, mint a felső küszöbérték, vagy alacsonyabb, mint az alsó küszöbérték (alapértelmezett)
- Nagyobb, mint a felső küszöbérték
- Alacsonyabb, mint az alsó küszöbérték.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Mit jelentenek a dinamikus küszöbértékek speciális beállításai?

**Sikertelen időszakok** – A dinamikus küszöbértékek lehetővé teszik a "Számmegsértések a riasztás aktiválásához" beállítást, egy bizonyos időablakon belül szükséges eltérések minimális számát a rendszer riasztásának kiemeléséhez (az alapértelmezett időablak négy eltérés 20 perc alatt). A felhasználó konfigurálhatja a hibás időszakokat, és kiválaszthatja, hogy mire kell figyelmeztetni a hibás időszakok és az időablak módosításával. Ez a képesség csökkenti az átmeneti tüskék által keltett riasztási zajt. Példa:

Ha riasztást szeretne kiváltani, ha a probléma 20 percig folyamatos, egy adott időszak 5 perces csoportjában 4 egymást követő alkalommal, használja a következő beállításokat:

![A folyamatos kibocsátás sikertelen időszakbeállításai 20 percig, 4 egymást követő alkalommal egy adott időszakban 5 perces csoportban](media/alerts-dynamic-thresholds/0008.png)

Ha riasztást szeretne kiváltani, ha a dinamikus küszöbértékek megsértése történt az elmúlt 30 percből 20 percen belül, 5 perces időszakkal, használja a következő beállításokat:

![Az elmúlt 30 percből 20 percig tartó sikertelen időszakok beállításai 5 perces időszakcsoporttal](media/alerts-dynamic-thresholds/0009.png)

**Adatok figyelmen kívül hagyása előtt** – A felhasználók tetszés szerint meghatározhatnak egy kezdő dátumot is, amelytől kezdve a rendszernek meg kell kezdenie a küszöbértékek kiszámítását. Egy tipikus használati eset akkor fordulhat elő, ha egy erőforrás tesztelési módban futó volt, és most elő léptetve éles számítási feladatok kiszolgálására, ezért a tesztelési fázisban lévő metrikák viselkedését figyelmen kívül kell hagyni.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Hogyan tudja meg, hogy miért aktiválta a dinamikus küszöbértékek riasztást?

A riasztási nézetben az aktivált riasztási példányokat az e-mailben vagy szöveges üzenetben található hivatkozásra kattintva, vagy a böngészőben az Azure Portalon lévő riasztások megtekintéséhez fedezheti fel. [További információ a riasztások nézetről.](alerts-overview.md#alerts-experience)

A riasztási nézet a következőket jeleníti meg:

- A metrika részletei abban a pillanatban, amikor a dinamikus küszöbértékek riasztás aktiválódik.
- Annak az időszaknak a diagramja, amelyben a riasztás tanusította, amely tartalmazza az adott időpontban használt dinamikus küszöbértékeket.
- A dinamikus küszöbértékek riasztással és a riasztások megtekintésével kapcsolatos visszajelzések, amelyek javíthatják a jövőbeli észleléseket, visszajelzést adhat.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>A lassú viselkedés változások a metrikában riasztást váltanak ki?

Valószínűleg nem. A dinamikus küszöbértékek a jelentős eltérések észlelésére jók, nem pedig a lassan változó problémákra.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Mennyi adatot használ a rendszer a küszöbértékek megtekintéséhez, majd kiszámításához?

Amikor először hoznak létre riasztási szabályt, a diagramon megjelenő küszöbértékek kiszámítása elegendő előzményadatok alapján történik az óra- vagy napi szezonális minták (10 nap) kiszámításához. A riasztási szabály létrehozása után a dinamikus küszöbértékek az összes rendelkezésre álló szükséges előzményadatot felhasználja, és folyamatosan tanulnak és alkalmazkodnak az új adatok alapján, hogy pontosabbá tegyék a küszöbértékeket. Ez azt jelenti, hogy a számítás után a diagram heti mintákat is megjelenít.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Mennyi adat szükséges a riasztás aktiválásához?

Ha új erőforrással rendelkezik, vagy hiányoznak a metrikaadatok, a dinamikus küszöbértékek nem indítanak riasztást három nap előtt, vagy 30 metrikaadat-minta áll rendelkezésre a pontos küszöbértékek biztosítása érdekében.

## <a name="dynamic-thresholds-best-practices"></a>A dinamikus küszöbértékek – gyakorlati tanácsok

Dinamikus küszöbértékek bármely platformra vagy egyéni metrikára alkalmazható az Azure Monitorban, és a közös alkalmazás- és infrastruktúra-metrikákhoz is be van hangolva.
A következő elemek gyakorlati tanácsok a riasztások konfigurálásához néhány ilyen metrikák dinamikus küszöbértékek használatával.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Dinamikus küszöbértékek a virtuális gép CPU-jának százalékos mutatóiban

1. Az [Azure Portalon](https://portal.azure.com)kattintson a **Figyelő**elemre. A Figyelő nézet egyetlen nézetben összesíti az összes figyelési beállítást és adatot.

2. Kattintson **a Riasztások,** majd **a + Új riasztási szabály**elemre.

    > [!TIP]
    > A legtöbb erőforrás-panelek is rendelkezik **riasztások az** erőforrás menüben **figyelése,** akkor létrehozhat riasztásokat onnan is.

3. Kattintson **a Cél kiválasztása**gombra a betöltődő környezet ablaktáblán válassza ki azt a célerőforrást, amelyről figyelmeztetni szeretne. **Az Előfizetés** és a **"Virtuális gépek" erőforrás-típus** legördülő menük segítségével megkeresheti a figyelni kívánt erőforrást. A keresősáv segítségével is megkeresheti az erőforrást.

4. Miután kiválasztottegy célerőforrást, kattintson a **Feltétel hozzáadása**gombra.

5. Válassza ki a **"CPU százalékot"**.

6. Szükség esetén finomíthatja a metrikát az **Időszak** és **az Összesítés**módosításával. Nem ajánlott a "Maximum" aggregációs típus használata ehhez a metrikatípushoz, mivel kevésbé reprezentatív a viselkedésre. A "Maximum" aggregációs típus statikus küszöbértéke talán megfelelőbb.

7. Az elmúlt 6 óra mérőszámdiagramja jelenik meg. Adja meg a riasztási paramétereket:
    1. **Feltétel típusa** - Válassza a "Dinamikus" lehetőséget.
    1. **Érzékenység** – A riasztási zaj csökkentéséhez válassza a Közepes/Alacsony érzékenység lehetőséget.
    1. **Operátor** – válassza a "Nagyobb, mint" lehetőséget, kivéve, ha a viselkedés az alkalmazás használatát jelöli.
    1. **Gyakoriság** – fontolja meg a riasztás üzleti hatása alapján történő csökkentését.
    1. **Sikertelen időszakok** (Advanced Option) – A visszatekintési ablaknak legalább 15 percnek kell lennie. Ha például az időszak öt percre van állítva, akkor a sikertelen időszakoknak legalább háromnak vagy többnek kell lenniük.

8. A metrikadiagram a számított küszöbértékeket jeleníti meg a legutóbbi adatok alapján.

9. Kattintson a **Done** (Kész) gombra.

10. Töltse ki a **riasztás részleteit,** például a **riasztási szabály nevét,** **a leírást**és **a súlyossági szintet.**

11. Adjon hozzá egy műveletcsoportot a riasztáshoz egy meglévő műveletcsoport kijelölésével vagy egy új műveletcsoport létrehozásával.

12. Kattintson a **Kész** gombra a metrikariasztási szabály mentéséhez.

> [!NOTE]
> A portálon keresztül létrehozott metrikariasztási szabályok ugyanabban az erőforráscsoportban jönnek létre, mint a célerőforrás.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Dinamikus küszöbértékek az Application Insights HTTP-kérelem végrehajtási idején

1. Az [Azure Portalon](https://portal.azure.com)kattintson a **Figyelő**elemre. A Figyelő nézet egyetlen nézetben összesíti az összes figyelési beállítást és adatot.

2. Kattintson **a Riasztások,** majd **a + Új riasztási szabály**elemre.

    > [!TIP]
    > A legtöbb erőforrás-panelek is rendelkezik **riasztások az** erőforrás menüben **figyelése,** akkor létrehozhat riasztásokat onnan is.

3. Kattintson **a Cél kiválasztása**gombra a betöltődő környezet ablaktáblán válassza ki azt a célerőforrást, amelyről figyelmeztetni szeretne. **Az Előfizetés** és az **"Application Insights" erőforrástípus-legördülő** verziókkal megkeresheti a figyelni kívánt erőforrást. A keresősáv segítségével is megkeresheti az erőforrást.

4. Miután kiválasztottegy célerőforrást, kattintson a **Feltétel hozzáadása**gombra.

5. Válassza ki a **"HTTP-kérelem végrehajtási idejét"**.

6. Szükség esetén finomíthatja a metrikát az **Időszak** és **az Összesítés**módosításával. Nem ajánlott a "Maximum" aggregációs típus használata ehhez a metrikatípushoz, mivel kevésbé reprezentatív a viselkedésre. A "Maximum" aggregációs típus statikus küszöbértéke talán megfelelőbb.

7. Az elmúlt 6 óra mérőszámdiagramja jelenik meg. Adja meg a riasztási paramétereket:
    1. **Feltétel típusa** - Válassza a "Dinamikus" lehetőséget.
    1. **Operátor** – Válassza a "Nagyobb, mint" lehetőséget az időtartam javulása miatt indított riasztások csökkentéséhez.
    1. **Gyakoriság** – fontolja meg a riasztás üzleti hatása alapján történő csökkentését.

8. A metrikadiagram a számított küszöbértékeket jeleníti meg a legutóbbi adatok alapján.

9. Kattintson a **Done** (Kész) gombra.

10. Töltse ki a **riasztás részleteit,** például a **riasztási szabály nevét,** **a leírást**és **a súlyossági szintet.**

11. Adjon hozzá egy műveletcsoportot a riasztáshoz egy meglévő műveletcsoport kijelölésével vagy egy új műveletcsoport létrehozásával.

12. Kattintson a **Kész** gombra a metrikariasztási szabály mentéséhez.

> [!NOTE]
> A portálon keresztül létrehozott metrikariasztási szabályok ugyanabban az erőforráscsoportban jönnek létre, mint a célerőforrás.

## <a name="interpreting-dynamic-threshold-charts"></a>Dinamikus küszöbérték-diagramok értelmezése

A következő diagram egy metrikát, a dinamikus küszöbértékeket és néhány riasztást jelenít meg, amelyek akkor aktiválódnak, amikor az érték kívül esik az engedélyezett küszöbértékeken.

![További információ a metrikariasztások konfigurálásáról](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Az előző diagram értelmezéséhez használja az alábbi információkat.

- **Kék vonal** – A ténylegesen mért metrika az idő múlásával.
- **Kék árnyékolt terület** – A metrika megengedett tartományát jeleníti meg. Mindaddig, amíg a metrikaértékek ezen a tartományon belül maradnak, nem jelennek meg riasztás.
- **Kék pöttyök** - Ha bal gombbal a diagram egy részére kattintasz, majd az egérmutatót a kék vonal fölé viszid, akkor egy kék pötty jelenik meg a kurzor alatt, amely egy adott összesített metrikus értéket mutat.
- **Előugró ablak kék pontokkal** – A mért metrikus értéket (a kék pontot) és a megengedett tartomány felső és alsó értékeit jeleníti meg.  
- **Piros sor fekete körrel** – Az első metrikus értéket mutatja az engedélyezett tartományon kívül. Ez az az érték, amely egy metrikariasztást ad ki, és aktív állapotba helyezi.
- **Piros pontos -** További mért értékeket jelöl a megengedett tartományon kívül. Nem tűz további metrikariasztások, de a riasztás marad az aktív.
- **Piros terület** – Azt az időt jeleníti meg, amikor a metrikaértéke kívül esik az engedélyezett tartományon. A riasztás aktív állapotban marad, amíg a későbbi mért értékek kívül esnek a megengedett tartományon, de nincs új riasztás aktiválódik.
- **A piros terület vége** - Amikor a kék vonal visszakerül az engedélyezett értékekbe, a piros terület megáll, és a mért értékvonal kékre vált. A fekete körvonalú piros ponton a metrikariasztás állapota megoldódott. 
