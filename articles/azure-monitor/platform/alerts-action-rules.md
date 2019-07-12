---
title: Az Azure Monitor riasztások műveleti szabályokban
description: Mik azok a műveleti szabályokban a Azure Monitor és hogyan konfigurálhatja és kezelheti őket ismertetése.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: df069ee398ea2937f03765b10576061b5e541390
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656724"
---
# <a name="action-rules-preview"></a>Műveleti szabályokban (előzetes verzió)

Műveleti szabályokban segítségével határozza meg, vagy mellőzhet műveletek (Azure-előfizetés, erőforráscsoport vagy célerőforrás) minden olyan Azure Resource Manager hatókörben. Ahhoz, amelyhez leszűkítése riasztási példányok adott részéhez tartozó súgó különböző szűrők rendelkeznek.

## <a name="why-and-when-should-you-use-action-rules"></a>Miért és mikor érdemes műveleti szabályokban használni?

### <a name="suppression-of-alerts"></a>A riasztások letiltása

Nincsenek számos olyan forgatókönyvekben, ahol azt javasoljuk, hogy az értesítéseket generáló értesítéseinek elrejtése. Ezen forgatókönyvek köre a tervezett karbantartási időszak közbeni elnyomáshoz tiltási munkaidőn kívüli időszakban. Ha például a felelős csapat **ContosoVM** szeretne tiltja le a riasztási értesítések a közelgő hétvégi, mert **ContosoVM** tervezett karbantartás alatt áll. 

Bár a csapat letilthatja egyes riasztási szabályt, amely konfigurálva van **ContosoVM** manuálisan (és a karbantartás után újra engedélyezheti) már nem egy egyszerű folyamattal. Műveleti szabályokban segítségével meghatározhatja a nagy mennyiségű riasztás letiltása és rugalmasan állítsa be a tiltási lehetővé teszi. Az előző példában a csapata egy művelet szabály definiálhat a **ContosoVM** minden riasztási értesítés, amely elrejti a a hétvégi számára.


### <a name="actions-at-scale"></a>Műveletek ipari méretekben

Bár a riasztási szabályok segítségével meghatározhatja a a műveletcsoport, amely, amikor a riasztás akkor jön létre, a felhasználóknak gyakran kell egy közös műveletcsoportot hatókörébe tartozó műveletek között. Ha például az erőforráscsoport felelős csapat **ContosoRG** valószínűleg határozza meg a meghatározott összes riasztási szabályt ugyanabban műveletcsoportot **ContosoRG**. 

Műveleti szabályokban segítenek a folyamat leegyszerűsítése érdekében. Nagy mennyiségű műveletek meghatározásával műveletcsoport minden riasztást, a hatókörbe tartozó is elindítható. Az előző példában a csapata egy művelet szabály definiálhat a **ContosoRG** , amelyek aktiválják az azonos műveletcsoportot belül létrehozott összes riasztás.

> [!NOTE]
> Műveleti szabályokban jelenleg nem vonatkoznak az Azure Service Health-riasztások.

## <a name="configuring-an-action-rule"></a>Egy művelet szabály konfigurálása

A funkció eléréséhez kiválasztásával **műveletek kezelése** származó a **riasztások** az Azure monitorban kezdőlapja. Ezután válassza ki **művelet szabályok (előzetes verzió)** . A szabályok kiválasztásával érheti **művelet szabályok (előzetes verzió)** az irányítópultról, riasztások kezdőlapjának alkotóelemeit.

![Az Azure Monitor kezdőlapja a műveleti szabályokban](media/alerts-action-rules/action-rules-landing-page.png)

Válassza ki **+ új művelet szabály**. 

![A művelet új szabály hozzáadása](media/alerts-action-rules/action-rules-new-rule.png)

Azt is megteheti létrehozhat egy művelet szabály riasztási szabály konfigurálásakor.

![A művelet új szabály hozzáadása](media/alerts-action-rules/action-rules-alert-rule.png)

Meg kell jelennie a folyamat lap művelet szabályok létrehozásához. Az alábbi elemeket konfigurálja: 

