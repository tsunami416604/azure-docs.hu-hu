---
title: Az Azure Monitor-riasztások műveletszabályai
description: Az Azure Monitor ban található műveletszabályok ismertetése, valamint konfigurálásuk és kezelésük.
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 0498325984641641d6dfc9ee6b89f66800b5c7d2
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546484"
---
# <a name="action-rules-preview"></a>Műveletszabályok (előzetes verzió)

A műveletszabályok segítségével definiálhatja vagy letilthatja a műveleteket bármely Azure Resource Manager hatókörben (Azure-előfizetés, erőforráscsoport vagy célerőforrás). Különböző szűrőkkel rendelkeznek, amelyek segítenek leszűkíteni a riasztási példányok adott részhalmazát, amelyet el szeretne látni.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]

## <a name="why-and-when-should-you-use-action-rules"></a>Miért és mikor érdemes akciószabályokat használni?

### <a name="suppression-of-alerts"></a>A riasztások elnyomása

Számos olyan forgatókönyv van, ahol hasznos letilthatja a riasztásokat generáló értesítéseket. Ezek a forgatókönyvek a tervezett karbantartási időszak alatti elnyomástól a munkaidőn kívüli letiltásig terjednek. A **ContosoVM-ért** felelős csapat például le szeretné nyomni a közelgő hétvégére vonatkozó riasztási értesítéseket, mert a **ContosoVM** tervezett karbantartáson megy keresztül. 

Bár a csapat manuálisan letilthatja a **ContosoVM-en** konfigurált riasztási szabályokat (és a karbantartás után újra engedélyezheti), ez nem egy egyszerű folyamat. A műveletszabályok segítségével nagy méretekben határozhatja meg a riasztások elnyomását, és rugalmasan konfigurálhatja az elnyomás időszakát. Az előző példában a csapat definiálhat egy műveletszabályt a **ContosoVM-en,** amely letiltja a hétvégén az összes riasztási értesítést.


### <a name="actions-at-scale"></a>Nagyságrendi műveletek

Bár a riasztási szabályok segítségével meghatározhatja a műveletcsoport, amely elindítja a riasztás létrehozásakor, az ügyfelek gyakran egy közös műveletcsoport a műveletek körében. Például egy csapat felelős az erőforráscsoport **ContosoRG** valószínűleg ugyanazt a műveletcsoportot határozza meg a **ContosoRG-ben**meghatározott összes riasztási szabályok. 

A műveletszabályok segítenek a folyamat egyszerűsítése érdekében. A műveletek nagy léptékben történő meghatározásával egy műveletcsoport aktiválható a konfigurált hatókörön létrehozott riasztásokhoz. Az előző példában a csapat definiálhat egy műveletszabályt a **ContosoRG-on,** amely ugyanazt a műveletcsoportot indítja el a benne létrehozott összes riasztáshoz.

> [!NOTE]
> A műveletszabályok jelenleg nem vonatkoznak az Azure Service Health-riasztásokra.

## <a name="configuring-an-action-rule"></a>Műveleti szabály konfigurálása

A funkciót úgy érheti el, hogy az Azure Monitor Riasztások céllapján a **Műveletek** kezelése lehetőséget **választja.** Ezután válassza **a Műveletszabályok (előzetes verzió) lehetőséget.** A szabályokeléréséhez válassza a **Műveletszabályok (előzetes verzió)** lehetőséget a céloldal irányítópultján a riasztásokhoz.

![Műveletszabályok az Azure Monitor céloldaláról](media/alerts-action-rules/action-rules-landing-page.png)

Válassza a **+ Új műveletszabály lehetőséget**. 

![Új műveletszabály hozzáadása](media/alerts-action-rules/action-rules-new-rule.png)

Másik lehetőségként létrehozhat egy műveletszabályt egy riasztási szabály konfigurálása közben.

![Új műveletszabály hozzáadása](media/alerts-action-rules/action-rules-alert-rule.png)

Most látnia kell a műveletszabályok létrehozásához szolgáló folyamatlapot. Konfigurálja a következő elemeket: 

