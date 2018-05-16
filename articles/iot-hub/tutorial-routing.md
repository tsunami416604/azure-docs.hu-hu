---
title: Üzenetek útválasztásának konfigurálása az Azure IoT Hub (.NET) használatával | Microsoft Docs
description: Üzenetek útválasztásának konfigurálása az Azure IoT Hub (.NET) használatával
services: iot-hub
documentationcenter: .net
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 0674ed033f77d7d2eca319d0b1e82171dfa4256d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-configure-message-routing-with-iot-hub"></a>Oktatóanyag: Üzenetek útválasztásának konfigurálása az IoT Hub használatával

Az üzenetek útválasztása lehetővé teszi telemetriaadatok küldését az IoT-eszközökről a beépített, Event Hub-kompatibilis végpontokra vagy olyan egyéni végpontokra, mint például a Blob Storage, a Service Bus-üzenetsorok, a Service Bus-témakörök és az eseményközpontok. Az üzenetek útválasztásának konfigurálásakor létrehozhat útválasztási szabályokat, amelyekkel megadható egy adott szabálynak megfelelő útvonal. A beállítás után az IoT Hub automatikusan a végpontokhoz irányítja át a bejövő adatokat. 

Ez az oktatóanyag bemutatja, hogyan állíthat be és használhat útválasztási szabályokat az IoT Hubbal. Az üzeneteket egy IoT-eszközről egy másik szolgáltatásra, például a Blob Storage-ra és egy Service Bus-üzenetsorra fogja átirányítani. A Service Bus-üzenetsorhoz küldött üzeneteket egy logikai alkalmazás veszi át, és a rendszer e-mailben küldi el őket. Azokat az üzeneteket, amelyekhez nincs külön útválasztás beállítva, a rendszer az alapértelmezett végpontra küldi, és a Power BI-vizualizációban tekinthetők meg.

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * A következő alapvető erőforrások beállítása az Azure CLI vagy a PowerShell használatával: IoT Hub, tárfiók, Service Bus-üzenetsor, szimulált eszköz.
> * Végpontok és útvonalak konfigurálása az IoT Hubban a tárfiókhoz és a Service Bus-üzenetsorokhoz.
> * Egy logikai alkalmazás létrehozása, amely egy üzenet Service Bus-üzenetsorba való érkezésekor indul el és küld e-maileket.
> * Egy olyan alkalmazás letöltése és futtatása, amely a Hubhoz üzeneteket küldő IoT-eszközt szimulál a különböző útválasztási lehetőségekhez.
> * Power BI-vizualizáció létrehozása az alapértelmezett végpontra küldött adatokról.
> * Eredmények megtekintése...
> * ... a Service Bus-üzenetsorban és az e-mailekben.
> * ... a tárfiókban.
> * ... a Power BI-vizualizációban.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

