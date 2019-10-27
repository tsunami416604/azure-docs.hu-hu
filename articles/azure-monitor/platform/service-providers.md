---
title: Azure Monitor a szolgáltatók számára | Microsoft Docs
description: A Azure Monitor a felügyelt szolgáltatók (MSP-k), a nagyvállalatok, a független szoftvergyártók (ISV-k) és az üzemeltetési szolgáltatók számára lehetővé teszik a kiszolgálók kezelését és figyelését az ügyfél helyszíni vagy felhőalapú infrastruktúrájában.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 08/06/2019
ms.openlocfilehash: b0f25d01421edd329b03d8f2b7e1aafaa2ba67d5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932056"
---
# <a name="azure-monitor-for-service-providers"></a>Azure Monitor a szolgáltatók számára
Log Analytics munkaterületek Azure Monitor segíthetnek a felügyelt szolgáltatók (MSP-k), a nagyvállalatok, a független szoftvergyártók (ISV-k) és az üzemeltetési szolgáltatók számára, hogy az ügyfél helyszíni vagy Felhőbeli infrastruktúrájában kezeljék és felügyeljék a kiszolgálókat. 

A nagyvállalatok számos hasonlóságot biztosítanak a szolgáltatók számára, különösen akkor, ha van egy központosított informatikai csapat, amely számos különböző üzleti egységért felelős. Az egyszerűség kedvéért ez a dokumentum a *szolgáltató* kifejezést használja, de ugyanez a funkció a vállalatok és más ügyfelek számára is elérhető.

A [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) program részét képező partnerek és szolgáltatók esetében a log Analytics Azure monitor az [Azure CSP-előfizetésekben](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)elérhető Azure-szolgáltatások egyike. 

## <a name="architectures-for-service-providers"></a>Szolgáltatók architektúrái

Log Analytics munkaterületek lehetővé teszik a rendszergazdák számára a [naplózási](data-platform-logs.md) folyamatok áramlását és elkülönítését, valamint az adott üzleti igényeknek megfelelő architektúra létrehozását. [Ez a cikk](design-logs-deployment.md) a munkaterület tervezési, üzembe helyezési és áttelepítési szempontjait ismerteti, és a [hozzáférés kezelése](manage-access.md) című cikk ismerteti, hogyan kell alkalmazni és kezelni a naplózási engedélyeket. A szolgáltatók további szempontokat is figyelembe kell venniük.

A szolgáltatók három lehetséges architektúrával rendelkeznek Log Analytics munkaterületekre vonatkozóan:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. az elosztott naplók az ügyfél bérlője területén található munkaterületeken tárolódnak 

