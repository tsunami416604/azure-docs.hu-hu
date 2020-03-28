---
title: Oktatóanyag – Az IoT Hub-események használatával indítsa el az Azure Logic Apps
description: Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Event Grid esemény-útválasztási szolgáltatását, és hogyan hozhat létre automatizált folyamatokat az IoT Hub-eseményeken alapuló Azure Logic Apps-műveletek végrehajtásához.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 334b7b2c59b328e8eff3c7c2b9c3ed46bffc3442
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706432"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Oktatóanyag: E-mailes értesítések küldése az Azure IoT Hub eseményeiről az Event Grid és a Logic Apps használatával

Az Azure Event Griddel reagálhat az IoT Hub eseményeire a folyamatok későbbi szakaszában használt üzleti alkalmazásokban kiváltott műveletekkel.

Ez a cikk az IoT Hub ot és az Event Gridet használó mintakonfigurációt ismerteti. A végén egy Azure-logikai alkalmazás beállítása egy értesítő e-mailt minden alkalommal, amikor egy eszköz hozzá adódik az IoT hub. 

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Logic Apps által támogatott bármely e-mail-szolgáltató (például Office 365 Outlook, Outlook.com vagy Gmail) által üzemeltetett e-mail-fiók. Ez az e-mail-fiók küldi majd az eseményértesítéseket. A támogatott Logic Apps-összekötők teljes listáját az [összekötők áttekintésében](https://docs.microsoft.com/connectors/) találja.
* Aktív Azure-fiók. Ha még nem rendelkezik ilyen, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Egy IoT Hub az Azure-ban. Ha még nem hozott létre központot, [az IoT Hub első lépéseit](../iot-hub/iot-hub-csharp-csharp-getstarted.md) ismertető cikkben talál útmutatást. 

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Először hozzon létre egy logikai alkalmazást, és adjon hozzá egy eseményrács-eseményindítót, amely figyeli a virtuális gép erőforráscsoportját. 

### <a name="create-a-logic-app-resource"></a>Logikai alkalmazás erőforrás létrehozása

1. Az [Azure Portalon](https://portal.azure.com)válassza az **Erőforrás létrehozása**lehetőséget, majd írja be a "logikai alkalmazás" kifejezést a keresőmezőbe, és válassza a return lehetőséget. Válassza ki **a logikai alkalmazás** az eredmények közül.

   ![Logikai alkalmazás létrehozása](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. A következő képernyőn válassza a **Létrehozás gombot.** 

1. Adjon az előfizetésben egyedi nevet a logikai alkalmazásnak, majd válassza ki ugyanazt az előfizetést, erőforráscsoportot és helyet, ahová az IoT-központ tartozik. 

   ![Logikai alkalmazás létrehozásához használt mezők](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Kattintson a **Létrehozás** gombra.

1. Az erőforrás létrehozása után keresse meg a logikai alkalmazást. Ehhez válassza **az Erőforráscsoportok**lehetőséget, majd válassza ki az oktatóanyaghoz létrehozott erőforráscsoportot. Ezután keresse meg a logikai alkalmazást az erőforrások listájában, és jelölje ki. 

1. A Logic Apps Designer lapban lapokkal lefelé a Sablonok megtekintéséhez kattintson **a lapra.** Válassza **az Üres logikai alkalmazást,** hogy a logikai alkalmazást teljesen újonnan hozhassa létre.

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

1. Válassza az **Új lépés** lehetőséget. Ekkor megnyílik egy ablak **a Művelet kiválasztásához.**

1. Keresse meg az **E-mail** elemet.

1. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ez az oktatóanyag az **Office 365 Outlook**programot használja. Az egyéb e-mail-szolgáltatókra vonatkozó lépések is hasonlók. 

   ![E-mail-szolgáltató összekötőjének kiválasztása](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Válassza az **E-mail küldése** műveletet. 

1. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába. 

1. Hozza létre az e-mail-sablont. 

   * **Címzett**: Adja meg az e-mail-címet, amely az értesítő e-maileket fogadja. Ehhez az oktatóanyaghoz használjon egy olyan e-mail-fiókot, amelyet a tesztelés során el tud majd érni. 

   * **Tárgy**: Töltse ki a tárgy szövegét. Ha a Tárgy mezőre kattint, kiválaszthatja a dinamikus tartalmat. Az oktatóanyag például a . `IoT Hub alert: {event Type}` Ha nem látható a Dinamikus tartalom, jelölje ki a **Dinamikus tartalom hozzáadása** hivatkozást – ez be- és kikapcsolja azt.

   * **Törzs**: Írja be az e-mail szövegét. A választóeszközben a megfelelő JSON-tulajdonságok kiválasztásával az eseményadatokon alapuló dinamikus tartalmakat szúrhat be. Ha nem látható a dinamikus tartalom, jelölje be a **Dinamikus tartalom hozzáadása** hivatkozást a **Törzs** szövegmező alatt. Ha nem jeleníti meg a kívánt mezőket, kattintson a Dinamikus tartalom *képernyőre,* ha az előző művelet mezőit is beszeretné vonni.

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

1. Az Azure Portalon keresse meg az IoT-központot. Ehhez válassza az **Erőforráscsoportok**lehetőséget, majd válassza ki az oktatóanyag erőforráscsoportját, majd válassza ki az IoT-központot az erőforrások listájából.

2. Válassza **az Események**lehetőséget.

   ![Az Event Grid-adatok megnyitása](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Válassza **az Esemény-előfizetés lehetőséget.** 

   ![Új esemény-előfizetés létrehozása](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Hozza létre az esemény-előfizetést a következő értékekkel: 

   * **Esemény-előfizetés részletei**: Adjon meg egy leíró nevet, és válassza **az Event Grid Séma**lehetőséget.

   * **Eseménytípusok**: A **Szűrő eseménytípusokra**mezőben törölje a jelet az összes választási lehetőség ről a **Létrehozott eszköz**kivételével.

       ![előfizetési eseménytípusok](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Végpont részletei:** Válassza a Végpont típusa **webhook-ként** lehetőséget, és *jelöljön ki egy végpontot,* és illessze be a logikai alkalmazásból másolt URL-címet, és erősítse meg a kijelölést.

     ![végpont url-jének kiválasztása](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   Ha elkészült, az ablaktáblának a következő példához kell hasonlítania: 

    ![Esemény-előfizetési űrlapminta](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Ha most mentené az esemény-előfizetést, akkor az IoT-központban létrehozott minden eszközről értesítést kapna. Ebben az oktatóanyagban azonban használjuk a választható mezőket az adott eszközök szűréséhez. Válassza a **Szűrők** lehetőséget az ablaktábla tetején.

6. Válassza **az Új szűrő hozzáadása**lehetőséget. Töltse ki a mezőket a következő értékekkel:

   * **Kulcs**: `Subject`Válassza a lehetőséget.

   * **Operátor**: `String begins with`Válassza a lehetőséget.

   * **Érték**: `devices/Building1_` Az 1-es épületben lévő eszközesemények szűréséhez írja be a szűrőt.
  
   Adjon hozzá egy másik szűrőt a következő értékekkel:

   * **Kulcs**: `Subject`Válassza a lehetőséget.

   * **Operátor**: `String ends with`Válassza a lehetőséget.

   * **Érték**: `_Temperature` Adja meg a hőmérséklethez kapcsolódó eszközesemények szűrését.

   Az esemény-előfizetés **Szűrők** lapjának most ugyanúgy kell kinéznie ehhez a képhez:

   ![Szűrők hozzáadása az esemény-előfizetéshez](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Kattintson a **Létrehozás** gombra az esemény-előfizetés mentéséhez.

## <a name="create-a-new-device"></a>Új eszköz létrehozása

A logikai alkalmazás teszteléséhez hozzunk létre egy új eszközt, amely kivált egy eseményértesítő e-mailt. 

1. Az IoT-központban válassza az **IoT-eszközök** lehetőséget. 

2. Válassza az **Új**lehetőséget.

3. Az **Eszközazonosító** mezőben adja meg a `Building1_Floor1_Room1_Light` azonosítót.

4. Kattintson a **Mentés** gombra. 

5. Több eszközt is hozzáadhat különböző eszközazonosítókkal az esemény-előfizetési szűrők teszteléséhez. Próbálja ki a következő példákat: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   Ha a négy példát adta hozzá, az IoT-eszközök listájának az alábbi képen kell kinéznie:

   ![IoT Hub-eszközök listája](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Miután hozzáadott néhány eszközt az IoT-központhoz, az e-mail-fiókjában ellenőrizheti, hogy melyik eszközök aktiválták a logikai alkalmazást. 

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Az Azure Portal helyett az Azure CLI használatával is végrehajthatja az IoT Hub lépéseit. További részletek az Azure CLI-lapok on [hozzon létre egy esemény-előfizetés](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) t és [egy IoT-eszköz létrehozásáról.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban olyan erőforrásokat használtunk, amelyek költségekkel terhelik az Azure-előfizetését. Ha befejezte az oktatóanyag kipróbálását és az eredmények tesztelését, tiltsa le vagy törölje azokat az erőforrásokat, amelyeket nem szeretne megtartani. 

Az oktatóanyagban létrehozott összes erőforrás törléséhez törölje az erőforráscsoportot. 

1. Válassza **az Erőforráscsoportok**lehetőséget, majd jelölje ki az oktatóanyaghoz létrehozott erőforráscsoportot.

2. Az Erőforráscsoport ablaktáblán válassza az **Erőforráscsoport törlése**lehetőséget. A program kéri, hogy adja meg az erőforráscsoport nevét, majd törölheti azt. Az abban foglalt összes erőforrást szintén eltávolítják.

Ha nem szeretné eltávolítani az összes erőforrást, egyenként kezelheti őket. 

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

## <a name="next-steps"></a>További lépések

* További információ [az IoT Hub-eseményekre való válaszadást az Event Griddel kiváltott műveletek használatával](../iot-hub/iot-hub-event-grid.md).
* [Ismerje meg, hogyan rendezheti az eszközhöz csatlakoztatott és nem csatlakoztatott eseményeket](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Ismerje meg részletesebben az [Event Grid](overview.md) által kínált lehetőségeket.