- Telepítse [a Windowshoz készült Visual Studio programot](https://www.visualstudio.com/). 

- Power BI-fiók az alapértelmezett végpont Stream Analytics-elemzéséhez. ([A Power BI ingyenes kipróbálása](https://app.powerbi.com/signupredirect?pbi_source=web))

- Office 365-fiók értesítési e-mailek küldéséhez. 

Azure CLI vagy Azure PowerShell az oktatóanyag beállítási lépéseinek elvégzéséhez. 

Az Azure CLI használatához az Azure CLI-t helyileg is telepítheti, de az Azure Cloud Shell használata ajánlott. Az Azure Cloud Shell egy ingyenes, interaktív felület, amelyet az Azure CLI-szkriptek futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak a Cloud Shellben, ezért nem kell helyileg telepítenie őket. 

Ha a PowerShellt kívánja használni, telepítse helyileg az alábbi utasítások szerint. 

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

A Cloud Shell többféleképpen is megnyitható:

|  |   |
|-----------------------------------------------|---|
| Kattintson a **Kipróbálás** elemre egy kódblokk jobb felső sarkában. | ![A Cloud Shell ebben a cikkben](./media/tutorial-routing/cli-try-it.png) |
| Nyissa meg a Cloud Shellt a böngészőben. | [![https://shell.azure.com/bash](./media/tutorial-routing/launchcloudshell.png)](https://shell.azure.com) |
| Az [Azure Portal](https://portal.azure.com) jobb felső sarkában található menüben kattintson a **Cloud Shell** gombra. |    ![A Cloud Shell a portálon](./media/tutorial-routing/cloud-shell-menu.png) |
|  |  |

### <a name="using-azure-cli-locally"></a>Az Azure CLI helyi használata

Ha inkább helyileg szeretné használni a CLI-t a Cloud Shell helyett, az Azure CLI moduljának 2.0.30.0-s vagy újabb verziójával kell rendelkeznie. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

### <a name="using-powershell-locally"></a>A PowerShell helyi használata

Az oktatóanyaghoz az Azure PowerShell-modul 5.7-es vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

## <a name="set-up-resources"></a>Erőforrások beállítása

Ehhez az oktatóanyaghoz a következőkre lesz szüksége: IoT Hub, tárfiók, Service Bus-üzenetsor. Ezek az erőforrások létrehozhatók az Azure CLI vagy az Azure PowerShell használatával. Ugyanazt az erőforráscsoportot és helyet használja minden erőforráshoz. Így mindent egyetlen lépésben távolíthat el az erőforráscsoport törlésével.

Az alábbi szakaszok ismertetik a szükséges lépések elvégzésének módját. Kövesse a CLI-re *vagy* a PowerShellre vonatkozó utasításokat.

1. Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md). 

    <!-- When they add the Basic tier, change this to use Basic instead of Standard. -->

2. Hozzon létre egy IoT Hubot az S1 szinten. Adjon hozzá egy fogyasztói csoportot az IoT Hubhoz. A fogyasztói csoportot az Azure Stream Analytics használja az adatok lekérésekor.

3. Hozzon létre egy standard szintű V1-tárfiókot Standard_LRS-replikációval.

4. Hozzon létre egy Service Bus-névteret és üzenetsort. 

5. Hozzon létre egy eszközidentitást ahhoz a szimulált eszközhöz, amely üzeneteket küld a központnak. Mentse a kulcsot a tesztelési fázishoz.

### <a name="azure-cli-instructions"></a>Azure CLI utasítások

A szkript használatának legegyszerűbb módja a szkript másolása és beillesztése a Cloud Shellbe. A szolgáltatás azt feltételezi, hogy már bejelentkezett, és soronként futtatja a szkriptet. 

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device 

# These resource names must be globally unique.
# You might need to change these if they are already in use by someone else.
iotHubName=ContosoTestHub 
storageAccountName=contosoresultsstorage 
sbNameSpace=ContosoSBNamespace 
sbQueueName=ContosoSBQueue

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "$storageAccountKey"

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off 

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNameSpace \
    --location $location
    
# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNameSpace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

### <a name="powershell-instructions"></a>PowerShell-utasítások

A szkript használatának legegyszerűbb módja a [PowerShell ISE](/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise.md) megnyitása, a szkript másolása a vágólapra, majd a teljes szkript beillesztése a szkriptablakba. Ezután, igény esetén módosíthatja az erőforrások nevének értékeit és futtathatja a teljes szkriptet. 

```azurepowershell-interactive
# Log into Azure account.
Login-AzureRMAccount

# Set the values for the resource names.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"
$iotDeviceName = "Contoso-Test-Device"

# These resource names must be globally unique.
# You might need to change these if they are already in use by someone else.
$iotHubName = "ContosoTestHub"
$storageAccountName = "contosoresultsstorage"
$serviceBusNamespace = "ContosoSBNamespace"
$serviceBusQueueName  = "ContosoSBQueue"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create the IoT hub.
New-AzureRmIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzureRmIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
$storageContext = $storageAccount.Context 

# Create the container in the storage account.
New-AzureStorageContainer -Name $containerName `
    -Context $storageContext

# Create the Service Bus namespace.
New-AzureRmServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# Create the Service Bus queue to be used as a routing destination.
New-AzureRmServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName 

```

Következő lépésként hozzon létre egy eszközidentitást, és mentse a hozzá tartozó kulcsot későbbi használatra. Ezt az eszközidentitást a szimulált alkalmazás használja az IoT Hubnak való üzenetküldésre. Ez a funkció nem érhető el a PowerShellben, de létrehozhatja az eszközt az [Azure Portalon](https://portal.azure.com).

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjába.

2. Kattintson az **Erőforráscsoportok** elemre, és válassza ki az erőforráscsoportját. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

3. Az erőforrások listájában kattintson az IoT Hub-jára. Ez az oktatóanyag a **ContosoTestHub** erőforrást használja. Válassza ki az **IoT-eszközök** lehetőséget a Hub panelről.

4. Kattintson a **+Hozzáadás** gombra. Az Eszköz hozzáadása panelen adja meg az eszköz azonosítóját. Ez az oktatóanyag a **Contoso-Test-Device** eszközt használja. Hagyja üresen a kulcsokat, majd jelölje be a **Kulcsok automatikus létrehozása** jelölőnégyzetet. Ellenőrizze, hogy az **Eszköz csatlakoztatása az IoT Hubhoz** engedélyezve van-e. Kattintson a **Save** (Mentés) gombra.

   ![Az eszközhozzáadási képernyő képernyőképe.](./media/tutorial-routing/add-device.png)

5. Most, hogy az eszköz létrejött, kattintson rá a létrehozott kulcsok megtekintéséhez. Kattintson az Elsődleges kulcs másolás ikonjára, és mentse (például egy Jegyzettömb-dokumentumba) az oktatóanyag tesztelési fázisában történő használatra.

   ![Képernyőkép az eszköz részleteiről, többek között a kulcsokról.](./media/tutorial-routing/device-details.png)



## <a name="set-up-message-routing"></a>Üzenetek útválasztásának beállítása

Az üzeneteket különböző forrásokhoz fogja irányítani azon tulajdonságok alapján, amelyeket a szimulált eszköz csatolt az üzenethez. Azokat az üzeneteket, amelyekhez nincs egyéni útválasztás beállítva, az alapértelmezett végpontra (üzenetek/események) küldi a rendszer. 

|érték |Eredmény|
|------|------|
|level="storage" |Írás az Azure Storage-ba.|
|level="critical" |Írás a Service Bus-üzenetsorba. Egy logikai alkalmazás lekéri az üzenetet a sorból, és az Office 365 használatával e-mailben küldi el az üzenetet.|
|alapértelmezett |Jelenítse meg az adatokat a Power BI használatával.|

### <a name="routing-to-a-storage-account"></a>Irányítás egy tárfiókba 

Most beállíthatja az útválasztást a tárfiókhoz. Adjon meg egy végpontot, majd állítson be hozzá egy útvonalat. Azokat az üzeneteket, amelyeknél a **level** tulajdonság **storage** értékre van állítva, automatikusan egy tárfiókba írja a rendszer.

1. Az [Azure Portalon](https://portal.azure.com) kattintson az **Erőforráscsoportok** elemre, majd válassza ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja. Az erőforrások listájában kattintson az IoT Hub elemre. Ez az oktatóanyag a **ContosoTestHub** erőforrást használja. Kattintson a **Végpontok** elemre. A **Végpontok** panelen kattintson a **+Hozzáadás** elemre. Adja meg a következő információkat:

   **Név**: Adja meg a végpont nevét. Ez az oktatóanyag a **StorageContainer** nevet használja.
   
   **Végpont típusa**: A legördülő listából válassza ki az **Azure Storage-tárolót**.

   Kattintson a **Tároló kiválasztása** elemre a tárfiókok listájának megtekintéséhez. Válassza ki a tárfiókot. Ez az oktatóanyag a **contosoresultsstorage** fiókot használja. Ezután válassza ki a tárolót. Ez az oktatóanyag a **contosoresults** tárolót használja. Kattintson a **Kiválasztás** gombra, ezzel visszatér a Végpont hozzáadása panelhez. 
   
   ![A végpont hozzáadását bemutató képernyőkép.](./media/tutorial-routing/add-endpoint-storage-account.png)
   
   A végpont hozzáadásának befejezéshez kattintson az **OK** gombra.
   
2. Az IoT Hubon kattintson az **Útvonalak** elemre. Létre fog hozni egy útválasztási szabályt, amely a végpontként az imént hozzáadott Storage-tárolóhoz irányítja az üzeneteket. Az Útvonalak panel tetején kattintson a **+Hozzáadás** elemre. Töltse ki a képernyőn látható mezőket. 

   **Név**: Adja meg az útválasztási szabály nevét. Ez az oktatóanyag a **StorageRule** nevet használja.

   **Adatforrás**: A legördülő listából válassza az **Eszközüzenetek** elemet.

   **Végpont**: Válassza ki az imént beállított végpontot. Ez az oktatóanyag a **StorageContainer** nevet használja. 
   
   **Lekérdezési karakterlánc**: Lekérdezési karakterláncként írja be a következőt: `level="storage"`. 

   ![A tárfiók útválasztási szabályának létrehozását bemutató képernyőkép.](./media/tutorial-routing/create-a-new-routing-rule-storage.png)
   
   Kattintson a **Save** (Mentés) gombra. A befejezés után a rendszer visszalép az Útvonalak panelre, ahol láthatja a tároló új útválasztási szabályát. Zárja be az Útvonalak panelt, ezzel visszatér az Erőforráscsoport oldalra.

### <a name="routing-to-a-service-bus-queue"></a>Irányítás Service Bus-üzenetsorra 

Most állítsa be az útválasztást a Service Bus-üzenetsorhoz. Adjon meg egy végpontot, majd állítson be hozzá egy útvonalat. Azokat az üzeneteket, amelyeknél a **level** tulajdonság **critical** értékre van állítva, a rendszer a Service Bus-üzenetsorba írja. Ez elindít egy logikai alkalmazást, amely ezután az információkat tartalmazó e-mailt küld. 

1. Az Erőforráscsoport lapon kattintson az IoT Hub, majd a **Végpontok** elemre. A **Végpontok** panelen kattintson a **+Hozzáadás** elemre. Adja meg a következő információkat:

   **Név**: Adja meg a végpont nevét. Ez az oktatóanyag a **CriticalQueue** nevet használja. 

   **Végpont típusa**: A legördülő listából válassza ki a **Service Bus-üzenetsor** elemet.

   **Service Bus-névtér**: A legördülő listából válassza az oktatóanyaghoz tartozó Service Bus-névteret. Ez az oktatóanyag a **ContosoSBNamespace** névteret használja.

   **Service Bus-üzenetsor**: A legördülő listából válassza ki a Service Bus-üzenetsort. Ez az oktatóanyag a **contososbqueue** üzenetsort használja.

   ![Végpont Service Bus-üzenetsorhoz való hozzáadását bemutató képernyőkép.](./media/tutorial-routing/add-endpoint-sb-queue.png)

   A végpont mentéséhez kattintson az **OK** gombra. Ha kész, zárja be a Végpontok panelt. 
    
2. Az IoT Hubon kattintson az **Útvonalak** elemre. Létre fog hozni egy útválasztási szabályt, amely a végpontként az imént hozzáadott Service Bus-üzenetsorhoz irányítja az üzeneteket. Az Útvonalak panel tetején kattintson a **+Hozzáadás** elemre. Töltse ki a képernyőn látható mezőket. 

   **Név**: Adja meg az útválasztási szabály nevét. Ez az oktatóanyag a **SBQueueRule** nevet használja. 

   **Adatforrás**: A legördülő listából válassza az **Eszközüzenetek** elemet.

   **Végpont**: Válassza ki az imént beállított **CriticalQueue** végpontot.

   **Lekérdezési karakterlánc**: Lekérdezési karakterláncként írja be a következőt: `level="critical"`. 

   ![Útválasztási szabály Service Bus-üzenetsorhoz való létrehozását bemutató képernyőkép.](./media/tutorial-routing/create-a-new-routing-rule-sbqueue.png)
   
   Kattintson a **Save** (Mentés) gombra. Miután visszatért az Útvonalak panelre, láthatja a két új útválasztási szabályt, ahogy itt is szerepelnek.

   ![Képernyőkép az imént beállított útvonalakról.](./media/tutorial-routing/show-routing-rules-for-hub.png)

   Zárja be az Útvonalak panelt, ezzel visszatér az Erőforráscsoport oldalra.

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása  

A Service Bus-üzenetsor a kritikusként megjelölt üzenetek fogadására használható. Állítson be egy logikai alkalmazást a Service Bus-üzenetsor monitorozására, illetve arra, hogy e-mailt küldjön, amikor a rendszer hozzáad egy üzenetet az üzenetsorhoz. 

1. Az [Azure Portalon](https://portal.azure.com) kattintson az **+ Erőforrás létrehozása** gombra. A keresőmezőbe írja a **logikai alkalmazás** kifejezést, és nyomja le az Enter billentyűt. Válassza a logikai alkalmazást lehetőséget a megjelenő keresési eredmények közül, majd a **Létrehozás** elemre kattintva lépjen tovább a **Logikai alkalmazás létrehozása** panelre. Töltse ki a mezőket. 

   **Név**: Ez a mező a logikai alkalmazás neve. Ez az oktatóanyag a **ContosoLogicApp** nevet használja. 

   **Előfizetés**: Válassza ki az Azure-előfizetést.

   **Erőforráscsoport**: Kattintson a **Meglévő használata** elemre, és válassza ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja. 

   **Hely**: Használja a saját helyzetét. Ez az oktatóanyag az **USA nyugati régióját** használja. 

   **Log Analytics**: Ezt a kapcsolót ki kell kapcsolni. 

   ![A Logikai alkalmazás létrehozása képernyő képernyőképe.](./media/tutorial-routing/create-logic-app.png)

   Kattintson a **Create** (Létrehozás) gombra.

4. Lépjen a logikai alkalmazáshoz. A logikai alkalmazás legegyszerűbb beszerzéséhez kattintson az **Erőforráscsoportok** elemre, válassza ki a saját erőforráscsoportját (ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja), majd válassza ki a logikai alkalmazást az erőforrások listájából. Megjelenik a Logic Apps Designer lap (előfordulhat, hogy jobbra kell görgetnie, hogy megjelenjen a teljes lap). Ezután a Logic Apps Designer oldalon görgessen lefelé, amíg meg nem jelenik az **Üres logikai alkalmazás +** csempe, majd kattintson rá. 

5. Megjelenik az összekötők listája. Válassza a **Service Bus** lehetőséget. 

   ![Képernyőkép az összekötők listájáról.](./media/tutorial-routing/logic-app-connectors.png)

6. Megjelenik az eseményindítók listája. Válassza a **Service Bus – Üzenet üzenetsorba érkezésekor (feldolgozottnak minősítés automatikusan)** lehetőséget. 

   ![Képernyőkép a Service Bus-triggerek listájáról.](./media/tutorial-routing/logic-app-triggers.png)

6. A következő képernyőn töltse ki a Kapcsolat neve mezőt. Ez az oktatóanyag a **ContosoConnection** nevet használja. 

   ![A Service Bus-üzenetsor kapcsolatának beállítását bemutató képernyőkép.](./media/tutorial-routing/logic-app-define-connection.png)

   Kattintson a Service Bus-névtérre. Ez az oktatóanyag a **ContosoSBNamespace** névteret használja. A névtér kiválasztásakor a Portal lekérdezi a Service Bus-névteret a kulcsok lekéréséhez. Válassza a **RootManageSharedAccessKey** lehetőséget, majd kattintson a **Létrehozás** elemre. 
   
   ![A kapcsolat beállításának befejezését bemutató képernyőkép.](./media/tutorial-routing/logic-app-finish-connection.png)

7. A következő képernyőn a legördülő listából válassza ki az üzenetsor nevét (ebben az oktatóanyagban ez **contososbqueue**). A többi mező esetében használhatja az alapértelmezett értékeket. 

   ![Képernyőkép az üzenetsor beállításairól.](./media/tutorial-routing/logic-app-queue-options.png)

7. Állítsa be a műveletet, hogy e-mailt küldjön, amikor egy üzenet érkezik az üzenetsorba. A Logic Apps Designerben egy lépés hozzáadásához kattintson az **+ Új lépés** elemre, majd a **Művelet hozzáadása** gombra. A **Művelet kiválasztása** panelen keresse meg az **Office 365 Outlook** elemet, és kattintson rá. A triggerek képernyőjén válassza az **Office 365 Outlook – e-mail küldése** lehetőséget.  

   ![Képernyőkép az Office 365 beállításairól.](./media/tutorial-routing/logic-app-select-outlook.png)

8. Ezután jelentkezzen be az Office 365-fiókjába a kapcsolat beállításához. Adja meg az e-mailek címzettjeinek e-mail-címeit. Adja meg a tárgyat is, és írja be a szövegtörzsben megjeleníteni kívánt üzenetet. Teszteléshez adja meg a saját e-mail-címét címzettként.

   Kattintson a **Dinamikus tartalom hozzáadása** elemre a belefoglalható üzenettartalom megjelenítéséhez. Válassza a **Tartalom** lehetőséget, amellyel belefoglalhatja az üzenetet az e-mailbe. 

   ![Képernyőkép a logikai alkalmazás e-mail-beállításairól.](./media/tutorial-routing/logic-app-send-email.png)

9. Kattintson a **Save** (Mentés) gombra. Ezután zárja be a Logic App Designert.

## <a name="set-up-azure-stream-analytics"></a>Az Azure Stream Analytics beállítása

A Power BI-vizualizáció adatainak megtekintéséhez először állítson be egy Stream Analytics-feladatot az adatok lekérésére. Ne feledje, hogy a rendszer csak azokat az üzeneteket küldi az alapértelmezett végpontra, amelyek **level** tulajdonsága **normal** értékű, és a Stream Analytics-feladat csak ezeket kéri le a Power BI-vizualizációhoz.

### <a name="create-the-stream-analytics-job"></a>A Stream Analytics-feladat létrehozása

1. Az [Azure Portalon](https://portal.azure.com) kattintson az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **Stream Analytics-feladat** elemre.

2. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie. Ez az oktatóanyag a **contosoJob** nevet használja.

   **Erőforráscsoport**: Használja az IoT Hubja által használt erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja. 

   **Hely**: Használja a beállítási szkriptnél is használt helyet. Ez az oktatóanyag az **USA nyugati régióját** használja. 

   ![A Stream Analytics-feladat létrehozását bemutató képernyőkép.](./media/tutorial-routing/stream-analytics-create-job.png)

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia** területen kattintson a **Bemenetek** elemre.

2. A **Bemenetek** panelen kattintson a **Streambemenet hozzáadása** elemre, és válassza az IoT Hubot. A megjelenő képernyőn töltse ki az alábbi mezőket:

   **Bemeneti áljel**: Ez az oktatóanyag a **contosoinputs** áljelet használja.

   **Előfizetés**: Válassza ki az előfizetését.

   **IoT Hub**: Válassza ki az IoT Hubot. Ez az oktatóanyag a **ContosoTestHub** erőforrást használja.

   **Végpont**: Válassza az **Üzenetkezelés** lehetőséget. (Ha a Műveletek monitorozása lehetőséget választja, akkor az IoT Hub telemetriaadatait kapja meg, nem pedig az átküldött adatokat.) 

   **Megosztott elérési házirend neve**: Válassza az **iothubowner** lehetőséget. A Portal automatikusan kitölti a megosztott elérési házirend kulcsát.

   **Fogyasztói csoport**: Válassza ki a korábban létrehozott fogyasztói csoportot. Ez az oktatóanyag a **contosoconsumers** csoportot használja.
   
   A többi mezőnél fogadja el az alapértelmezett beállításokat. 

   ![A Stream Analytics-feladat bemeneteinek beállítását bemutató képernyőkép.](./media/tutorial-routing/stream-analytics-job-inputs.png)

5. Kattintson a **Save** (Mentés) gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia** területen kattintson a **Kimenetek** elemre.

2. A **Kimenetek** panelen kattintson a **Hozzáadás**, majd a **Power BI** elemre. A megjelenő képernyőn töltse ki az alábbi mezőket:

   **Kimeneti áljel**: A kimenet egyedi áljele. Ez az oktatóanyag a **contosooutputs** áljelet használja. 

   **Adatkészlet neve**: A Power BI-ban használni kívánt adatkészletnév. Ez az oktatóanyag a **contosodataset** nevet használja. 

   **Tábla neve**: A Power BI-ban használni kívánt táblanév. Ez az oktatóanyag a **contosotable** nevet használja.

   A többi mezőnél fogadja el az alapértelmezett beállításokat.

3. Kattintson az **Engedélyezés** elemre, és jelentkezzen be a Power BI-fiókjába.

   ![A Stream Analytics-feladat kimeneteinek beállítását bemutató képernyőkép.](./media/tutorial-routing/stream-analytics-job-outputs.png)

4. Kattintson a **Save** (Mentés) gombra.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **Feladattopológia** területen kattintson a **Lekérdezés** elemre.

2. A `[YourInputAlias]` elemet cserélje le a feladat bemeneti áljelére. Ez az oktatóanyag a **contosoinputs** bemeneti áljelet használja.

3. A `[YourOutputAlias]` elemet cserélje le a feladat kimeneti áljelére. Ez az oktatóanyag a **contosooutputs** áljelet használja.

   ![A Stream Analytics-feladat lekérdezésének beállítását bemutató képernyőkép.](./media/tutorial-routing/stream-analytics-job-query.png)

4. Kattintson a **Save** (Mentés) gombra.

5. Zárja be a Lekérdezés panelt.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A Stream Analytics-feladat területen kattintson az **Indítás** > **Most** > **Indítás** elemre. Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

A Power BI-jelentés beállításához adatokra van szükség, ezért a Power BI-t az eszköz létrehozása és az eszközszimulációs alkalmazás futtatása után fogja beállítani.

## <a name="run-simulated-device-app"></a>Szimulálteszköz-alkalmazás futtatása

Korábban, a szkript telepítési szakaszában beállított egy eszközt az IoT-eszköz használatának szimulálására. Ebben a szakaszban egy .NET-konzolalkalmazást tölt le, amely egy, az eszközről a felhőbe irányuló üzeneteket egy IoT Hubra küldő eszközt szimulál. Ez az alkalmazás mindegyik útválasztási módszerrel küld üzeneteket. 

Az [IoT-eszköz szimulációjára](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) szolgáló megoldás letöltése. Ez letölt egy olyan adattárat, amelyben számos alkalmazás található. A keresett megoldás a Tutorials/Routing/SimulatedDevice/ mappában található.

Kattintson duplán a megoldásfájlra (SimulatedDevice.sln), a kód megnyitásához a Visual Studióban, majd nyissa meg a Program.cs fájlt. Az `{iot hub hostname}` értéket cserélje le az IoT Hub gazdagépnevére. Az IoT Hub gazdagépnevének formátuma: **{iot-hub-name}.azure-devices.net**. Ebben az oktatóanyagban a központ gazdagépneve: **ContosoTestHub.azure-devices.net**. Ezután a `{device key}` értéket cserélje le az eszközkulcsra, amelyet korábban, a szimulált eszköz beállítása során mentett. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Futtatás és tesztelés 

Futtassa a konzolalkalmazást. Várjon néhány percet. Az alkalmazás konzolképernyőjén láthatja az elküldött üzeneteket.

Az alkalmazás másodpercenként elküld egy új üzenetet az eszközről a felhőbe az IoT Hubnak. Az üzenet egy JSON-szerializált objektumot tartalmaz az eszköz azonosítójával, a hőmérséklettel, a páratartalommal és az üzenet szintjével, amely alapértelmezés szerint `normal`. Az alkalmazás véletlenszerűen rendeli hozzá a `critical` vagy a `storage` szintet, így az üzenet a tárfiókhoz vagy a Service Bus-üzenetsorhoz lesz irányítva (a logikai alkalmazás pedig ennek hatására egy e-mailt küld). Az alapértelmezett olvasás (`normal`) megjelenik a BI-jelentésben, amelyet a következő lépésben fog beállítani.

Ha minden helyesen lett beállítva, a következő eredményeket fogja látni:

1. E-maileket kap a kritikus üzenetekről. 

   ![Képernyőkép az eredményként kapott e-mailekről.](./media/tutorial-routing/results-in-email.png)

   Ez a következőt jelenti:

   * A Service Bus-üzenetsorra történő irányítás megfelelően működik.
   * Az üzenetet a Service Bus-üzenetsorból lekérő logikai alkalmazás megfelelően működik.
   * A logikai alkalmazás Outlook-összekötője megfelelően működik. 

2. Az [Azure Portalon](https://portal.azure.com) kattintson az **Erőforráscsoportok** elemre, és válassza ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja. Válassza ki a tárfiókot, kattintson a **Blobok** elemre, majd válassza a Tároló lehetőséget. Ez az oktatóanyag a **contosoresults** tárolót használja. Megjelenik egy mappa, amelyben lefúrhat a könyvtárakba, amíg egy vagy több fájlt nem lát. Nyissa meg az egyik fájlt. A fájlok a tárfiókhoz irányított bejegyzéseket tartalmazzák. 

   ![A tároló eredményfájljait bemutató képernyőkép.](./media/tutorial-routing/results-in-storage.png)

Ez a következőt jelenti:

   * A tárfiókhoz történő útválasztás megfelelően működik.

Most, hogy az alkalmazás fut, állítsa be a Power BI-vizualizációt az alapértelmezett útválasztáson keresztül érkező üzenetek megtekintéséhez. 

## <a name="set-up-the-powerbi-visualizations"></a>Power BI-vizualizációk beállítása

1. Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/)-fiókjába.

2. Lépjen a **Munkaterületek** részhez, és válassza ki azt a munkaterületet, amelyet a Stream Analytics-feladat kimenetének létrehozásakor állított be. Ez az oktatóanyag a **My Workspace** nevű munkaterületet használja. 

3. Kattintson az **Adatkészletek** elemre.

   Megjelenik az adatkészlet, amelyet a Stream Analytics-feladat kimenetének létrehozásakor adott meg. Ez az oktatóanyag a **contosodataset** nevet használja. (Akár 5-10 percet is igénybe vehet, mire az adatkészlet először megjelenik.)

4. A **MŰVELETEK** területen kattintson az első ikonra egy jelentés létrehozásához.

   ![Képernyőkép a Power BI-munkaterületről; a Műveletek és a jelentés ikon ki van emelve.](./media/tutorial-routing/power-bi-actions.png)

5. Hozzon létre egy vonaldiagramot, amely a valós időben jeleníti meg a hőmérséklet változását.

   a. A jelentéslétrehozási lapon vegyen fel egy vonaldiagramot a vonaldiagram ikonra kattintva.

   ![Képernyőkép a vizualizációkról és mezőkről.](./media/tutorial-routing/power-bi-visualizations-and-fields.png)

   b. A **Mezők** panelen bontsa ki Stream Analytics-feladat kimenetének létrehozásakor megadott táblát. Ez az oktatóanyag a **contosotable** nevet használja.

   c. Az **EventEnqueuedUtcTime** elemet húzza a **Vizualizációk** panel **Tengely** részére.

   d. Húzza a **hőmérséklet** elemet az **Értékek** helyre.

   Létrejön a vonaldiagram. Az X tengely az UTC időzóna szerinti dátumot is időt mutatja. Az Y tengelyen az érzékelőből származó hőmérsékleti adatok láthatók.

7. Hozzon létre egy másik vonaldiagramot, amely a valós időben jeleníti meg a páratartalom változását. Második diagram beállításához kövesse a fenti lépéseket, és az **EventEnqueuedUtcTime** elemet helyezze az X tengelyre, a **páratartalom** értéket pedig az Y tengelyre.

   ![Képernyőkép a végleges, két diagramot tartalmazó Power BI-jelentésről.](./media/tutorial-routing/power-bi-report.png)

8. Kattintson a **Mentés** elemre a jelentés mentéséhez.

Az adatoknak mindkét diagramon meg kell jelenniük. Ez a következőt jelenti:

   * Az alapértelmezett végpontra történő irányítás megfelelően működik.
   * Az Azure Stream Analytics-feladat streamelése megfelelően működik.
   * A Power BI-vizualizáció megfelelően van beállítva.

A Power BI-ablak tetején található Frissítés gombra kattintva frissítheti a diagramokat a legújabb adatok megtekintéséhez. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha az összes létrehozott erőforrást el kívánja távolítani, törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben eltávolítja az IoT Hubot, a Service Bus-névteret és -üzenetsort, a logikai alkalmazást, a tárfiókot és magát az erőforráscsoportot is. 

### <a name="clean-up-resources-in-the-powerbi-visualization"></a>Erőforrások eltávolítása a Power BI-vizualizációban

Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/)-fiókjába. Lépjen a munkaterülethez. Ez az oktatóanyag a **My Workspace** nevű munkaterületet használja. A Power BI-vizualizáció eltávolításához lépjen az Adatkészletek területre, és törölje az adatkészletet a kuka ikonra kattintva. Ez az oktatóanyag a **contosodataset** nevet használja. Az adatkészlet eltávolításakor a jelentés is törlődik.

### <a name="clean-up-resources-using-azure-cli"></a>Az erőforrások eltávolítása az Azure CLI használatával

Az erőforráscsoport az [az group delete](https://docs.microsoft.com/en-us/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal távolítható el.

```azurecli-interactive
az group delete --name $resourceGroup
```
### <a name="clean-up-resources-using-powershell"></a>Az erőforrások eltávolítása a PowerShell használatával

Az erőforráscsoport a [Remove-AzureRmResourceGroup](https://docs.microsoft.com/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal távolítható el. Az oktatóanyag elején a **ContosoIoTRG1** értéket adta meg a $resourceGroup beállításhoz.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $resourceGroup
```


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan használhatja az üzenetek útvonalválasztását IoT Hub üzenetek különböző célokhoz történő irányítására az alábbi feladatok elvégzésével.  

> [!div class="checklist"]
> * A következő alapvető erőforrások beállítása az Azure CLI vagy a PowerShell használatával: IoT Hub, tárfiók, Service Bus-üzenetsor, szimulált eszköz.
> * Végpontok és útvonalak konfigurálása az IoT Hubban a tárfiókhoz és a Service Bus-üzenetsorokhoz.
> * Egy logikai alkalmazás létrehozása, amely egy üzenet Service Bus-üzenetsorba való érkezésekor indul el és küld e-maileket.
> * Egy olyan alkalmazás letöltése és futtatása, amely a Hubhoz üzeneteket küldő IoT-eszközt szimulál a különböző útválasztási lehetőségekhez.
> * Power BI-vizualizáció létrehozása az alapértelmezett végpontra küldött adatokról.
> * Eredmények megtekintése...
> * ... a Service Bus-üzenetsorban és az e-mailekben.
> * ... a tárfiókban.
> * ... a Power BI-vizualizációban.

A következő oktatóanyag az IoT-eszközök állapotának kezelését mutatja be. 

> [!div class="nextstepaction"]
[Ismerkedés az Azure IoT Hub-ikereszközökkel](iot-hub-node-node-twin-getstarted.md)

 <!--  [Manage the state of a device](./tutorial-manage-state.md) -->