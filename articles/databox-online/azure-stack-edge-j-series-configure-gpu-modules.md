---
title: GPU-modul futtatása Microsoft Azure Stack Edge Pro GPU-eszközön | Microsoft Docs
description: Ismerteti, hogyan konfigurálhat és futtathat egy modult a GPU-n egy Azure Stack Edge Pro-eszközön a Azure Portal használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 8b9f1180639f638e72fdea2f87958628a2e9e86b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891469"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Modul konfigurálása és futtatása GPU-ban Azure Stack Edge Pro-eszközön

Az Azure Stack Edge Pro-eszköz egy vagy több grafikus feldolgozó egységet (GPU) tartalmaz. A GPU-k olyan népszerű AI-számítások, amelyek párhuzamos feldolgozási képességeket kínálnak, és gyorsabbak a képmegjelenítésnél, mint a központi feldolgozó egységek (CPU-k). Az Azure Stack Edge Pro-eszközön található GPU-val kapcsolatos további információkért látogasson el az [Edge Pro-eszköz technikai specifikációjának Azure stack](azure-stack-edge-gpu-technical-specifications-compliance.md).

Ez a cikk bemutatja, hogyan konfigurálhat és futtathat egy modult a GPU-ban az Azure Stack Edge Pro-eszközön. Ebben a cikkben az NVIDIA T4 GPU-hoz írt, nyilvánosan elérhető tároló modul **számjegyeit** fogjuk használni. Ezzel az eljárással konfigurálhatja az NVIDIA által a GPU-k által közzétett egyéb modulokat.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Hozzáférése van egy GPU-t támogató, 1 csomópontos Azure Stack Edge Pro-eszközhöz. Ez az eszköz egy Azure-erőforrással van aktiválva.  

## <a name="configure-module-to-use-gpu"></a>Modul konfigurálása a GPU használatára

Ha egy modult úgy szeretne konfigurálni, hogy a GPU-t használja a Azure Stack Edge Pro-eszközön egy modul futtatásához, kövesse az alábbi lépéseket.

1. A Azure Portal nyissa meg az eszközhöz hozzárendelt erőforrást. 

2. Ugrás az **Edge-számításra > első lépések**. Az **Edge-számítás konfigurálása** csempén válassza a Konfigurálás lehetőséget.

    ![Modul konfigurálása a GPU 1 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. Az **Edge-számítás konfigurálása** panelen:

    1. **IoT hub**esetében válassza az **új létrehozása**lehetőséget.
    2. Adja meg az eszközhöz létrehozni kívánt IoT Hub-erőforrás nevét. Ha ingyenes szintet szeretne használni, válasszon ki egy meglévő erőforrást. 
    3. Jegyezze fel a IoT Edge eszközt és a IoT Hub erőforrással létrehozott IoT-átjáró eszközét. Ezeket az információkat a későbbi lépésekben fogja használni.

    ![Modul konfigurálása a GPU 2 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. A IoT Hub erőforrás létrehozása több percet is igénybe vehet. Az erőforrás létrehozása után az **Edge-számítás konfigurálása** csempén a **konfiguráció megtekintése** elemre kattintva megtekintheti a IoT hub erőforrás részleteit.

    ![Modul konfigurálása a GPU 4 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-4.png)

5. Nyissa meg az **automatikus eszközkezelés > IoT Edge**.

    ![Modul konfigurálása a GPU 6 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

    A jobb oldali ablaktáblában megjelenik az Azure Stack Edge Pro-eszközhöz társított IoT Edge eszköz. Ez megfelel az IoT Hub erőforrás létrehozásakor az előző lépésben létrehozott IoT Edge eszköznek. 
    
6. Válassza ki ezt az IoT Edge eszközt.

   ![Modul konfigurálása a GPU 7 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

7.  Válassza a **Set modules** (Modulok beállítása) lehetőséget.

    ![Modul konfigurálása a GPU 8 használatához](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

8. Válassza a **+ Hozzáadás** , majd a **+ IoT Edge modul**lehetőséget. 

    ![Modul konfigurálása a GPU 9-es használatához](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

9. A **IoT Edge modul hozzáadása** lapon:

    1. Adja meg a **rendszerkép URI-ját**. Itt fogja használni a nyilvánosan elérhető NVIDIA-modulok **számjegyeit** . 
    
    2. Állítsa az **Újraindítási szabályzatot** **mindig**értékre.
    
    3. Állítsa a **kívánt állapotot** a **futtatásra**.
    
    ![Modul konfigurálása a GPU 10 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

10. A **környezeti változók** lapon adja meg a változó nevét és a hozzá tartozó értéket. 

    1. Ha azt szeretné, hogy az aktuális modul egy GPU-t használjon az eszközön, használja a NVIDIA_VISIBLE_DEVICES. 

    2. Állítsa az értéket 0 vagy 1 értékre. Ez biztosítja, hogy az eszköz az adott modulhoz legalább egy GPU-t használ. Ha az értéket 0, 1 értékre állítja, az azt jelenti, hogy az eszközön található GPU-k is ezt a modult használják.

        ![Modul konfigurálása a GPU 11 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

        További információ az NVIDIA GPU-val használható környezeti változókról: [NVIDIA Container Runtime](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > A GPU-t csak egyetlen modulhoz lehet hozzárendelni. Egy modul azonban a GPU-k egyikét is használhatja. 

11. Adja meg a modul nevét. Ezen a ponton dönthet úgy, hogy a tároló létrehozása lehetőséget adja meg, és módosítja a modul különálló beállításait, vagy ha elkészült, válassza a **Hozzáadás**lehetőséget. 

    ![Modul konfigurálása a GPU 12 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

12. Győződjön meg arról, hogy a modul fut, és válassza a **felülvizsgálat + létrehozás**elemet.    

    ![Modul konfigurálása a GPU 13 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

13. A **felülvizsgálat + létrehozás** lapon megjelenik a kiválasztott központi telepítési beállítások. Tekintse át a beállításokat, és válassza a **Létrehozás**lehetőséget.
    
    ![Modul konfigurálása a GPU 14 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

14. Jegyezze fel a modul **futtatókörnyezeti állapotát** . 
    
    ![Modul konfigurálása a GPU 15 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    A modul üzembe helyezése néhány percet vesz igénybe. Válassza a **frissítés** lehetőséget, és a **futásidejű állapot** frissítését **futtatni**kell.

    ![Modul konfigurálása a GPU 16 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Következő lépések

- További információ [az NVIDIA GPU-val használható környezeti változókról](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
