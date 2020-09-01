---
title: Kubernetes állapot nélküli alkalmazás üzembe helyezése a Azure Stack Edge GPU-n keresztül IoT Edge modul használatával | Microsoft Docs
description: Ismerteti, hogyan helyezhet üzembe egy Kubernetes állapot nélküli alkalmazást a Azure Stack Edge GPU-eszközön egy olyan IoT Edge modul használatával, amely külső IP-n keresztül érhető el.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 15316cbdd44053bfaf7403815ba42d92e2264b7b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254168"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-gpu-device"></a>IoT Edge modul használata Kubernetes állapot nélküli alkalmazások futtatásához az Azure Stack Edge GPU-eszközön

Ez a cikk bemutatja, hogyan helyezhet üzembe egy állapot nélküli alkalmazást az Azure Stack Edge-eszközön egy IoT Edge modul használatával.

Az állapot nélküli alkalmazás üzembe helyezéséhez hajtsa végre a következő lépéseket:

- IoT Edge modul üzembe helyezése előtt győződjön meg arról, hogy az előfeltételek befejeződik.
- IoT Edge modul hozzáadásával elérheti a számítási hálózatot a Azure Stack Edge-ben.
- Ellenőrizze, hogy a modul képes-e hozzáférni az engedélyezett hálózati adapterhez.

Ebben a útmutatóban egy webkiszolgáló-modul használatával mutatjuk be a forgatókönyvet.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következőkre lesz szüksége:

- Egy Azure Stack peremhálózati eszköz. Ügyeljen a következőkre:

    - A számítási hálózati beállítások konfigurálva vannak az eszközön.
    - Az eszköz a következő oktatóanyagban ismertetett lépések szerint aktiválódik [: eszköz aktiválása](azure-stack-edge-gpu-deploy-activate.md).
- Elvégezte a **számítási lépés konfigurálását** az [oktatóanyagban: a számítás konfigurálása a Azure stack peremhálózati eszközön](azure-stack-edge-gpu-deploy-configure-compute.md) az eszközön. Az eszköznek rendelkeznie kell egy társított IoT Hub erőforrással, egy IoT eszközzel és egy IoT Edge eszközzel.


## <a name="add-webserver-app-module"></a>Webkiszolgáló-alkalmazás hozzáadása modul

A következő lépésekkel adhat hozzá egy webkiszolgáló-alkalmazás-modult az Azure Stack Edge-eszközhöz.

1. Az eszközhöz hozzárendelt IoT Hub erőforrásban nyissa meg az **automatikus eszközkezelés > IoT Edge**.
1. Válassza ki, majd kattintson az Azure Stack Edge-eszközhöz társított IoT Edge eszközre. 

    ![IoT Edge eszköz kiválasztása](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. Válassza a **Set modules** (Modulok beállítása) lehetőséget. Az **eszközön beállított modulok**lapon válassza a **+ Hozzáadás** lehetőséget, majd válassza ki **IoT Edge modult**.

    ![IoT Edge modul kiválasztása](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. A **IoT Edge hozzáadása modulban**:

    1. Adja meg a telepíteni kívánt webkiszolgáló-modul **nevét** .
    2. A **modul beállításai** lapon adja meg a **rendszerkép URI azonosítóját** . A megadott névvel és címkékkel egyező modul beolvasása. Ebben az esetben `nginx:stable` egy stabil Nginx-rendszerképet fog lekérni a nyilvános [Docker-tárházból](https://hub.docker.com/_/nginx/).

        ![IoT Edge modul hozzáadása](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. A **tároló létrehozása beállítások** lapon illessze be a következő mintakód-kódot:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Ez a konfiguráció lehetővé teszi a modul elérését a számítási hálózati IP-cím használatával *http* protokollon keresztül a 8080-as TCP-porton (az alapértelmezett webkiszolgáló-port a 80-es). Válassza a **Hozzáadás** lehetőséget.

        ![A port adatainak meghatározása IoT Edge egyéni modul panelen](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. Tekintse át a modul részleteit, és válassza a **Létrehozás**lehetőséget.

## <a name="verify-module-access"></a>Modul-hozzáférés ellenőrzése

1. Ellenőrizze, hogy a modul telepítése sikeres volt-e, és fut-e. A **modulok** lapon **futnia**kell a modul futtatókörnyezeti állapotának.  

    ![Ellenőrizze, hogy fut-e a modul állapota](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. A webkiszolgáló alkalmazás külső végpontjának beszerzéséhez [nyissa meg a Kubernetes-irányítópultot](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 
1. Az irányítópult bal oldali ablaktábláján szűrje a **iotedge** névteret. Nyissa meg a **felderítési és terheléselosztási > szolgáltatásokat**. A felsorolt szolgáltatások listájában keresse meg a webkiszolgáló alkalmazás moduljának külső végpontját. 

    ![Kapcsolódás a webkiszolgáló alkalmazáshoz külső végponton](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. Válassza ki a külső végpontot egy új böngészőablak megnyitásához.

    Látnia kell, hogy a webkiszolgáló alkalmazás fut.

    ![A modulhoz való kapcsolódás ellenőrzése a megadott porton keresztül](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan teheti elérhetővé az állapot-nyilvántartó alkalmazást IoT Edge modulon keresztül<!--insert link-->.
