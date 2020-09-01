---
title: A Kubernetes számítási feladatok kezelésének megismerése Azure Stack Edge-eszközön | Microsoft Docs
description: Ismerteti, hogyan kezelhetők a Kubernetes számítási feladatok az Azure Stack Edge-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 38c46bdcce64f726b3a7ddf74e0cfd10a14ba663
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268030"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-device"></a>Kubernetes munkaterhelés-kezelés az Azure Stack Edge-eszközön

A Azure Stack Edge-eszközön létrejön egy Kubernetes-fürt a számítási szerepkör konfigurálásakor. A Kubernetes-fürt létrehozása után a tároló alkalmazások a Kubernetes-fürtön helyezhetők üzembe a hüvelyben. A számítási feladatok a Kubernetes-fürtön való üzembe helyezésének különböző módjai vannak. 

Ez a cikk azokat a különböző módszereket ismerteti, amelyekkel a munkaterhelések a Azure Stack peremhálózati eszközön helyezhetők üzembe.

## <a name="workload-types"></a>Számítási feladatok típusai

Az Azure Stack Edge-eszközön üzembe helyezhető munkaterhelések két gyakori típusa állapot nélküli alkalmazások vagy állapot-nyilvántartó alkalmazások.

- Az **állapot nélküli alkalmazások** nem őrzik meg az állapotukat, és nem mentik az adatvesztést az állandó tárterületre. Az összes felhasználói és munkamenet-adatmennyiség az ügyféllel együtt marad. Néhány példa az állapot nélküli alkalmazásokra, például az Nginx és más webalkalmazások webes felületére.

    Az állapot nélküli alkalmazások fürtön való üzembe helyezéséhez létrehozhat egy Kubernetes-telepítést. 

- Az **állapot-nyilvántartó alkalmazások** megkövetelik, hogy az állapotuk mentése megtörténjen. Az állapot-nyilvántartó alkalmazások állandó tárterületet (például állandó köteteket) használnak a kiszolgáló vagy más felhasználók általi használatra való adatmentéshez. Az állapot-nyilvántartó alkalmazások példái közé tartoznak például az MongoDB-adatbázisok.

    Az állapot-nyilvántartó alkalmazások üzembe helyezéséhez létrehozhat egy Kubernetes-telepítést. 

## <a name="deployment-flow"></a>Üzembe helyezési folyamat

Az alkalmazások Azure Stack peremhálózati eszközön való üzembe helyezéséhez kövesse az alábbi lépéseket: 
 
1. **Hozzáférés konfigurálása**: először a PowerShell-RunSpace használatával hozzon létre egy felhasználót, hozzon létre egy névteret, és adja meg a felhasználói hozzáférést a névtérhez.
2. **Tároló konfigurálása**: ezután a Azure Portal Azure stack Edge erőforrását fogja használni az állandó kötetek létrehozásához, statikus vagy dinamikus kiépítés használatával a telepítendő állapot-nyilvántartó alkalmazások számára.
3. **Hálózatkezelés konfigurálása**: Végezetül a szolgáltatásokkal külső és a Kubernetes-fürtön is elérhetővé teheti az alkalmazásokat.
 
## <a name="deployment-types"></a>Központi telepítési típusok

A munkaterhelések üzembe helyezésének három fő módja van. Az üzembe helyezési módszerek mindegyike lehetővé teszi, hogy az eszköz egy különálló névteréhez kapcsolódjon, majd állapot nélküli vagy állapot-nyilvántartó alkalmazásokat helyezzen üzembe.

![Kubernetes munkaterhelés üzembe helyezése](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Helyi telepítés**: ez egy parancssori hozzáférési eszközön keresztül történik, `kubectl` amely lehetővé teszi a Kubernetes üzembe helyezését `yamls` . A Kubernetes-fürthöz a fájllal létrehozott Azure Stack szélén csatlakozhat `kubeconfig` . További információért látogasson el a [Kubernetes-fürt eléréséhez a kubectl-on keresztül](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **IoT Edge**üzemelő példány: ez a IoT Edgeon keresztül történik, amely az Azure IoT hubhoz csatlakozik. Az Azure Stack Edge-eszközön a névtéren keresztül csatlakozik a Kubernetes-fürthöz `iotedge` . Az ebben a névtérben üzembe helyezett IoT Edge ügynökök felelősek az Azure-hoz való csatlakozásért. A `IoT Edge deployment.json` konfigurációt az Azure DEVOPS CI/CD használatával alkalmazhatja. A névtér és a IoT Edge kezelése a Cloud Operator használatával történik.

- **Azure/arc üzembe helyezése**: az Azure arc egy hibrid felügyeleti eszköz, amely lehetővé teszi, hogy alkalmazásokat helyezzen üzembe a Kubernetes-fürtökön. A Kubernetes-fürtöt a Azure Stack peremhálózati eszközén kell összekapcsolni a használatával `azure-arc namespace` . Az ügynökök üzembe helyezése ebben a névtérben történik, amely felelős az Azure-hoz való csatlakozásért. A központi telepítési konfigurációt a GitOps-alapú konfiguráció-kezelés használatával alkalmazhatja. Az Azure arc azt is lehetővé teszi, hogy a tárolók Azure Monitor használatával megtekinthesse és figyelje a fürtöket. További információért látogasson el az [Azure-arc-kompatibilis Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)című témakörre.

## <a name="choose-the-deployment-type"></a>A központi telepítés típusának kiválasztása

Az alkalmazások központi telepítése során vegye figyelembe a következő információkat:

- Egy **vagy több típus**: egyetlen központi telepítési lehetőség vagy különböző telepítési lehetőségek kombinációja is kiválasztható.
- A felhő és a **helyi**környezet: az alkalmazástól függően a helyi telepítést a kubectl vagy a Cloud deployment használatával IoT Edge és az Azure arc segítségével választhatja ki. 
    - A helyi üzembe helyezés a fejlesztési forgatókönyvekhez alkalmasabb. Ha helyi központi telepítést választ, arra a hálózatra korlátozódik, amelyben az Azure Stack Edge-eszköz telepítve van.
    - Ha van olyan felhőalapú ügynöke, amelyet üzembe helyezhet, üzembe helyezheti a Felhőbeli operátort, és használhatja a felhőalapú felügyeletet.
- **IoT vs Azure arc**: az üzembe helyezés megválasztása a termék forgatókönyvének szándékával is függ. Ha olyan alkalmazásokat vagy tárolókat helyez üzembe, amelyek mélyebb integrációt végeznek a IoT vagy a IoT ökoszisztémával, akkor ki kell választania az alkalmazások központi telepítésének IoT Edge módját. Ha már rendelkezik Kubernetes üzemelő példányokkal, az Azure arc az előnyben részesített választás lenne.


## <a name="next-steps"></a>További lépések

Ha helyileg szeretné telepíteni az alkalmazást a kubectl-on keresztül, olvassa el a következőt:

- [Állapot nélküli alkalmazás üzembe helyezése az Azure stack Edge-n keresztül a kubectl-on keresztül](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Alkalmazás IoT Edgeon keresztüli üzembe helyezéséhez lásd:

- [Helyezzen üzembe egy minta modult az Azure stack Edge-ben IoT Edge használatával](azure-stack-edge-gpu-deploy-sample-module.md).

Az alkalmazások Azure arc használatával történő üzembe helyezéséhez lásd:

- [Alkalmazás üzembe helyezése az Azure arc használatával](azure-stack-edge-gpu-deploy-sample-module.md).
