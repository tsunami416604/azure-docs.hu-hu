---
title: Azure CLI-vel és az IoT-bővítmény használata kezelheti az IoT Hub Device Provisioning Service |} A Microsoft Docs
description: Azure CLI és az IoT-bővítmény használata az IoT Hub Device Provisioning Service kezelése
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: cfbebf8570ee044698b0f4e0abdd58370b04f759
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992864"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Azure CLI-vel és az IoT-bővítmény használata az IoT Hub Device Provisioning Service kezelése

[Az Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forráskódú közötti platform parancssori eszköz, például az IoT Edge Azure-erőforrások kezeléséhez. Az Azure parancssori felület a Windows, Linux és MacOS rendszeren érhető el. Az Azure parancssori felület lehetővé teszi, hogy kezelheti az erőforrásokat az Azure IoT Hub Device Provisioning service-példányok és csatolt központok beépített.

Az IoT-bővítmény Azure CLI-vel bővíti, például az Eszközfelügyelet és teljes körű IoT Edge-képességek.

Ebben az oktatóanyagban, először hajtsa végre az Azure CLI-vel és az IoT-bővítmény telepítése. Ezután megismerheti, hogyan Device Provisioning Service alapszintű műveletek végrehajtása a CLI-parancsok futtatásához. 

## <a name="installation"></a>Telepítés 

### <a name="step-1---install-python"></a>1. lépés – A Python telepítése

[Python 2.7x vagy Python 3.x](https://www.python.org/downloads/) szükséges.

### <a name="step-2---install-the-azure-cli"></a>2. lépés – az Azure CLI telepítése

Kövesse a [– telepítési utasítások](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) állíthatja be az Azure CLI-vel a környezetben. Legalább az Azure CLI 2.0.24-es verzióját kell vagy újabb. A verziószámot az `az –version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. Windows rendszeren a legegyszerűbb megoldás a telepítésre az [MSI](https://aka.ms/InstallAzureCliWindows) letöltése és telepítése.

### <a name="step-3---install-iot-extension"></a>3. lépés – Az IoT-bővítmény telepítése

[Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension) több módszert is ismertet a bővítmény telepítésére. A legegyszerűbb módszer az `az extension add --name azure-cli-iot-ext` futtatása. A telepítés után az `az extension list` paranccsal ellenőrizheti az aktuálisan telepített bővítményeket, az `az extension show --name azure-cli-iot-ext` paranccsal pedig megtekintheti az IoT-bővítmény adatait. A bővítményt az `az extension remove --name azure-cli-iot-ext` paranccsal távolíthatja el.


## <a name="basic-device-provisioning-service-operations"></a>Alapszintű Device Provisioning Service-műveletek
A példa bemutatja, hogyan jelentkezzen be az Azure-fiókkal, hozzon létre egy Azure-erőforráscsoportot (egy Azure-megoldás kapcsolt erőforrásait tartalmazó tárolót), hozzon létre egy IoT hubot, hozzon létre a Device Provisioning service, listázás, meglévő Eszközkiépítési szolgáltatást, és Hozzon létre egy kapcsolt IoT hubot CLI-parancsokkal. 

Mielőtt nekikezdene, végezze el az előzőekben ismertetett telepítési lépéseket. Ha még nem rendelkezik Azure-előfizetéssel, akár most is létrehozhat [egy ingyenes fiókot](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Bejelentkezés az Azure-fiókba
  
    az login

![bejelentkezés][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Az IoTHubBlogDemo erőforráscsoport létrehozása az eastus régióban

    az group create -l eastus -n IoTHubBlogDemo

![Erőforráscsoport létrehozása][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Két Eszközkiépítési szolgáltatás létrehozása

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Eszközregisztrációs szolgáltatás létrehozása][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Az összes meglévő Eszközkiépítési szolgáltatást az erőforráscsoporthoz tartozó listázása

    az iot dps list --resource-group IoTHubBlogDemo

![Eszközregisztrációs szolgáltatások listája][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. A blogDemoHub IoT Hub létrehozása az újonnan létrehozott erőforráscsoport alatt

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![IoT Hub létrehozása][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Egy meglévő IoT Hub csatolása a Device Provisioning service

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Hub csatolása][5]

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
> * Az eszköz regisztrálása
> * Az eszköz elindítása
> * Az eszköz sikeres regisztrálásának ellenőrzése

A következő oktatóanyag azt mutatja be, hogyan regisztrálhat több eszközt az elosztott terhelésű hubok között. 

> [!div class="nextstepaction"]
> [Eszközök regisztrálása elosztott terhelésű IoT Hubokon](./tutorial-provision-multiple-hubs.md)
