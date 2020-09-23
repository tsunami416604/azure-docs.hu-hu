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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 1335b1034304b7efe2b113f7ff2d2927fea41638
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977359"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Security Center Azure Container Registry képvizsgálat

A Azure Container Registry (ACR) egy felügyelt, privát Docker beállításjegyzék-szolgáltatás, amely egy központi beállításjegyzékben tárolja és kezeli az Azure-beli központi telepítésekhez tartozó tároló lemezképeit. Ez a nyílt forráskódú Docker beállításjegyzék 2,0-es adatbázisán alapul.

Lehetővé teszi az **Azure Defender számára a tároló** -beállításjegyzékek számára a Azure Resource Manager-alapú nyilvántartásokban lévő rendszerképek sebezhetőségének mélyebb láthatóságát. Engedélyezheti vagy letilthatja a csomagot az előfizetési szinten, hogy az előfizetéshez tartozó összes regisztrációs adatbázisra vonatkozzon. A szolgáltatás díját a [díjszabás lapon](security-center-pricing.md)látható módon számítjuk fel. Az Azure Defender engedélyezése lehetővé teszi, hogy Security Center készen álljon a beállításjegyzékbe leküldhető lemezképek vizsgálatára. 


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
A rendelkezésre állási szakasz felsorolja az Azure Defender által a Container nyilvántartók számára támogatott tároló-nyilvántartások típusait. 

Ha az Azure-előfizetéshez nem támogatott kibocsátásiegység-forgalmi jegyzékek kapcsolódnak, nem lesznek beolvasva, és nem lesznek kiszámlázva.


## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Security Center tárolójának biztonsági funkcióiról, tekintse meg a következő témakört:

* [Azure Security Center és tárolók biztonsága](container-security.md)

* [Integráció az Azure Kubernetes Service-szel](azure-kubernetes-service-integration.md)


