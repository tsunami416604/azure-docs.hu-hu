---
title: 'Rövid útmutató: Egyéni események küldése a webvégpontra – Eseményrács, Azure portal'
description: 'Rövid útmutató: Az Azure Event Grid és az Azure Portal használatával egyéni témakört tehet közzé, és előfizethet az adott témakör eseményeire. Az eseményeket egy webalkalmazás kezeli.'
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 11/05/2019
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: 9edeecdfb0c0e7ef0ef6e9d1704d81b844ac8c53
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80293759"
---
# <a name="quickstart-route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>Rövid útmutató: Egyéni események irányítsa a webvégpontot az Azure Portalon és az Event Gridben

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben létrehozunk egy egyéni témakört az Azure Portallal, feliratkozunk az egyéni témakörre, majd kiváltjuk az eseményt az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A cikk egyszerűsítése érdekében azonban az eseményeket egy olyan webalkalmazásnak küldjük el, amely az üzenetek gyűjtésével és megjelenítésével foglalkozik.

A folyamat végén látni fogja, hogy a rendszer elküldte az eseményadatokat a webalkalmazásnak.

![Eredmények megtekintése](./media/custom-event-quickstart-portal/view-result.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az Event Grid-témakörök egy felhasználó által meghatározott végpontot biztosítanak, amelyben közzéteheti az eseményeket. 

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza a bal oldali navigációs menü **Minden szolgáltatás lehetőséget,** keresse meg az **Eseményrácsot**, és válassza az **Eseményrács témaköreit.** 

    ![Eseményrács-témakörök kiválasztása](./media/custom-event-quickstart-portal/select-event-grid-topics.png)
3. Az **Eseményrács témakörei** lapon válassza a **+ Hozzáadás lehetőséget** az eszköztáron. 

    ![Eseményrács témakörének hozzáadása gomb](./media/custom-event-quickstart-portal/add-event-grid-topic-button.png)
4. A **Témakör létrehozása** lapon hajtsa végre az alábbi lépéseket:
    1. Adjon meg egyedi **nevet** az egyéni témakörnek. A témakör nevének egyedinek kell lennie, mert a nevet egy DNS-bejegyzés képviseli. Ne a képen látható nevet használja. Ehelyett hozzon létre saját nevet - 3-50 karakter között kell lennie, és csak az a-z, Az-Z, 0-9 és "-" értékeket tartalmazza.
    2. Válassza ki **Azure-előfizetését.**
    3. Jelöljön ki egy meglévő erőforráscsoportot, vagy válassza **az Új létrehozása**lehetőséget, és adja meg az **erőforráscsoport** **nevét.**
    4. Válassza ki az eseményrács témakörének **helyét.**
    5. Tartsa meg az **Eseményséma** mező alapértelmezett **eseményrács-sémáját.** 

       ![Témakör létrehozása lap](./media/custom-event-quickstart-portal/create-custom-topic.png)
    6. Kattintson a **Létrehozás** gombra. 
5. Az egyéni témakör létrehozása után láthatja a sikeres műveletről szóló értesítést. Válassza **az Ugrás erőforráscsoportra**lehetőséget. 

   ![Sikeres műveletről szóló értesítés megtekintése](./media/custom-event-quickstart-portal/success-notification.png)
6. Az **Erőforráscsoport** lapon jelölje ki az eseményrács témakörét. 

   ![Az eseményrács témakörerőforrásának kijelölése](./media/custom-event-quickstart-portal/select-event-grid-topic.png)
7. Az **eseményrács Eseményrács témaköre** lapot látja. Tartsa nyitva ezt a lapot. Később használja a rövid útmutatóban. 

    ![Az Eseményrács témakörének kezdőlapja](./media/custom-event-quickstart-portal/event-grid-topic-home-page.png)

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása
Mielőtt létrehozna egy előfizetést az egyéni témakörhöz, hozzon létre egy végpontot az eseményüzenethez. A végpont általában az eseményadatok alapján hajt végre műveleteket. Az útmutató egyszerűsítése érdekében egy olyan [előre létrehozott webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyezünk üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A cikk lapban válassza **üzembe helyezése az Azure-ban** a megoldás üzembe helyezéséhez az előfizetésben. Az Azure Portalon adjon meg értékeket a paraméterekhez.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.
1. A hely látható, de még nem lett közzétéve esemény.

   ![Új hely megtekintése](./media/custom-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>Feliratkozás egyéni témakörre

Az Event Grid-témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni, és hová szeretné küldeni az eseményeket.

1. Most az egyéni témakör **Eseményrács témaköre** lapon válassza a **+ Esemény-előfizetés lehetőséget** az eszköztáron.

   ![Esemény-előfizetés hozzáadása](./media/custom-event-quickstart-portal/new-event-subscription.png)
2. Az **Esemény-előfizetés létrehozása** lapon hajtsa végre az alábbi lépéseket:
    1. Adja meg az esemény-előfizetés **nevét.**
    3. Válassza a **Web Hook** lehetőséget a **Végpont típushoz**. 
    4. Válassza **a Végpont kiválasztása**lehetőséget. 

       ![Esemény-előfizetés értékeinek megadása](./media/custom-event-quickstart-portal/provide-subscription-values.png)
    5. A webhook végponthoz adja meg a webalkalmazás URL-címét, és adja hozzá az `api/updates` elemet a kezdőlap URL-címéhez. Válassza a **Kiválasztás megerősítése** lehetőséget.

       ![Végpont URL-címének megadása](./media/custom-event-quickstart-portal/provide-endpoint.png)
    6. Az **Esemény-előfizetés létrehozása** lapon kattintson a **Létrehozás gombra.**

3. Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

    ![Előfizetési esemény megtekintése](./media/custom-event-quickstart-portal/view-subscription-event.png)

## <a name="send-an-event-to-your-topic"></a>Esemény elküldése a témakörbe

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Az Azure CLI vagy a PowerShell használatával küldjön teszteseményt az egyéni témakörbe. Egy alkalmazás vagy Azure-szolgáltatás általában eseményadatokat küld el.

Az első példa az Azure CLI-t használja. Ez lekéri az egyéni témakör URL-címét és kulcsát, valamint a mintául szolgáló eseményadatokat. A `<topic name>` helyett használja az egyéni témakör nevét. Ez mintául szolgáló eseményadatokat hoz létre. A JSON `data` eleme az esemény hasznos adata. Bármilyen, megfelelően formált JSON megadható ebben a mezőben. A speciális útválasztáshoz és szűréshez használhatja a tárgy mezőt is. A CURL egy olyan segédprogram, amely HTTP-kéréseket küld.


### <a name="azure-cli"></a>Azure CLI
1. Az Azure Portalon válassza a **Cloud Shell**lehetőséget. Válassza a **Bash** lehetőséget a Felhőhéj ablak bal felső sarkában. 

    ![Felhő héj - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Futtassa a következő parancsot a témakör **végpontjának** lekérnie: A parancs másolása és beillesztése után frissítse a **témakör nevét** és **az erőforráscsoport nevét** a parancs futtatása előtt. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Futtassa a következő parancsot az egyéni témakör **kulcsának** bemásolásához: A parancs másolása és beillesztése után frissítse a **témakör nevét** és **az erőforráscsoport** nevét a parancs futtatása előtt. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Másolja a következő utasítást az eseménydefinícióval, és nyomja **le az ENTER billentyűt.** 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Az esemény feladására futtassa a következő **Curl** parancsot:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
A második példa a PowerShell használatával végez hasonló lépéseket.

1. Az Azure Portalon válassza a **Cloud** `https://shell.azure.com/`Shell (másik lehetőség: ). Válassza a **PowerShell** a Cloud Shell ablak bal felső sarkában. Tekintse meg a minta **Cloud Shell** ablakkép az Azure CLI szakaszban.
2. Állítsa be a következő változókat. Az egyes parancsok másolása és beillesztése után frissítse a **témakör nevét** és **az erőforráscsoport nevét** a parancs futtatása előtt:

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. A következő parancsokkal lekéri a **végpontot** és a témakör **kulcsait:**

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. Készítsd elő az eseményt. Másolja és futtassa az állításokat a Cloud Shell ablakban. 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. Az esemény elküldéséhez használja az **Invoke-WebRequest** parancsmagát. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Ellenőrzés az Eseményrács-megjelenítőben
Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Tekintse meg a webalkalmazást az imént elküldött esemény megtekintéséhez.

```json
{
  "id": "974",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2019-03-28T01:11:59+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/myegridrg/providers/Microsoft.EventGrid/topics/myegridtopic"
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha tovább kívánja használni az eseményt, akkor ne törölje a cikkben létrehozott erőforrásokat. Ellenkező esetben törölje a cikkben létrehozott erőforrásokat.

1. Válassza a bal oldali menü **Erőforráscsoportok parancsát.** Ha nem látható a bal oldali menüben, válassza a bal oldali menü **Minden szolgáltatás parancsát,** és válassza az **Erőforráscsoportok parancsot.** 
2. Válassza ki az erőforráscsoportot az **Erőforráscsoport** lap elindításához. 
3. Az eszköztáron válassza az **Erőforráscsoport törlése** lehetőséget. 
4. A törlés megerősítésének megerősítése az erőforráscsoport nevének megadásával, majd a **Törlés gombra.** 

    ![Erőforráscsoportok](./media/custom-event-quickstart-portal/delete-resource-groups.png)

    A képen látható másik erőforráscsoportot a Cloud Shell ablak hozta létre és használta. Törölje, ha később nem kívánja használni a Cloud Shell ablakot. 

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre egyéni témaköröket és esemény-előfizetéseket, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Bevezetés az Event Grid használatába](overview.md)
- [Azure Blob Storage-események átirányítása egyéni webes végpontra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)
