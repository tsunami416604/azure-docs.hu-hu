---
title: Oktatóanyag az adatok szűréséhez és elemzéséhez Azure Stack Edge-beli számításokkal | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a számítási szerepkört Azure Stack Edge-ben, és hogyan alakíthatja át az adatokat az Azure-ba való küldés előtt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 8ba460168edc03b1cb491d69010acd03f4a84ae3
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181583"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-gpu-device"></a>Oktatóanyag: számítás konfigurálása Azure Stack Edge GPU-eszközön

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ez az oktatóanyag ismerteti, hogyan konfigurálhat számítási szerepkört, és hogyan hozhat létre Kubernetes-fürtöt az Azure Stack Edge-eszközön. 

Ez az eljárás körülbelül 20 – 30 percet vesz igénybe.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Kubernetes API-végpont beolvasása

 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt beállít egy számítási szerepkört az Azure Stack Edge-eszközön, ügyeljen rá, hogy:

- Aktiválta a Azure Stack Edge-eszközt a [Azure stack Edge aktiválása](azure-stack-edge-gpu-deploy-activate.md)című témakörben leírtak szerint.
- Győződjön meg arról, hogy követte a [számítási hálózat engedélyezése](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) és a:
    - Hálózati adapter engedélyezése a számítási feladatokhoz.
    - Hozzárendelt Kubernetes Node IP-címek és Kubernetes külső szolgáltatás IP-címei.

## <a name="configure-compute"></a>Számítás konfigurálása

A számítás Azure Stack Edge-ben való konfigurálásához IoT Hub erőforrást fog létrehozni a Azure Portal használatával.

1. Az Azure Stack Edge-erőforrás Azure Portal válassza az **Áttekintés**lehetőséget. A jobb oldali ablaktáblán, a **számítási** csempén válassza az első **lépések**lehetőséget.

    ![Ismerkedés a számítási feladatokkal](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. Az **Edge-számítás konfigurálása** csempén válassza a **számítás konfigurálása**lehetőséget.

    ![Számítás konfigurálása](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. Az **Edge-számítás konfigurálása panelen adja** meg a következőket:

   
    |Mező  |Érték  |
    |---------|---------|
    |IoT Hub     | Válasszon az **új** vagy a **meglévő**listából. <br> Alapértelmezés szerint a rendszer egy standard szintű (S1) szintet használ IoT-erőforrás létrehozásához. Az ingyenes szintű IoT erőforrás használatához hozzon létre egyet, majd válassza ki a meglévő erőforrást. <br> A IoT Hub erőforrás minden esetben ugyanazt az előfizetést és erőforráscsoportot használja, amelyet az Azure Stack Edge-erőforrás használ.     |
    |Név     |Adja meg a IoT Hub erőforrás nevét.         |

    ![Ismerkedés a számítási feladatokkal](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Válassza a **Létrehozás** lehetőséget. A IoT Hub erőforrás létrehozása több percet vesz igénybe. A IoT Hub erőforrás létrehozása után a számítási csempék **konfigurálása** a számítási konfiguráció megjelenítéséhez. 

    ![Ismerkedés a számítási feladatokkal](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Annak ellenőrzéséhez, hogy a peremhálózati számítási szerepkör konfigurálva van-e, válassza a számítás **megjelenítése** lehetőséget a **számítás konfigurálása** csempén.
    
    ![Ismerkedés a számítási feladatokkal](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

    > [!NOTE]
    > Ha a **számítási párbeszédpanel konfigurálása párbeszédpanelen be** van zárva a IoT Hub az Azure stack Edge-eszközhöz való társítása előtt, akkor a IoT hub jön létre, de nem jelenik meg a számítási konfigurációban. 
    
Amikor a peremhálózati eszközön beállította a peremhálózat számítási szerepkört, két eszközt hoz létre: egy IoT eszközt és egy IoT Edge eszközt. Mindkét eszköz megtekinthető a IoT Hub erőforrásban. Ezen a IoT Edge eszközön a IoT Edge futtatókörnyezet is fut. Ezen a ponton csak a Linux platform érhető el a IoT Edge eszközhöz.

A számítási műveletek végrehajtása a háttérben, a virtuális gépek és a Kubernetes-fürt létrehozása óta 20-30 percet is igénybe vehet. 

Miután sikeresen konfigurálta a Azure Portal-beli számítást, egy Kubernetes-fürtöt és egy, az IoT-névtérhez társított alapértelmezett felhasználót (az Azure Stack Edge által vezérelt rendszernévtér) létezik. 

## <a name="get-kubernetes-endpoints"></a>Kubernetes-végpontok beolvasása

Ha az ügyfelet a Kubernetes-fürt eléréséhez szeretné konfigurálni, szüksége lesz az Kubernetes-végpontra. Az alábbi lépéseket követve beolvashatja a Kubernetes API-végpontot az Azure Stack Edge-eszköz helyi felhasználói felületéről.

1. Az eszköz helyi webes FELÜLETén nyissa meg az **eszközök** lapot.
2. Másolja a **KUBERNETES API Service** -végpontot az **eszköz végpontjai**alatt. Ez a végpont egy karakterlánc a következő formátumban: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` . 

    ![Eszköz lap a helyi felhasználói felületen](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Mentse a végponti karakterláncot. Ezt később fogja használni, amikor a kubectl-on keresztül konfigurálja az ügyfelet a Kubernetes-fürt eléréséhez.

4. A helyi webes KEZELŐFELÜLETen a következőket teheti:

    - Nyissa meg a Kubernetes API-t, válassza a **Speciális beállítások** lehetőséget, majd töltse le a Kubernetes speciális konfigurációs fájlját. 

        ![Eszköz lap a helyi felhasználói felületen 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Ha a Microsofttól kapott kulcsot (válassza ki a felhasználókat), akkor ezt a konfigurációs fájlt használhatja.

        ![Eszköz lap a helyi felhasználói felületen 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - Megtekintheti a **Kubernetes irányítópult** -végpontot is, és letöltheti a `aseuser` konfigurációs fájlt. 
    
        ![Eszköz lap a helyi felhasználói felületen 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        A `aseuser` konfigurációs fájl lehetővé teszi a `iotedge` Kubernetes-fürt névterével kapcsolatos problémák hibakeresését. További információ: [hibakeresés Kubernetes kapcsolatos problémák](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge). 


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Kubernetes-végpontok beolvasása


Az Azure Stack Edge-eszköz felügyeletének megismeréséhez lásd:

> [!div class="nextstepaction"]
> [Helyi webes felhasználói felület használata Azure Stack Edge felügyeletéhez](azure-stack-edge-manage-access-power-connectivity-mode.md)
