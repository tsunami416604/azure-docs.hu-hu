---
title: A Kubernetes-fürt áttekintése Microsoft Azure Stack Edge-eszközön | Microsoft Docs
description: Ismerteti, hogyan történik a Kubernetes megvalósítása az Azure Stack Edge-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: b85586a431a20102035e253537fc45c8a8a54796
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084105"
---
# <a name="kubernetes-on-your-azure-stack-edge-device"></a>Kubernetes az Azure Stack Edge-eszközön

A Kubernetes egy népszerű, nyílt forráskódú platform a tároló alkalmazások előkészítéséhez. Ez a cikk áttekintést nyújt a Kubernetes, majd leírja, hogy a Kubernetes hogyan működik az Azure Stack Edge-eszközön. 

## <a name="about-kubernetes"></a>A Kubernetes névjegye 

A Kubernetes egyszerű és megbízható platformot biztosít a tároló-alapú alkalmazások és a hozzájuk társított hálózatkezelési és tárolási összetevők kezeléséhez. A Kubernetes segítségével gyorsan kialakíthatja, továbbíthatja és méretezheti a tároló alkalmazásokat.

Nyílt platformként a Kubernetes használatával hozhat létre alkalmazásokat az előnyben részesített programozási nyelvvel, az operációs rendszer könyvtáraival vagy az üzenetkezelési busszal. A kiadások bevezetéséhez és üzembe helyezéséhez a Kubernetes integrálható a meglévő folyamatos integrációs és folyamatos kézbesítési eszközökkel.

További információ: [how Kubernetes Works](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s).

## <a name="kubernetes-on-azure-stack-edge"></a>Kubernetes Azure Stack Edge-ben

A Azure Stack Edge-eszközön létrehozhat egy Kubernetes-fürtöt a számítás konfigurálásával. Ha a számítási szerepkör konfigurálva van, a Kubernetes-fürt, beleértve a főkiszolgálói és a feldolgozó csomópontokat, minden üzembe van állítva és konfigurálva van. Ezt a fürtöt a rendszer felhasználja a munkaterhelések üzembe helyezéséhez `kubectl` , IoT Edge vagy Azure-ív használatával.

Az Azure Stack Edge-eszköz 1 csomópontos konfigurációként érhető el, amely az infrastruktúra-fürtöt képezi. A Kubernetes-fürt elkülönül az infrastruktúra-fürttől, és az infrastruktúra-fürtön van üzembe helyezve. Az infrastruktúra-fürt biztosítja a Azure Stack peremhálózati eszköz állandó tárterületét, miközben a Kubernetes-fürt kizárólag az alkalmazások előkészítésére szolgál. 

Ebben az esetben a Kubernetes-fürt fő csomópontja és egy feldolgozó csomópontja van. A fürtben található Kubernetes-csomópontok olyan virtuális gépek, amelyek az alkalmazásokat és a Felhőbeli munkafolyamatokat futtatják. 

A Kubernetes fő csomópontja felelős a fürt kívánt állapotának fenntartásáért. A fő csomópont a feldolgozó csomópontot is szabályozza, amely a tárolóban lévő alkalmazásokat futtatja. 

A következő ábra szemlélteti a Kubernetes megvalósítását egy 1 csomópontos Azure Stack peremhálózati eszközön. Az 1 csomópontos eszköz nem nagyon elérhető, és ha az egyetlen csomópont meghibásodik, az eszköz leáll. A Kubernetes-fürt is leáll.

