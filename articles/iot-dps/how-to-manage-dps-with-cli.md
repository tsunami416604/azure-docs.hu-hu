---
title: IoT Hub Device Provisioning Service kezelése az Azure CLI & IoT bővítménnyel
description: Ismerje meg, hogyan kezelheti a IoT Hub Device Provisioning Service (DPS) az Azure CLI és a IoT bővítmény használatával
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 03ec0b41ad910ff0d1dcdc17148e01ec94ea9fb0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78674518"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Az Azure CLI és a IoT bővítmény használata az IoT Hub Device Provisioning Service kezeléséhez

Az [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forráskódú, többplatformos parancssori eszköz az Azure-erőforrások, például a IoT Edge kezelésére. Az Azure CLI Windows, Linux és MacOS rendszeren érhető el. Az Azure CLI lehetővé teszi az Azure IoT Hub-erőforrások, a Device kiépítési szolgáltatás példányainak és a kapcsolt hubok felügyeletét a box-ban.

Az IoT bővítmény az Azure CLI-t az eszközök kezelésével és a teljes IoT Edge funkcióval gazdagítja.

Ebben az oktatóanyagban először végre kell hajtania az Azure CLI és a IoT-bővítmény beállításának lépéseit. Ezután megtudhatja, hogyan futtathat CLI-parancsokat az alapszintű eszközök kiépítési szolgáltatási műveleteinek végrehajtásához. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="installation"></a>Telepítés 

### <a name="install-python"></a>Telepítse a Pythont

[Python 2.7x vagy Python 3.x](https://www.python.org/downloads/) szükséges.

### <a name="install-the-azure-cli"></a>Telepítse az Azure CLI-t

Kövesse a [telepítési utasításokat](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) az Azure CLI beállításához a környezetben. Legalább az Azure CLI-verziójának 2.0.70 vagy újabbnak kell lennie. A verziószámot az `az –version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. Windows rendszeren a legegyszerűbb megoldás a telepítésre az [MSI](https://aka.ms/InstallAzureCliWindows) letöltése és telepítése.

### <a name="install-iot-extension"></a>A IoT bővítmény telepítése

[Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension) több módszert is ismertet a bővítmény telepítésére. A legegyszerűbb módszer az `az extension add --name azure-iot` futtatása. A telepítés után az `az extension list` paranccsal ellenőrizheti az aktuálisan telepített bővítményeket, az `az extension show --name azure-iot` paranccsal pedig megtekintheti az IoT-bővítmény adatait. A bővítményt az `az extension remove --name azure-iot` paranccsal távolíthatja el.


## <a name="basic-device-provisioning-service-operations"></a>Alapszintű eszközök kiépítési szolgáltatásának műveletei

A példa bemutatja, hogyan jelentkezhet be az Azure-fiókjába, hogyan hozhat létre egy Azure-erőforráscsoportot (egy Azure-megoldáshoz kapcsolódó erőforrásokkal rendelkező tárolót), hogyan hozhat létre IoT Hub, létrehozhat egy eszköz-kiépítési szolgáltatást, listázhatja a meglévő eszközök kiépítési szolgáltatásait, és létrehozhat egy csatolt IoT hubot CLI-parancsokkal. 

Mielőtt nekikezdene, végezze el az előzőekben ismertetett telepítési lépéseket. Ha még nem rendelkezik Azure-előfizetéssel, akár most is létrehozhat [egy ingyenes fiókot](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Jelentkezzen be az Azure-fiókba
  
    az login

![bejelentkezés](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. erőforráscsoport IoTHubBlogDemo létrehozása a eastus-ben

    az group create -l eastus -n IoTHubBlogDemo

![Erőforráscsoport létrehozása](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. hozzon létre két eszközt a kiépítési szolgáltatásokhoz

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Eszköz kiépítési szolgáltatásának létrehozása](./media/how-to-manage-dps-with-cli/create-dps.jpg)

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. az erőforráscsoport alatt lévő összes meglévő eszköz-kiépítési szolgáltatás listázása

    az iot dps list --resource-group IoTHubBlogDemo

![Eszköz kiépítési szolgáltatásainak listázása](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. hozzon létre egy IoT Hub blogDemoHub az újonnan létrehozott erőforráscsoport alatt

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![IoT Hub létrehozása](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. egy meglévő IoT Hub összekapcsolása egy eszköz kiépítési szolgáltatásával

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
