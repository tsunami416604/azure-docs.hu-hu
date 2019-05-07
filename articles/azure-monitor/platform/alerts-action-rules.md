---
title: Az Azure Monitor riasztások műveleti szabályokban
description: Mik azok a műveleti szabályokban, és hogyan konfigurálhatja és kezelheti őket ismertetése.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: e5d04fd136848684e866fae9768b252e3b6ca77f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65137941"
---
# <a name="action-rules-preview"></a>Műveleti szabályokban (előzetes verzió)

A cikkből megtudhatja, Mik azok a műveleti szabályokban, és hogyan konfigurálhatja és kezelheti őket.

## <a name="what-are-action-rules"></a>Mik a műveleti szabályokban?

Műveleti szabályokban engedélyezése, hogy meghatározza a műveletek (vagy tiltási műveletek) minden olyan erőforrás-kezelő hatókörben (előfizetés, erőforráscsoport, vagy az erőforrás). Szűrők, amelyek lehetővé teszik, hogy el szeretné végezni a riasztási példányok adott részhalmazára leszűkíthető különböző rendelkeznek. 

Műveleti szabályokban segítségével:

* Mellőzheti műveletek és az értesítések tervezett karbantartási időszakokat, vagy a hétvégi/ünnepnapok, ahelyett, hogy tiltsa le az egyes riasztási szabály külön-külön.
* Adja meg a műveleteket, és értesítéseket méretezve: Nem kell külön-külön az egyes riasztási szabály műveletcsoport határozza meg, most már minden hatókörben létrehozott riasztások vonatkozóan a műveletcsoport adhatja meg. Választhatom is például ahhoz, hogy a művelet csoport "ContosoActionGroup" eseményindító minden riasztás az előfizetésen belül jön létre.

## <a name="configuring-an-action-rule"></a>Egy művelet szabály konfigurálása

A funkció eléréséhez kiválasztásával **kezelheti** , a kezdőlap az Azure monitorban riasztásokat. Válassza ki **művelet szabályok (előzetes verzió)**. Elérheti azokat kiválasztásával **művelet szabályok (előzetes verzió)** az irányítópultról, riasztások kezdőlapjának alkotóelemeit.

![Az Azure Monitor kezdőlapja a műveleti szabályokban](media/alerts-action-rules/action-rules-landing-page.png)

Válassza ki **+ új művelet szabály**. 

![A művelet új szabály hozzáadása](media/alerts-action-rules/action-rules-new-rule.png)

Másik lehetőségként azt is beállíthatja egy riasztási szabály konfigurálása során egy műveleti szabály létrehozásához.

![A művelet új szabály hozzáadása](media/alerts-action-rules/action-rules-alert-rule.png)

Meg kell jelennie a művelet szabály nyissa meg a létrehozási folyamatot. Az alábbi elemeket konfigurálja: 

