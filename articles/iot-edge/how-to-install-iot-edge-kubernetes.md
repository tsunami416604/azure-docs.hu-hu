---
title: IoT Edge telepítése Kubernetes |} A Microsoft Docs
description: Ismerje meg az IoT Edge használatával egy helyi fejlesztési fürt környezet Kubernetes telepítése
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 66aca7be9a2df93d846d7e78bc64c93279afc2d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160695"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>IoT Edge Kubernetes (előzetes verzió) telepítése

IoT Edge használatával, egy rugalmas, magas rendelkezésre állású infrastruktúrát rétegként Kubernetes integrálható. Regisztrálja azokat az IoT Edge *egyéni erőforrás-definíció* (CRD) a Kubernetes API-kiszolgálóval. Emellett tartalmazza egy *operátor* (IoT Edge-ügynök), amely összehangolja ezzel a felhőben kezelt kívánt állapotát a helyi fürt állapota. 

A modul élettartamát az ütemező Kubernetes, amely fenntartja a modul rendelkezésre állási, és úgy dönt, az elhelyezési kezeli. IoT Edge kezeli a képernyő felső részén futó edge alkalmazásplatform folyamatosan összeegyezteti az IoT Hub az edge-fürt állapotát a megadott állapothoz. Az edge alkalmazásmodell még mindig a jól ismert modellt az IoT Edge-modulok és útvonalak alapján. Az IoT Edge-ügynök operátor hajt végre *automatikus* a Kubernetes natives való hoz létre, mint például a podok, a központi telepítéseket és a szolgáltatások stb.

Íme egy magas szintű architektúra ábrája:

![kubernetes arch](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Az edge üzemelő példány minden összetevő egy Kubernetes-névtér az eszközre, az ugyanazon fürt erőforrások megosztása között több peremhálózati eszközökön és a központi telepítések lehetővé teszik adott hatókörét.

>[!NOTE]
>Az IoT Edge a Kubernetes [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>A gyors tesztkörnyezetben helyi telepítése

### <a name="prerequisites"></a>Előfeltételek

* Kubernetes 1.10 vagy újabb. Ha nem rendelkezik egy meglévő fürt beállítása, [Minikube](https://kubernetes.io/docs/setup/minikube/) helyi fürtkörnyezetre vonatkozóan. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), a Kubernetes-Csomagkezelőt.

* [a kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) megtekintését és a fürt számára.

### <a name="setup-steps"></a>A telepítő lépéseit

1. Indítsa el **Minikube**

    ``` shell
    minikube start
    ```

1. Inicializálja a **Helm** kiszolgáló-összetevő (*a tiller valóban*) a fürtben

    ``` shell
    helm init
    ```

1. Adja hozzá az IoT Edge-adattárat, és a helm-telepítés frissítése

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Hozzon létre egy IoT hubot](../iot-hub/iot-hub-create-through-portal.md), [IoT Edge-eszköz regisztrálása](how-to-register-device-portal.md), és jegyezze fel a kapcsolati karakterláncot.

1. A fürtbe való iotedged és az IoT Edge-ügynök telepítése

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. A Kubernetes-irányítópult megnyitása a böngészőben

    ```shell
    minikube dashboard
    ```

    A fürt névterek alatt megjelenik egy, az IoT Edge-eszköz szabálya a következő *msiot -\<iothub-neve >-\<edgedevice-name >* . Az IoT Edge-ügynök és iotedged podok és legyen a névtér.

1. Egy szimulált hőmérsékleti érzékelő modul használata a lépések hozzáadása a [modul üzembe helyezése](quickstart-linux.md#deploy-a-module) a rövid útmutató szakasza. IoT Edge-modul kezelése történik, csakúgy, mint bármely más IoT Edge-eszközt az IoT Hub portálról. A modul konfigurációs eszközök segítségével Kubernetes helyi módosítása nem ajánlott, mivel előfordulhat, hogy első felül.

1. Néhány másodpercen belül frissítése a **Podok** a peremhálózati eszköz névtérben, az irányítópult lapon megjelennek az IoT Edge hubot és a szimulált érzékelő podok, mintha az az IoT Edge hub pod adatokat tölt be az IoT Hub.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az edge üzemelő példány által létrehozott összes erőforrás eltávolításához használja a következő parancsot az előző szakasz 5. lépésben használt nevét.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>További lépések

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Egy magas rendelkezésre állású edge-átjáró üzembe helyezése 

A peremhálózati eszköz egy Kubernetes-fürtöt az alsóbb rétegbeli eszközök IoT-átjáróként is használható. Is Csomóponthiba, így biztosítva az edge-telepítések a magas rendelkezésre állású ellenáll konfigurálható. Ez [részletes bemutató](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) IoT Edge használata ebben a forgatókönyvben.