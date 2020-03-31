---
title: Tárolóbiztonság az Azure Security Centerben | Microsoft dokumentumok
description: Ismerje meg az Azure Security Center tárolóbiztonsági funkcióit.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: d46e2a9820ec0c45d197f135428f1ace712b2fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125145"
---
# <a name="container-security-in-security-center"></a>Tárolóbiztonság a Biztonsági központban

Az Azure Security Center az Azure-natív megoldás a tárolók biztonsága. A Security Center egyben az optimális egyetlen üvegpanel a felhőbeli számítási feladatok, virtuális gépek, kiszolgálók és tárolók biztonsága érdekében.

Ez a cikk azt ismerteti, hogy a Security Center hogyan segíti a tárolók és alkalmazásaik biztonságának javítását, figyelését és karbantartását. Megtudhatja, hogy a Security Center hogyan segíti a tárolóbiztonság alábbi alapvető szempontjait:

* A biztonsági rés kezelése
* A tároló környezetének megkeményedése
* Futásidejű védelem

[![Az Azure Security Center tárolóbiztonsági lapja](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

A szolgáltatások használatáról a [Tárolók biztonságának figyelése](monitor-container-security.md)című témakörben talál.

## <a name="vulnerability-management---scanning-container-images"></a>Biztonsági rés kezelése – tárolóképek beolvasása
Az ARM-alapú Azure Container Registry figyeléséhez győződjön meg arról, hogy a Security Center szabványos csomagját tartalmazza (lásd: [díjszabás).](/azure/security-center/security-center-pricing) Ezután engedélyezze az opcionális tárolójegyzék-csomagot. Új kép leküldésekor a Security Center az iparágvezető biztonsági résvizsgálati szállítójának, a Qualys-nak a képolvasójával olvassa be a képet.

Ha problémákat talál – a Qualys vagy a Security Center – értesítést kap a Security Center irányítópultján. A Security Center minden biztonsági réshez használható javaslatokat, valamint súlyossági besorolást és útmutatást nyújt a probléma megoldásához. A Security Center tárolókra vonatkozó javaslatainak részleteit a [javaslatok referencialistájában találja.](recommendations-reference.md#recs-containers)

A Security Center szűri és osztályolja a lapolvasó ból származó eredményeket. Ha egy kép kifogástalan állapotú, a Security Center ilyenként jelöli meg. A Security Center csak a megoldandó problémákat tartalmazó képekre vonatkozó biztonsági javaslatokat hoz létre. Ha csak problémák esetén értesít, a Security Center csökkenti a nem kívánt információs riasztások lehetőségét.

## <a name="environment-hardening"></a>Környezetedzés

### <a name="continuous-monitoring-of-your-docker-configuration"></a>A Docker-konfiguráció folyamatos figyelése
Az Azure Security Center azonosítja az IaaS Linux-virtuális gépeken vagy más, Docker-tárolókat futtató Linux-gépeken tárolt nem felügyelt tárolókat. A Security Center folyamatosan felméri ezeknek a tárolóknak a konfigurációit. Ezután összehasonlítja őket a [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

A Security Center tartalmazza a CIS Docker benchmark teljes szabálykészletét, és figyelmezteti, ha a tárolók nem felelnek meg a vezérlők egyikének sem. Ha helytelen konfigurációkat talál, a Security Center biztonsági javaslatokat hoz létre. A **javaslatok lapon** megtekintheti a javaslatokat, és orvosolja a problémákat. A Tárolók lapon is megjelennek a **javaslatok,** amelyek a Docker-sel telepített összes virtuális gépet megjelenítik. 

A biztonsági központ erre a funkcióhoz esetleg megjelenő javaslatairól a javaslatok hivatkozási táblázat [tárolószakaszában](recommendations-reference.md#recs-containers) talál.

A virtuális gép biztonsági problémáinak feltárásasorán a Security Center további információkat nyújt a számítógépen lévő tárolókról. Ezek az információk közé tartozik a Docker-verzió és a gazdagépen futó képek száma. 

>[!NOTE]
> Ezek a CIS-teljesítményteszt-ellenőrzések nem futnak a KS által felügyelt példányokon vagy databricks által felügyelt virtuális gépeken.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>A Kubernetes-fürtök folyamatos figyelése
A Security Center együttműködik az Azure Kubernetes-szolgáltatással (AKS), a Microsoft felügyelt tárolóvezénylési szolgáltatásával a tárolóalapú alkalmazások fejlesztéséhez, üzembe helyezéséhez és kezeléséhez.

Az AKS biztonsági vezérlőket és a fürtök biztonsági állapotát is láthatóvá teszi. A Security Center a következő szolgáltatásokat használja:
* Az AKS-fürtök konfigurációjának folyamatos figyelése
* Az iparági szabványoknak megfelelő biztonsági javaslatok létrehozása

A biztonsági központ erre a funkcióhoz esetleg megjelenő javaslatairól a javaslatok hivatkozási táblázat [tárolószakaszában](recommendations-reference.md#recs-containers) talál.

## <a name="run-time-protection---real-time-threat-detection"></a>Futásidejű védelem – Valós idejű fenyegetésészlelés

A Security Center valós idejű fenyegetésészlelést biztosít a tárolóba helyezett környezetekben, és riasztásokat hoz létre a gyanús tevékenységekhez. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét.

Észleljük a fenyegetéseket a gazdagép és az AKS-fürt szintjén. A részleteket az [Azure-tárolók fenyegetésészlelése ( fenyegetésészlelés) ismerteti.](threat-protection.md#azure-containers)


## <a name="container-security-faq"></a>Konténerbiztonság – gyakori kérdések

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Milyen típusú képeket lehet az Azure Security Center által bekéselt?
A Security Center megvizsgálja a Linux operációs rendszer alapú lemezképeket, amelyek shell hozzáférést biztosítanak. 

A Qualys-lapolvasó nem támogatja a szuper minimalista képeket, például [a Docker-kaparós lemezképeket,](https://hub.docker.com/_/scratch/) vagy a "Distroless" lemezképeket, amelyek csak az alkalmazást és a futásidejű függőségeket tartalmazzák csomagkezelő, rendszerhéj vagy operációs rendszer nélkül.

### <a name="how-does-azure-security-center-scan-an-image"></a>Hogyan késik az Azure Security Center egy lemezkép?
A rendszerképet a rendszer lekéri a rendszerleíró adatbázisból. Ezután egy elkülönített homokozóban fut a Qualys szkennerrel, amely kinyeri az ismert biztonsági rések listáját.

A Security Center szűri és osztályolja a lapolvasó ból származó eredményeket. Ha egy kép kifogástalan állapotú, a Security Center ilyenként jelöli meg. A Security Center csak a megoldandó problémákat tartalmazó képekre vonatkozó biztonsági javaslatokat hoz létre. Ha csak problémák esetén értesít, a Security Center csökkenti a nem kívánt információs riasztások lehetőségét.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Milyen gyakran szokta be az Azure Security Center a lemezképeimet?
A képletapogatás minden lenyomáskor aktiválódik.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Lekaphatom a vizsgálat eredményeit a REST API-n keresztül?
Igen. Az eredmények a [Sub-Assessments Rest API alatt](/rest/api/securitycenter/subassessments/list/)találhatók. Emellett használhatja az Azure Resource Graph (ARG), a Kusto-szerű API-t az összes erőforráshoz: egy lekérdezés lehívhat egy adott vizsgálatot.
 

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Security Center tárolóbiztonságáról, olvassa el az alábbi kapcsolódó cikkeket:

* A tárolóval kapcsolatos erőforrások biztonsági állapotának megtekintéséhez tekintse meg a [Gépek és alkalmazások védelme](security-center-virtual-machine-protection.md#containers)című tárolószakaszt.

* Az [Azure Kubernetes-szolgáltatással való integráció](azure-kubernetes-service-integration.md) részletei

* Az [Azure Container Registry szolgáltatással való integráció](azure-container-registry-integration.md) részletei