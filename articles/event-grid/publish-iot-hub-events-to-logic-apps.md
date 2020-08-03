---
title: Oktatóanyag – IoT Hub-események használata az aktiváláshoz Azure Logic Apps
description: Ez az oktatóanyag bemutatja, hogyan használhatja a Azure Event Grid esemény-útválasztási szolgáltatását, és hogyan hozhat létre automatizált folyamatokat IoT Hub események alapján végzett Azure Logic Apps műveletek végrehajtásához.
services: iot-hub, event-grid
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 07/07/2020
ms.author: robinsh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 35359c63b79d9eea6f8f6ad688bd040428a39eb8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503446"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Oktatóanyag: Azure IoT Hub-eseményekkel kapcsolatos e-mail-értesítések küldése az Event Grid és a Logic Apps használatával

Az Azure Event Griddel reagálhat az IoT Hub eseményeire a folyamatok későbbi szakaszában használt üzleti alkalmazásokban kiváltott műveletekkel.

Ez a cikk egy IoT Hub és Event Grid használó minta-konfigurációt mutat be. A végén egy Azure Logic apps van beállítva, amely értesítő e-mailt küld minden alkalommal, amikor egy eszköz bekerül az IoT hubhoz. 

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [létrehozhat egy ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/)is.

* A Azure Logic Apps által támogatott e-mail-szolgáltatótól származó e-mail-fiók, például Office 365 Outlook, Outlook.com vagy gmail. Ez az e-mail-fiók küldi majd az eseményértesítéseket. A támogatott Logic apps-összekötők teljes listájáért tekintse meg az [Összekötők áttekintése](/connectors/)című témakört.

  > [!IMPORTANT]
  > A Gmail használata előtt győződjön meg arról, hogy van-e a G-Suite üzleti fiókja (egyéni tartománnyal rendelkező e-mail-cím) vagy egy Gmail fogyasztói fiók (e-mail-cím a következővel: @gmail.com vagy @googlemail.com ). Csak a G-Suite üzleti fiókok használhatják a Gmail-összekötőt más összekötők korlátozás nélkül a Logic Appsben. Ha rendelkezik Gmail-fiókkal, akkor a Gmail-összekötőt csak bizonyos Google által jóváhagyott szolgáltatásokkal használhatja, vagy [létrehozhat egy Google-ügyfélprogramot, amelyet a hitelesítéshez használhat](/connectors/gmail/#authentication-and-bring-your-own-application). További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Egy IoT Hub az Azure-ban. Ha még nem hozott létre központot, [az IoT Hub első lépéseit](../iot-hub/quickstart-send-telemetry-dotnet.md) ismertető cikkben talál útmutatást.

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Először hozzon létre egy logikai alkalmazást, és adjon hozzá egy Event Grid-eseményindítót, amely figyeli az erőforráscsoportot a virtuális gép számára. 

### <a name="create-a-logic-app-resource"></a>Logikai alkalmazás erőforrás létrehozása

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforrás létrehozása**lehetőséget, majd írja be a "Logic app" kifejezést a keresőmezőbe, és válassza a Return (visszatérés) lehetőséget. Válassza ki a **logikai alkalmazást** az eredmények közül.

   ![Logikai alkalmazás létrehozása](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. A következő képernyőn válassza a **Létrehozás**elemet. 

1. Adjon az előfizetésben egyedi nevet a logikai alkalmazásnak, majd válassza ki ugyanazt az előfizetést, erőforráscsoportot és helyet, ahová az IoT-központ tartozik. 

   ![A logikai alkalmazás létrehozására szolgáló mezők](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Kattintson a **Létrehozás** gombra.

1. Az erőforrás létrehozása után navigáljon a logikai alkalmazáshoz. Ehhez válassza az **erőforráscsoportok**lehetőséget, majd válassza ki az oktatóanyaghoz létrehozott erőforráscsoportot. Ezután keresse meg a logikai alkalmazást az erőforrások listájában, és válassza ki. 

1. A Logic Apps Designer lapon lefelé a **sablonok**megtekintéséhez. Válassza az **üres logikai alkalmazás** lehetőséget, hogy teljesen új logikai alkalmazást lehessen létrehozni.

### <a name="select-a-trigger"></a>Eseményindító kiválasztása

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

1. Válassza az **Új lépés** lehetőséget. Ekkor megnyílik egy ablak, amely **kiválasztja a műveletet**.

1. Keresse meg az **E-mail** elemet.

1. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ez az oktatóanyag az **Office 365 Outlookot**használja. Az egyéb e-mail-szolgáltatókra vonatkozó lépések is hasonlók. 

   ![E-mail-szolgáltató összekötőjének kiválasztása](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Válassza az **E-mail küldése** műveletet. 

1. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába. 

1. Készítse el az e-mail-sablont. 

   * **Címzett**: Adja meg az értesítési e-maileket fogadó e-mail-címet. Ehhez az oktatóanyaghoz olyan e-mail-fiókot használjon, amelyhez hozzáfér majd a tesztelés során. 

   * **Tárgy**: Írja be a tárgy szövegét. Ha a tárgy szövegmezőre kattint, kiválaszthatja a felvenni kívánt dinamikus tartalmat is. Ez az oktatóanyag például a következőt használja: `IoT Hub alert: {event Type}` . Ha nem látja a dinamikus tartalmat, válassza a **dinamikus tartalom hozzáadása** hiperhivatkozást. Ez a beállítás be-és kikapcsolható.

   * **Törzs**: Írja meg az e-mail szövegét. A választó eszközről választott JSON-tulajdonságokkal az esemény adataira alapuló dinamikus tartalmat illeszthet be. Ha nem látja a dinamikus tartalmat, válassza a **dinamikus tartalom hozzáadása** hiperhivatkozást a **szövegtörzs** szövegmezőben. Ha nem jeleníti meg a kívánt mezőket, a dinamikus tartalom képernyőn kattintson a *továbbiak* lehetőségre, hogy az előző műveletből származó mezőket is tartalmazza.

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

1. Az Azure Portalon keresse meg az IoT-központot. Ehhez válassza az **erőforráscsoportok**lehetőséget, majd jelölje ki az oktatóanyaghoz tartozó erőforráscsoportot, majd válassza ki az IoT hubot az erőforrások listájából.

2. Válassza az **események**lehetőséget.

   ![Az Event Grid-adatok megnyitása](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Válassza az **esemény-előfizetés**lehetőséget. 

   ![Új esemény-előfizetés létrehozása](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Hozza létre az esemény-előfizetést a következő értékekkel: 

    1. Az **esemény-előfizetés részletei** szakaszban hajtsa végre a következő feladatokat:
        1. Adja meg az esemény-előfizetés **nevét** . 
        2. Válassza ki **Event Grid sémát** az **eseményvezérelt sémához**. 
   2. A **témakör részletei** részben hajtsa végre a következő feladatokat:
       1. Ellenőrizze, hogy a **témakör típusa** **IoT hub**értékre van-e állítva. 
       2. Győződjön meg róla, hogy az IoT hub neve a **forrás erőforrás** mező értékeként van beállítva. 
       3. Adja meg annak a **rendszertémakörnek** a nevét, amelyet létre fog hozni Önnek. A rendszertémakörökről a [rendszertémakörök áttekintésében](system-topics.md)talál további információt.
   3. Az **Event types (eseménytípus** ) szakaszban hajtsa végre a következő feladatokat: 
        1. Az **eseménytípus szűréséhez**törölje az összes lehetőséget, kivéve az **eszköz létrejöttét**.

           ![előfizetési események típusai](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   4. A **VÉGPONT részletei** szakaszban hajtsa végre a következő feladatokat: 
       1. Válassza ki a **végpont típusát** **webhookként**.
       2. Kattintson a **válasszon ki egy végpontot**, illessze be a logikai alkalmazásból másolt URL-címet, és erősítse meg a kijelölést.

         ![végpont url-jének kiválasztása](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         Ha végzett, a panelnek az alábbi példához kell hasonlítania: 

        ![Esemény-előfizetési űrlapminta](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Ha most mentené az esemény-előfizetést, akkor az IoT-központban létrehozott minden eszközről értesítést kapna. Az oktatóanyag kedvéért most mégis végezzen szűrést bizonyos eszközökre a nem kötelező mezők használatával. A panel felső részén válassza a **Szűrők** elemet.

6. Válassza az **Új szűrő hozzáadása** lehetőséget. Töltse ki a mezőket az alábbi értékekkel:

   * **Kulcs**: `Subject`.

   * **Operátor**: `String begins with`.

   * **Érték**: adja meg az `devices/Building1_` eszköz eseményeinek szűrését az 1. épületben.
  
   Adjon hozzá még egy szűrőt az alábbi értékekkel:

   * **Kulcs**: `Subject`.

   * **Operátor**: `String ends with`.

   * **Érték**: Írja meg a `_Temperature` értéket a hőmérséklettel kapcsolatos eszközesemények szűréséhez.

   Az esemény-előfizetés **szűrők** lapjának ekkor a következő képhez hasonlóan kell kinéznie:

   ![Szűrők hozzáadása esemény-előfizetéshez](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Az eseményfeliratkozás mentéséhez válassza a **Létrehozás** lehetőséget.

## <a name="create-a-new-device"></a>Új eszköz létrehozása

A logikai alkalmazást új eszköz létrehozásával tesztelheti, amellyel egy eseményértesítő e-mailt aktivál. 

1. Válassza az IoT hub IoT- **eszközök**elemét. 

2. Válassza az **Új** lehetőséget.

3. Az **Eszközazonosító** mezőbe írja be a következőt: `Building1_Floor1_Room1_Light`.

4. Válassza a **Mentés** lehetőséget. 

5. Több eszközt is hozzáadhat különböző eszközazonosítókkal az esemény-előfizetési szűrők teszteléséhez. Kipróbálhatja az alábbiakat: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   Ha ezt a négy példát vette fel, az IoT-eszközök listájának az alábbi ábrán láthatóhoz hasonlónak kell lennie:

   ![IoT Hub-eszközlista](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Miután hozzáadott néhány eszközt az IoT-központhoz, az e-mail-fiókjában ellenőrizheti, hogy melyik eszközök aktiválták a logikai alkalmazást. 

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Az Azure Portal helyett az Azure CLI használatával is végrehajthatja az IoT Hub lépéseit. Részletekért lásd: Azure CLI-lapok az [esemény-előfizetések létrehozásához](/cli/azure/eventgrid/event-subscription) és [egy IoT-eszköz létrehozásához](/cli/azure/ext/azure-iot/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban olyan erőforrásokat használtunk, amelyek költségekkel terhelik az Azure-előfizetését. Ha befejezte az oktatóanyag kipróbálását és az eredmények tesztelését, tiltsa le vagy törölje azokat az erőforrásokat, amelyeket nem szeretne megőrizni. 

Az oktatóanyagban létrehozott összes erőforrás törléséhez törölje az erőforráscsoportot. 

1. Válassza ki az **erőforráscsoportok**elemet, majd válassza ki az oktatóanyaghoz létrehozott erőforráscsoportot.

2. Az erőforráscsoport ablaktáblán válassza az **erőforráscsoport törlése**elemet. A rendszer felszólítja az erőforráscsoport nevének megadására, majd törölheti azt. A benne foglalt összes erőforrást is eltávolítja.

Ha nem szeretné eltávolítani az összes erőforrást, egyenként is kezelheti őket. 

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
