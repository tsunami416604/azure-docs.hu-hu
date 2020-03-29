---
title: IoT Hub-eszközkiépítési szolgáltatás kezelése az Azure CLI & IoT-bővítmény használatával
description: Ismerje meg, hogyan használhatja az Azure CLI-t és az IoT-bővítményt az IoT Hub-eszközkiépítési szolgáltatás (DPS) kezeléséhez
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 03ec0b41ad910ff0d1dcdc17148e01ec94ea9fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674518"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Az Azure CLI és az IoT-bővítmény használata az IoT Hub-eszközkiépítési szolgáltatás kezeléséhez

[Az Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forráskódú, platformfüggetlen parancssori eszköz az Azure-erőforrások, például az IoT Edge kezelésére. Az Azure CLI Windows, Linux és MacOS rendszeren érhető el. Az Azure CLI lehetővé teszi az Azure IoT Hub-erőforrások, az eszközkiépítési szolgáltatáspéldányok és a csatolt hubok kezelését a dobozból.

Az IoT-bővítmény olyan funkciókkal gazdagítja az Azure CLI-t, mint az eszközkezelés és a teljes IoT Edge-képesség.

Ebben az oktatóanyagban először hajtsa végre az Azure CLI és az IoT-bővítmény beállításának lépéseit. Ezután megtudhatja, hogyan futtathatja a CLI-parancsokat az alapvető eszközkiépítési szolgáltatás műveletek végrehajtásához. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="installation"></a>Telepítés 

### <a name="install-python"></a>Telepítse a Pythont

[Python 2.7x vagy Python 3.x](https://www.python.org/downloads/) szükséges.

### <a name="install-the-azure-cli"></a>Telepítse az Azure CLI-t

Kövesse a [telepítési utasítást](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) az Azure CLI beállításához a környezetben. Az Azure CLI-verziónak legalább 2.0.70-nek kell lennie. A verziószámot az `az –version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. Windows rendszeren a legegyszerűbb megoldás a telepítésre az [MSI](https://aka.ms/InstallAzureCliWindows) letöltése és telepítése.

### <a name="install-iot-extension"></a>IoT-bővítmény telepítése

[Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension) több módszert is ismertet a bővítmény telepítésére. A legegyszerűbb módszer az `az extension add --name azure-iot` futtatása. A telepítés után az `az extension list` paranccsal ellenőrizheti az aktuálisan telepített bővítményeket, az `az extension show --name azure-iot` paranccsal pedig megtekintheti az IoT-bővítmény adatait. A bővítményt az `az extension remove --name azure-iot` paranccsal távolíthatja el.


## <a name="basic-device-provisioning-service-operations"></a>Alapvető eszközkiépítési szolgáltatás műveletek

A példa bemutatja, hogyan jelentkezhet be az Azure-fiókjába, hozhat létre egy Azure-erőforráscsoportot (egy Azure-megoldás kapcsolódó erőforrásait tároló tárolót), hozzon létre egy IoT Hubot, hozzon létre egy eszközkiépítési szolgáltatást, sorolja fel a meglévő eszközkiépítési szolgáltatásokat és hozzon létre egy összekapcsolt IoT hubcli parancsokkal. 

Mielőtt nekikezdene, végezze el az előzőekben ismertetett telepítési lépéseket. Ha még nem rendelkezik Azure-előfizetéssel, akár most is létrehozhat [egy ingyenes fiókot](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Bejelentkezés az Azure-fiókba
  
    az login

![bejelentkezés](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Hozzon létre egy erőforráscsoportot IoTHubBlogDemo az eastus

    az group create -l eastus -n IoTHubBlogDemo

![Erőforráscsoport létrehozása](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. Hozzon létre két eszközkiépítési szolgáltatást

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Eszközkiépítési szolgáltatás létrehozása](./media/how-to-manage-dps-with-cli/create-dps.jpg)

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Az erőforráscsoport összes meglévő eszközkiépítési szolgáltatásának listázása

    az iot dps list --resource-group IoTHubBlogDemo

![Eszközkiépítési szolgáltatások listázása](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Hozzon létre egy IoT Hub-blogOtDemoHub az újonnan létrehozott erőforráscsoport alatt

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![IoT Hub létrehozása](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Egy meglévő IoT Hub összekapcsolása egy eszközkiépítési szolgáltatással

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Hub csatolása](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az eszköz regisztrálása
> * Az eszköz elindítása
> * Az eszköz sikeres regisztrálásának ellenőrzése

A következő oktatóanyag azt mutatja be, hogyan regisztrálhat több eszközt az elosztott terhelésű hubok között. 

> [!div class="nextstepaction"]
> [Eszközök regisztrálása elosztott terhelésű IoT Hubokon](./tutorial-provision-multiple-hubs.md)
