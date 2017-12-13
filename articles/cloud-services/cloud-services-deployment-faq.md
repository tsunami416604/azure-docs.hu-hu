---
title: "Telepítési problémák esetére, a Microsoft Azure Cloud Services – gyakori kérdések |} Microsoft Docs"
description: "Ez a cikk felsorolja a kapcsolatos gyakori kérdések a Microsoft Azure-szolgáltatásokhoz központi telepítés."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: genli
ms.openlocfilehash: 61d1cc511bf541e75ffda3e84b116f78a434f6f1
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure-szolgáltatásokhoz központi telepítési problémák: gyakran ismételt kérdések (GYIK)

Ez a cikk tartalmazza a központi telepítési problémái kapcsolatos gyakran ismételt kérdések [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Is tud kezelni a [felhőalapú szolgáltatások Virtuálisgép-méretet lap](cloud-services-sizes-specs.md) mérete információt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Miért nem felhőalapú szolgáltatás telepítése az átmeneti helyet néha sikertelen, és egy erőforrás-elosztási hiba, ha már van egy meglévő telepítését az éles ponton?
Ha egy felhőalapú szolgáltatás a központi telepítés vagy tárolóhelye van, a teljes felhőszolgáltatás adott fürt van rögzítve. Ez azt jelenti, hogy a központi telepítés már szerepel az éles webalkalmazásra, ha egy új átmeneti telepítési csak lehet hozzárendelni a fürtön, amelyen az éles webalkalmazásra.

Pufferallokációs hibák akkor történik, ha a fürt, ahol megtalálható a felhőalapú szolgáltatás nem rendelkezik elég fizikai számítási erőforrásokat, a központi telepítés kérelem teljesítéséhez.

Ilyen pufferallokációs hibák kiküszöböléséhez útmutatásért lásd: [Felhőszolgáltatás memóriafoglalási hiba: megoldásokkal](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Miért nem vertikális felskálázásával vagy scaling out néha egy felhőalapú szolgáltatás telepítését, ezért foglalási nem?
Amikor telepít egy felhőalapú szolgáltatás, akkor általában lekérdezi rögzítve fürtön. Ez azt jelenti, hogy egy meglévő felhőszolgáltatáshoz fel és kimeneti skálázás kell rendelnie az ugyanabban a fürtben található új példányok. A fürt közelít kapacitásának határához, vagy a kívánt virtuális gép mérete/típus nem érhető el, ha a kérelem sikertelen lehet.

Ilyen pufferallokációs hibák kiküszöböléséhez útmutatásért lásd: [Felhőszolgáltatás memóriafoglalási hiba: megoldásokkal](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Miért nem olyan lesznek üzembe helyezve egy felhőalapú szolgáltatás affinitáscsoporthoz néha eredményez memóriafoglalási hiba?
Új központi telepítést egy üres felhőszolgáltatásban oszthat ki által a hálót a fürthöz az adott régióban, kivéve, ha a felhőalapú szolgáltatás affinitáscsoporthoz van rögzítve. Ugyanabban az affinitáscsoportban történő telepítését ugyanazon a fürtön kísérli meg a rendszer. Ha a fürt közelít kapacitásának határához, a kérelem sikertelen lehet.

Ilyen pufferallokációs hibák kiküszöböléséhez útmutatásért lásd: [Felhőszolgáltatás memóriafoglalási hiba: megoldásokkal](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Miért nem módosítása a Virtuálisgép-méretet, vagy egy új virtuális gép hozzáadása egy meglévő felhőalapú szolgáltatást néha eredményez memóriafoglalási hiba?
Előfordulhat, hogy az adatközpontban a fürtök különböző konfigurációiról gépek típusainak (például egy sorozat Av2 adatsorozat, D sorozat, Dv2 adatsorozat, G adatsorozat, H adatsorozat, stb.). De a fürt nem feltétlenül kell minden olyan virtuális gépek. Például ha egy virtuális gép D sorozat hozzáadása egy felhőszolgáltatás, amely már üzembe van helyezve A csak adatsorozat-fürtben lévő, a működés egy memóriafoglalási hiba. Ez akkor is történik, ha megpróbálja módosítani VM SKU méretének (például egy A-sorozatú naplókról a D sorozat).

Ilyen pufferallokációs hibák kiküszöböléséhez útmutatásért lásd: [Felhőszolgáltatás memóriafoglalási hiba: megoldásokkal](cloud-services-allocation-failures.md#solutions).

Az adott régióban elérhető méretek ellenőrzéséhez tekintse meg a [Microsoft Azure: régiónként rendelkezésre álló termékek](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Miért nem egy felhőalapú szolgáltatás telepítése egy kis ideig miatt sikertelenek korlátok/kvóták/megkötések a előfizetés vagy szolgáltatás?
Egy felhőalapú szolgáltatás telepítése sikertelen lehet, ha az erőforrásokat lefoglalni szükséges meghaladja az alapértelmezett vagy a szolgáltatás a régió/adatközpont szinten engedélyezett kvóta felső határát. További információkért lásd: [Felhőszolgáltatások korlátozza](../azure-subscription-service-limits.md#cloud-services-limits).

Az előfizetést, a portál sikerült is nyomon az aktuális memóriahasználati kvóta: Azure-portálon = > előfizetések = > \<a megfelelő előfizetés > = > "Használati + kvóta".

Erőforrás-használat/fogyasztás kapcsolatos információkat is beolvashatók az Azure számlázási API-k használatával. Lásd: [az Azure erőforrás-használat (előzetes verzió) API](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Hogyan lehet módosítani, üzembe helyezésével telepített felhőszolgáltatás virtuális gép mérete?
A Virtuálisgép-méretet a telepített felhőszolgáltatást, üzembe helyezésével nem módosítható. A Virtuálisgép-méretet a CSDEF, amelyek csak egy helyezze üzembe újra frissíthető van beépítve.

További információkért lásd: [egy felhőalapú szolgáltatás frissítése](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Miért nem tudja telepíteni a Service Management API-k vagy a Powershellen keresztül Cloud Services, Azure Resource Manager tárfiók használata esetén? 

Mivel a felhőalapú szolgáltatás egy klasszikus erőforrást, amely nem kompatibilis közvetlenül az Azure Resource Manager modellt, az Azure Resource Manager tárfiókokkal nem társítható. Íme néhány lehetőség: 
 
- Deploying REST API-n keresztül.

    Telepítésekor Service Management REST API-n keresztül, akkor lehetett beolvasni a korlátozás körül megadásával a blob-tároló, amely működni fog-e mind a klasszikus, és a Azure Resource Manager Tárfiók SAS URL-CÍMÉT. További információk a "PackageUrl" tulajdonság [Itt](https://msdn.microsoft.com/library/azure/ee460813.aspx).
  
- Keresztül telepítése [Azure-portálon](https://portal.azure.com).

    Ez a fog működni a [Azure-portálon](https://portal.azure.com) , a hívást a proxy/kódrészlet Azure Resource Manager és klasszikus erőforrások közötti kommunikációs így végig kell vinnie. 
 
## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Miért nem Azure-portálon követeljen meg a tárfiók számára történő központi telepítés? 

A klasszikus portálon a csomag közvetlenül a felügyeleti API réteg feltöltött, majd a API réteg volna ideiglenesen tegye a csomag egy belső tároló figyelembe.  Ez a folyamat teljesítményének és méretezhetőségének problémákat okoz, mivel az API réteg nem volt célja, hogy a fájl feltöltési szolgáltatás lehet.  Az Azure portálon (Resource Manager üzembe helyezési modellben) azt kell megkerülte az ideiglenes lépés API réteg első feltöltése a gyorsabb és megbízhatóbb központi telepítések eredményez. 

A költség, mint nagyon kicsi, és újra felhasználhatja a tárfiókon összes központi telepítések egységességét. Használhatja a [tárolási költség Számológép](https://azure.microsoft.com/en-us/pricing/calculator/#storage1) annak meghatározásához, a költség, töltse fel a service-csomag (CSPKG), töltse le a CSPKG, majd törölje a CSPKG. 