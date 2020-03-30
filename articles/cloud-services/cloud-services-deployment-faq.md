---
title: Üzembe helyezési problémák a Microsoft Azure Cloud Services rendszerével kapcsolatban – gyakori kérdések| Microsoft dokumentumok
description: Ez a cikk a Microsoft Azure Cloud Services üzembe helyezésével kapcsolatos gyakori kérdéseket sorolja fel.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 63a219078927e9001a8eb4085c722e7ec8d2fac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980624"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Üzembe helyezési problémák az Azure Cloud Services szolgáltatáshoz: gyakori kérdések (gyakori kérdések)

Ez a cikk a Microsoft Azure Cloud Services üzembe helyezési problémáival kapcsolatos gyakori kérdéseket [tartalmazza.](https://azure.microsoft.com/services/cloud-services) A [felhőszolgáltatások virtuális gép méretlapján](cloud-services-sizes-specs.md) is megtekintheti a méretadatokat.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Miért egy felhőalapú szolgáltatás üzembe helyezése az átmeneti tárolóhelyre néha sikertelen egy erőforrás-elosztási hiba, ha már van egy meglévő központi telepítés az éles tárolóhelyen?
Ha egy felhőszolgáltatás rendelkezik egy központi telepítés bármelyik tárolóhelyen, a teljes felhőszolgáltatás egy adott fürthöz van rögzítve. Ez azt jelenti, hogy ha egy központi telepítés már létezik az éles környezetben, egy új átmeneti központi telepítés csak az éles tárolóhellyel azonos fürtön foglalható le.

Foglalási hibák akkor fordulnak elő, ha a fürt, ahol a felhőszolgáltatás található, nem rendelkezik elegendő fizikai számítási erőforrásokkal a telepítési kérelem teljesítéséhez.

Az ilyen foglalási hibák csökkentésével kapcsolatban a [Felhőszolgáltatás foglalási hibája: Megoldások](cloud-services-allocation-failures.md#solutions)című témakörben talál segítséget.

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Miért egy felhőszolgáltatás-telepítés felskálázása vagy horizontális felskálázása néha foglalási hibát eredményez?
Ha egy felhőalapú szolgáltatás telepítve van, általában egy adott fürthöz kerül. Ez azt jelenti, hogy egy meglévő felhőszolgáltatás nak új példányokat kell lefoglalnia ugyanabban a fürtben. Ha a fürt kapacitása megközelíti a kapacitást, vagy a kívánt virtuális gép mérete/típusa nem érhető el, a kérés sikertelen lehet.

Az ilyen foglalási hibák csökkentésével kapcsolatban a [Felhőszolgáltatás foglalási hibája: Megoldások](cloud-services-allocation-failures.md#solutions)című témakörben talál segítséget.

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Miért egy felhőalapú szolgáltatás üzembe helyezése egy affinitási csoport néha eredményez foglalási hiba?
Egy új üzembe helyezés egy üres felhőszolgáltatás hozrendelhető a háló bármely fürtaz adott régióban, kivéve, ha a felhőszolgáltatás van rögzítve egy affinitási csoport. Ugyanahhoz az affinitási csoporthoz történő központi telepítést kísérel meg a telepítés ugyanazon a fürtön. Ha a fürt kapacitása közeledik, a kérés sikertelen lehet.

Az ilyen foglalási hibák csökkentésével kapcsolatban a [Felhőszolgáltatás foglalási hibája: Megoldások](cloud-services-allocation-failures.md#solutions)című témakörben talál segítséget.

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Miért a virtuális gép méretének módosítása vagy egy új virtuális gép hozzáadása egy meglévő felhőszolgáltatáshoz néha foglalási hibát eredményez?
Az adatközpontban lévő fürtök különböző típusú konfigurációkkal rendelkezhetnek (például A sorozat, Av2 sorozat, D sorozat, Dv2 sorozat, G sorozat, H sorozat stb.). De nem minden a fürtök szükségszerűen rendelkezik az összes típusú virtuális gépek. Ha például megpróbál hozzáadni egy D sorozatú virtuális gép egy felhőszolgáltatás, amely már telepítve van egy A sorozatcsak fürt, tapasztalni fogja a foglalási hiba. Ez akkor is előfordul, ha megpróbálja módosítani a virtuális gép termékváltozatának méretét (például A sorozatról D sorozatra vált).

Az ilyen foglalási hibák csökkentésével kapcsolatban a [Felhőszolgáltatás foglalási hibája: Megoldások](cloud-services-allocation-failures.md#solutions)című témakörben talál segítséget.

A régióban elérhető méretek ellenőrzéséhez olvassa el a [Microsoft Azure: Régiónként elérhető termékek című témakört.](https://azure.microsoft.com/regions/services)

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Miért működik be egy felhőszolgáltatás telepítése valamikor az előfizetésem vagy szolgáltatásom korlátai/kvótái/korlátai miatt?
A felhőszolgáltatás telepítése sikertelen lehet, ha a lefoglalandó erőforrások meghaladják a szolgáltatás számára a régió/adatközpont szintjén engedélyezett alapértelmezett vagy maximális kvótát. További információ: [Cloud Services limits](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits).

Az előfizetés aktuális használatát/kvótáját is nyomon követheti a portálon: Azure \<Portal => Subscriptions => megfelelő előfizetési> => "Használat + kvóta".

Erőforrás-használat/felhasználással kapcsolatos információk is lekérésre az Azure számlázási API-k. Lásd: [Azure Resource Usage API (előzetes verzió)](../cost-management-billing/manage/usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Hogyan módosíthatja a telepített felhőszolgáltatás virtuális gépének méretét újraüzembe helyezés nélkül?
A virtuális gép mérete nem módosíthatja a telepített felhőszolgáltatás újratelepítése nélkül. A virtuális gép mérete be van építve a CSDEF-be, amely csak újraüzembe helyezéssel frissíthető.

További információ: [Felhőszolgáltatások frissítése.](cloud-services-update-azure-service.md)

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Miért nem tudom telepíteni a Felhőszolgáltatásokat szolgáltatáskezelési API-kon vagy PowerShellen keresztül az Azure Resource Manager Storage-fiók használatakor? 

Mivel a felhőszolgáltatás egy klasszikus erőforrás, amely nem közvetlenül kompatibilis az Azure Resource Manager modell, nem társíthatja az Azure Resource Manager Storage-fiókok. Íme néhány lehetőség: 

- Üzembe helyezés a REST API-n keresztül.

    Ha a Service Management REST API-n keresztül telepíti, megkerülheti a korlátozást egy SAS-URL-cím megadásával a blob storage-hoz, amely a klasszikus és az Azure Resource Manager Storage-fiókkal is működni fog. Tudjon meg többet a "PackageUrl" [tulajdonságról itt.](/previous-versions/azure/reference/ee460813(v=azure.100))

- Üzembe helyezése az [Azure Portalon](https://portal.azure.com)keresztül.

    Ez az [Azure Portalon](https://portal.azure.com) fog működni, mivel a hívás egy proxy/alácé, amely lehetővé teszi az Azure Resource Manager és a klasszikus erőforrások közötti kommunikációt. 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Miért van szükség az Azure Portalon, hogy egy tárfiókot a központi telepítés?

A klasszikus portálon a csomagot közvetlenül a felügyeleti API-rétegre töltötték fel, majd az API-réteg ideiglenesen egy belső tárfiókba helyezte a csomagot.  Ez a folyamat teljesítmény- és méretezhetőségi problémákat okoz, mivel az API-réteget nem fájlfeltöltési szolgáltatásnak tervezték.  Az Azure Portalon (Resource Manager üzembe helyezési modell) megkerültük az API-rétegre való első feltöltés közbenső lépését, ami gyorsabb és megbízhatóbb üzemelő példányokat eredményez.

Ami a költségeket illeti, nagyon kicsi, és újra felhasználhatja ugyanazt a tárfiókot az összes központi telepítésben. A tárolási [költségkalkulátor](https://azure.microsoft.com/pricing/calculator/#storage1) segítségével meghatározhatja a szolgáltatáscsomag (CSPKG) feltöltési költségét, letöltheti a CSPKG-et, majd törölheti a CSPKG-et.
