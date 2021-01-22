---
title: IoT Hub Device Provisioning Service kezelése az Azure CLI & IoT bővítménnyel
description: Ismerje meg, hogyan kezelheti a IoT Hub Device Provisioning Service (DPS) az Azure CLI és a IoT bővítmény használatával
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: devx-track-azurecli
services: iot-dps
ms.openlocfilehash: dd0564fbb23a0695d849852fd464308cd1b5fac9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678944"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Az Azure CLI és a IoT bővítmény használata az IoT Hub Device Provisioning Service kezeléséhez

Az [Azure CLI](/cli/azure) egy nyílt forráskódú, többplatformos parancssori eszköz az Azure-erőforrások, például a IoT Edge kezelésére. Az Azure CLI Windows, Linux és macOS rendszeren érhető el. Az Azure CLI lehetővé teszi az Azure IoT Hub-erőforrások, a Device kiépítési szolgáltatás példányainak és a kapcsolt hubok felügyeletét a box-ban.

Az IoT bővítmény az Azure CLI-t az eszközök kezelésével és a teljes IoT Edge funkcióval gazdagítja.

Ebben az oktatóanyagban először végre kell hajtania az Azure CLI és a IoT-bővítmény beállításának lépéseit. Ezután megtudhatja, hogyan futtathat CLI-parancsokat az alapszintű eszközök kiépítési szolgáltatási műveleteinek végrehajtásához. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Python 2.7x vagy Python 3.x](https://www.python.org/downloads/) szükséges.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2.0.70 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="basic-device-provisioning-service-operations"></a>Alapszintű eszközök kiépítési szolgáltatásának műveletei

A példa bemutatja, hogyan jelentkezhet be az Azure-fiókjába, hogyan hozhat létre egy Azure-erőforráscsoportot (egy Azure-megoldáshoz kapcsolódó erőforrásokkal rendelkező tárolót), hogyan hozhat létre IoT Hub, létrehozhat egy eszköz-kiépítési szolgáltatást, listázhatja a meglévő eszközök kiépítési szolgáltatásait, és létrehozhat egy csatolt IoT hubot CLI-parancsokkal. 

Mielőtt nekikezdene, végezze el az előzőekben ismertetett telepítési lépéseket. Ha még nem rendelkezik Azure-előfizetéssel, akár most is létrehozhat [egy ingyenes fiókot](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Jelentkezzen be az Azure-fiókba
  
```azurecli
az login
```

![A képernyőképen egy parancssori ablak jelenik meg, amely az az login parancsot futtatja.](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. erőforráscsoport IoTHubBlogDemo létrehozása a eastus-ben

```azurecli
az group create -l eastus -n IoTHubBlogDemo
```

![Erőforráscsoport létrehozása](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. hozzon létre két eszközt a kiépítési szolgáltatásokhoz

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps
```

![Eszköz kiépítési szolgáltatásának létrehozása](./media/how-to-manage-dps-with-cli/create-dps.jpg)

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps2
```

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. az erőforráscsoport alatt lévő összes meglévő eszköz-kiépítési szolgáltatás listázása

```azurecli
az iot dps list --resource-group IoTHubBlogDemo
```

![Eszköz kiépítési szolgáltatásainak listázása](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. hozzon létre egy IoT Hub blogDemoHub az újonnan létrehozott erőforráscsoport alatt

```azurecli
az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo
```

![IoT Hub létrehozása](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. egy meglévő IoT Hub összekapcsolása egy eszköz kiépítési szolgáltatásával

```azurecli
az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus
```

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