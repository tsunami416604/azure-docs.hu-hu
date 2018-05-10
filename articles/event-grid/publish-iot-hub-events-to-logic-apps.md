---
title: Az IoT-központ események elindítani az Azure Logic Apps |} Microsoft Docs
description: Az esemény Azure esemény rács útválasztási szolgáltatás használatával hozhat létre automatikus folyamatot az IoT-központ események alapján Azure Logic Apps-műveletek elvégzéséhez.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 4fed42a45f8d291bd3ba1e4fd5d636b7d0b0fbfc
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>E-mail értesítést kapjanak a Logic Apps segítségével Azure IoT Hub-események

Az Azure Event rács lehetővé teszi reagálni események az IoT-központ az alsóbb rétegbeli üzleti alkalmazások műveleteket váltanak ki.

Ez a cikk végigvezeti az IoT-központ és az esemény rács használó minta konfiguráció. Végén hogy egy e-mailben értesítést küldeni, minden alkalommal, amikor egy eszköz hozzáadódik az IoT hub beállítva Azure Logic Apps alkalmazást. 

## <a name="prerequisites"></a>Előfeltételek

* Az e-mail-fiók bármely Azure Logic Apps, például az Office 365 Outlook-, Outlook.com-os, illetve Gmail által támogatott e-mail szolgáltató. Az e-mail fiókot az esemény értesítések küldéséhez használatos. Támogatott logikai alkalmazás-összekötők teljes listáját lásd: a [összekötők áttekintése](https://docs.microsoft.com/connectors/)
* Aktív Azure-fiók. Ha még nincs fiókja, akkor [ingyenes fiók létrehozását](http://azure.microsoft.com/pricing/free-trial/).
* Az Iot-központ az Azure-ban. Ha még nem hozott létre egy még, lásd: [Ismerkedés az IoT-központ](../iot-hub/iot-hub-csharp-csharp-getstarted.md) útmutatást. 

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Először logikai alkalmazás létrehozása és hozzáadása, amely figyeli a virtuális géphez tartozó erőforráscsoport rács eseményindító. 

### <a name="create-a-logic-app-resource"></a>Hozzon létre egy logic app erőforrást


1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **új** > **vállalati integrációs** > **logikai alkalmazás**.

   ![Logikai alkalmazás létrehozása](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Nevezze el a logikai alkalmazás, amely egyedi az előfizetéshez, majd jelölje ki az ugyanazon előfizetés, erőforráscsoportot és helyet az IoT hub. 
3. Ha elkészült, válassza ki a **rögzítés az irányítópulton**, és válassza a **létrehozása**.

   Ezzel létrehozott egy Azure-erőforrást a logikai alkalmazás számára. Miután az Azure üzembe helyezte a logikai alkalmazást, a Logic Apps Designer gyakran használt minták sablonjait jeleníti meg, hogy megkönnyítse az első lépéseket.

   > [!NOTE] 
   > Ha bejelöli **rögzítés az irányítópulton**, a logikai alkalmazás automatikusan megnyílik a Logic Apps-tervezőben. Ellenkező esetben manuálisan is található, majd nyissa meg a Logic Apps alkalmazást.

4. A Logic App Designer alatt **sablonok**, válassza a **üres logikai alkalmazás** , hogy a logikai alkalmazás teljesen új hozhat létre.

## <a name="select-a-trigger"></a>Jelöljön ki egy eseményindítót

Egy eseményindító az adott esemény, amely elindítja a Logic Apps alkalmazást. Ebben az oktatóanyagban az eseményindító, amely beállítja a munkafolyamat ki van kérelem fogadása HTTP Protokollon keresztül.  

1. Írja be a összekötők és eseményindítók keresősáv **HTTP**.
2. Válassza ki **kérelem - amikor egy HTTP-kérelem érkezik** , az eseményindító. 

   ![Válassza ki a kérelem HTTP-eseményindítóval](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. Válassza ki **séma létrehozásához használja a minta hasznos**. 

   ![Válassza ki a kérelem HTTP-eseményindítóval](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Illessze be a következő példa a JSON-kódot a szövegmezőbe, majd válasszon **végzett**:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<IoT hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
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
       "deviceId": "LogicAppTestDevice",
       "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
       "opType": "DeviceCreated"
     },
     "dataVersion": "",
     "metadataVersion": "1"
   }]
   ```
5. Jelenhet meg, amely szerint, előugró értesítést **ne felejtse el bevenni a kérés az application/json beállítása Content-Type fejléc.** Nyugodtan figyelmen kívül hagyása Ez a javaslat, és lépjen tovább a következő szakaszban. 


### <a name="create-an-action"></a>Művelet létrehozása

A műveletek olyan lépéseket a indítsák el a logic app munkafolyamat után kerül sor. Ebben az oktatóanyagban a művelet, hogy e-mailben értesítést küldeni az e-mail-szolgáltatótól. 

1. Válassza ki **új lépés** majd **művelet hozzáadása**. 

   ![Új lépésre, a művelet hozzáadása](./media/publish-iot-hub-events-to-logic-apps/new-step.png)

2. Keresse meg **E-mail**. 
3. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ez az oktatóanyag használja **Office 365 Outlook**. A más e-mail szolgáltatókat lépései hasonlóak. 

   ![Válassza ki az e-mailt provider összekötő](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. Válassza ki a **egy e-mailek küldése** művelet. 
5. Ha a rendszer kéri, jelentkezzen be az e-mail fiókjába. 
6. Az e-mail sablon létrehozása. 
   * **A**: Adja meg az e-mail cím az értesítési e-mailek fogadásához. Ebben az oktatóanyagban egy e-mail fiókot használjon, amely van-e hozzáférési tesztelési. 
   * **Tulajdonos** és **törzs**: írja be a szöveget az e-mailek. Válassza ki a JSON-tulajdonságok a választó eszköz, a dinamikus tartalom esemény adatok alapján.  

   Ebben a példában az e-mail sablon nézhet ki:

   ![E-mailek adatok](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Mentse a logikai alkalmazást. 

### <a name="copy-the-http-url"></a>A HTTP URL-Címének másolása

Mielőtt kilép a Logic Apps Designer, másolja az URL-cím, mely a logic Apps alkalmazásait eseményindító. Az URL-cím segítségével esemény rács konfigurálásához. 

1. Bontsa ki a **érkezik, amikor egy HTTP-kérelem** eseményindító konfiguráció mezőben kattintással. 
2. Másolja a értékének **HTTP POST URL-cím** ; ehhez válassza a Másolás gombra látható. 

   ![A HTTP POST URL-Címének másolása](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Mentse az URL-cím, hogy a következő szakaszban is hivatkozik rá. 

## <a name="publish-an-event-from-iot-hub"></a>Az IoT-központ esemény közzététele

Ebben a szakaszban konfigurálhatja az IoT Hub közzé eseményeket azok bekövetkezésekor. 

1. Az Azure-portálon lépjen az IoT hub. 
2. Válassza ki **események**.

   ![Nyissa meg a rács esemény részletei](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Válassza ki **Eseményelőfizetés**. 

   ![Új esemény-előfizetés létrehozása](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Az esemény-előfizetés létrehozása a következő értékekkel: 
   * **Név**: Adjon meg egy leíró nevet.
   * **Minden eseménytípushoz előfizetni**: törölje a jelölőnégyzet jelölését.
   * **Eseménytípusok**: válasszon **DeviceCreated**.
   * **Előfizető típus**: válasszon **webes Hook**.
   * **Előfizető végpont**: az a logikai alkalmazás fájlból másolt URL-címet. 

   Mentse a Eseményelőfizetés, és az összes eszközt, az IoT hub létrehozott értesítéseket. Ebben az oktatóanyagban azonban most a választható mezőket szűréséhez használja az adott eszközök: 

   * **Előtag-szűrő**: Adjon meg `devices/Building1_` . épület 1 eszköz események szűréséhez.
   * **Utótag szűrő**: Adjon meg `_Temperature` hőmérséklet kapcsolódó események szűréséhez.

   Amikor elkészült, a képernyő az alábbihoz hasonlóan kell kinéznie: 

   ![A minta esemény előfizetés képernyő](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Válassza ki **létrehozása** menteni az esemény-előfizetést.

## <a name="create-a-new-device"></a>Új eszköz létrehozása

Tesztelje a Logic Apps alkalmazást hozzon létre egy új eszközt kiváltó esemény értesítési e-mailt. 

1. Válassza ki az IoT hub **az IoT-eszközök**. 
2. Válassza a **Hozzáadás** lehetőséget.
3. A **Eszközazonosító**, adja meg `Building1_Floor1_Room1_Temperature`.
4. Kattintson a **Mentés** gombra. 
5. Különböző eszközazonosítók tesztelése az esemény előfizetés-szűrők használatával több eszközt is hozzáadhat. Próbálja meg ezek a példák: 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

Miután hozzáadott néhány olyan eszközt az IoT hub, ellenőrizze az e-maileket, hogy melyeket elindul a logikai alkalmazást. 

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Az Azure portál használata helyett az IoT-központ lépéseket az Azure parancssori felület használatával érhető el. További információkért lásd: az Azure CLI oldalak [esemény-előfizetés létrehozása](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) és [egy IoT-eszköz létrehozása](https://docs.microsoft.com/cli/azure/iot/device)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban használt erőforrásokat, amelyek az Azure-előfizetéshez a költségek. Amikor elkészült, az oktatóanyag kipróbálásánál és tesztelését az eredményeket, tiltsa le, vagy törli az erőforrást, amely meg szeretne tartani. 

Ha meg szeretne tartani a munkát a Logic Apps alkalmazást, tiltsa le azt törlése helyett. 

1. Nyissa meg a Logic Apps alkalmazást.
2. Az a **áttekintése** panelen válassza ki **törlése** vagy **letiltása**. 

Minden előfizetés rendelkezhet egy ingyenes IoT-központot. Ha egy szabad hub ehhez az oktatóanyaghoz létrehozott, majd nincs szükségünk törli-e további díjak elkerülése érdekében.

1. Nyissa meg az IoT hub. 
2. Az a **áttekintése** panelen válassza ki **törlése**. 

Akkor is, ha megtartja az IoT hub, előfordulhat, hogy törölni kívánja az esemény-előfizetést, amelyet létrehozott. 

1. Válassza ki az IoT hub **esemény rács**.
2. Válassza ki az eltávolítani kívánt esemény-előfizetést. 
3. Válassza a **Törlés** elemet. 

## <a name="next-steps"></a>További lépések

További információ [műveleteket esemény rács használatával az IoT-központ események reagálnak](../iot-hub/iot-hub-event-grid.md).

Ismerje meg, milyen egyéb információ teheti [esemény rács](overview.md).


