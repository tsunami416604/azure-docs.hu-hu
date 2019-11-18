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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: fb8f5f2adf497a12b4ed54b381a2dbf85e160b8e
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123966"
---
# <a name="container-security-in-security-center"></a>Tárolók biztonsága Security Center

A Azure Security Center az Azure-natív megoldás a tárolók biztonságához. A Security Center a Felhőbeli számítási feladatok, a virtuális gépek, a kiszolgálók és a tárolók biztonsága terén is optimális, egyetlen üvegtábla.

Ez a cikk bemutatja, hogyan fejlesztheti, figyelheti és kezelheti a tárolók és alkalmazásaik biztonságát. Megtudhatja, hogyan segíti a Security Center a tárolók biztonságával kapcsolatos alapvető szempontokat:

* Sebezhetőségek kezelése
* A tároló környezetének megerősítése
* Futtatókörnyezet védelme

[![Azure Security Center tárolójának biztonsági lapja](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

## <a name="vulnerability-management---scanning-container-images-preview"></a>Biztonsági rések kezelése – tároló lemezképek ellenőrzése (előzetes verzió)
A Azure Container Registry figyeléséhez kapcsolódjon Security Centerhoz. Új rendszerképek leküldésekor Security Center megvizsgálja a képet a piacvezető sebezhetőség-ellenőrzési szállítótól, a Qualys.

Ha problémák merülnek fel, a Qualys vagy a Security Center – értesítést kap a Security Center irányítópulton. Security Center a biztonsági rések esetében gyakorlati ajánlásokat és súlyossági besorolást biztosít, valamint útmutatást nyújt a probléma megoldásához. Security Center javaslatainak részleteiért tekintse meg a [virtuális gépek védelmével](security-center-virtual-machine-protection.md##compute-and-app-recs)kapcsolatos javaslatok listáját.

## <a name="environment-hardening"></a>Környezet megerősítése

### <a name="continuous-monitoring-of-your-docker-configuration"></a>A Docker-konfiguráció folyamatos figyelése
Azure Security Center azonosítja a IaaS Linux rendszerű virtuális gépeken üzemeltetett nem felügyelt tárolókat, illetve a Docker-tárolókat futtató más Linux-gépeket. Security Center folyamatosan elemzi a tárolók konfigurációit. Ezután összehasonlítja azokat a [Center for Internet Security (CIS) Docker-teljesítményteszttel](https://www.cisecurity.org/benchmark/docker/). 

Security Center tartalmazza a CIS Docker-teljesítményteszt teljes szabályait, és riasztást küld, ha a tárolók nem elégítik ki a vezérlőelemek egyikét sem. Ha helytelen konfigurációkat talál, Security Center biztonsági javaslatokat hoz létre. A javaslatok **lapon** megtekintheti az ajánlásokat és javíthatja a problémákat. Emellett a **containers (tárolók** ) lapon megjelenő javaslatokat is láthatja, amelyek a Docker-vel üzembe helyezett összes virtuális gépet megjelenítik. Ha a virtuális gép biztonsági problémáit vizsgálja, Security Center további információkat nyújt a gépen lévő tárolókkal kapcsolatban. Ezek az adatok tartalmazzák a Docker-verziót és a gazdagépen futó rendszerképek számát. A javaslatok részleteiről [itt](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-protection)olvashat bővebben. 

>[!NOTE]
> Ezek a CIS-alapú teljesítményteszt-ellenőrzések nem fognak futni az AK által felügyelt példányokon vagy Databricks által felügyelt virtuális gépeken.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>A Kubernetes-fürtök folyamatos figyelése (előzetes verzió)
A Security Center együttműködik az Azure Kubernetes szolgáltatással (ak), a Microsoft felügyelt tároló-előkészítési szolgáltatásával a tároló alkalmazások fejlesztéséhez, üzembe helyezéséhez és kezeléséhez.

Az AK biztonsági ellenőrzéseket és láthatóságot biztosít a fürtök biztonsági állapotáról. Security Center a következő funkciókat használja:
* Az AK-fürtök konfigurációjának folyamatos figyelése
* Iparági szabványokhoz igazított biztonsági javaslatok előállítása

Security Center javaslatairól a [virtuális gép védelme](security-center-virtual-machine-protection.md)című témakörben olvashat bővebben.

## <a name="run-time-protection---real-time-threat-detection"></a>Futásidejű védelem – valós idejű fenyegetések észlelése 

Security Center valós idejű veszélyforrások észlelését teszi lehetővé a tároló környezetek számára, és riasztásokat hoz létre a gyanús tevékenységekhez. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét.

A fenyegetéseket a gazdagép és az AK-fürt szintjén észleljük. Részletekért lásd: [Azure Container Service](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-).


## <a name="to-view-the-security-posture-of-your-container-related-resources"></a>A tárolóval kapcsolatos erőforrások biztonsági állapotának megtekintése
1.  Nyissa meg Security Center **számítási & alkalmazások** lapját.
2.  Kattintson a **tárolók** fülre. Ekkor megjelenik az AK-fürtök, az ACR-jegyzékek és a Docker-t futtató virtuális gépek testtartása.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Security Center tárolók biztonságáról, tekintse meg a következőt:
* Az [Azure Kubernetes szolgáltatással való integráció](azure-kubernetes-service-integration.md) részletei

* Az Azure Container Registry-nal való [integráció](azure-container-registry-integration.md) részletei