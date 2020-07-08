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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: c01ed6dbbd6e1f7febfb99df11d2ee67cb1e5465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800603"
---
# <a name="container-security-in-security-center"></a>Tárolók biztonsága Security Center

Azure Security Center az Azure-natív megoldás a tárolók biztonságossá tételéhez. A Security Center a következő tároló-erőforrástípusok elleni védelemmel rendelkezhet:



|Erőforrás |Name  |Részletek  |
|:---------:|---------|---------|
|![Tároló gazdagépe](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)|Tároló-gazdagépek (a Docker-t futtató virtuális gépek)|A Security Center megvizsgálja a Docker-konfigurációkat, és egy listát készít az összes átvizsgált hibás szabályról, így láthatóvá teszi a konfigurációs hibákat. Security Center útmutatást nyújt a problémák gyors megoldásához és az idő megtakarításához. A Security Center folyamatosan értékeli a Docker-konfigurációkat, és tájékoztatja a legfrissebb állapotukról.|
|![Kubernetes szolgáltatás](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)|Azure Kubernetes szolgáltatás (ak) fürtök|A standard szintű felhasználók számára, hogy mélyebben lássa el az AK-csomópontokat, a felhő forgalmát és a biztonsági ellenőrzéseket [Security Center a választható AK-csomaggal](azure-kubernetes-service-integration.md) .|
|![Tárolóregisztrációs adatbázis](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)|Azure Container Registry (ACR) jegyzékek|Az ARM-alapú ACR-jegyzékekben található rendszerképek biztonsági réseit alaposabban megismerheti, ha a standard szintű felhasználók számára [Security Center opcionális ACR-csomaggal](azure-kubernetes-service-integration.md) rendelkezik.|
||||


Ez a cikk bemutatja, hogyan használhatja ezeket a csomagokat a tárolók és alkalmazásaik biztonságának fejlesztéséhez, figyeléséhez és karbantartásához. Megtudhatja, hogyan segíti a Security Center a tárolók biztonságával kapcsolatos alapvető szempontokat:

- [Sebezhetőségi kezelés – tároló lemezképének vizsgálata](#vulnerability-management---scanning-container-images)
- [Környezet megerősítése – a Docker-konfiguráció és a Kubernetes-fürtök folyamatos figyelése](#environment-hardening)
- [Futásidejű védelem – valós idejű fenyegetések észlelése](#run-time-protection---real-time-threat-detection)

[![Azure Security Center a tároló biztonsága lapon](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

A szolgáltatások használatáról a [tárolók biztonságának figyelését](monitor-container-security.md)ismertető cikkben olvashat bővebben.

## <a name="vulnerability-management---scanning-container-images"></a>Sebezhetőségi kezelés – tároló lemezképének vizsgálata
Az ARM-alapú Azure Container Registry figyeléséhez győződjön meg arról, hogy a Security Center standard szintű csomagjában van (lásd: [díjszabás](/azure/security-center/security-center-pricing)). Ezután engedélyezze a választható tároló-beállításjegyzékek köteget. Új rendszerképek leküldésekor Security Center megvizsgálja a képet a piacvezető sebezhetőség-ellenőrzési szállítótól, a Qualys.

Ha problémák merülnek fel, a Qualys vagy a Security Center – értesítést kap a Security Center irányítópulton. Security Center a biztonsági rések esetében gyakorlati ajánlásokat és súlyossági besorolást biztosít, valamint útmutatást nyújt a probléma megoldásához. A Security Center a tárolókkal kapcsolatos javaslatairól a [javaslatok hivatkozási listájában](recommendations-reference.md#recs-containers)talál további információt.

Security Center szűrők és a vizsgálati eredmények osztályozása. Ha egy rendszerkép kifogástalan állapotú, Security Center megjelöli. A Security Center csak olyan rendszerképekhez hoz létre biztonsági javaslatokat, amelyeknek feloldhatók a problémák. Ha csak akkor értesíti, ha problémák merülnek fel, Security Center csökkenti a nemkívánatos tájékoztatási riasztások lehetséges lehetőségét.

## <a name="environment-hardening"></a>Környezet megerősítése

### <a name="continuous-monitoring-of-your-docker-configuration"></a>A Docker-konfiguráció folyamatos figyelése
Azure Security Center azonosítja a IaaS Linux rendszerű virtuális gépeken üzemeltetett nem felügyelt tárolókat, illetve a Docker-tárolókat futtató más Linux-gépeket. Security Center folyamatosan elemzi a tárolók konfigurációit. Ezután összehasonlítja azokat a [Center for Internet Security (CIS) Docker-teljesítményteszttel](https://www.cisecurity.org/benchmark/docker/).

Security Center tartalmazza a CIS Docker-teljesítményteszt teljes szabályait, és riasztást küld, ha a tárolók nem elégítik ki a vezérlőelemek egyikét sem. Ha helytelen konfigurációkat talál, Security Center biztonsági javaslatokat hoz létre. A javaslatok **lapon** megtekintheti az ajánlásokat és javíthatja a problémákat. Emellett a **containers (tárolók** ) lapon megjelenő javaslatokat is láthatja, amelyek a Docker-vel üzembe helyezett összes virtuális gépet megjelenítik. 

A szolgáltatással kapcsolatos releváns Security Center javaslatok részleteiért tekintse meg az ajánlásokat ismertető táblázat [tároló szakaszát](recommendations-reference.md#recs-containers) .

Ha egy virtuális gép biztonsági problémáit vizsgálja, Security Center további információkat nyújt a gépen lévő tárolókkal kapcsolatban. Ezek az adatok tartalmazzák a Docker-verziót és a gazdagépen futó rendszerképek számát. 

>[!NOTE]
> Ezek a CIS-alapú teljesítményteszt-ellenőrzések nem fognak futni az AK által felügyelt példányokon vagy Databricks által felügyelt virtuális gépeken.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>A Kubernetes-fürtök folyamatos figyelése
A Security Center együttműködik az Azure Kubernetes szolgáltatással (ak), a Microsoft felügyelt tároló-előkészítési szolgáltatásával a tároló alkalmazások fejlesztéséhez, üzembe helyezéséhez és kezeléséhez.

Az AK biztonsági ellenőrzéseket és láthatóságot biztosít a fürtök biztonsági állapotáról. Security Center a következő funkciókat használja:
* Az AK-fürtök konfigurációjának folyamatos figyelése
* Iparági szabványokhoz igazított biztonsági javaslatok előállítása

A szolgáltatással kapcsolatos releváns Security Center javaslatok részleteiért tekintse meg az ajánlásokat ismertető táblázat [tároló szakaszát](recommendations-reference.md#recs-containers) .

## <a name="run-time-protection---real-time-threat-detection"></a>Futásidejű védelem – valós idejű fenyegetések észlelése

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]




## <a name="next-steps"></a>További lépések

Ebben az áttekintésben megtanulta a tárolók biztonságának alapvető elemeit Azure Security Centerban. Folytassa a [tárolók biztonságának monitorozásával](monitor-container-security.md).
> [!div class="nextstepaction"]
> [A tárolók biztonságának figyelése](monitor-container-security.md)