Ebben az architektúrában egy munkaterületet helyezünk üzembe az ügyfél bérlője, amely az adott ügyfél összes naplójában használatos. A szolgáltatói rendszergazdák [Azure Active Directory vendég felhasználó (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)használatával kapnak hozzáférést ehhez a munkaterülethez. A szolgáltató rendszergazdáinak át kell térniük az ügyfél címtárára a Azure Portal, hogy el tudják érni ezeket a munkaterületeket.

Az architektúra előnyei a következők:
* Az ügyfél a saját [szerepköralapú hozzáférésével](https://docs.microsoft.com/azure/role-based-access-control/overview)kezelheti a naplókhoz való hozzáférést.
* Minden ügyfél különböző beállításokkal rendelkezhet a munkaterülethez, például a megőrzéshez és az adatkorláthoz.
* Az ügyfelek közötti elkülönítés szabályozási és kompatibilitás.
* Az egyes munkaterületek díját az ügyfél előfizetése fogja bevezetni.
* A naplók az összes típusú erőforrásból összegyűjthetők, nem csak az ügynök-alapúak. Például az Azure naplók.

Az architektúra hátrányai a következők:
* A szolgáltató számára nehezebb a nagy számú ügyfél-bérlő kezelése egyszerre.
* A szolgáltatói rendszergazdákat az ügyfél címtárában kell kiépíteni.
* A szolgáltató nem tudja elemezni az egyes ügyfeleken tárolt adatelemzéseket.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. a központi naplók tárolása a szolgáltatói bérlőben található munkaterületen történik

Ebben az architektúrában a rendszer nem tárolja a naplókat az ügyfél bérlői között, de csak a szolgáltató előfizetésében lévő egyik központi helyen. Az ügyfél virtuális gépei számára telepített ügynökök úgy vannak konfigurálva, hogy a munkaterület-azonosító és a titkos kulcs használatával küldjék el a naplókat erre a munkaterületre.

Az architektúra előnyei a következők:
* Könnyen kezelheti a nagy mennyiségű ügyfelet, és integrálhatja őket a különböző háttérrendszer-rendszerekbe.

* A szolgáltató teljes körű tulajdonjogot biztosít a naplók és a különböző összetevők, például a függvények és a mentett lekérdezések felett.

* A szolgáltató az összes ügyfelén elvégezheti az elemzést.

Az architektúra hátrányai a következők:
* Ez az architektúra csak az ügynök-alapú virtuálisgép-adatforrásokra vonatkozik, nem fedi le a Pásti, az SaaS és az Azure Fabric adatforrásait.

* Előfordulhat, hogy a felhasználók közötti adategységeket nem lehet külön munkaterületre egyesíteni. Az egyetlen jó módszer, ha a számítógép teljes tartománynevét (FQDN) vagy az Azure-előfizetés AZONOSÍTÓját használja. 

* Az összes ügyféltől származó összes adatok ugyanabban a régióban lesznek tárolva, mint egyetlen számla és egy adatmegőrzési és konfigurációs beállítás.

* Az Azure Fabric és a Pásti-szolgáltatások, mint például a Azure Diagnostics és az Azure-naplók esetében a munkaterület ugyanabban a bérlőben kell lennie, mint az erőforrásnak, így a naplók nem küldhetők el a központi munkaterületre.

* Az összes ügyféltől származó összes virtuálisgép-ügynök hitelesítése a központi munkaterületre ugyanazzal a munkaterület-AZONOSÍTÓval és kulccsal történik. Egy adott ügyfél naplóit nem lehet blokkolni más ügyfelek megszakítása nélkül.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. a hibrid naplók az ügyfél bérlője területén található munkaterületen tárolódnak, és némelyikük központi helyre van húzva.

A harmadik architektúra a két lehetőség között van. Ez az első olyan elosztott architektúrán alapul, ahol a naplók helyiek az egyes ügyfelek számára, de a naplók központi tárházának létrehozásához valamilyen mechanizmust használunk. A naplók egy része a jelentéskészítés és az elemzés központi helyére kerül. Ez a rész lehet kis számú adattípus vagy a tevékenység összegzése, például a napi statisztika.

A naplók központi helyen történő megvalósításának két lehetősége van:

1. Központi munkaterület: a szolgáltató létrehozhat egy munkaterületet a bérlőben, és olyan parancsfájlt használhat, amely a [lekérdezési API](https://dev.loganalytics.io/) -t használja az [ADATgyűjtési API](../../azure-monitor/platform/data-collector-api.md) -val, hogy az adatok a különböző munkaterületekről a központi helyre kerüljenek. Egy másik lehetőség, amely nem parancsfájl, a [Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)használatára szolgál.

2. Power BI központi helyként: Power BI a központi helyként működhet, ha a különböző munkaterületek az Log Analytics munkaterület és a [Power bi](../../azure-monitor/platform/powerbi.md)közötti integráció használatával exportálják az adataikat. 

## <a name="next-steps"></a>Következő lépések

* Munkaterületek létrehozásának és konfigurálásának automatizálása [Resource Manager-sablonok](template-workspace-configuration.md) használatával

* Munkaterületek létrehozásának automatizálása a [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) használatával 

* [Riasztások](../../azure-monitor/platform/alerts-overview.md) használata a meglévő rendszerekkel való integrációhoz

* Összegző jelentések előállítása [Power bi](../../azure-monitor/platform/powerbi.md) használatával

* Tekintse át a [log Analytics és Power bi konfigurálásának folyamatát több CSP-ügyfél figyeléséhez](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
