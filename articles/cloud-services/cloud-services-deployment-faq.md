---
title: Üzembe helyezési problémák a Microsoft Azure Cloud Services – gyakori kérdések |} A Microsoft Docs
description: Ez a cikk a Microsoft Azure Cloud Servicesben üzembe helyezés – gyakori kérdések listája.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 08d74f866fe28a4c424ba504795b4a22f09785ca
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790605"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Az Azure Cloud Services üzembe helyezési problémák: Gyakori kérdések (GYIK)

Ez a cikk tartalmazza a felmerülő üzembehelyezési hibák – gyakori kérdések [a Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Is tud kezelni a [Cloud Services Virtuálisgép-méretet lapján](cloud-services-sizes-specs.md) mérete információt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Miért nem néha az előkészítési ponton történő üzembe helyezése egy felhőalapú szolgáltatás sikertelen és a egy erőforrás-foglalási hiba, ha már van egy meglévő üzemelő példány az üzemelési?
Ha egy felhőalapú szolgáltatás egy központi telepítést vagy a tárolóhely, a teljes felhőalapú szolgáltatás egy adott fürtben van rögzítve. Ez azt jelenti, hogy ha egy telepítés már létezik az üzemelési, egy új átmeneti üzembe helyezés csak lehet hozzárendelni a fürtön, az éles webalkalmazásra.

Foglalási hibák fordulhatnak elő, amikor a fürt hol helyezkedik el a felhőalapú szolgáltatás nem rendelkezik elég fizikai számítási erőforrásokat az üzembe helyezés kérelem teljesítéséhez.

Az ilyen hibák csökkentése, lásd [Felhőszolgáltatás-foglalási hiba: Megoldások](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Miért nem és leskálázása ki egy felhőszolgáltatás üzembe helyezésének néha eredményez a foglalási hiba?
Amikor telepít egy felhőalapú szolgáltatás, általában lekérdezi rögzítve egy adott fürtön. Ez azt jelenti, hogy új példányok ugyanazon fürt méretezése felfelé és kimenő egy meglévő felhőszolgáltatáshoz kell lefoglalni. Ha a fürt közelít kapacitásának határához, vagy nem érhető el a kívánt virtuális gép méretének/típusának, a kérelem sikertelen lehet.

Az ilyen hibák csökkentése, lásd [Felhőszolgáltatás-foglalási hiba: Megoldások](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Miért nem környezetbe való üzembe helyezés egy felhőalapú szolgáltatás affinitáscsoporthoz néha eredményez foglalási hiba?
Új üzemelő példányt egy üres felhőszolgáltatásban a háló minden olyan fürt, az adott régióban, oszthat ki, kivéve, ha a felhőszolgáltatás egy affinitáscsoporthoz van rögzítve. Központi telepítések számára ugyanabban az affinitáscsoportban kísérli meg a rendszer ugyanazon a fürtön. Ha a fürt közelít kapacitásának határához, a kérelem sikertelen lehet.

Az ilyen hibák csökkentése, lásd [Felhőszolgáltatás-foglalási hiba: Megoldások](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Miért nem virtuális gép méretének módosítása vagy egy új virtuális gép hozzáadása egy meglévő felhőszolgáltatáshoz néha eredményez foglalási hiba?
Előfordulhat, hogy az egy kínai adatközpont fürtök különböző konfigurációjú géptípus (például egy sorozat, az Av2 sorozat, a D sorozat, a Dv2 sorozat, G sorozat, H-sorozat stb.). De a fürtök nem feltétlenül kell minden olyan virtuális gépek. Például ha a D sorozatú virtuális gép hozzáadása egy felhőszolgáltatás, amely már üzembe helyezte A csak sorozat fürtben, tapasztalható-foglalási hiba. Ez is történik, ha megpróbálja módosítani a VM-Termékváltozatok (például az A sorozatú átváltása a D sorozat) mérete.

Az ilyen hibák csökkentése, lásd [Felhőszolgáltatás-foglalási hiba: Megoldások](cloud-services-allocation-failures.md#solutions).

A régióban elérhető méretek megtekintéséhez [Microsoft Azure: Régiónként elérhető termékek](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Miért nem egy felhőszolgáltatás telepítésének valamikor miatt meghiúsul korlátok és kvóták/megkötések a saját előfizetés vagy szolgáltatás?
A felhőszolgáltatás üzemelő példányának meghiúsulhatnak, ha az erőforrások lefoglalását szükséges meghaladja az alapértelmezett vagy a régió/adatközpont szinten a szolgáltatás számára engedélyezett maximális kvótát. További információkért lásd: [korlátozza a Cloud Services](../azure-subscription-service-limits.md#azure-cloud-services-limits).

Az előfizetést, a portálon is a jelenlegi használati kvóta sikerült nyomon: Az Azure portal = > előfizetések = > \<a megfelelő előfizetést > = > "Használati + kvóta".

Erőforrás-használat, felhasználás-kapcsolatos adatai is beolvashatók az Azure számlázási API-kon keresztül. Lásd: [Azure erőforrás-használati API (előzetes verzió)](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Hogyan módosíthatom egy üzembe helyezett felhőalapú szolgáltatás virtuális gép méretének újbóli üzembe helyezése nélkül?
Üzembe helyezett cloud service Virtuálisgép-méretének újbóli üzembe helyezése nélkül nem módosítható. A Virtuálisgép-méretet, amely frissíthetők egy ismételt üzembe helyezése csak a CSDEF be van építve.

További információkért lásd: [egy felhőalapú szolgáltatás frissítése](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Miért nem tudok üzembe helyezéséhez a Service Management API-k vagy a Powershellen keresztül a Cloud Services, Azure Resource Manager-tárfiókok használata? 

Mivel a felhőalapú szolgáltatás, nem közvetlenül az Azure Resource Manager-modell kompatibilis klasszikus erőforráshoz, az Azure Resource Manager tárfiókok nem társítható. Az alábbiakban néhány lehetőséget: 
 
- Az üzembe helyezést REST API-t.

    Service Management REST API-n keresztül történő telepítésekor, a blob Storage, amely működik a klasszikus és az Azure Resource Manager-Storage-fiók egy SAS URL-cím megadásával megkerülésében sikerült a korlátozást. További információ a "PackageUrl" tulajdonság [Itt](/previous-versions/azure/reference/ee460813(v=azure.100)).
  
- Az üzembe helyezést [az Azure portal](https://portal.azure.com).

    Ez akkor fog működni a a [az Azure portal](https://portal.azure.com) , a hívás halad végig a proxy/kódrészlet, amely lehetővé teszi, hogy az Azure Resource Manager és klasszikus erőforrások közti kommunikációt. 
 
## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Miért az Azure portal szükséges ponton, és adja meg a tárfiók üzembe helyezéshez? 

A klasszikus portálon a csomag közvetlenül a felügyeleti API-réteget feltöltött, és csak utána az API-réteget szeretné ideiglenesen a csomag be egy belső tárfiókot.  Ez a folyamat teljesítményét és méretezhetőségét problémákat okoz, mivel az API-réteget nem lehet egy fájl feltöltési szolgáltatás tervezték.  Az Azure Portalon (Resource Manager üzembe helyezési modell) azt kell telepítővarázslója kihagyja a közbenső lépés az API-réteget, hogy először feltöltene gyorsabb és megbízhatóbb központi telepítések eredményez. 

A költség, mint a nagyon kicsi, és újra felhasználhatja ugyanazt a tárfiókot az összes üzemelő alkalmazáspéldányok között. Használhatja a [tárolási költségkalkulátor](https://azure.microsoft.com/pricing/calculator/#storage1) annak megállapításához, a költség, a service-csomag (CSPKG) feltöltése, letöltése a CSPKG, majd törölje a CSPKG. 