![Új műveletszabály-létrehozási folyamat](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Hatókör

Először válassza ki a hatókört (Azure-előfizetés, erőforráscsoport vagy célerőforrás). A hatókörök kombinációját is többlehetőségközül választhatja ki egyetlen előfizetésen belül.

![Műveletszabály hatóköre](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Szűrési feltételek

Ezenkívül szűrőket is megadhat, hogy a riasztások egy adott részhalmazára szűkítse őket. 

A rendelkezésre álló szűrők a következők: 

* **Súlyosság**: Egy vagy több riasztási súlyosság kiválasztásának lehetősége. **Súlyossági = Sev1** azt jelenti, hogy a műveletszabály az Sev1 értékre beállított összes riasztásra alkalmazható.
* **Monitor Szolgáltatás**: Az eredeti figyelőszolgáltatáson alapuló szűrő. Ez a szűrő is többszörösen kijelöli. Például **a Figyelő szolgáltatás = "Application Insights"** azt jelenti, hogy a műveletszabály minden Application Insights-alapú riasztások alkalmazható.
* **Erőforrástípusa**: Adott erőforrástípuson alapuló szűrő. Ez a szűrő is többszörösen kijelöli. Például **erőforrástípusa = "Virtuális gépek"** azt jelenti, hogy a műveletszabály minden virtuális gépre alkalmazható.
* **Riasztási szabály azonosítója**: Adott riasztási szabályok szűrésére szolgáló lehetőség a riasztási szabály Erőforrás-kezelő azonosítójának használatával.
* **Figyelő feltétele**: Riasztási példányok szűrője, amelynek figyelőfeltétele **az Elsütött vagy** **a Feloldott.**
* **Leírás**: Regex (reguláris kifejezés) egyezés, amely meghatározza a karakterlánc egyezését a leírás, a riasztási szabály részeként definiálva. Például **a Leírás tartalmazza a "prod"** minden olyan riasztást, amely a leírásokban a "prod" karakterláncot tartalmazza.
* **Riasztási környezet (hasznos adat)**: Regex-egyezés, amely meghatározza a riasztási tartalom riasztási környezet mezőivel való karakterlánc-egyezést. Például **a "Computer-01" (a továbbiakban: "Számítógép-01" tartalmat tartalmazó riasztási környezet** megfelel minden olyan riasztásnak, amelynek hasznos tartalma tartalmazza a "Computer-01" karakterláncot.

Ezeket a szűrőket a rendszer egymással együtt alkalmazza. Ha például az **erőforrás típusát" = Virtuális gépek** és **súlyossága' = Sev0,** majd szűrte az összes **Sev0** riasztások csak a virtuális gépek. 

![Műveletszabály-szűrők](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Elnyomás vagy műveletcsoport konfigurációja

Ezután konfigurálja a műveletszabályt a riasztáselnyomás vagy a műveletcsoport támogatásához. Nem választhatod mindkettőt. A konfiguráció a korábban definiált hatókörnek és szűrőknek megfelelő összes riasztási példányon működik.

#### <a name="suppression"></a>Elnyomása

Ha a letiltás lehetőséget **választja,** konfigurálja a műveletek és értesítések elnyomásának időtartamát. Válasszon egyet az alábbi lehetőségek közül:
* **Mostantól (mindig)**: Az összes értesítés letiltása határozatlan ideig.
* **Ütemezett időpontban:** Letiltja az értesítéseket egy határolt időtartamon belül.
* **Ismétlődés esetén**: Letiltja az ismétlődő napi, heti vagy havi ütemezésre vonatkozó értesítéseket.

![Műveletszabály elnyomása](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Műveletcsoport

Ha a váltóban a **Művelet csoport** lehetőséget választja, vegyen fel egy meglévő műveletcsoportot, vagy hozzon létre egy újat. 

> [!NOTE]
> Egy műveletszabályhoz csak egy műveletcsoportot társíthat.

![Műveletcsoport kijelölésével új műveletszabály hozzáadása vagy létrehozása](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Műveletszabály részletei

Végül konfigurálja a következő részleteket a műveletszabályhoz:
* Név
* Erőforráscsoport, amelyben a mentésre kerül
* Leírás 

## <a name="example-scenarios"></a>Példaforgatókönyvek

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>1. forgatókönyv: A riasztások súlyossága alapján való letiltása

A Contoso minden hétvégén le szeretné nyomni az összes Sev4-riasztás értesítését a **ContosoSub** előfizetésen belüli összes virtuális gépről.

**Megoldás:** Műveletszabály létrehozása a következővel:
* Hatókör = **ContosoSub**
* Szűrők
    * Súlyosság = **Sev4**
    * Erőforrás típusa = **Virtuális gépek**
* Elnyomás az ismétlődésheti, **valamint a szombat** és a **vasárnap** ellenőrzésével

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>2. forgatókönyv: A riasztások riasztási környezeten alapuló riasztási környezet (hasznos adat) letiltása

A Contoso le szeretné nyomni a **Számítógép-01-hez** a **ContosoSub-ban** létrehozott összes naplóriasztásértesítését határozatlan ideig, mivel karbantartáson megy keresztül.

**Megoldás:** Műveletszabály létrehozása a következővel:
* Hatókör = **ContosoSub**
* Szűrők
    * Figyelő szolgáltatás = **Log Analytics**
    * Riasztási környezet (hasznos adat) **tartalmazza computer-01**
* Elnyomás beállítása **mostantól (Mindig)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>3. forgatókönyv: Erőforráscsoportban definiált műveletcsoport

A Contoso [egy időszakriasztást](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)hozott meg egy előfizetési szinten. De azt akarja, hogy meghatározza a műveleteket, amelyek kifejezetten a **ContosoRG**erőforráscsoportból létrehozott riasztások hoz létre.

**Megoldás:** Műveletszabály létrehozása a következővel:
* Hatókör = **ContosoRG**
* Nincsenek szűrők
* A műveletcsoport **beállítása ContosoActionGroup**

> [!NOTE]
> *A műveletszabályokban és riasztási szabályokban meghatározott műveletcsoportok egymástól függetlenül működnek, deduplikáció nélkül.* A korábban ismertetett forgatókönyvben, ha egy műveletcsoport van definiálva a riasztási szabályhoz, akkor a műveletszabályban meghatározott műveletcsoporttal együtt aktiválódik. 

## <a name="managing-your-action-rules"></a>A műveletszabályok kezelése

A műveletszabályokat a listanézetből tekintheti meg és kezelheti:

![Műveletszabályok listanézete](media/alerts-action-rules/action-rules-list-view.png)

Itt engedélyezheti, letilthatja vagy törölheti a műveletszabályokat méretarányosan, ha bejelöli a mellettük lévő jelölőnégyzetet. Amikor kijelöl egy műveletszabályt, megnyílik annak konfigurációs lapja. A lap segítségével frissítheti a műveletszabály definícióját, és engedélyezheti vagy letilthatja azt.

## <a name="best-practices"></a>Ajánlott eljárások

Az [eredmények számával](alerts-unified-log.md) létrehozott naplóriasztások egyetlen riasztási példányt hoznak létre a teljes keresési eredmény használatával (amely több számítógépen is átnyúlhat). Ebben a forgatókönyvben, ha egy műveletszabály használja a **riasztási környezet (hasznos adat)** szűrő, akkor a riasztási példány, amíg van egy egyezés. A korábban ismertetett 2. **Computer-01** **Computer-02** A **Computer-02-hez** egyáltalán nem jött létre értesítés.

![Cselekvési szabályok és naplóriasztások (eredmények száma)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

A naplóriasztások műveletszabályokkal való legjobb használatához hozzon létre naplóriasztásokat a [metrikamérési](alerts-unified-log.md) beállítással. Ez a beállítás külön riasztási példányokat hoz létre a megadott csoportmező alapján. Ezután a **2.** **Computer-01** A forgatókönyvben leírt műveletszabály miatt csak a **Computer-01** értesítése lesz letiltva. A **computer-02** értesítése a szokásos módon folytatódik.

![Cselekvési szabályok és naplóriasztások (eredmények száma)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>GYIK

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Műveletszabály konfigurálása közben szeretném látni az összes lehetséges egymást átfedő műveletszabályt, hogy elkerüljem az ismétlődő értesítéseket. Lehetséges ezt megtenni?

Miután egy hatókört definiált egy műveletszabály konfigurálásakor, megtekintheti az ugyanazon hatókörön (ha van ilyen) átfedő műveletszabályok listáját. Ez az átfedés a következő lehetőségek egyike lehet:

* Pontos egyezés: Például a definiált műveletszabály és az egymást átfedő műveletszabály ugyanazon az előfizetésen van.
* Egy részhalmaz: Például a műveletszabály, amelyet definiál, egy előfizetésen van, és az egymást átfedő műveletszabály az előfizetésen belüli erőforráscsoporton van.
* Egy szuperhalmaz: Például a végrehajtott műveletszabály egy erőforráscsoporton van, és az egymást átfedő műveletszabály az erőforráscsoportot tartalmazó előfizetésen van.
* Metszéspont: Például a definiált műveletszabály **a VM1** és a **VM2,** az egymást átfedő műveletszabály pedig **a VM2** és a **VM3.**

![Egymást átfedő műveletszabályok](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Egy riasztási szabály konfigurálása közben megállapítható-e, hogy vannak-e már olyan műveletszabályok, amelyek a meghatározandó riasztási szabály szerint működhetnek?

Miután definiálta a célerőforrást a riasztási szabályhoz, megtekintheti az azonos hatókörön (ha van ilyen) ható műveletszabályok listáját, ha a Műveletek szakaszban a **Konfigurált műveletek megtekintése** lehetőséget **választja.** Ez a lista a hatókör alábbi forgatókönyvei alapján kerül feltöltésre:

* Pontos egyezés: Például a meghatározott riasztási szabály és a műveletszabály ugyanazon az előfizetésen van.
* Egy részhalmaz: Például a riasztási szabály, amelyet definiál, egy előfizetésen van, és a műveletszabály az előfizetésen belüli erőforráscsoporton van.
* Egy szuperhalmaz: Például a beállított riasztási szabály egy erőforráscsoporton van, és a műveletszabály az erőforráscsoportot tartalmazó előfizetésen van.
* Metszéspont: Például a definiált riasztási szabály **a VM1** és a **VM2,** a műveletszabály pedig **VM2** és **VM3.**
    
![Egymást átfedő műveletszabályok](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Láthatom a műveletszabály által letiltott riasztásokat?

A [riasztások listájában](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)egy további oszlopot választhat, amelynek neve **Elnyomás állapota**. Ha egy riasztási példány értesítése le van tiltva, akkor ezt az állapotot a listában jeleníti meg.

![Letiltott riasztási példányok](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Ha van egy műveletszabály egy műveletcsoporttal, és egy másik, amelynek elnyomása aktív ugyanazon a hatókörön, mi történik?

Az elnyomás mindig azonos hatókört élvez.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Mi történik, ha két külön műveletszabályban figyelt egy erőforrás? Kapok egy vagy két értesítést? Például a **VM2** a következő forgatókönyv:

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

A VM1 és a VM3 minden riasztás, az AG1 műveletcsoport egyszer aktiválódna. A **VM2**minden riasztása esetén az AG1 műveletcsoport kétszer aktiválódik, mert a műveletszabályok nem duplikálják a műveleteket. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Mi történik, ha egy erőforrást két külön műveletszabály figyel, és az egyik cselekvésre szólít fel, míg a másik a letiltásra? Például a **VM2** a következő forgatókönyv:

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

A VM1 minden riasztás, a műveletcsoport AG1 egyszer aktiválódna. Műveletek és értesítések a VM2 és a VM3 minden riasztást le lesz tiltva. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Mi történik, ha egy riasztási szabály és egy műveletszabály definiálva van ugyanahhoz az erőforráshoz, amely különböző műveletcsoportokat hív meg? Például a **VM1** a következő forgatókönyv:

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
A VM1 minden riasztás, a műveletcsoport AG1 egyszer aktiválódna. Amikor a "rule1" riasztási szabály aktiválódik, az AG2-t is elindítja. A műveletszabályokban és riasztási szabályokban meghatározott műveletcsoportok egymástól függetlenül működnek, deduplikáció nélkül. 

## <a name="next-steps"></a>További lépések

- [További információ az Azure-beli riasztásokról](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
