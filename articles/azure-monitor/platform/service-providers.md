---
title: Azure Monitor a szolgáltatók számára | Microsoft Docs
description: A Azure Monitor a felügyelt szolgáltatók (MSP-k), a nagyvállalatok, a független szoftvergyártók (ISV-k) és az üzemeltetési szolgáltatók számára lehetővé teszik a kiszolgálók kezelését és figyelését az ügyfél helyszíni vagy felhőalapú infrastruktúrájában.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: meirm
ms.openlocfilehash: 971757a4778dd50be486bead0c50fd6b3a25002e
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839275"
---
# <a name="azure-monitor-for-service-providers"></a>Azure Monitor a szolgáltatók számára
Log Analytics munkaterületek Azure Monitor segíthetnek a felügyelt szolgáltatók (MSP-k), a nagyvállalatok, a független szoftvergyártók (ISV-k) és az üzemeltetési szolgáltatók számára, hogy az ügyfél helyszíni vagy Felhőbeli infrastruktúrájában kezeljék és felügyeljék a kiszolgálókat. 

A nagyobb cégeknek is megoszthatja számos Hasonlóságok szolgáltatók, különösen akkor, ha van egy központi informatikai csoportját, amelyek kezeléséért felelős informatikai számos különböző üzleti egységek számára. Az egyszerűség kedvéért a jelen dokumentum kifejezést használja *szolgáltató* , de ugyanazokat a funkciókat is vállalatok és más ügyfelek számára érhető el.

A [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) program részét képező partnerek és szolgáltatók esetében a log Analytics Azure monitor az [Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)-előfizetésekben elérhető Azure-szolgáltatások egyike. 

## <a name="architectures-for-service-providers"></a>A szolgáltatók számára architektúrák

Log Analytics munkaterületek lehetővé teszik a rendszergazdák számára a [naplózási](data-platform-logs.md) folyamatok áramlását és elkülönítését, valamint az adott üzleti igényeknek megfelelő architektúra létrehozását. [Ez a cikk](design-logs-deployment.md) a munkaterület tervezési, üzembe helyezési és áttelepítési szempontjait ismerteti, és a [hozzáférés kezelése](manage-access.md) című cikk ismerteti, hogyan kell alkalmazni és kezelni a naplózási engedélyeket. Szolgáltatók további szempontokat kell.

Nincsenek három lehetséges architektúrák szolgáltatók Log Analytics-munkaterületek vonatkozásában:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Elosztott - naplók vannak tárolva az ügyfél-bérlőben található munkaterületek 

