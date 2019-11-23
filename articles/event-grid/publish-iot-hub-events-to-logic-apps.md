---
title: Tutorial - Use IoT Hub events to trigger Azure Logic Apps
description: Tutorial - Using the event routing service of Azure Event Grid, create automated processes to perform Azure Logic Apps actions based on IoT Hub events.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 70ad74715446a54605a23a049ebc92a81d7ee673
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423738"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Tutorial: Send email notifications about Azure IoT Hub events using Event Grid and Logic Apps

Az Azure Event Griddel reagálhat az IoT Hub eseményeire a folyamatok későbbi szakaszában használt üzleti alkalmazásokban kiváltott műveletekkel.

This article walks through a sample configuration that uses IoT Hub and Event Grid. At the end, you have an Azure logic app set up to send a notification email every time a device is added to your IoT hub. 

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Logic Apps által támogatott bármely e-mail-szolgáltató (például Office 365 Outlook, Outlook.com vagy Gmail) által üzemeltetett e-mail-fiók. Ez az e-mail-fiók küldi majd az eseményértesítéseket. A támogatott Logic Apps-összekötők teljes listáját az [összekötők áttekintésében](https://docs.microsoft.com/connectors/) találja.
* Aktív Azure-fiók. Ha még nincs fiókja, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Egy IoT Hub az Azure-ban. Ha még nem hozott létre központot, [az IoT Hub első lépéseit](../iot-hub/iot-hub-csharp-csharp-getstarted.md) ismertető cikkben talál útmutatást. 

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

First, create a logic app and add an event grid trigger that monitors the resource group for your virtual machine. 

### <a name="create-a-logic-app-resource"></a>Logikai alkalmazás erőforrás létrehozása

1. In the [Azure portal](https://portal.azure.com), select **Create a resource**, then type "logic app" in the search box and select return. Select **Logic App** from the results.

   ![Logikai alkalmazás létrehozása](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. On the next screen, select **Create**. 

1. Adjon az előfizetésben egyedi nevet a logikai alkalmazásnak, majd válassza ki ugyanazt az előfizetést, erőforráscsoportot és helyet, ahová az IoT-központ tartozik. 

   ![Fields for create logic app](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Kattintson a **Létrehozás** gombra.

1. Once the resource is created, navigate to your logic app. To do this, select **Resource groups**, then select the resource group you created for this tutorial. Then find the logic app in the list of resources and select it. 

1. In the Logic Apps Designer, page down to see **Templates**. Choose **Blank Logic App** so that you can build your logic app from scratch.

### <a name="select-a-trigger"></a>Trigger kiválasztása

A trigger a logikai alkalmazást elindító konkrét esemény. Ebben az oktatóanyagban a munkafolyamatot aktiváló trigger HTTP-kapcsolaton keresztül fogad egy kérést.  

1. Írja be a **HTTP** kifejezést az összekötők és triggerek keresősávjába.

1. Válassza ki a **Kérés – HTTP-kérés fogadásakor** lehetőséget triggerként. 

   ![HTTP-kérés trigger kiválasztása](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget. 

   ![HTTP-kérés trigger kiválasztása](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Másolja az alábbi JSON-kódmintát a szövegmezőbe, majd kattintson a **Kész** gombra:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

1. Egy előugró értesítés jelenhet meg a következő üzenettel: **Ne felejtsen el egy alkalmazás/JSON értékű Content-Type fejlécet beszúrni a kérésbe**. A javaslatot nyugodtan figyelmen kívül hagyhatja, és továbbléphet a következő szakaszra. 

### <a name="create-an-action"></a>Művelet létrehozása

A műveletek azok a lépések, azután mennek végbe, hogy a trigger elindítja a logikai alkalmazás munkafolyamatát. Ebben az oktatóanyagban a művelet egy e-mail-értesítés küldése az e-mail-szolgáltatóról. 

1. Válassza az **Új lépés** lehetőséget. This opens a window to **Choose an action**.

1. Keresse meg az **E-mail** elemet.

1. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ebben az oktatóanyagban az **Office 365 Outlookot használjuk**. Az egyéb e-mail-szolgáltatókra vonatkozó lépések is hasonlók. 

   ![E-mail-szolgáltató összekötőjének kiválasztása](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Válassza az **E-mail küldése** műveletet. 

1. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába. 

1. Hozza létre az e-mail-sablont. 

   * **Címzett**: Adja meg az e-mail-címet, amely az értesítő e-maileket fogadja. Ehhez az oktatóanyaghoz használjon egy olyan e-mail-fiókot, amelyet a tesztelés során el tud majd érni. 

   * **Subject**: Fill in the text for the subject. When you click on the Subject text box, you can select dynamic content to include. For example, this tutorial uses `IoT Hub alert: {event Type}`. If you can't see Dynamic content, select the **Add dynamic content** hyperlink -- this toggles it on and off.

   * **Body**: Write the text for your email. A választóeszközben a megfelelő JSON-tulajdonságok kiválasztásával az eseményadatokon alapuló dinamikus tartalmakat szúrhat be. If you can't see the Dynamic content, select the **Add dynamic content** hyperlink under the **Body** text box. If it doesn't show you the fields you want, click *more* in the Dynamic content screen to include the fields from the previous action.

   Az e-mail-sablon a következő példához hasonló lehet:

   ![E-mail-adatok kitöltése](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Mentse a logikai alkalmazást. 

### <a name="copy-the-http-url"></a>A HTTP URL-cím másolása

Mielőtt kilép a Logic Apps Designerből, másolja ki az URL-címet, amelyen a logikai alkalmazás figyeli a triggereket. Erre az URL-címre az Event Grid konfigurálásához lesz szükség. 

1. Kattintással bontsa ki a **HTTP-kérés fogadásakor** triggerkonfigurációs mezőt. 

1. Másolja ki a **HTTP POST URL** értékét a mellette lévő másolás gombra kattintva. 

   ![A HTTP POST URL másolása](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Mentse ezt az URL-címet, hogy a következő szakaszban hivatkozhasson majd rá. 

## <a name="configure-subscription-for-iot-hub-events"></a>Az IoT Hub eseményeire való előfizetés beállítása

Ebben a szakaszban konfiguráljuk az IoT-központot, hogy közzétegye a bekövetkező eseményeket. 

1. Az Azure Portalon keresse meg az IoT-központot. You can do this by selecting **Resource groups**, then select the resource group for this tutorial, and then select your IoT hub from the list of resources.

2. Válassza az **Események** lehetőséget.

   ![Az Event Grid-adatok megnyitása](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Válassza az **Esemény-előfizetés** lehetőséget. 

   ![Új esemény-előfizetés létrehozása](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Hozza létre az esemény-előfizetést a következő értékekkel: 

   * **Event Subscription Details**: Provide a descriptive name and select **Event Grid Schema**.

   * **Event Types**: In the **Filter to Event Types**, uncheck all of the choices except **Device Created**.

       ![subscription event types](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Endpoint Details**: Select Endpoint Type as **Web Hook** and select *select an endpoint* and paste the URL that you copied from your logic app and confirm selection.

     ![végpont url-jének kiválasztása](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   When you're done, the pane should look like the following example: 

    ![Esemény-előfizetési űrlapminta](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Ha most mentené az esemény-előfizetést, akkor az IoT-központban létrehozott minden eszközről értesítést kapna. For this tutorial, though, let's use the optional fields to filter for specific devices. Select **Filters** at the top of the pane.

6. Select **Add new filter**. Fill in the fields with these values:

   * **Key**: Select `Subject`.

   * **Operator**: Select `String begins with`.

   * **Value**:  Enter `devices/Building1_` to filter for device events in building 1.
  
   Add another filter with these values:

   * **Key**: Select `Subject`.

   * **Operator**: Select `String ends with`.

   * **Value**: Enter `_Temperature` to filter for device events related to temperature.

   The **Filters** tab of your event subscription should now look similar to this image:

   ![Adding filters to event subscription](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Kattintson a **Létrehozás** gombra az esemény-előfizetés mentéséhez.

## <a name="create-a-new-device"></a>Új eszköz létrehozása

A logikai alkalmazás teszteléséhez hozzunk létre egy új eszközt, amely kivált egy eseményértesítő e-mailt. 

1. Az IoT-központban válassza az **IoT-eszközök** lehetőséget. 

2. Válassza az **Új** lehetőséget.

3. Az **Eszközazonosító** mezőben adja meg a `Building1_Floor1_Room1_Light` azonosítót.

4. Kattintson a **Mentés** gombra. 

5. Több eszközt is hozzáadhat különböző eszközazonosítókkal az esemény-előfizetési szűrők teszteléséhez. Próbálja ki a következő példákat: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   If you added the four examples, your list of IoT devices should look like the following image:

   ![IoT Hub device list](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Miután hozzáadott néhány eszközt az IoT-központhoz, az e-mail-fiókjában ellenőrizheti, hogy melyik eszközök aktiválták a logikai alkalmazást. 

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Az Azure Portal helyett az Azure CLI használatával is végrehajthatja az IoT Hub lépéseit. For details, see the Azure CLI pages for [creating an event subscription](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) and [creating an IoT device](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban olyan erőforrásokat használtunk, amelyek költségekkel terhelik az Azure-előfizetését. When you're finished trying out the tutorial and testing your results, disable or delete resources that you don't want to keep. 

To delete all of the resources created in this tutorial, delete the resource group. 

1. Select **Resource groups**, then select the resource group you created for this tutorial.

2. On the Resource group pane, select **Delete resource group**. You are prompted to enter the resource group name, and then you can delete it. All of the resources contained therein are also removed.

If you don't want to remove all of the resources, you can manage them one by one. 

Ha nem szeretné elveszteni a logikai alkalmazásba fektetett munkáját, a törlés helyett csak tiltsa le. 

1. Keresse meg a logikai alkalmazást.

2. Az **Áttekintés** panelen válassza a **Törlés** vagy a **Letiltás** elemet. 

Mindegyik előfizetés egy ingyenes IoT-központtal rendelkezhet. Ha ingyenes központot hozott létre az oktatóanyaghoz, azt nem kell törölnie a költségek megelőzéséhez.

1. Keresse meg az IoT-központot. 

2. Az **Áttekintés** panelen válassza a **Törlés** elemet. 

Az esemény-előfizetést akkor is érdemes lehet törölnie, ha megtartja az IoT-központot. 

1. Az IoT-központban válassza az **Event Grid** lehetőséget.

2. Válassza ki a törölni kívánt esemény-előfizetést. 

3. Válassza a **Törlés** elemet. 

## <a name="next-steps"></a>Következő lépések

* További információ [az IoT Hub-eseményekre való válaszadást az Event Griddel kiváltott műveletek használatával](../iot-hub/iot-hub-event-grid.md).
* [Ismerje meg, hogyan rendezheti az eszközhöz csatlakoztatott és nem csatlakoztatott eseményeket](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Ismerje meg részletesebben az [Event Grid](overview.md) által kínált lehetőségeket.