![Új művelet szabály létrehozási folyamat](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Scope

Először válassza ki a hatókör (Azure-előfizetés, erőforráscsoport vagy célként megadott erőforrás). Akkor is is többszörös kijelölést egyetlen előfizetésben jön létre a hatókör kombinációját.

![A művelet szabály hatóköre](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Szűrési feltétel

Emellett megadhatja a szűrők szűkítéséhez őket egy adott részének a riasztások le. 

A rendelkezésre álló szűrők a következők: 

* **Súlyosság**: Lehetőség van egy vagy több riasztás súlyossági szint kiválasztása. **Súlyosság = Sev1** azt jelenti, hogy a művelet szabály alkalmazható az összes riasztás Sev1 beállítása.
* **Szolgáltatás figyelése**: A származó figyelési szolgáltatás alapján történő szűrés. Ez a szűrő abban is többszörös kijelölést. Ha például **Monitor Service = "Application Insights"** azt jelenti, hogy a művelet a szabály az Application Insights-alapú értesítések esetén alkalmazható.
* **Erőforrástípus**:  Egy adott erőforrástípusra alapján történő szűrés. Ez a szűrő abban is többszörös kijelölést. Ha például **erőforrástípus = "Virtual Machines"** azt jelenti, hogy a művelet a szabály minden virtuális gépekre alkalmazható.
* **Riasztási szabály azonosítója**: Egy konkrét riasztási szabályok használatával a Resource Manager-Azonosítót a riasztási szabály szerinti szűréshez használandó beállítást.
* **Az állapot figyelése**:  Szűrheti a riasztási példányok mindkettővel **Fired** vagy **Megoldva** , a figyelési feltétel.
* **Leírás**: Reguláris kifejezés (reguláris kifejezés) egyezést, amely meghatározza a leírás, a riasztási szabály részeként megadott karakterlánc egyezést. Ha például **leírása tartalmazza a 'prod'** egyezni fog a hozzájuk tartozó leírások az összes olyan riasztásokat, amelyek tartalmazzák az "éles".
* **Riasztás a környezetben (tartalom)** : Reguláris kifejezéssel egyező, amely meghatározza a riasztás környezete mezőket egy riasztás hasznos karakterlánc egyezést. Ha például **riasztási környezet (tartalom) tartalmazza a "Számítógép-01"** egyezést fog mutatni minden riasztás, amelynek is észleltünk adattartalmakat. tartalmazzák a "Számítógép-01."

Ezek a szűrők egymással összefüggésben érvényesek. Például, ha a beállított **erőforrás típusa a(z) = a virtuális gépek** és **súlyossági "= Sev0**, majd minden szűrés **Sev0** riasztásokat csak a virtuális gépeken. 

![A művelet szabály szűrők](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Tiltási vagy műveleti csoport konfigurálása

Ezután konfigurálja a művelet a szabály riasztás letiltása vagy a művelet csoporttámogatás. Mindkettő nem választható. A konfiguráció az összes riasztás példányok, amelyek megfelelnek a korábban meghatározott hatókör és a szűrők funkcionál.

#### <a name="suppression"></a>Tiltási

Ha **tiltási**, műveleteket és értesítések a Mellőzés időtartamának beállítása. Válasszon egyet az alábbi lehetőségek közül:
* **Múlva (mindig)** : Minden értesítés határozatlan időre letiltja.
* **Ütemezett időpontban**: Értesítések elrejti egy korlátozott időtartamú belül.
* **Az ismételt**: Elrejti az ismétlődő napi, heti vagy havi ütemezés szerint értesítések.

![A művelet szabály letiltása](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Műveletcsoport

Ha **műveletcsoport** a váltógomb vagy meglévő műveletcsoport hozzáadása vagy hozzon létre egy újat. 

> [!NOTE]
> Csak egy műveletcsoport társíthatja egy művelet szabályt.

![Hozzáadhat vagy létrehozhat új művelet szabály műveleti csoport kiválasztásával](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>A művelet szabály részletei

Legutóbbi a következő adatokat, a művelet szabály konfigurálása:
* Name (Név)
* Az erőforráscsoport, amelyben mentve
* Leírás 

## <a name="example-scenarios"></a>Példaforgatókönyvek

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>forgatókönyv 1: A riasztások súlyosság alapján letiltása

A Contoso biztosítani szeretné a az előfizetésen belüli összes virtuális gép összes Sev4 riasztásaihoz értesítéseinek elrejtése **ContosoSub** minden hétvégi.

**Megoldás:** A művelet szabály létrehozása:
* Hatókör = **ContosoSub**
* Szűrők
    * Súlyosság = **Sev4**
    * Erőforrástípus = **virtuális gépek**
* Az ismétlődési beállítása heti, tiltási és **szombat** és **vasárnap** be van jelölve

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>2\. forgatókönyv: A riasztás környezete (tartalom) alapuló riasztások letiltása

A Contoso biztosítani szeretné az összes naplófájl az riasztások értesítéseinek elrejtése **számítógép-01** a **ContosoSub** határozatlan ideig, mert megy keresztül karbantartás.

**Megoldás:** A művelet szabály létrehozása:
* Hatókör = **ContosoSub**
* Szűrők
    * Szolgáltatás figyelése = **Log Analytics**
    * Riasztás a környezetben (tartalom) tartalmaz **számítógép-01**
* Mellőzés beállítása **múlva (mindig)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>3\. forgatókönyv: Műveletcsoport meg van határozva egy erőforráscsoportot

Contoso definiált [előfizetés szintjén metrikariasztás](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Csak akkor szeretne kimondottan az erőforráscsoport által előállított riasztásokat kiváltó műveletek meghatározása **ContosoRG**.

**Megoldás:** A művelet szabály létrehozása:
* Hatókör = **ContosoRG**
* Nincs szűrő
* Műveletcsoport beállítása **ContosoActionGroup**

> [!NOTE]
> *Műveletcsoportok meghatározott műveleti szabályokban és a riasztási szabályok egymástól függetlenül, nem deduplikált működnek.* A bemutatott forgatókönyvben korábbi, ha a műveletcsoport van definiálva a riasztási szabályt, a művelet a műveleti-szabályban megadott csoporttal együtt elindítja. 

## <a name="managing-your-action-rules"></a>A művelet szabályok kezelése

Megtekintheti és kezelheti a műveleti szabályokban a lista nézet:

![Műveleti szabályokban listanézet](media/alerts-action-rules/action-rules-list-view.png)

Itt engedélyezheti, tiltsa le, vagy műveleti szabályokban ipari méretekben törli a jelölőnégyzet bejelölésével mellettük. Amikor kiválaszt egy művelet szabályt, a konfigurációs lapon nyílik meg. Az oldal segítséget nyújt a műveletet határoz meg szabálydefiníciót, és engedélyezze vagy tiltsa le azt.

## <a name="best-practices"></a>Ajánlott eljárások

Naplóriasztások hoz létre a [eredmények száma](alerts-unified-log.md) beállítás az egész keresési eredmény (ami előfordulhat, hogy több számítógép között span) használatával hozzon létre riasztási egypéldányos. Ebben a forgatókönyvben, ha egy művelet szabályt használ a **riasztási környezet (tartalom)** szűrő működik a riasztási példányon mindaddig, amíg az egyezést. 2\. forgatókönyv esetében, a korábban leírt, ha a létrehozott riasztás a keresési eredmények tartalmazzák **számítógép-01** és **számítógép-02**, a teljes bejelentés le lesz tiltva. Nem kap értesítést generál **számítógép-02** egyáltalán.

![Műveleti szabályokban és a naplóriasztások (eredmények száma)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Naplóriasztások legjobb használata műveleti szabályokban, a riasztások létrehozása a [metrikamérési](alerts-unified-log.md) lehetőséget. Külön riasztás példányok alapján ezt a beállítást, a meghatározott csoport mező alapján jönnek létre. Ezt követően a 2. forgatókönyv esetében, különálló riasztás példány jön létre **számítógép-01** és **számítógép-02**. A művelet szabály miatt a forgatókönyv csak az értesítési ismertetett **számítógép-01** le lesz tiltva. Az értesítés a **számítógép-02** továbbra is a megszokott módon aktiválódik.

![Műveleti szabályokban és a naplóriasztások (eredmények száma)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>GYIK

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Amíg műveleti szabály vagyok beállításához szeretnék kapni az egymást átfedő műveleti szabályokban, az összes lehetséges megtekintéséhez, hogy el a duplikált értesítések. Az ennek lehetséges?

Miután egy műveletet a szabály konfigurálását a hatókör határozza meg, láthatja, amely átfedésben meg ugyanabban a hatókörben (ha vannak) művelet szabályok listáját. Ez átfedésben a következő lehetőségek egyike lehet:

* Pontos egyezés: Például a művelet szabály most definiálja, és az átfedésben lévő művelet szabály van ugyanahhoz az előfizetéshez.
* Részhalmaz: Például a művelet szabály, amely akkor van egy adott előfizetés, és az átfedésben lévő művelet szabály egy erőforráscsoportot az előfizetésen belül.
* Bővített halmaz: Például a műveleti szabály most definiálja az erőforráscsoport, pedig az átfedésben lévő művelet szabály, amely tartalmazza az erőforráscsoport az előfizetésben.
* Metszet: A művelet szabály most definiálja van például **VM1** és **VM2**, és az átfedésben lévő művelet szabály **VM2** és **vm3 virtuális gép**.

![Egymást átfedő műveleti szabályokban](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Amíg a riasztási szabály vagyok beállításához az előfordulhat, hogy a riasztási szabály I vagyok meghatározása a járhat el lehet tudni, hogy van-e már műveleti szabályokban meghatározva, amely?

A riasztási szabály meghatározása a célként megadott erőforrás, után reagálhat rájuk ugyanabban a hatókörben (ha vannak) kiválasztásával művelet szabályok listája látható **nézet konfigurált műveleteket** alatt a **műveletek** szakaszban. Ez a lista a következő esetekben a hatókör alapján van feltöltve:

* Pontos egyezés: Például a riasztási szabályt, amely akkor és a művelet szabály van ugyanahhoz az előfizetéshez.
* Részhalmaz: Például most definiálja a riasztási szabály egy adott előfizetés, pedig a művelet a szabály az erőforráscsoport az előfizetésben.
* Bővített halmaz: Például most definiálja a riasztási szabályt az erőforráscsoport, pedig a művelet szabály, amely tartalmazza az erőforráscsoport az előfizetésben.
* Metszet: Például a riasztási szabályt, amely akkor lesz a **VM1** és **VM2**, és a művelet szabály **VM2** és **VM3**.
    
![Egymást átfedő műveleti szabályokban](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Láthatja, hogy egy művelet a szabály le van tiltva a riasztások?

Az a [riasztások listája az oldal](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), kiválaszthatja, hogy egy további oszlopot nevű **tiltási állapotának**. Az értesítés egy riasztás példánya számára a rendszer mellőzte az, ha a lista jelenik meg ez az állapot.

![Letiltott riasztás példányok](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Ha a műveletcsoport műveleti szabály és a egy másik letiltás aktív az ugyanabban a hatókörben, mi történik?

Mellőzés always élvez elsőbbséget a ugyanabban a hatókörben.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Mi történik, ha két külön műveletet szabályokat a figyelt erőforrás van? Jelenik meg egy vagy két értesítések? Ha például **VM2** a következő esetben:

      "action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1"

A VM1 és vm3 virtuális gép minden értesítés egyszer lenne műveletcsoport AG1 aktiválódik. A minden riasztás **VM2**, műveletcsoport AG1 akkor aktiválódik, kétszer, mert műveleti szabályokban nem deduplikálása műveleteket. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Mi történik, ha van két külön műveletet szabályokat a figyelt erőforrások és a egy meghívja a művelet során figyelmen kívül hagyás egy másik? Ha például **VM2** a következő esetben:

      "action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression"

Minden riasztás a VM1 egyszer lenne műveletcsoport AG1 aktiválódik. Műveletek és a VM2 és vm3 virtuális gép minden egyes riasztás értesítéseket a rendszer letiltja. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Mi történik, ha van egy másik Műveletcsoportok hívása ugyanarra az erőforrásra vonatkozó definiált műveletet szabály és a egy riasztási szabályt? Ha például **VM1** a következő esetben:

      "alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1" 
 
Minden riasztás a VM1 egyszer lenne műveletcsoport AG1 aktiválódik. Riasztási szabály "felhasználóval a rule1" akkor aktiválódik, amikor is kezdeményezi AG2 emellett. Műveletcsoportok meghatározott műveleti szabályokban és a riasztási szabályok egymástól függetlenül, nem deduplikált működnek. 

## <a name="next-steps"></a>További lépések

- [További információ a riasztások az Azure-ban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
