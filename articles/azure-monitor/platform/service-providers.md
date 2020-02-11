---
title: A szolgáltatók naplófájljainak Azure Monitor | Microsoft Docs
description: Azure Monitor naplók segíthetnek a felügyelt szolgáltatók (MSP-k), a nagyvállalatok, a független szoftvergyártók (ISV-k) és az üzemeltetési szolgáltatók számára a kiszolgálók kezelésében és figyelésében az ügyfél helyszíni vagy Felhőbeli infrastruktúrájában.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: 1b5c181569ea569ecc4808284683501cb20f7bf5
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117061"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Szolgáltatók Azure Monitor naplófájljai

Log Analytics munkaterületek Azure Monitor segíthetnek a felügyelt szolgáltatók (MSP-k), a nagyvállalatok, a független szoftvergyártók (ISV-k) és az üzemeltetési szolgáltatók számára, hogy az ügyfél helyszíni vagy Felhőbeli infrastruktúrájában kezeljék és felügyeljék a kiszolgálókat.

A nagyobb cégeknek is megoszthatja számos Hasonlóságok szolgáltatók, különösen akkor, ha van egy központi informatikai csoportját, amelyek kezeléséért felelős informatikai számos különböző üzleti egységek számára. Az egyszerűség kedvéért ez a dokumentum a *szolgáltató* kifejezést használja, de ugyanez a funkció a vállalatok és más ügyfelek számára is elérhető.

A [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) program részét képező partnerek és szolgáltatók esetében a log Analytics Azure monitor az Azure CSP-előfizetésekben elérhető Azure-szolgáltatások egyike.

A Azure Monitor Log Analyticst egy, az Azure-beli Azure-beli felügyelt erőforrás-kezelési képességgel rendelkező szolgáltató is használhatja az [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview)-ban.

## <a name="architectures-for-service-providers"></a>A szolgáltatók számára architektúrák

Log Analytics munkaterületek lehetővé teszik a rendszergazdák számára a [naplózási](data-platform-logs.md) folyamatok áramlását és elkülönítését, valamint az adott üzleti igényeknek megfelelő architektúra létrehozását. [Ez a cikk](design-logs-deployment.md) a munkaterület tervezési, üzembe helyezési és áttelepítési szempontjait ismerteti, és a [hozzáférés kezelése](manage-access.md) című cikk ismerteti, hogyan kell alkalmazni és kezelni a naplózási engedélyeket. Szolgáltatók további szempontokat kell.

Nincsenek három lehetséges architektúrák szolgáltatók Log Analytics-munkaterületek vonatkozásában:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. az elosztott naplók az ügyfél bérlője területén található munkaterületeken tárolódnak

Ebben az architektúrában a munkaterület az ügyfél-bérlőben, hogy az ügyfél a naplókhoz használt van telepítve.

A szolgáltatói rendszergazdák kétféleképpen férhetnek hozzá egy Log Analytics munkaterülethez az ügyfél bérlője számára:

