---
title: Egyéni események küldése az webes végpont – Event Grid, Azure Portalon
description: Azure Event Grid és az Azure portal használatával közzététele egy egyéni témakör és feliratkozás témakör eseményeire. Az események a webalkalmazás kezeli.
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 03/27/2019
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: 0861c47ef9f9649dfe223d8abeb51310a87ea4a9
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549967"
---
# <a name="quickstart-route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>Gyors útmutató: Események átirányítása egyéni webes végpontra, az Azure portal és az Event Grid

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben létrehozunk egy egyéni témakört az Azure Portallal, feliratkozunk az egyéni témakörre, majd kiváltjuk az eseményt az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A cikk egyszerűsítése érdekében azonban az eseményeket egy olyan webalkalmazásnak küldjük el, amely az üzenetek gyűjtésével és megjelenítésével foglalkozik.

A folyamat végén látni fogja, hogy a rendszer elküldte az eseményadatokat a webalkalmazásnak.

![Eredmények megtekintése](./media/custom-event-quickstart-portal/view-result.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az Event Grid-témakörök egy felhasználó által meghatározott végpontot biztosítanak, amelyben közzéteheti az eseményeket. 

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza ki **minden szolgáltatás** a bal oldali navigációs menüben lévő keresése **Event Grid**, és válassza ki **Event Grid-témakörök**. 

    ![Válassza ki az Event Grid-témakörök](./media/custom-event-quickstart-portal/select-event-grid-topics.png)
3. Az a **Event Grid-témakörök** lapon jelölje be **+ Hozzáadás** az eszköztáron. 

    ![Event Grid-témakör gomb hozzáadása](./media/custom-event-quickstart-portal/add-event-grid-topic-button.png)
4. Az a **témakör létrehozása** lapon, kövesse az alábbi lépéseket:
    1. Adjon meg egy egyedi **neve** az egyéni témakör. A témakör nevének egyedinek kell lennie, mert a nevet egy DNS-bejegyzés képviseli. Ne a képen látható nevet használja. Ehelyett hozzon létre a saját nevét – kell 3 – 50 karakter hosszúságú lehet, és tartalmaznia csak a-z, A-Z, 0-9, értékek és a "-".
    2. Jelölje ki az Azure-**előfizetést**.
    3. Válasszon ki egy meglévő erőforráscsoportot, vagy válasszon **új létrehozása**, és adja meg egy **neve** a a **erőforráscsoport**.
    4. Válassza ki a **hely** az event grid-témakör.
    5. Tartsa meg az alapértelmezett érték **Event Grid séma** számára a **Eseménysémája** mező. 

       ![Témakör-lap létrehozása](./media/custom-event-quickstart-portal/create-custom-topic.png)
    6. Kattintson a **Létrehozás** gombra. 
5. Az egyéni témakör létrehozása után láthatja a sikeres műveletről szóló értesítést. Válassza ki **erőforráscsoport megnyitása**. 

   ![Sikeres műveletről szóló értesítés megtekintése](./media/custom-event-quickstart-portal/success-notification.png)
6. Az a **erőforráscsoport** lapon, válassza ki az event grid-témakör. 

   ![Az event grid témakör erőforrás kiválasztása](./media/custom-event-quickstart-portal/select-event-grid-topic.png)
7. Megjelenik a **Event Grid-témakör** az event grid lapját. Hagyja megnyitva ezt oldal. Az útmutató későbbi részében használni. 

    ![Event Grid-témakör kezdőlapja](./media/custom-event-quickstart-portal/event-grid-topic-home-page.png)

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása
Mielőtt az egyéni témakör-előfizetést hoz létre, hozzon létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Az útmutató egyszerűsítése érdekében egy olyan [előre létrehozott webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyezünk üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A cikk oldalon válassza ki a **üzembe helyezés az Azure** a megoldás üzembe helyezéséhez az előfizetéshez. Az Azure Portalon adjon meg értékeket a paraméterekhez.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.
1. A hely látható, de még nem lett közzétéve esemény.

   ![Új hely megtekintése](./media/custom-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>Feliratkozás egyéni témakörre

Az Event Grid-témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni, és hová szeretné küldeni az eseményeket.

1. Most, az a **Event Grid-témakör** az egyéni témakörre, válassza a lap **+ esemény-előfizetés** az eszköztáron.

   ![Esemény-előfizetés hozzáadása](./media/custom-event-quickstart-portal/new-event-subscription.png)
2. Az a **esemény-előfizetés létrehozása** lapon, kövesse az alábbi lépéseket:
    1. Adjon meg egy **neve** az esemény-előfizetés számára.
    3. Válassza ki **Webhook** számára a **típusú végpont**. 
    4. Válasszon **válasszon végpontot**. 

       ![Esemény-előfizetés értékeinek megadása](./media/custom-event-quickstart-portal/provide-subscription-values.png)
    5. A webhook végponthoz adja meg a webalkalmazás URL-címét, és adja hozzá az `api/updates` elemet a kezdőlap URL-címéhez. Válassza a **Kiválasztás megerősítése** lehetőséget.

       ![Végpont URL-címének megadása](./media/custom-event-quickstart-portal/provide-endpoint.png)
    6. Lépjen vissza a **esemény-előfizetés létrehozása** lapon jelölje be **létrehozás**.

3. Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

    ![Előfizetési esemény megtekintése](./media/custom-event-quickstart-portal/view-subscription-event.png)

## <a name="send-an-event-to-your-topic"></a>Esemény elküldése a témakörbe

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Az Azure CLI vagy a PowerShell használatával küldjön teszteseményt az egyéni témakörbe. Egy alkalmazás vagy Azure-szolgáltatás általában eseményadatokat küld el.

Az első példa az Azure CLI-t használja. Ez lekéri az egyéni témakör URL-címét és kulcsát, valamint a mintául szolgáló eseményadatokat. A `<topic name>` helyett használja az egyéni témakör nevét. Ez mintául szolgáló eseményadatokat hoz létre. A JSON `data` eleme az esemény hasznos adata. Bármilyen, megfelelően formált JSON megadható ebben a mezőben. A speciális útválasztáshoz és szűréshez használhatja a tárgy mezőt is. A CURL egy olyan segédprogram, amely HTTP-kéréseket küld.


### <a name="azure-cli"></a>Azure CLI
1. Az Azure Portalon válassza ki a **Cloud Shell**. Válassza ki **Bash** Cloud Shell-ablakról bal felső sarkában. 

    ![A cloud Shell - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. A következő parancsot a lekéréséhez futtassa a **végpont** a témakör: Másolja és illessze be a parancsot, után frissítse a **témakör neve** és **erőforráscsoport-név** a parancs futtatása előtt. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. A következő parancsot a lekéréséhez futtassa a **kulcs** az egyéni témakör: Másolja és illessze be a parancsot, miután a frissítés a **témakör neve** és **erőforráscsoport** nevezze el a parancs futtatása előtt. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Másolja a következő utasítást a esemény definíciója, majd nyomja le az **ENTER**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Futtassa a következő **Curl** parancsot az esemény közzététele:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
A második példa a PowerShell használatával végez hasonló lépéseket.

1. Az Azure Portalon válassza ki a **Cloud Shell**. Válassza ki **PowerShell** Cloud Shell-ablakról bal felső sarkában. Tekintse meg a mintát **Cloud Shell** ablak rendszerképet az Azure parancssori felület szakaszban. 
2. A következő parancsot a lekéréséhez futtassa a **végpont** a témakör: Másolja és illessze be a parancsot, után frissítse a **témakör neve** és **erőforráscsoport-név** a parancs futtatása előtt. 

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName <resource group name> -Name <topic name>).Endpoint
    ```
3. A következő parancsot a lekéréséhez futtassa a **kulcs** az egyéni témakör: Másolja és illessze be a parancsot, miután a frissítés a **témakör neve** és **erőforráscsoport** nevezze el a parancs futtatása előtt.

    ```powershell
    $keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name <topic name>
    ```
4. Készítse elő az eseményt. Másolja, és futtassa a utasításokat a Cloud Shell-ablakról. 

    ```azurepowershell
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
5. Használja a **Invoke-WebRequest** parancsmag az eseményt. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Ellenőrizze az eseménynaplóban rács
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

1. Válassza ki **erőforráscsoportok** a bal oldali menüben. Ha nem látja a bal oldali menüben, válassza ki a **minden szolgáltatás** a bal oldali menüben, és válassza a **erőforráscsoportok**. 
2. Válassza ki az erőforráscsoportot, elindíthatja a **erőforráscsoport** lapot. 
3. Válassza ki **erőforráscsoport törlése** az eszköztáron. 
4. Az erőforráscsoport nevének megadásával erősítse meg a törlést, és válassza ki **törlése**. 

    ![Erőforráscsoportok](./media/custom-event-quickstart-portal/delete-resource-groups.png)

    A másik erőforráscsoportban, az a képen látható volt által létrehozott és használt a Cloud Shell ablakába. Törölheti, ha nem tervezi a Cloud Shell-ablakról későbbi használat céljából. 

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre egyéni témaköröket és esemény-előfizetéseket, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Bevezetés az Event Grid használatába](overview.md)
- [Azure Blob Storage-események átirányítása egyéni webes végpontra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)
