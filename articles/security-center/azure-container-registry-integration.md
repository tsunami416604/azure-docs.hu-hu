---
title: Azure Security Center és Azure Container Registry
description: Tudnivalók a tároló-beállításjegyzékek Azure Security Center való vizsgálatáról
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2020
ms.author: memildin
ms.openlocfilehash: b66969b26a801e6bd9aacf999c1c1ef9179ef1bd
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534668"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Security Center Azure Container Registry képvizsgálat

A Azure Container Registry (ACR) egy felügyelt, privát Docker beállításjegyzék-szolgáltatás, amely egy központi beállításjegyzékben tárolja és kezeli az Azure-beli központi telepítésekhez tartozó tároló lemezképeit. Ez a nyílt forráskódú Docker beállításjegyzék 2,0-es adatbázisán alapul.

Ha Azure Security Center standard szintű csomaggal rendelkezik, hozzáadhatók a Container nyilvántartók csomagja. Ez a választható funkció mélyebb betekintést nyújt a Azure Resource Manager-alapú nyilvántartásokban lévő rendszerképek biztonsági rései között. Engedélyezheti vagy letilthatja az előfizetés szintjén lévő köteget, hogy az előfizetéshez tartozó összes regisztrációs adatbázisra vonatkozzon. A szolgáltatás díját a [díjszabás lapon](security-center-pricing.md)látható módon számítjuk fel. A Container registrys csomag engedélyezésével biztosíthatja, hogy Security Center készen álljon a beállításjegyzékbe leküldeni kívánt rendszerképek vizsgálatára. 

## <a name="availability"></a>Rendelkezésre állás

- Kiadási állapot: **általánosan elérhető**
- Szükséges szerepkörök: **biztonsági olvasó** és [Azure Container Registry olvasó szerepkör](https://docs.microsoft.com/azure/container-registry/container-registry-roles)
- Támogatott nyilvántartások és lemezképek:
    - ✔ Linux rendszerű, a nyilvános internetről elérhető és a rendszerhéj-hozzáférést biztosító ACR-jegyzékeket.
    - ✘ Windows által üzemeltetett ACR-jegyzékek.
    - A "privát" ✘-Security Center megköveteli, hogy a kibocsátásiegység-forgalmi jegyzékek elérhetők legyenek a nyilvános internetről. Security Center jelenleg nem tud csatlakozni a szolgáltatáshoz, vagy beolvasni azokat a beállításjegyzékeket, amelyekhez hozzáférés van korlátozva tűzfallal, szolgáltatási végponttal vagy privát végpontokkal, például az Azure Private-hivatkozással.
    - A ✘ olyan Super minimalista képeket, például [Docker](https://hub.docker.com/_/scratch/) -rendszerképeket vagy "eltérítetlen" lemezképeket tartalmaz, amelyek csak egy alkalmazást és annak futásidejű függőségeit tartalmazzák csomagkezelő, rendszerhéj vagy operációs rendszer nélkül.
- Felhők 
    - ✔ Kereskedelmi felhők
    - ✘ Egyesült államokbeli kormányzati felhő
    - ✘ Kínai kormányzati felhő, egyéb kormányzati felhők


## <a name="when-are-images-scanned"></a>Mikor vannak beolvasva a képek?

Ha egy rendszerképet küld a beállításjegyzékbe, Security Center automatikusan megvizsgálja a képet. A rendszerkép vizsgálatának elindításához küldje le az adattárba.

Ha a vizsgálat befejeződik (általában körülbelül 2 percen belül, de akár 15 percet is igénybe vehet), a megállapítások a következőképpen érhetők el Security Center javaslatokként:

[![Példa Azure Security Center a Azure Container Registry (ACR) tárolt rendszerképben felderített biztonsági rések észlelésére](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Az integráció előnyei

A Security Center az előfizetésében Azure Resource Manager alapú ACR-jegyzékeket azonosít, és zökkenőmentesen biztosítja a következőket:

* **Azure-natív sebezhetőségi vizsgálat** az összes leküldött Linux-lemezképhez. Security Center megvizsgálja a képet az iparág vezető sebezhetőségét vizsgáló gyártótól, a Qualys. Ez a natív megoldás alapértelmezés szerint zökkenőmentesen integrálható.

* **Biztonsági javaslatok** az ismert biztonsági réseket tartalmazó Linux-lemezképekhez. Security Center részletesen ismerteti az egyes jelentett biztonsági réseket és a súlyossági besorolást. Emellett útmutatást nyújt a beállításjegyzékbe leküldett egyes rendszerbiztonsági rések javításához.

![Azure Security Center és Azure Container Registry (ACR) – magas szintű áttekintés](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Gyakori kérdések Azure Container Registry rendszerképek vizsgálatához

### <a name="how-does-security-center-scan-an-image"></a>Hogyan vizsgálja Security Center a rendszerképet?
A rendszerkép a beállításjegyzékből lett kihúzva. Ezután egy elkülönített homokozóban fut a Qualys-olvasóval, amely kibontja az ismert sebezhetőségek listáját.

Security Center szűrők és a vizsgálati eredmények osztályozása. Ha egy rendszerkép kifogástalan állapotú, Security Center megjelöli. A Security Center csak olyan rendszerképekhez hoz létre biztonsági javaslatokat, amelyeknek feloldhatók a problémák. Ha csak akkor értesíti, ha problémák merülnek fel, Security Center csökkenti a nemkívánatos tájékoztatási riasztások lehetséges lehetőségét.

### <a name="how-often-does-security-center-scan-my-images"></a>Milyen gyakran Security Center beolvasni a képeiket?
A képvizsgálatok minden leküldésen aktiválva lesznek.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Lekérhetem a vizsgálat eredményeit REST APIon keresztül?
Igen. Az eredmények az [alárendelt értékelések REST API](/rest/api/securitycenter/subassessments/list/)-ban találhatók. Emellett használhatja az Azure Resource Graph (ARG), a Kusto API-t az összes erőforráshoz: a lekérdezés egy adott vizsgálatot tud beolvasni.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Milyen beállításjegyzék-típusok vannak beolvasva? Milyen típusú számlázást kell fizetni?
A [rendelkezésre állási szakasz](#availability) felsorolja a Container nyilvántartók csomagja által támogatott tároló-beállításjegyzékek típusait. 

Ha az Azure-előfizetéshez nem támogatott kibocsátásiegység-forgalmi jegyzékek kapcsolódnak, nem lesznek beolvasva, és nem lesznek kiszámlázva.


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Security Center tárolójának biztonsági funkcióiról, tekintse meg a következő témakört:

* [Azure Security Center és tárolók biztonsága](container-security.md)

* [Integráció az Azure Kubernetes Service-szel](azure-kubernetes-service-integration.md)

* [Virtuális gépek védelme](security-center-virtual-machine-protection.md) – a Security Center javaslatainak ismertetése
