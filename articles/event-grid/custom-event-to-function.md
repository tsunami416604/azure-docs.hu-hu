---
title: 'Rövid útmutató: Egyéni események küldése az Azure-függvénybe – Eseményrács'
description: 'Rövid útmutató: Az Azure Event Grid és az Azure CLI vagy a portál használatával közzé tehet egy témakört, és előfizethet az eseményre. Egy Azure-függvény a végpont hoz használt.'
services: event-grid
keywords: ''
author: banisadr
ms.author: babanisa
ms.date: 11/15/2019
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 5e38571cf84537fd722093b96cd277743e8ce80c
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80292155"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>Rövid útmutató: Egyéni események irányítsa az Azure-függvényt az Event Griddel

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Az Azure Functions a támogatott eseménykezelők egyike. Ebben a cikkben létrehozunk egy egyéni témakört az Azure Portallal, feliratkozunk az egyéni témakörre, majd kiváltjuk az eseményt az eredmény megtekintéséhez. Az eseményeket egy Azure-függvénybe küldi.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Azure-függvény létrehozása

Mielőtt előírna az egyéni témakörre, hozzon létre egy függvényt az események kezelésére. Az Azure Portalon kattintson a "Erőforrás létrehozása" elemre, és írja be a "függvény" parancsot, majd válassza a "Függvényalkalmazás" lehetőséget, és kattintson a létrehozás gombra. Válassza az "Új létrehozása" lehetőséget az erőforráscsoportban, és adjon neki nevet. Ezt fogja használni az oktatóanyag többi részéhez. Adjon nevet a függvényalkalmazásnak, hagyja a "Közzététel" kapcsolót a "Kód" mezőben, válassza ki a futásidejűt és a régiót, majd nyomja meg a create parancsot.

Miután a Függvényalkalmazás készen áll, keresse meg, és kattintson a "+ új funkció" gombra. Válassza ki a "Portálon" a fejlesztési környezetben, és nyomja meg a continue. A Függvény létrehozása csoportban válassza a "További sablonok" lehetőséget a további sablonok megtekintéséhez, majd keresse meg az "Azure Event Grid Trigger" kifejezést, és válassza ki. Ha ez az első alkalom, hogy ezt az eseményindítót használja, előfordulhat, hogy a bővítmény telepítéséhez a "Telepítés" gombra kell kattintania.

![Függvényesemény-rács eseményindítója](./media/custom-event-to-function/grid-trigger.png)

Miután telepítette a bővítményt, kattintson a Folytatás gombra, adjon nevet a függvénynek, majd nyomja meg a create (Létrehozás) gombot.

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az Event Grid-témakörök egy felhasználó által meghatározott végpontot biztosítanak, amelyben közzéteheti az eseményeket. 

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza a bal oldali navigációs menü **Minden szolgáltatás lehetőséget,** keresse meg az **Eseményrácsot**, és válassza az **Eseményrács témaköreit.** 

    ![Eseményrács-témakörök kiválasztása](./media/custom-event-to-function/select-event-grid-topics.png)
3. Az **Eseményrács témakörei** lapon válassza a **+ Hozzáadás lehetőséget** az eszköztáron. 

    ![Eseményrács témakörének hozzáadása gomb](./media/custom-event-to-function/add-event-grid-topic-button.png)

4. A **Témakör létrehozása** lapon hajtsa végre az alábbi lépéseket:

    1. Adjon meg egyedi **nevet** az egyéni témakörnek. A témakör nevének egyedinek kell lennie, mert a nevet egy DNS-bejegyzés képviseli. Ne a képen látható nevet használja. Ehelyett hozzon létre saját nevet - 3-50 karakter között kell lennie, és csak az a-z, Az-Z, 0-9 és "-" értékeket tartalmazza.
    2. Válassza ki **Azure-előfizetését.**
    3. Válassza ki ugyanazt az erőforráscsoportot az előző lépésekből.
    4. Válassza ki az eseményrács témakörének **helyét.**
    5. Tartsa meg az **Eseményséma** mező alapértelmezett **eseményrács-sémáját.** 

       ![Témakör létrehozása lap](./media/custom-event-to-function/create-custom-topic.png)
    6. Kattintson a **Létrehozás** gombra. 

