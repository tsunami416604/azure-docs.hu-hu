---
title: Az Azure CLI 2.0 és az IoT-bővítmény használata eszközregisztrációs szolgáltatások kezelésére | Microsoft Docs
description: Útmutató az Azure CLI 2.0 és az IoT-bővítmény használatához eszközregisztrációs szolgáltatások kezelésére
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 174f8447b17d1fa580472cbb45d0a72f41c793c3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628317"
---
# <a name="how-to-use-azure-cli-20-and-the-iot-extension-to-manage-device-provisioning-services"></a>Az Azure CLI 2.0 és az IoT-bővítmény használata eszközregisztrációs szolgáltatások kezelésére

Az [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forrású, több platformon is használható parancssori eszköz különböző Azure-erőforrások, például az IoT Edge felügyeletére. Az Azure CLI 2.0 Windows, Linux és Mac OS rendszereken érhető el. Az Azure CLI 2.0 egy használatra kész megoldás Azure IoT Hub-erőforrások, eszközregisztrációs szolgáltatáspéldányok és csatolt központok felügyeletéhez.

Az IoT-bővítmény az Azure CLI 2.0 funkcióinak körét olyan szolgáltatásokkal egészíti ki, amilyen például az eszközfelügyelet és a teljes körű IoT Edge-képességek.

Ebben az oktatóanyagban először az Azure CLI 2.0 és az IoT-bővítmény beállítására szolgáló lépéseket hajtjuk végre. Ezután azt ismertetjük, hogyan lehet CLI-parancsokkal végrehajtani az eszközregisztrációs szolgáltatások alapvető műveleteit. 

## <a name="installation"></a>Telepítés 

### <a name="step-1---install-python"></a>1. lépés – A Python telepítése

[Python 2.7x vagy Python 3.x](https://www.python.org/downloads/) szükséges.

### <a name="step-2---install-azure-cli-20"></a>2. lépés – Az Azure CLI 2.0 telepítése

Kövesse a [telepítési utasításokat](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) az Azure CLI 2.0 telepítéséhez a saját környezetében. Az Azure CLI 2.0 legalább 2.0.24-es verzióját kell használnia. A verziószámot az `az –version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. Windows rendszeren a legegyszerűbb megoldás a telepítésre az [MSI](https://aka.ms/InstallAzureCliWindows) letöltése és telepítése.

### <a name="step-3---install-iot-extension"></a>3. lépés – Az IoT-bővítmény telepítése

[Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension) több módszert is ismertet a bővítmény telepítésére. A legegyszerűbb módszer az `az extension add --name azure-cli-iot-ext` futtatása. A telepítés után az `az extension list` paranccsal ellenőrizheti az aktuálisan telepített bővítményeket, az `az extension show --name azure-cli-iot-ext` paranccsal pedig megtekintheti az IoT-bővítmény adatait. A bővítményt az `az extension remove --name azure-cli-iot-ext` paranccsal távolíthatja el.


## <a name="basic-device-provisioning-service-operations"></a>Az eszközregisztrációs szolgáltatás alapszintű műveletei
A példában bemutatjuk, hogyan jelentkezhet be Azure-fiókjába, hogyan hozhat létre Azure-erőforráscsoportot (egy Azure-megoldás kapcsolt erőforrásait tartalmazó tárolót), egy IoT Hubot és egy eszközregisztrációs szolgáltatást, hogyan listázhatja a meglévő eszközregisztrációs szolgáltatásokat, és hogyan hozhat létre egy kapcsolt IoT Hubot CLI-parancsokkal. 

Mielőtt nekikezdene, végezze el az előzőekben ismertetett telepítési lépéseket. Ha még nem rendelkezik Azure-előfizetéssel, akár most is létrehozhat [egy ingyenes fiókot](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Bejelentkezés az Azure-fiókba
  
    az login

![bejelentkezés][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Az IoTHubBlogDemo erőforráscsoport létrehozása az eastus régióban

    az group create -l eastus -n IoTHubBlogDemo

![Erőforráscsoport létrehozása][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Két eszközregisztrációs szolgáltatás létrehozása

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![DPS létrehozása][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Az erőforráscsoporthoz tartozó összes meglévő eszközregisztrációs szolgáltatás listázása

    az iot dps list --resource-group IoTHubBlogDemo

![DPS-ek listázása][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. A blogDemoHub IoT Hub létrehozása az újonnan létrehozott erőforráscsoport alatt

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![IoT Hub létrehozása][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Meglévő IoT Hub csatolása egy eszközregisztrációs szolgáltatáshoz

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
