---
title: Log Analytics a szolgáltatók számára |} A Microsoft Docs
description: A log Analytics segítségével a felügyelt szolgáltatóknak (MSP), a nagyobb vállalatok független szoftver szállítói (ISV-k) és az üzemeltetési szolgáltatók kiszolgálók kezelése és figyelése az ügyfél helyszíni vagy felhőalapú infrastruktúrában.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: meirm
ms.component: ''
ms.openlocfilehash: ef1956f5283105548ba8790b87e82c35d6c9e1cd
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50960939"
---
# <a name="log-analytics-for-service-providers"></a>A log Analytics, a szolgáltatók számára
A log Analytics segítségével a felügyelt szolgáltatóknak (MSP), a nagyobb vállalatok, független szoftverszállítók (ISV-k) és üzemeltetési szolgáltatók az ügyfél helyszíni vagy felhőalapú infrastruktúrában lévő kiszolgálók kezelése és figyelése. 

A nagyobb cégeknek is megoszthatja számos Hasonlóságok szolgáltatók, különösen akkor, ha van egy központi informatikai csoportját, amelyek kezeléséért felelős informatikai számos különböző üzleti egységek számára. Az egyszerűség kedvéért a jelen dokumentum kifejezést használja *szolgáltató* , de ugyanazokat a funkciókat is vállalatok és más ügyfelek számára érhető el.

A partnerek és szolgáltatók, akik részei, a [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) program, a Log Analytics az elérhető az Azure-szolgáltatások egyik [Azure CSP-előfizetésekben](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>A szolgáltatók számára architektúrák

Log Analytics-munkaterületek biztosít módszert a rendszergazda szabályozhatja a flow és a naplók elkülönítése, és hozzon létre egy log architektúra, amely az adott üzleti igényeire. [Ez a cikk](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) körül munkaterület-felügyelet kapcsolatos általános szempontokról ismerteti. Szolgáltatók további szempontokat kell.

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
* Minden ügyfél az összes Virtuálisgép-ügynökök hitelesítése a cental-munkaterülethez a azonos munkaterület Azonosítóját és kulcsát. Nincs más ügyfelek megszakítása nélkül naplók egy adott ügyfél blokkolása módszer.


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hibrid - naplók az ügyfél-bérlőben található munkaterület tárolódnak, és közülük néhány egy központi helyen kéri le.

A harmadik architektúra vegyesen a két lehetőség között. A naplók esetén az egyes vásárlók helyi első elosztott architektúrája alapján, de néhány mechanizmus segítségével hozhat létre egy központi tárházban a naplók. A naplók egy része egy központi helyre, jelentéskészítési és elemzési kéri le. Ez a rész kis számú adattípusok és a napi statisztika például a tevékenységek összegzésének lehet.

A központi hely megvalósításához a Log Analytics két lehetőség van:

1. Központi munkaterület: A szolgáltató hozzon létre egy munkaterületet a bérlőben, és a egy parancsfájlt, amelyik a használni a [Query API](https://dev.loganalytics.io/) az a [adatok gyűjtési API](log-analytics-data-collector-api.md) életre az adatokat a különböző munkaterületekhez, ez a központi hely. Egy másik lehetőség, a parancsfájl nem [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power bi-ban egy központi helyet: Ha a különböző munkaterületekhez rájuk a Log Analytics integrációjával adatok exportálása Power BI működhet a központi helyet és [Power BI](log-analytics-powerbi.md). 


## <a name="next-steps"></a>További lépések
* Létrehozási és munkaterületeket konfigurációjának automatizálása [Resource Manager-sablonok](log-analytics-template-workspace-configuration.md)
* Munkaterületek használatával létrehozásának automatizálása [PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Használat [riasztások](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) a meglévő rendszerekkel való integrációt segítik
* Összegző jelentéseket használatával [Power bi-ban](log-analytics-powerbi.md)
* Tekintse át a folyamat [konfigurálása a Log Analytics és a Power BI több CSP-ügyfeleinek figyelése](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
