---
title: "Eszköz-üzembehelyezési szolgáltatások kezeléséhez Azure CLI 2.0 és az IoT-bővítmény használatával |} Microsoft Docs"
description: "Azure CLI 2.0 és az IoT-bővítmény használata az eszköz telepítési szolgáltatások kezelésére"
services: iot-dps
keywords: 
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 674245f1e284e7308474fed0f6c53b350ec1c819
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-cli-20-and-the-iot-extension-to-manage-device-provisioning-services"></a>Eszköz-üzembehelyezési szolgáltatások kezeléséhez Azure CLI 2.0 és az IoT-bővítmény segítségével

[Az Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) egy nyílt forráskódú közötti platform parancssori eszköz, például az IoT-Edge Azure-erőforrások kezeléséhez. Az Azure CLI 2.0 a Windows, Linux és MacOS érhető el. Az Azure CLI 2.0 lehetővé teszi Azure IoT Hub-erőforrások, eszköz üzembe helyezési szolgáltatáspéldány és kapcsolódó hubok nem kezelését.

Az IoT-bővítmény Azure CLI 2.0 szolgáltatások, mint a kezelés, és a teljes IoT peremhálózati képes a következőképpen színesíti.

Az oktatóanyag első lépések elvégzése beállítani az Azure CLI 2.0 és az IoT-bővítmény. Majd megtanulhatja a CLI-parancsok futtatásával alapvető eszköz üzembe helyezési szolgáltatási műveleteket hajtson végre. 

## <a name="installation"></a>Telepítés 

### <a name="step-1---install-python"></a>1. lépés – Install Python

[Python 2.7 x vagy Python 3.x](https://www.python.org/downloads/) szükséges.

### <a name="step-2---install-azure-cli-20"></a>– 2. lépés: az Azure CLI 2.0 telepítése

Kövesse a [telepítési útmutató](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) beállítani az Azure CLI 2.0 a környezetben. Az Azure CLI 2.0-s verziója legalább a 2.0.24 kell lennie vagy újabb. Használjon `az –version` érvényesítéséhez. Ebben a verzióban az bővítmény parancsok támogatja, és vezet be Knack parancs keretében. Egy egyszerű telepítése Windows módja töltse le és telepítse a [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>3. lépés – telepítés IoT-bővítmény

[Az IoT-bővítmény fontos](https://github.com/Azure/azure-iot-cli-extension) többféle módon, a bővítmény telepítéséhez ismerteti. A legegyszerűbb módszer `az extension add --name azure-cli-iot-ext`. A telepítés után is használhatja `az extension list` a jelenleg telepített bővítmények érvényesítéséhez vagy `az extension show --name azure-cli-iot-ext` az IoT-bővítmény kapcsolatos részletek megtekintéséhez. A bővítmény eltávolításához használhatja `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Alapvető eszköz-üzembehelyezési a szolgáltatási műveletek
A példa bemutatja, hogyan jelentkezzen be az Azure-fiókjával, hozzon létre egy Azure-erőforráscsoportot (olyan tároló, amely egy Azure megoldás kapcsolódó erőforrásokat tárol), létrehoz egy IoT-központot, hozzon létre egy eszköz-üzembehelyezési szolgálata van, a meglévő eszköz-üzembehelyezési szolgáltatások listában, és Hozzon létre egy csatolt IoT-központ parancssori felület parancsait. 

Hajtsa végre a megkezdése előtt a fentiekben ismertetett telepítési lépéseit. Ha nem rendelkezik Azure-fiókkal, még akkor is [ingyenes fiók létrehozását](https://azure.microsoft.com/free/?v=17.39a) ma. 


### <a name="1-log-in-to-the-azure-account"></a>1. Jelentkezzen be az Azure-fiók
  
    az login

![bejelentkezés][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Hozzon létre egy erőforráscsoportot IoTHubBlogDemo eastus

    az group create -l eastus -n IoTHubBlogDemo

![Erőforráscsoport létrehozása][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Hozzon létre két üzembe helyezési eszköz-szolgáltatások

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Terjesztési pontok létrehozása][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Minden a meglévő eszköz-üzembehelyezési az erőforráscsoportba tartozó szolgáltatások listázása

    az iot dps list --resource-group IoTHubBlogDemo

![Terjesztési pontok listája][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Az IoT-központ blogDemoHub alatt az újonnan létrehozott erőforráscsoport létrehozása

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Az IoT Hub létrehozása][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Egy eszköz-üzembehelyezési szolgáltatás csatolja egy meglévő IoT-központ

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Link Hub][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Regisztrálja az eszközt
> * Indítsa el az eszközt
> * Ellenőrizze, hogy az eszköz regisztrálva van

A következő oktatóanyag áttekintésével megismerheti, hogyan több különböző terhelésű hubok központilag továbblépés. 

> [!div class="nextstepaction"]
> [Különböző terhelésű IoT-központok eszközök beállításához](./tutorial-provision-multiple-hubs.md)
