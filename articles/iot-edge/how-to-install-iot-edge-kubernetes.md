---
title: A IoT Edge telepítése a Kubernetes-on | Microsoft Docs
description: Ismerje meg, hogyan telepítheti a IoT Edget a Kubernetes egy helyi fejlesztési fürt-környezet használatával
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a453779ffe4ae20acf55510d0ac9f9483763af21
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964838"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>A IoT Edge telepítése a Kubernetes (előzetes verzió)

IoT Edge integrálható a Kubernetes a rugalmas, magasan elérhető infrastruktúra-rétegként használva. IoT Edge *egyéni erőforrás-definíciót* (CRD) regisztrál a Kubernetes API-kiszolgálóval. Emellett egy *operátort* (IoT Edge ügynököt) is biztosít, amely összehangolja a felhőben felügyelt kívánt állapotot a helyi fürt állapotával. 

A modul élettartamát a Kubernetes Scheduler kezeli, amely fenntartja a modul rendelkezésre állását, és kiválasztja az elhelyezést. IoT Edge felügyeli a fent futó Edge Application platformot, és folyamatosan összehangolja a IoT Hubban megadott kívánt állapotot a peremhálózati fürt állapotával. Az Edge-alkalmazás modellje továbbra is ismert modell IoT Edge modulok és útvonalak alapján. A IoT Edge ügynök operátora *automatikus* fordítást végez a Kubernetes-natív eszközökhöz, például hüvelyek, üzembe helyezések, szolgáltatások stb. alapján.

A magas szintű architektúra diagramja:

![kubernetes Arch](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Az Edge-telepítés minden összetevője az eszközre jellemző Kubernetes-névtérre van korlátozva, így több peremhálózati eszköz és a hozzájuk tartozó üzemelő példányok között is megosztható.

>[!NOTE]
>A Kubernetes IoT Edge [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el.

## <a name="install-locally-for-a-quick-test-environment"></a>Helyi telepítés gyors tesztelési környezethez

### <a name="prerequisites"></a>Előfeltételek

* Kubernetes 1,10 vagy újabb. Ha nem rendelkezik meglévő fürttel, a [Minikube](https://kubernetes.io/docs/setup/minikube/) -t használhatja helyi fürt környezetéhez. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), a Kubernetes csomagkezelő.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) a fürt megtekintésére és a velük való interakcióra.

### <a name="setup-steps"></a>Telepítési lépések

1. **Minikube** elindítása

    ``` shell
    minikube start
    ```

1. A **Helm** Server összetevő (*kormányrúd*) inicializálása a fürtben

    ``` shell
    helm init
    ```

1. IoT Edge adattár hozzáadása és a Helm telepítésének frissítése

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Hozzon létre egy IoT hub](../iot-hub/iot-hub-create-through-portal.md), [regisztráljon egy IoT Edge eszközt](how-to-register-device.md), és jegyezze fel a kapcsolati karakterláncát.

1. A iotedged és a IoT Edge ügynök telepítése a fürtbe

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. Nyissa meg a Kubernetes-irányítópultot a böngészőben

    ```shell
    minikube dashboard
    ```

    A fürt névterei alatt láthat egyet a IoT Edge eszközhöz a következő egyezményt követve: *msiot-\<iothub-name >-\<edgedevice-name >* . A IoT Edge-ügynöknek és a iotedged-hüvelynek ebben a névtérben kell futnia.

1. Vegyen fel egy szimulált hőmérséklet-érzékelő modult a gyors útmutató [modul telepítése](quickstart-linux.md#deploy-a-module) szakaszának lépéseit követve. IoT Edge modul felügyeletét a IoT Hub portálon végezheti el, ugyanúgy, mint bármely más IoT Edge eszköz. A modulok Kubernetes-eszközökön keresztüli helyi módosítása nem ajánlott, mert előfordulhat, hogy a rendszer felülírja a beállításokat.

1. Néhány másodpercen belül az irányítópult Edge-eszköz névterében lévő **hüvelyek** oldal frissítése a IoT Edge hub és a szimulált érzékelő hüvelyét fogja kilistázni az IoT Edge hub Pod-val, amely az IoT Hubbe helyezi az adatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Edge-telepítés által létrehozott összes erőforrás eltávolításához használja az alábbi parancsot az előző szakasz 5. lépésében használt névvel.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Következő lépések

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Üzembe helyezés magasan elérhető peremhálózati átjáróként 

A Kubernetes-fürt peremhálózati eszköze az alsóbb rétegbeli eszközök IoT-átjáróként is használható. Konfigurálható úgy, hogy rugalmas legyen a csomópontok meghibásodása esetén, így magas rendelkezésre állást biztosítson az Edge-környezetek számára. Ebben a forgatókönyvben a IoT Edge használatára vonatkozó [részletes útmutatóban](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) talál.