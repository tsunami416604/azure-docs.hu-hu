---
title: Azure Monitor naplók a szolgáltatók számára | Microsoft dokumentumok
description: Az Azure Monitor Logs segítségével felügyelt szolgáltatók (MSP- k), nagyvállalatok, független szoftverszállítók (ISV-k) és tárhelyszolgáltatók kezelhetik és figyelhetik az ügyfél helyszíni vagy felhőalapú infrastruktúrájának kiszolgálóit.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658880"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Azure figyelőnaplók szolgáltatókszámára

Az Azure Monitor Log Analytics-munkaterületei segíthetnek a felügyelt szolgáltatóknak (MSP-k), a nagyvállalatoknak, a független szoftverszállítóknak, valamint a tárhelyszolgáltatóknak az ügyfél helyszíni vagy felhőalapú infrastruktúrájában lévő kiszolgálók kezelésében és figyelésében.

A nagyvállalatok sok hasonlóságot mutatnak a szolgáltatókkal, különösen akkor, ha van egy központosított informatikai csapat, amely számos különböző üzleti egység informatikai irányításáért felelős. Az egyszerűség kedvéért ez a dokumentum a *szolgáltató* kifejezést használja, de ugyanez a funkció a vállalatok és más ügyfelek számára is elérhető.

A [felhőszolgáltató (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) program részét vevő partnerek és szolgáltatók számára az Azure Monitor log analytics szolgáltatása az Azure CSP-előfizetésekben elérhető Azure-szolgáltatások egyike.

Az Azure Monitor Log Analytics szolgáltatását az Azure Lighthouse delegált erőforrás-kezelési képességén keresztül az ügyfélerőforrásokat kezelő szolgáltató is [használhatja.](https://docs.microsoft.com/azure/lighthouse/overview)

## <a name="architectures-for-service-providers"></a>Szolgáltatók architektúrái

A Log Analytics-munkaterületek olyan módszert biztosítanak a rendszergazda számára, amely szabályozza a [naplóadatok](data-platform-logs.md) áramlását és elkülönítését, és olyan architektúrát hoz létre, amely az adott üzleti igényeket kielégíti. [Ez](design-logs-deployment.md) a cikk ismerteti a munkaterület tervezési, telepítési és áttelepítési szempontjait, a [hozzáférés kezelése](manage-access.md) című cikk pedig ismerteti, hogyan lehet alkalmazni és kezelni a naplóadatokra vonatkozó engedélyeket. A szolgáltatóknak további szempontokat is figyelembe kell venniük.

A szolgáltatók számára három lehetséges architektúra létezik a Log Analytics-munkaterületekkel kapcsolatban:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Elosztott – A naplók az ügyfél bérlőjében található munkaterületeken tárolódnak

Ebben az architektúrában egy munkaterület van telepítve az ügyfél bérlője, amely az ügyfél összes naplójához használatos.

A szolgáltatói rendszergazdák kétféleképpen férhetnek hozzá a Log Analytics-munkaterülethez az ügyfél-bérlőben:

- Az ügyfél hozzáadhat egyéni felhasználókat a szolgáltatótól [az Azure Active Directory vendégfelhasználói (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)néven. A szolgáltató rendszergazdáinak be kell jelentkezniük az azure-portál on minden ügyfél címtárába, hogy elérhesd ezeket a munkaterületeket. Ez azt is megköveteli az ügyfelektől, hogy minden egyes szolgáltató rendszergazdája egyéni hozzáférést kezeljenek.
- A nagyobb méretezhetőség és rugalmasság érdekében a szolgáltatók az [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) [Delegált erőforrás-kezelési](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) funkciójával érhetik el az ügyfél bérlőjét. Ezzel a módszerrel a szolgáltató rendszergazdák szerepelnek egy Azure AD felhasználói csoport a szolgáltató bérlője, és ez a csoport hozzáférést kap a bevezetési folyamat során az egyes ügyfelek számára. Ezek a rendszergazdák ezután hozzáférhetnek az egyes ügyfelek munkaterületeiasaját szolgáltató bérlő, ahelyett, hogy jelentkezzen be az egyes ügyfelek bérlői külön-külön. Az ügyfelek Log Analytics-munkaterületeinek ily módon történő elérése csökkenti az ügyféloldalon szükséges munkát, és megkönnyíti az adatok gyűjtését és elemzését több ügyfél között, amelyeket ugyanaz a szolgáltató kezel olyan eszközökön keresztül, mint például az [Azure Monitor Munkafüzetek.](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview) További információ: [Az ügyfelek erőforrásainak figyelése nagy méretekben.](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale)

Az elosztott architektúra előnyei a következők:

* Az ügyfél megerősítheti az engedélyek meghatározott szintjeit az [Azure delegált erőforrás-kezelésén](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)keresztül, vagy kezelheti a naplókhoz való hozzáférést a saját [szerepköralapú hozzáférésével.](https://docs.microsoft.com/azure/role-based-access-control/overview)
* Naplók gyűjthetők minden típusú erőforrások, nem csak ügynök-alapú virtuális gép adatok. Például az Azure naplózási naplók.
* Minden ügyfél különböző beállításokkal rendelkezhet a munkaterülethez, például megőrzésés adatkorlát.
* Elkülönítés az ügyfelek között a szabályozási és a megfelelőség.
* Az egyes munkaterületek díja bekerül az ügyfél előfizetésébe.

Az elosztott architektúra hátrányai a következők:

* Az adatok központi megjelenítése és elemzése az ügyfél-bérlők között olyan eszközökkel, mint például az Azure Monitor Workbooks lassabb felhasználói élményt eredményezhet, különösen akkor, ha több mint 50 munkaterületen elemez adatokat.
* Ha az ügyfelek nincsenek beszervezve az Azure delegált erőforrás-kezeléséhez, a szolgáltató rendszergazdáit ki kell építeni az ügyfélcímtárban, és a szolgáltató nak nehezebb egyszerre nagyszámú ügyfél-bérlőt kezelnie.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Központi – A naplók a szolgáltató bérlőjében található munkaterületen tárolódnak

Ebben az architektúrában a naplók nem az ügyfél bérlői, hanem csak egy központi helyen belül a szolgáltató előfizetések. Az ügyfél virtuális gépein telepített ügynökök úgy vannak konfigurálva, hogy a munkaterület-azonosító és a titkos kulcs használatával küldjék el a naplóikat erre a munkaterületre.

A központosított építészet előnyei a következők:

* Könnyen kezelhető nagyszámú ügyfél, és integrálja őket a különböző háttérrendszerek.
* A szolgáltató teljes tulajdonjoggal rendelkezik a naplók és a különböző összetevők, például a függvények és a mentett lekérdezések felett.
* A szolgáltató elemzéseket végezhet az összes ügyfele számára.

A központosított architektúra hátrányai a következők:

* Ez az architektúra csak ügynökalapú virtuálisgép-adatok, nem terjed ki a PaaS, SaaS és az Azure fabric adatforrások.
* Előfordulhat, hogy nehéz elkülöníteni az adatokat az ügyfelek között, amikor egyetlen munkaterületre vannak egyesítve. Az egyetlen jó módszer erre a számítógép teljesen minősített tartománynév (FQDN) vagy az Azure-előfizetés-azonosító használatával. 
* Az összes ügyfél től származó összes adat ugyanabban a régióban lesz tárolva, egyetlen számlával és azonos megőrzési és konfigurációs beállításokkal.
* Az Azure fabric és a PaaS-szolgáltatások, például az Azure Diagnosztika és az Azure naplózási naplók megköveteli, hogy a munkaterület ugyanabban a bérlőben legyen, mint az erőforrás, így nem küldhetik el a naplókat a központi munkaterületre.
* Az összes ügyfél összes virtuálisgép-ügynöke ugyanazzal a munkaterület-azonosítóval és kulccsal lesz hitelesítve a központi munkaterületen. Nincs olyan módszer, amely letiltaná egy adott ügyfél naplóit anélkül, hogy más ügyfeleket megzavarna.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hibrid – A naplók az ügyfél bérlőjében található munkaterületen tárolódnak, és némelyikük egy központi helyre kerül.

A harmadik architektúra a két lehetőség között. Ez alapján az első elosztott architektúra, ahol a naplók helyi minden ügyfél számára, de valamilyen mechanizmushasználatával egy központi tárház naplók létrehozása. A naplók egy része bevan húzva egy központi helyre a jelentéskészítés hez és az elemzéshez. Ez a rész lehet kis számú adattípus, vagy a tevékenység összegzése, például a napi statisztika.

A naplók központi helyen történő megvalósításának két lehetősége van:

1. Központi munkaterület: A szolgáltató létrehozhat egy munkaterületet a bérlőben, és egy parancsfájlt használhat, amely a [Query API-t](https://dev.loganalytics.io/) használja az [adatgyűjtési API-val,](../../azure-monitor/platform/data-collector-api.md) hogy a különböző munkaterületekből származó adatokat erre a központi helyre hozza. Egy másik lehetőség, nem egy script, az [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)használata.

2. A Power BI mint központi hely: A Power BI központi helyként működhet, amikor a különböző munkaterületek adatokat exportálnak hozzá a Log Analytics munkaterület és a [Power BI](../../azure-monitor/platform/powerbi.md)integrációja segítségével. 

## <a name="next-steps"></a>További lépések

* A munkaterületek létrehozásának és konfigurálásának automatizálása [az Erőforrás-kezelő sablonjaival](template-workspace-configuration.md)

* A munkaterületek létrehozásának automatizálása a [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) használatával 

* [A riasztások](../../azure-monitor/platform/alerts-overview.md) használata a meglévő rendszerekkel való integrációhoz

* Összegző jelentések készítése a [Power BI használatával](../../azure-monitor/platform/powerbi.md)

* Az ügyfelek fedélzeti szolgáltatása az [Azure delegált erőforrás-kezelésére.](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)
