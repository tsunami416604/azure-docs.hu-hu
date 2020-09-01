---
title: GPU-modul üzembe helyezése az Microsoft Azure Stack Edge-eszközön az Azure Marketplace-en | Microsoft Docs
description: Ismerteti, hogyan engedélyezhető a számítási funkció, és hogyan végezhető el a Azure Stack Edge-eszköz számítási felkészültsége a helyi felhasználói felületen keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 1532283be8521ad5c6cd5a9e2252e5ba839a4aa3
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084334"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-device"></a>GPU-t támogató IoT-modul üzembe helyezése az Azure Marketplace-en Azure Stack Edge-eszközön

Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy grafikus processzorral (GPU) IoT Edge modult az Azure Marketplace-en az Azure Stack Edge-eszközön. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:
  - Készítse elő Azure Stack Edge-t egy GPU-modul futtatásához.
  - A GPU-t támogató IoT modul letöltése és üzembe helyezése az Azure Marketplace-en.
  - A modul kimenetének figyelése.

## <a name="about-sample-module"></a>A minta modul ismertetése

A cikkben található GPU-minta modul a PyTorch és a TensorFlow teljesítménytesztet tartalmazza a CPU-hoz a GPU-val szemben.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Hozzáférése van egy GPU-t támogató 1 csomópontos Azure Stack peremhálózati eszközhöz. Ez az eszköz egy Azure-erőforrással van aktiválva. 
- Konfigurálta az eszközön a számítást. 
    - Kövesse az [oktatóanyag: számítás konfigurálása az Azure stack Edge-eszközön](azure-stack-edge-gpu-deploy-configure-compute.md)című témakör lépéseit.
    - Ügyeljen rá, hogy 
- A következő fejlesztői erőforrások egy Windows-ügyfélen:
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Azure IoT Edge bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)-hoz.   


## <a name="get-module-from-azure-marketplace"></a>Modul beszerzése az Azure Marketplace-ről

1. Böngésszen [az összes alkalmazásban az Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps).

    ![Alkalmazások tallózása az Azure Marketplace-en](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. Keressen rá a GPU-k **használatába**.

    ![Keresési GPU-minta modul](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. Válassza a **Letöltés most**lehetőséget.

    ![Minta modul beolvasása](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. A **tovább** gombra kattintva nyugtázza a szolgáltató használati feltételeit és adatvédelmi szabályzatát. 

    ![Minta modul beolvasása](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Válassza ki azt az előfizetést, amelyet az Azure Stack Edge-eszköz üzembe helyezéséhez használt.

    ![Előfizetés kiválasztása](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Adja meg annak a IoT Hub szolgáltatásnak a nevét, amelyet az Azure Stack Edge-eszköz konfigurálásakor hozott létre. A IoT Hub szolgáltatás nevének megkereséséhez nyissa meg az eszközhöz társított Azure Stack Edge-erőforrást Azure Portal. 

    1. A bal oldali ablaktábla menüjében válassza az **Edge számítás > első lépések**lehetőséget. 

    1. Az **Edge-számítás konfigurálása** csempén válassza a **konfiguráció megtekintése**lehetőséget. 

        ![Számítási konfiguráció megtekintése](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. Az **Edge számítási konfiguráció** paneljén:

        1. Jegyezze fel a IoT Hub szolgáltatást, amely akkor jött létre, amikor az Azure Stack Edge-eszközön beállította a számítást.
        2. Jegyezze fel annak a IoT Edge eszköznek a nevét, amelyet a számítás beállításakor hozott létre. Ezt a nevet fogja használni a következő lépésben.

        ![Peremhálózati számítási konfiguráció](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. Válassza **a telepítés eszközre**lehetőséget.

11. Adja meg a IoT Edge eszköz nevét, vagy válassza az **eszköz keresése**elemet a   hub-ban regisztrált eszközök között.

    ![Eszköz keresése](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. Válassza a **Létrehozás**lehetőséget   a központi telepítési jegyzék konfigurálásának szabványos folyamatának folytatásához, beleértve a további modulok hozzáadását, ha szükséges. Az új modul adatai, például a rendszerkép URI-ja, a létrehozási beállítások és a kívánt tulajdonságok előre definiálva vannak, de módosíthatók.

    ![Kattintás a Létrehozás gombra](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. Ellenőrizze, hogy a modul telepítve van-e a IoT Hub a Azure Portalban. Válassza ki az eszközt, válassza a **modulok beállítása**elemet,   és a modult a **IoT Edge modulok**szakaszban kell megadnia   .

    ![Kattintás a Létrehozás gombra](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>A modul figyelése  

1. A VS Code parancskatalógusában futtassa az **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: IoT Hub kiválasztása) parancsot.

2. Válassza ki a konfigurálni kívánt IoT Edge-eszközt tartalmazó előfizetést és IoT Hubot. Ebben az esetben válassza ki az Azure Stack Edge-eszköz telepítéséhez használt előfizetést, és válassza ki az Azure Stack Edge-eszközhöz létrehozott IoT Edge eszközt. Ez akkor fordul elő, ha a számítást a korábbi lépések Azure Portalján keresztül konfigurálja.

3. A VS Code Explorerben bontsa ki az Azure IoT Hub szakaszt. Az **eszközök**területen az Azure stack Edge-eszköznek megfelelő IoT Edge eszköznek kell megjelennie. 

    1. Válassza ki az eszközt, kattintson a jobb gombbal, majd válassza a **figyelés beépített esemény végpontja**lehetőséget.
  
        ![Figyelés indítása](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Nyissa meg az **eszközök > modulokat** , és ellenőrizze, hogy fut-e a **GPU-modul** .

    3. A VS Code terminálnak a Azure Stack Edge-eszköz figyelési kimenetének IoT Hubi eseményeit is meg kell jelenítenie.

        ![Figyelési kimenet](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Láthatja, hogy a GPU által az azonos műveletek végrehajtásához szükséges idő (5000-es ismétlés) nem sokkal kisebb, mint a CPU esetében.

## <a name="next-steps"></a>További lépések

- További információ arról, hogyan [konfigurálhatja a GPU-t modul használatára](azure-stack-edge-j-series-configure-gpu-modules.md).
