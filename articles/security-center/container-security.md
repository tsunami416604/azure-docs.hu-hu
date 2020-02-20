---
title: Tárolók biztonsága a Azure Security Centerban | Microsoft Docs
description: Ismerkedjen meg Azure Security Center tárolók biztonsági funkcióival.
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
ms.openlocfilehash: c18751d315af6da1a4b2f06aaca28c84746b7be5
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470768"
---
# <a name="container-security-in-security-center"></a>Tárolók biztonsága Security Center

A Azure Security Center az Azure-natív megoldás a tárolók biztonságához. A Security Center a Felhőbeli számítási feladatok, a virtuális gépek, a kiszolgálók és a tárolók biztonsága terén is optimális, egyetlen üvegtábla.

Ez a cikk azt ismerteti, hogyan segíti a Security Center a tárolók és alkalmazásaik biztonságának javítását, figyelését és karbantartását. Megtudhatja, hogyan segíti a Security Center a tárolók biztonságával kapcsolatos alapvető szempontokat:

* Sebezhetőségek kezelése
* A tároló környezetének megerősítése
* Futtatókörnyezet védelme

[![Azure Security Center tárolójának biztonsági lapja](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

A szolgáltatások használatáról a [tárolók biztonságának figyelését](monitor-container-security.md)ismertető cikkben olvashat bővebben.

## <a name="vulnerability-management---scanning-container-images-preview"></a>Biztonsági rések kezelése – tároló lemezképek ellenőrzése (előzetes verzió)
Az ARM-alapú Azure Container Registry figyeléséhez győződjön meg arról, hogy a Security Center standard szintű csomagjában van (lásd: [díjszabás](/azure/security-center/security-center-pricing)). Ezután engedélyezze a választható tároló-beállításjegyzékek köteget. Új rendszerképek leküldésekor Security Center megvizsgálja a képet a piacvezető sebezhetőség-ellenőrzési szállítótól, a Qualys.

Ha problémák merülnek fel, a Qualys vagy a Security Center – értesítést kap a Security Center irányítópulton. Security Center a biztonsági rések esetében gyakorlati ajánlásokat és súlyossági besorolást biztosít, valamint útmutatást nyújt a probléma megoldásához. A Security Center a tárolókkal kapcsolatos javaslatairól a [javaslatok hivatkozási listájában](recommendations-reference.md#recs-containers)talál további információt.

## <a name="environment-hardening"></a>Környezet megerősítése

### <a name="continuous-monitoring-of-your-docker-configuration"></a>A Docker-konfiguráció folyamatos figyelése
Azure Security Center azonosítja a IaaS Linux rendszerű virtuális gépeken üzemeltetett nem felügyelt tárolókat, illetve a Docker-tárolókat futtató más Linux-gépeket. Security Center folyamatosan elemzi a tárolók konfigurációit. Ezután összehasonlítja azokat a [Center for Internet Security (CIS) Docker-teljesítményteszttel](https://www.cisecurity.org/benchmark/docker/).

Security Center tartalmazza a CIS Docker-teljesítményteszt teljes szabályait, és riasztást küld, ha a tárolók nem elégítik ki a vezérlőelemek egyikét sem. Ha helytelen konfigurációkat talál, Security Center biztonsági javaslatokat hoz létre. A javaslatok **lapon** megtekintheti az ajánlásokat és javíthatja a problémákat. Emellett a **containers (tárolók** ) lapon megjelenő javaslatokat is láthatja, amelyek a Docker-vel üzembe helyezett összes virtuális gépet megjelenítik. 

A szolgáltatással kapcsolatos releváns Security Center javaslatok részleteiért tekintse meg az ajánlásokat ismertető táblázat [tároló szakaszát](recommendations-reference.md#recs-containers) .

Ha egy virtuális gép biztonsági problémáit vizsgálja, Security Center további információkat nyújt a gépen lévő tárolókkal kapcsolatban. Ezek az adatok tartalmazzák a Docker-verziót és a gazdagépen futó rendszerképek számát. 

>[!NOTE]
> Ezek a CIS-alapú teljesítményteszt-ellenőrzések nem fognak futni az AK által felügyelt példányokon vagy Databricks által felügyelt virtuális gépeken.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>A Kubernetes-fürtök folyamatos figyelése (előzetes verzió)
A Security Center együttműködik az Azure Kubernetes szolgáltatással (ak), a Microsoft felügyelt tároló-előkészítési szolgáltatásával a tároló alkalmazások fejlesztéséhez, üzembe helyezéséhez és kezeléséhez.

Az AK biztonsági ellenőrzéseket és láthatóságot biztosít a fürtök biztonsági állapotáról. Security Center a következő funkciókat használja:
* Az AK-fürtök konfigurációjának folyamatos figyelése
* Iparági szabványokhoz igazított biztonsági javaslatok előállítása

A szolgáltatással kapcsolatos releváns Security Center javaslatok részleteiért tekintse meg az ajánlásokat ismertető táblázat [tároló szakaszát](recommendations-reference.md#recs-containers) .

## <a name="run-time-protection---real-time-threat-detection"></a>Futásidejű védelem – valós idejű fenyegetések észlelése

Security Center valós idejű veszélyforrások észlelését teszi lehetővé a tároló környezetek számára, és riasztásokat hoz létre a gyanús tevékenységekhez. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét.

A fenyegetéseket a gazdagép és az AK-fürt szintjén észleljük. A részletekért lásd: [Az Azure-tárolók veszélyforrások észlelése](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-).


## <a name="container-security-faq"></a>Container Security – gyakori kérdések

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Milyen típusú képeket tud Azure Security Center a vizsgálat?
Security Center a rendszerhéj-hozzáférést biztosító Linux operációs rendszerű lemezképeket vizsgálja. 

A Qualys képolvasó nem támogatja a Super minimalista rendszerképeket, például a [Docker](https://hub.docker.com/_/scratch/) -rendszerképeket vagy a "eltérítetlen" lemezképeket, amelyek csak az alkalmazást és a futásidejű függőségeit tartalmazzák (csomagkezelő, rendszerhéj vagy operációs rendszer nélkül).

### <a name="how-does-we-scan-azure-security-center-scan-an-image"></a>Hogyan vizsgáljuk meg Azure Security Center a képek vizsgálatát?
A rendszer kinyeri a rendszerképet a beállításjegyzékből. Ezután egy elkülönített homokozóban fut a Qualys szkennerrel, amely kibontja az ismert sebezhetőségek listáját.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Milyen gyakran Azure Security Center beolvasni a képeiket?
A képvizsgálatok minden leküldésen aktiválva lesznek.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Lekérhetem a vizsgálat eredményeit REST APIon keresztül?
Igen. Az eredmények az [alárendelt értékelések REST API](/rest/api/securitycenter/subassessments/list/)-ban találhatók. Emellett használhatja az Azure Resource Graph (ARG), a Kusto API-t az összes erőforráshoz: a lekérdezés egy adott vizsgálatot tud beolvasni.
 

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Security Center tárolók biztonságáról, tekintse meg a következő kapcsolódó cikkeket:

* A tárolóval kapcsolatos erőforrások biztonsági állapotának megtekintéséhez tekintse meg a [gépek és alkalmazások védelme](security-center-virtual-machine-protection.md#containers)a tárolókkal foglalkozó szakaszt.

* Az [Azure Kubernetes szolgáltatással való integráció](azure-kubernetes-service-integration.md) részletei

* Az Azure Container Registry-nal való [integráció](azure-container-registry-integration.md) részletei