Ebben az architektúrában a munkaterület az ügyfél-bérlőben, hogy az ügyfél a naplókhoz használt van telepítve. A service provider rendszergazdái hozzáférhetnek a munkaterület használatával [Azure Active Directory vendégfelhasználók (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). A service provider rendszergazdák kell váltania, az ügyfél Directory férhetnek hozzá ezekhez a munkaterületekhez, az Azure Portalon.

Ez az architektúra előnyei a következők:
* Az ügyfél a naplók használata a saját hozzáférését kezelheti [szerepköralapú hozzáférés-](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Minden ügyfél saját munkaterület megőrzésére és az adatok kapacitástól különböző beállítással is rendelkezhetnek.
* Az ügyfelek közötti elkülönítés szabályozási és a megfelelőség érdekében.
* Minden munkaterülethez a díja az ügyfél-előfizetés be lesz állítva.
* Az összes típusú erőforrások, nem csak az ügynök-alapú naplók gyűjthetők össze. Ha például az Azure vizsgálati naplók.

Ez az architektúra a hátrányai:
* Nagy számú vevő bérlők egyszerre kezelheti a szolgáltató nehezebb.
* Szolgáltatás-rendszergazdák szolgáltató kell építhető ki a könyvtárat.
* A szolgáltató nem adatainak elemzését az ügyfeleinek.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Közép - naplók a service provider bérlőben található munkaterület vannak tárolva.

Ebben az architektúrában a naplók nem tárolódnak, a vevő bérlők számára, de csak egy központi helyen belül a szolgáltató előfizetések egyike. Az ügyfél virtuális gépeken telepített ügynökök a naplók elküldése a munkaterület Azonosítójára és a titkos kulcs használatával vannak konfigurálva.

Ez az architektúra előnyei a következők:
* Az ügyfelek nagy számú kezelhet, és integrálhatja őket különböző háttérrendszerekhez, könnyebbé vált.

* A szolgáltató rendelkezik teljes körű tulajdonjogát a naplókból és a különböző összetevőket, például a functions és a mentett lekérdezések.

* A szolgáltató végezhet analytics összes ügyfeleinek.

Ez az architektúra a hátrányai:
* Ez az architektúra az ügynök-alapú virtuális gép adatai csak az érvényes, nem fedezik, PaaS, SaaS- és az Azure fabric adatforrások.

* Rögzített adatokat az ügyfelek olyan egységes munkaterületre egyesítésekor között lehet. Ehhez az egyetlen jó módszer, hogy a számítógép teljesen minősített tartománynevét (FQDN) használja, vagy keresztül az Azure-előfizetés azonosítóját. 

* Minden az ügyfelektől származó összes adatot ugyanabban a régióban, az egyetlen számlán és azonos megőrzési és konfigurációs beállítások tárolódik.

* Az Azure fabric és a PaaS szolgáltatásokat, mint az Azure Diagnostics és az Azure-Auditnaplók a munkaterületet és az erőforrásnak ugyanabban a bérlőben így ezek nem küldi el a naplókat a központi munkaterület szükséges.

* Az összes ügyféltől származó összes virtuálisgép-ügynök hitelesítése a központi munkaterületre ugyanazzal a munkaterület-AZONOSÍTÓval és kulccsal történik. Nincs más ügyfelek megszakítása nélkül naplók egy adott ügyfél blokkolása módszer.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hibrid - naplók az ügyfél-bérlőben található munkaterület tárolódnak, és közülük néhány egy központi helyen kéri le.

A harmadik architektúra vegyesen a két lehetőség között. A naplók esetén az egyes vásárlók helyi első elosztott architektúrája alapján, de néhány mechanizmus segítségével hozhat létre egy központi tárházban a naplók. A naplók egy része egy központi helyre, jelentéskészítési és elemzési kéri le. Ez a rész kis számú adattípusok és a napi statisztika például a tevékenységek összegzésének lehet.

A naplók központi helyen történő megvalósításának két lehetősége van:

1. Központi munkaterület: A szolgáltató létrehozhat egy munkaterületet a bérlőben, és használhat egy olyan parancsfájlt, amely a [lekérdezési API](https://dev.loganalytics.io/) -t használja az adatgyűjtési [API](../../azure-monitor/platform/data-collector-api.md) -val, hogy az adatok a különböző munkaterületekről a központi helyre kerüljenek. Egy másik lehetőség, a parancsfájl nem [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI központi helyként: A Power BI a központi helyként működhet, ha a különböző munkaterületek az Log Analytics munkaterület és a [Power bi](../../azure-monitor/platform/powerbi.md)közötti integráció használatával exportálják az adataikat. 

## <a name="next-steps"></a>További lépések

* Létrehozási és munkaterületeket konfigurációjának automatizálása [Resource Manager-sablonok](template-workspace-configuration.md)

* Munkaterületek használatával létrehozásának automatizálása [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Használat [riasztások](../../azure-monitor/platform/alerts-overview.md) a meglévő rendszerekkel való integrációt segítik

* Összegző jelentéseket használatával [Power bi-ban](../../azure-monitor/platform/powerbi.md)

* Tekintse át a folyamat [konfigurálása a Log Analytics és a Power BI több CSP-ügyfeleinek figyelése](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
