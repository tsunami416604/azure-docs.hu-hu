---
title: Azure IoT Edge-eszköz üzembe helyezése az Azure Machine Learning-modellel |} A Microsoft Docs
description: Ez a dokumentum ismerteti, hogyan telepíthetők az Azure Machine Learning-modellek Azure IoT Edge-eszközökön.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 24d3cf0c4b1a1283e7a6a7f61f0bb23dae7143d5
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43112986"
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Az Azure IoT Edge-eszköz üzembe helyezése az Azure Machine Learning-modell

Az Azure Machine Learning-modellek webszolgáltatásként Docker-alapú tárolóba is lehet. Az Azure IoT Edge lehetővé teszi a távoli eszközök alakzatot a tárolók üzembe helyezése. Ezek a szolgáltatások együtt használni a modellek futtatása a peremhálózaton, gyorsabb válaszidőt és adatátvitel kevesebb. 

További szkripteket és útmutatást találhat a [-hez készült Azure IoT Edge AI eszközkészlet](http://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>A modell üzembe helyezése

Az Azure IoT Edge-modulok tárolórendszerképek alapulnak. A Machine Learning-modellek az IoT Edge-eszköz üzembe helyezéséhez hozzon létre egy Docker-rendszerképet kell.

A modell üzembe helyezése a következő témakör utasításait követve [Azure Machine Learning minta felügyeleti webszolgáltatás üzembe](model-management-service-deploy.md) Docker-rendszerkép létrehozása a modellel.

## <a name="deploy-to-azure-iot-edge"></a>Az Azure IoT Edge üzembe helyezése

Miután a modell képe, telepítheti bármely Azure IoT Edge-eszköz. Machine Learning-modellek az IoT Edge-eszközökön futtathatja. 

### <a name="set-up-an-iot-edge-device"></a>IoT Edge-eszköz beállítása

Segítségével az Azure IoT Edge dokumentációja egy eszközt. 

1. [Eszköz regisztrálása az Azure IoT Hub](../../iot-edge/how-to-register-device-portal.md). A folyamatok kimenete egy kapcsolati karakterláncot, amellyel konfigurálhatja a fizikai eszközt. 
2. Telepítse az IoT Edge-futtatókörnyezet a fizikai eszközt, és konfigurálja azt a kapcsolati karakterláncot. A futtatókörnyezet telepítheti [Windows](../../iot-edge/how-to-install-iot-edge-windows-with-windows.md) vagy [Linux](../../iot-edge/how-to-install-iot-edge-linux.md) eszközök.  


### <a name="find-the-machine-learning-container-image-location"></a>Keresse meg a Machine Learning-tároló lemezképet helye
A Machine Learning-tároló rendszerképének helye van szüksége. A tároló-lemezképet helye megkeresése:

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com/).
2. Az a **Azure Container Registry**, válassza ki a megvizsgálni kívánt beállításjegyzék.
3. Kattintson a beállításjegyzékben **Tárházak** minden tárház és saját rendszerképek listájának megtekintéséhez.

Bár az Azure Portalon a tárolóregisztrációs adatbázis dolgozik, beolvasni a container registry hitelesítő adatokat. Ezeket a hitelesítő adatokat kell adni az IoT Edge-eszközön, hogy a kép képes bekötni a privát tárolójegyzékből. 

1. A container registry esetében kattintson **hozzáférési kulcsok**. 
2. **Engedélyezése** a rendszergazdai felhasználó, ha még nem tette. 
3. Mentse az értékeket a **bejelentkezési kiszolgáló**, **felhasználónév**, és **jelszó**. 

### <a name="deploy-the-container-image-to-your-device"></a>Az eszköz a tároló rendszerképének üzembe helyezéséhez

A tárolórendszerképet, és a container registry hitelesítő adatokat, és már készen áll a gépi tanulási modellt az IoT Edge-eszköz üzembe helyezése. 

Kövesse a [üzembe helyezése IoT Edge-modulok az Azure Portalról](../../iot-edge/how-to-deploy-modules-portal.md) a modell az IoT Edge-eszköz elindításához. 