- Az ügyfél [Azure Active Directory vendég felhasználóként (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)veheti fel az egyes felhasználókat a szolgáltatótól. A szolgáltató rendszergazdáinak be kell jelentkezniük az egyes ügyfelek könyvtárába a Azure Portal, hogy el tudják érni ezeket a munkaterületeket. Ehhez az is szükséges, hogy az ügyfelek az egyes szolgáltatók rendszergazdáinak egyéni hozzáférését kezeljék.
- A jobb méretezhetőség és rugalmasság érdekében a szolgáltatók az [Azure világítótorony](https://docs.microsoft.com/azure/lighthouse/overview) Azure-beli [delegált erőforrás-kezelési](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) funkciójának használatával érhetik el az ügyfél bérlőjét. Ezzel a módszerrel a szolgáltatói rendszergazdák egy Azure AD felhasználói csoportba tartoznak a szolgáltató bérlője számára, és ez a csoport a bevezetési folyamat során kap hozzáférést minden ügyfél számára. Ezek a rendszergazdák ezután hozzáférhetnek az egyes ügyfelek munkaterületeihez a saját szolgáltatói bérlőn belül, és nem kell egyenként bejelentkezniük az egyes ügyfelek bérlői számára. Az ügyfelek Log Analytics munkaterületek erőforrásainak ily módon való elérése csökkenti az ügyfél oldalán szükséges munkát, és megkönnyíti az adatok összegyűjtését és elemzését ugyanazon szolgáltató által felügyelt eszközökön keresztül, például [Azure monitor munkafüzetek](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview)használatával. További információ: az [ügyfelek erőforrásainak figyelése nagy méretekben](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale).

Az elosztott architektúra előnyei a következők:

* Az ügyfél megerősítheti bizonyos szintű engedélyeket az [Azure-beli delegált erőforrás-kezelési](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)szolgáltatáson keresztül, vagy a saját [szerepköralapú hozzáférésével](https://docs.microsoft.com/azure/role-based-access-control/overview)kezelheti a naplóhoz való hozzáférést.
* A naplók bármilyen típusú erőforrásból gyűjthetők, nem csak az ügynök-alapú virtuálisgép-adatok. Ha például az Azure vizsgálati naplók.
* Minden ügyfél saját munkaterület megőrzésére és az adatok kapacitástól különböző beállítással is rendelkezhetnek.
* Az ügyfelek közötti elkülönítés szabályozási és a megfelelőség érdekében.
* Minden munkaterülethez a díja az ügyfél-előfizetés be lesz állítva.

Az elosztott architektúra hátrányai a következők:

* Az olyan eszközök, mint például a Azure Monitor munkafüzetek, központilag jeleníthető meg és elemezhető az ügyfél-bérlők között, ami lassabb élményt eredményezhet, különösen az adatok több mint 50 + munkaterületen való elemzésekor.
* Ha az ügyfeleket nem az Azure-beli delegált erőforrás-kezeléshez rendeli, a szolgáltatói rendszergazdákat az ügyfél címtárában kell kiépíteni, és a szolgáltató számára nehezebb a nagyszámú ügyfél-bérlő kezelése egyszerre.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. a központi naplók tárolása a szolgáltatói bérlőben található munkaterületen történik

Ebben az architektúrában a naplók nem tárolódnak, a vevő bérlők számára, de csak egy központi helyen belül a szolgáltató előfizetések egyike. Az ügyfél virtuális gépeken telepített ügynökök a naplók elküldése a munkaterület Azonosítójára és a titkos kulcs használatával vannak konfigurálva.

A központosított architektúra előnyei a következők:

* Az ügyfelek nagy számú kezelhet, és integrálhatja őket különböző háttérrendszerekhez, könnyebbé vált.
* A szolgáltató rendelkezik teljes körű tulajdonjogát a naplókból és a különböző összetevőket, például a functions és a mentett lekérdezések.
* A szolgáltató végezhet analytics összes ügyfeleinek.

A központosított architektúra hátrányai a következők:

* Ez az architektúra az ügynök-alapú virtuális gép adatai csak az érvényes, nem fedezik, PaaS, SaaS- és az Azure fabric adatforrások.
* Rögzített adatokat az ügyfelek olyan egységes munkaterületre egyesítésekor között lehet. Ehhez az egyetlen jó módszer, hogy a számítógép teljesen minősített tartománynevét (FQDN) használja, vagy keresztül az Azure-előfizetés azonosítóját. 
* Minden az ügyfelektől származó összes adatot ugyanabban a régióban, az egyetlen számlán és azonos megőrzési és konfigurációs beállítások tárolódik.
* Az Azure fabric és a PaaS szolgáltatásokat, mint az Azure Diagnostics és az Azure-Auditnaplók a munkaterületet és az erőforrásnak ugyanabban a bérlőben így ezek nem küldi el a naplókat a központi munkaterület szükséges.
* Az összes ügyféltől származó összes virtuálisgép-ügynök hitelesítése a központi munkaterületre ugyanazzal a munkaterület-AZONOSÍTÓval és kulccsal történik. Nincs más ügyfelek megszakítása nélkül naplók egy adott ügyfél blokkolása módszer.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. a hibrid naplók az ügyfél bérlője területén található munkaterületen tárolódnak, és némelyikük központi helyre van húzva.

A harmadik architektúra vegyesen a két lehetőség között. A naplók esetén az egyes vásárlók helyi első elosztott architektúrája alapján, de néhány mechanizmus segítségével hozhat létre egy központi tárházban a naplók. A naplók egy része egy központi helyre, jelentéskészítési és elemzési kéri le. Ez a rész kis számú adattípusok és a napi statisztika például a tevékenységek összegzésének lehet.

A naplók központi helyen történő megvalósításának két lehetősége van:

1. Központi munkaterület: a szolgáltató létrehozhat egy munkaterületet a bérlőben, és olyan parancsfájlt használhat, amely a [lekérdezési API](https://dev.loganalytics.io/) -t használja az [ADATgyűjtési API](../../azure-monitor/platform/data-collector-api.md) -val, hogy az adatok a különböző munkaterületekről a központi helyre kerüljenek. Egy másik lehetőség, amely nem parancsfájl, a [Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)használatára szolgál.

2. Power BI központi helyként: Power BI a központi helyként működhet, ha a különböző munkaterületek az Log Analytics munkaterület és a [Power bi](../../azure-monitor/platform/powerbi.md)közötti integráció használatával exportálják az adataikat. 

## <a name="next-steps"></a>Következő lépések

* Munkaterületek létrehozásának és konfigurálásának automatizálása [Resource Manager-sablonok](template-workspace-configuration.md) használatával

* Munkaterületek létrehozásának automatizálása a [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) használatával 

* [Riasztások](../../azure-monitor/platform/alerts-overview.md) használata a meglévő rendszerekkel való integrációhoz

* Összegző jelentések előállítása [Power bi](../../azure-monitor/platform/powerbi.md) használatával

* Ügyfelek bevonása az Azure-beli [delegált erőforrás-felügyeletbe](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).