5. Az egyéni témakör létrehozása után láthatja a sikeres műveletről szóló értesítést. Válassza **az Ugrás erőforráscsoportra**lehetőséget. 

   ![Sikeres műveletről szóló értesítés megtekintése](./media/custom-event-to-function/success-notification.png)

6. Az **Erőforráscsoport** lapon jelölje ki az eseményrács témakörét. 

   ![Az eseményrács témakörerőforrásának kijelölése](./media/custom-event-to-function/select-event-grid-topic.png)

7. Az **eseményrács Eseményrács témaköre** lapot látja. Tartsa nyitva ezt a lapot. Később használja a rövid útmutatóban. 

    ![Az Eseményrács témakörének kezdőlapja](./media/custom-event-to-function/event-grid-topic-home-page.png)

## <a name="subscribe-to-custom-topic"></a>Feliratkozás egyéni témakörre

Az Event Grid-témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni, és hová szeretné küldeni az eseményeket.

1. Most az egyéni témakör **Eseményrács témaköre** lapon válassza a **+ Esemény-előfizetés lehetőséget** az eszköztáron.

   ![Esemény-előfizetés hozzáadása](./media/custom-event-to-function/new-event-subscription.png)

2. Az **Esemény-előfizetés létrehozása** lapon hajtsa végre az alábbi lépéseket:
    1. Adja meg az esemény-előfizetés **nevét.**
    3. Válassza ki az **Azure Függvényt** a **végpont típushoz.** 
    4. Válassza **a Végpont kiválasztása**lehetőséget. 

       ![Esemény-előfizetés értékeinek megadása](./media/custom-event-to-function/provide-subscription-values.png)

    5. A függvényvégponthoz válassza ki az Azure-előfizetést és erőforráscsoportot, amelyben a függvényalkalmazás található, majd válassza ki a korábban létrehozott függvényalkalmazást és függvényt. Válassza a **Kiválasztás megerősítése** lehetőséget.

       ![Végpont URL-címének megadása](./media/custom-event-to-function/provide-endpoint.png)

    6. Az **Esemény-előfizetés létrehozása** lapon kattintson a **Létrehozás gombra.**

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
Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Keresse meg az Event Grid aktivált funkciót, és nyissa meg a naplókat. Meg kell jelennie az esemény adathasznos adatának másolatát a naplókban. Ha nem győződjön meg arról, hogy először megnyitja a naplók ablakot, vagy nyomja meg újra a kapcsolatot, majd próbáljon meg újra teszteseményt küldeni.

![Sikeres függvényeseményindító naplója](./media/custom-event-to-function/successful-function.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha tovább kívánja használni az eseményt, akkor ne törölje a cikkben létrehozott erőforrásokat. Ellenkező esetben törölje a cikkben létrehozott erőforrásokat.

1. Válassza a bal oldali menü **Erőforráscsoportok parancsát.** Ha nem látható a bal oldali menüben, válassza a bal oldali menü **Minden szolgáltatás parancsát,** és válassza az **Erőforráscsoportok parancsot.** 
2. Válassza ki az erőforráscsoportot az **Erőforráscsoport** lap elindításához. 
3. Az eszköztáron válassza az **Erőforráscsoport törlése** lehetőséget. 
4. A törlés megerősítésének megerősítése az erőforráscsoport nevének megadásával, majd a **Törlés gombra.** 

    ![Erőforráscsoportok](./media/custom-event-to-function/delete-resource-groups.png)

    A képen látható másik erőforráscsoportot a Cloud Shell ablak hozta létre és használta. Törölje, ha később nem kívánja használni a Cloud Shell ablakot. 

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett vele, hogyan hozhat létre témaköröket és eseményfeliratkozásokat, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Bevezetés az Event Grid használatába](overview.md)
- [Azure Blob Storage-események átirányítása egyéni webes végpontra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)