![1 csomópontos Azure Stack peremhálózati eszköz Kubernetes architektúrája](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

A Kubernetes-fürt architektúrával kapcsolatos további információkért látogasson el a [Kubernetes Core fogalmak](https://kubernetes.io/docs/concepts/architecture/)témakörre.


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>Tárolási kötetek kiépítés

Az alkalmazások számítási feladatainak támogatásához csatlakoztathatja az állandó adatok tárolási köteteit az Azure Stack peremhálózati eszközök megosztásán. Statikus és dinamikus kötetek egyaránt használhatók. 

További információ: Storage-létesítési beállítások az [Azure stack Edge-eszköz Kubernetes-tárolójában](azure-stack-edge-gpu-kubernetes-storage.md)lévő alkalmazásokhoz.

## <a name="networking"></a>Hálózat

A Kubernetes hálózatkezelés lehetővé teszi a Kubernetes-hálózaton belüli kommunikáció konfigurálását, többek között a tárolók közötti hálózatkezelést, a pod-Pod hálózatkezelést, a pod-szolgáltatást és az internetről a szolgáltatásba történő hálózatkezelést. További információ: hálózati modell a [Kubernetes hálózatkezelésben az Azure stack Edge-eszközhöz](azure-stack-edge-gpu-kubernetes-networking.md).

## <a name="updates"></a>Frissítések

Mivel az új Kubernetes-verziók elérhetővé válnak, a fürt frissíthető az Azure Stack Edge-eszközhöz elérhető standard frissítések használatával. A frissítésével kapcsolatos lépésekért tekintse meg az [Azure stack Edge frissítéseinek alkalmazása](azure-stack-edge-gpu-install-update.md)című témakört.

## <a name="access-monitoring"></a>Hozzáférés, figyelés

A Azure Stack peremhálózati eszközön található Kubernetes-fürt lehetővé teszi a szerepköralapú hozzáférés-vezérlést (RBAC). További információ: [szerepköralapú hozzáférés-vezérlés a Kubernetes-fürthöz az Azure stack Edge-eszközön](azure-stack-edge-gpu-kubernetes-rbac.md).

A fürt és az erőforrások állapotát a Kubernetes irányítópulton is nyomon követheti. A tároló naplói is elérhetők. További információ: [a Kubernetes irányítópult használata a Kubernetes-fürt állapotának figyelésére az Azure stack Edge-eszközön](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).

A Azure Monitor a tárolók, csomópontok és vezérlők állapotának begyűjtésére szolgáló kiegészítő szolgáltatásként is elérhető. További információ: [Azure monitor áttekintése](../azure-monitor/overview.md)

<!--## Private container registry

Kubernetes on Azure Stack Edge device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>Alkalmazáskezelés

Miután létrehozta a Kubernetes-fürtöt az Azure Stack Edge-eszközön, a következő módszerek bármelyikével kezelheti a fürtön üzembe helyezett alkalmazásokat:

- Natív hozzáférés keresztül `kubectl`
- IoT Edge 
- Azure Arc

Ezeket a módszereket a következő részben ismertetjük.


### <a name="kubernetes-and-kubectl"></a>Kubernetes és kubectl

Miután telepítette a Kubernetes-fürtöt, a fürtön helyileg telepített alkalmazásokat az ügyfélgépről kezelheti. Az alkalmazásokkal való kommunikációhoz natív eszközt (például *kubectl* ) használhat a parancssorból. 

A Kubernetes-fürt üzembe helyezésével kapcsolatos további információkért látogasson el a [Kubernetes-fürt üzembe helyezése az Azure stack peremhálózati eszközön](azure-stack-edge-gpu-create-kubernetes-cluster.md). További információ a felügyeletről: a [Kubectl használata a Kubernetes-fürt felügyeletéhez az Azure stack Edge-eszközön](azure-stack-edge-gpu-create-kubernetes-cluster.md).


### <a name="kubernetes-and-iot-edge"></a>Kubernetes és IoT Edge

A Kubernetes integrálható IoT Edge munkaterhelésekkel Azure Stack Edge-eszközön, ahol a Kubernetes méretezhető, az ökoszisztéma és a IoT pedig a IoT központú ökoszisztémát biztosítja. A Kubernetes réteg infrastruktúra-rétegként szolgál Azure IoT Edge számítási feladatok telepítéséhez. A modul élettartamát és a hálózati terheléselosztást a Kubernetes felügyeli, míg az Edge-alkalmazás platformját a IoT Edge felügyeli.

Az alkalmazások Kubernetes-fürtön IoT Edge használatával történő központi telepítésével kapcsolatos további információkért lépjen a következőre: 

- [Állapot nélküli alkalmazások közzététele Azure stack peremhálózati eszközön IoT Edge használatával](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).


### <a name="kubernetes-and-azure-arc"></a>Kubernetes és az Azure arc

Az Azure arc egy hibrid felügyeleti eszköz, amely lehetővé teszi, hogy alkalmazásokat helyezzen üzembe a Kubernetes-fürtökön. Az Azure arc azt is lehetővé teszi, hogy a tárolók Azure Monitor használatával megtekinthesse és figyelje a fürtöket. További információért látogasson el az [Azure-arc-kompatibilis Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)című témakörre. Az Azure arc díjszabásával kapcsolatos információkért látogasson el az [Azure arc díjszabására](https://azure.microsoft.com/services/azure-arc/#pricing).


## <a name="next-steps"></a>Következő lépések

- További információ az Kubernetes Storage [Azure stack Edge-eszközön](azure-stack-edge-gpu-kubernetes-storage.md).
- A Kubernetes hálózati modell megismerése [Azure stack peremhálózati eszközön](azure-stack-edge-gpu-kubernetes-networking.md).
- [Azure stack Edge](azure-stack-edge-gpu-deploy-prep.md) üzembe helyezése Azure Portalban.