![Új művelet szabály létrehozási folyamat](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Scope

Először válassza ki a hatókört, célként megadott erőforrás, erőforráscsoportot vagy előfizetést. Akkor is többszörös kijelöléssel lehetővé teszi a fentiek (belül egyetlen előfizetéssel) bármilyen kombinációját. 

![A művelet szabály hatóköre](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Szűrési feltétel

A riasztások a meghatározott hatókörben egy adott részének le további szűkítéséhez emellett szűrő(k) alapján határozhatja meg. 

A rendelkezésre álló szűrők a következők: 

* **Súlyosság**: Válassza ki egy vagy több riasztás súlyossági. Súlyosság = Sev1 azt jelenti, hogy a művelet a szabály a súlyosság szerint Sev1 az összes riasztás vonatkozik.
* **Szolgáltatás figyelése**: A szűrő a származó figyelési szolgáltatás alapján. Ez akkor is többszörös kijelöléses. Például a figyelőszolgáltatás = "Application Insights"kifejezés azt jelenti, hogy a művelet a szabály az összes "Application Insights" vonatkozó riasztások alapján.
* **Erőforrástípus**: A szűrő egy adott erőforrás típusa alapján. Ez akkor is többszörös kijelöléses. Például erőforrástípus = "Virtual Machines" azt jelenti, hogy a művelet a szabály minden virtuális gépekre alkalmazható.
* **Riasztási szabály azonosítója**: Lehetővé teszi a meghatározott riasztási szabályok használatával a Resource Manager-Azonosítót a riasztási szabály szűrést.
* **Az állapot figyelése**: Szűrheti a figyelési feltétel, a "Fired" vagy "Megoldva" riasztási példányok.
* **Leírás**: A reguláris kifejezéssel egyező belül a riasztási szabály részeként határozza meg a leírást.
* **Riasztás környezete (tartalom)**: Reguláris kifejezéssel egyező belül a [riasztás környezete](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) riasztási példány mezőket.

Ezeket a szűrőket egymással összefüggésben érvényesek. Például ha megadom a "Resource type" = "Virtual Machines" és 'Súlyosság' = "Sev0", majd I szűrt a riasztásokhoz "Sev0" csak saját virtuális gépeken. 

![A művelet szabály szűrők](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Tiltási vagy műveleti csoport konfigurálása

Ezután konfigurálja a művelet a szabály riasztás letiltása vagy a művelet csoporttámogatás. Mindkettő nem választható. A konfiguráció megfelelő a korábban meghatározott hatókör és a szűrők riasztási példányokon funkcionál.

#### <a name="suppression"></a>Tiltási

Ha **tiltási**, műveleteket és értesítések a Mellőzés időtartamának beállítása. Válassza ki az alábbi lehetőségek közül:
* **Múlva (mindig)**: Minden értesítés határozatlan időre letiltja.
* **Ütemezett időpontban**: Értesítéseinek elrejtése, korlátozott időtartamú belül.
* **Az ismételt**: Az ismétlődési ütemezés, amely lehet a napi, heti vagy havi mellőzése.

![A művelet szabály letiltása](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Műveletcsoport

Ha **műveletcsoport** a váltógomb vagy meglévő műveletcsoport hozzáadása vagy hozzon létre egy újat. 

> [!NOTE]
> Csak egy műveletcsoport társíthatja egy művelet szabályt.

![Műveletcsoport szabály művelet](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>A művelet szabály részletei

Végül az a következő adatokat, a művelet szabály konfigurálása
* Name (Név)
* Az erőforráscsoport, amelyben fogja menteni
* Leírás 

## <a name="example-scenarios"></a>Példaforgatókönyvek

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>1. forgatókönyv: A riasztások súlyosság alapján letiltása

A Contoso biztosítani szeretné a saját előfizetés "ContosoSub" minden hétvégi belül minden virtuális gép összes Sev4 riasztásaihoz értesítéseinek elrejtése.

**Megoldás:** A művelet szabály létrehozása
* Hatókör = "ContosoSub"
* Szűrők
    * Súlyosság = "Sev4"
    * Erőforrástípus = "Virtual Machines"
* Az ismétlődési tiltási beállítása heti, és ellenőrzi a "Szombat" és "Vasárnap"

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>2. forgatókönyv: A riasztás környezete (tartalom) alapuló riasztások letiltása

A Contoso biztosítani szeretné az összes bejelentkezéshez, az "Számítógép-01" riasztások értesítéseinek elrejtése az "ContosoSub" határozatlan ideig mert karbantartás keresztül fogja.

**Megoldás:** A művelet szabály létrehozása
* Hatókör = "ContosoSub"
* Szűrők
    * Szolgáltatás figyelése = "Log Analytics"
    * Riasztás környezete (tartalom) tartalmazza a "Számítógép-01"
* Mellőzés beállítása "múlva (mindig)"

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>3. forgatókönyv: Műveletcsoport meg van határozva egy erőforráscsoportot

Contoso definiált [előfizetés szintjén metrikariasztás](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor), csak a külön-külön az az erőforráscsoport "ContosoRG" riasztásokat kiváltó műveletek határozhatók meg szeretne.

**Megoldás:** A művelet szabály létrehozása
* Hatókör = "ContosoRG"
* Nincs szűrő
* Action Group set to 'ContosoActionGroup'

## <a name="managing-your-action-rules"></a>A művelet szabályok kezelése

Megtekintheti és kezelheti a műveleti szabályokban a listanézet alább látható módon.

![Műveleti szabályokban listanézet](media/alerts-action-rules/action-rules-list-view.png)

Itt engedélyezése/letiltása/törlése műveleti szabályokban ipari méretekben mellettük jelölőnégyzet bejelölésével is. Minden művelet a szabály a hivatkozásra kattintva megnyílik a konfigurációs oldalára, a definíció frissítésének és az engedélyezés/letiltás, lehetővé teszi.

## <a name="best-practices"></a>Ajánlott eljárások

Naplóriasztások hoztak létre a ["eredmények száma"](https://docs.microsoft.com/azure-monitor/platform/alerts-unified-log) . lehetőség **riasztási egypéldányos** az egész keresési eredmény (amely lehet több számítógépen például) használatával. Ebben a forgatókönyvben egy művelet szabály használja a "Riasztás környezete (tartalom)" szűrőt, ha úgy fog működni a riasztási példányon mindaddig, amíg az egyezést. A 2. forgatókönyv szerint az előzőekben leírt, ha a létrehozott riasztás a keresési eredmények tartalmazzák a "Számítógép-01" és a "Számítógép-02", a teljes bejelentés le van tiltva (azaz nincs nincs értesítés minden létrehozott számítógép-02).

![Műveleti szabályokban és a naplóriasztások (eredmények száma)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

A legjobb emelés naplóriasztások művelet szabályokkal, azt javasoljuk, hogy a riasztások létrehozása a ["metrikamérési"](https://docs.microsoft.com/azure-monitor/platform/alerts-unified-log) lehetőséget. Ezzel a beállítással külön riasztás példányok jönnek létre a meghatározott csoport mező alapján. Majd a 2. forgatókönyv, különálló riasztás példány jön létre a "Számítógép-01" és "Számítógép-02". A művelet szabállyal, a forgatókönyvünkben ismertetett csak a "Számítógép-01" értesítést lenne letiltja, miközben a számítógép-02 értesítést aktiválódik a megszokott módon folytathatja.

![Műveleti szabályokban és a naplóriasztások (eredmények száma)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>GYIK

* K. Egy művelet szabály konfigurálása, során szeretném, egymással átfedésben lévő művelet az összes lehetséges, hogy el a duplikált értesítések szabályok megtekintéséhez. Az esetleges ehhez?

    A. Miután egy művelet szabály konfigurálása során meghatározott hatókör, láthatja az átfedésben lévő ugyanabban a hatókörben (ha vannak) művelet szabályok listáját. Ez átfedésben a következő lehetőségek egyike lehet:
    * Pontos egyezés: Például a művelet szabály definiálása és az átfedésben lévő művelet szabály van ugyanahhoz az előfizetéshez.
    * Egy részére: Például a definiálása művelet szabály egy adott előfizetés van, és az átfedésben lévő művelet szabály egy erőforráscsoportot az előfizetésen belül.
    * Felülbírálja: Például a művelet szabály határoz meg egy erőforráscsoportot, pedig az átfedésben lévő művelet szabály, amely tartalmazza az erőforráscsoport az előfizetésben.
    * Metszettel: Például a műveleti szabály definiálása a "VM1" és "VM2", pedig az átfedésben lévő művelet szabály a "VM2" és "Vm3 virtuális gép".

    ![Egymást átfedő műveleti szabályokban](media/alerts-action-rules/action-rules-overlapping.png)

* K. Amíg egy riasztási szabály konfigurálása tudni, hogy van-e már műveleti szabályokban meghatározva, amely a lehető I vagyok meghatározása a riasztási szabály előfordulhat, hogy cselekedhet?

    A. A célként megadott erőforrás a riasztási szabály határozza meg, ha a műveleti szabályokban, amely alatt az "Actions" szakaszban kattintson a "Nézet konfigurált műveleteket" reagálhat rájuk ugyanabban a hatókörben (ha van) listáját láthatja. Ez a lista a következő esetekben a hatókör alapján van feltöltve:
    * Pontos egyezés: Például a riasztási szabály definiálása és a művelet szabály van ugyanahhoz az előfizetéshez.
    * Egy részére: Például definiálása a riasztási szabály egy adott előfizetés, pedig a művelet a szabály az erőforráscsoport az előfizetésben.
    * Felülbírálja: Például ez a riasztási szabály, határoz meg egy erőforráscsoportot, és a művelet szabály, amely tartalmazza az erőforráscsoport az előfizetésben van.
    * Metszettel: Például a "VM1" és "VM2" Ez a riasztási szabály határoz meg, és a művelet szabály "VM2" és "Vm3 virtuális gép".
    
    ![Egymást átfedő műveleti szabályokban](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

* K. Láthatja, hogy egy művelet a szabály le van tiltva a riasztások?

    A. Az a [riasztások listája az oldal](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), van egy további oszlop, amely a "Mellőzés Status" nevű választható ki. Az értesítés egy riasztás példánya számára a rendszer mellőzte az, ha a lista jelenik meg ez az állapot.

    ![Letiltott riasztás példányok](media/alerts-action-rules/action-rules-suppressed-alerts.png)

* K. Ha a műveletcsoport műveleti szabály és a egy másik letiltás aktív az ugyanabban a hatókörben, mi történik?

    A. **Mellőzés always elsőbbséget ugyanabban a hatókörben lévő**.

* K. Mi történik, ha két külön műveletet szabályokat a figyelt erőforrások? Jelenik meg egy vagy két értesítések? Például "VM2" Ebben a forgatókönyvben:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1'
      action rule 'AR2' defined for 'VM2' and 'VM3' with action group 'AG1'

    A. A "VM1" és "Vm3 virtuális gép" minden riasztás egyszer lenne műveletcsoport "AG1" aktiválódik. Minden riasztás a "VM2", "AG1" műveletcsoport lenne kétszer aktivált (**műveleti szabályokban nem függeszthetők ismétlődő műveletek**). 

* K. Mi történik, ha van két külön műveletet szabályokat a figyelt erőforrások és a egy meghívja a művelet során figyelmen kívül hagyás egy másik? Ha például "VM2" Ebben a forgatókönyvben:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with suppression

    A. Minden riasztás a "VM1" "AG1" műveletcsoport lenne aktiválódik, egyszer. Műveletek és a "VM2" és "Vm3 virtuális gép" minden riasztás értesítéseket a rendszer letiltja. 

* K. Mi történik, ha van egy másik Műveletcsoportok hívása ugyanarra az erőforrásra vonatkozó definiált műveletet szabály és a egy riasztási szabályt? Ha például "VM1" Ebben a forgatókönyvben:

      alert rule  'rule1' on          'VM1' with action group 'AG2'
      action rule 'AR1'   defined for 'VM1' with action group 'AG1' 
 
    A. Minden riasztás a "VM1" "AG1" műveletcsoport lenne aktiválódik, egyszer. Riasztási szabály "felhasználóval a rule1" akkor aktiválódik, amikor ezen kívül is aktivál "AG2". **Műveletcsoportok meghatározott műveleti szabályokban és a riasztási szabályok egymástól függetlenül, a duplikátumok megszüntetése nem működnek**. 

## <a name="next-steps"></a>További lépések

- [További információ a riasztások az Azure-ban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
