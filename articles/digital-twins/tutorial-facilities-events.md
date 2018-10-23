---
title: Azure Digital Twins-terek eseményeinek rögzítése | Microsoft Docs
description: Megtudhatja, hogy a jelen oktatóanyagban lévő lépésekkel hogyan kaphat értesítéseket a tereitől az Azure Digital Twins a Logic Appsszel való integrálása révén.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 91dd16938efbd1e24419352f66e3238646a77e8a
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323284"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-using-logic-apps"></a>Oktatóanyag: Értesítések fogadása az Azure Digital Twins-terektől a Logic Apps használatával

Miután üzembe helyezte az Azure Digital Twins-példányt, kiépítette a tereket, és egyéni függvényeket implementált adott feltételek monitorozására, e-mailben értesítheti az irodai rendszergazdát, ha a monitorozott események bekövetkeznek. 

Az [első oktatóanyagban](tutorial-facilities-setup.md) egy képzeletbeli épület térbeli diagramját konfigurálta, amelynek egyik helyiségében mozgás-, széndioxid- és hőmérséklet-érzékelők voltak. A [második oktatóanyagban](tutorial-facilities-udf.md) üzembe helyezte a diagramot és egy egyéni, úgynevezett felhasználó által meghatározott függvényt az érzékelőadatok értékeinek monitorozására, és értesítések aktiválására, ha egy helyiség üres, és a hőmérséklet és a szén-dioxid szintje az elfogadható tartományba esik. Ez az oktatóanyag bemutatja, hogyan integrálhatja ezeket az értesítéseket az Azure Logic Appsszel és küldhet e-mailt, amint egy ilyen helyiség elérhetővé válik. Az irodai rendszergazda ezekkel az információkkal segíthet a munkatársaknak lefoglalni a legmegfelelőbb tárgyalótermet. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Események integrálása az Event Griddel
> * Eseményértesítések küldése a Logic Appsszel
    
## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már [konfigurálta](tutorial-facilities-setup.md) és [üzembe helyezte](tutorial-facilities-udf.md) az Azure Digital Twins-környezetet. Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik a következőkkel:
- Egy [Azure-fiók](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy futó Digital Twins-példány.
- A munkavégzéshez használt gépre letöltött és kicsomagolt [Digital Twins C#-minták](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- A [.NET Core SDK 2.1.403-as vagy újabb](https://www.microsoft.com/net/download) verziója a fejlesztői gépen a minta futtatására. A `dotnet --version` parancs futtatásával ellenőrizze, hogy a megfelelő verzió van-e telepítve. 
- Office 365-fiók értesítési e-mailek küldéséhez.

## <a name="integrate-events-with-event-grid"></a>Események integrálása az Event Griddel 
Ebben a részben egy [Event Gridet](../event-grid/overview.md) helyezünk üzembe a Digital Twins-példány eseményeinek gyűjtésére és átirányítására egy [eseménykezelő](../event-grid/event-handlers.md), például a Logic Apps számára.

### <a name="create-event-grid-topic"></a>Event Grid-témakör létrehozása
Az [Event Grid-témakörök](../event-grid/concepts.md#topics) interfészként szolgálnak a felhasználó által meghatározott függvény által létrehozott események átirányításához. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali navigációs panelen kattintson az **Erőforrás létrehozása** elemre. 

1. Keressen rá és válassza az **Event Grid-témakör** elemet. Kattintson a **Create** (Létrehozás) gombra.

1. Adja meg az Event Grid-témakör **nevét**, és válassza ki az **előfizetést**. Válassza ki a Digital Twins-példányhoz használt vagy létrehozott **erőforráscsoportot**, valamint a **helyet**. Kattintson a **Create** (Létrehozás) gombra. 

    ![Event Grid-témakör létrehozása](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Lépjen az Event Grid-témakörre az erőforráscsoportban, kattintson az **Áttekintés** elemre, és másolja a **Téma végpontja** értékét egy ideiglenes fájlba. Erre az URL-címre a következő részben lesz szükség. 

1. Kattintson a **Hozzáférési kulcsok** elemre, és másolja az **1. kulcsot** és a **2. kulcsot** egy ideiglenes fájlba. Ezekre az értékekre a végpont létrehozásához lesz szükség a következő részben.

    ![Event Grid-kulcsok](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Végpont létrehozása az Event Grid-témakörhöz

1. A parancsablakban ellenőrizze, hogy a Digital Twins-minta **_occupancy-quickstart\src_** mappájában van-e.

1. Nyissa meg az **_actions\createEndpoints.yaml_** fájlt a Visual Studio Code szerkesztőjében. Győződjön meg róla, hogy tartalmazza az alábbiakat:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_Event_Grid
      secondaryConnectionString: Secondary_connection_string_for_your_Event_Grid
      path: Event_Grid_Topic_Path
    ```

1. Cserélje le a `Primary_connection_string_for_your_Event_Grid` helyőrzőt az **1. kulcs** értékére. 

1. Cserélje le a `Secondary_connection_string_for_your_Event_Grid` helyőrzőt a **2. kulcs** értékére.

1. Cserélje le az `Event_Grid_Topic_Path` helyőrzőt az Event Grid-témakör elérési útjára. Az elérési utat a *https://* előtag és a záró erőforrás-útvonalak a **témakörvégpont** URL-címéből való eltávolításával kapja meg. Ennek a következő formátumhoz hasonlónak kell lennie: **yourEventGridName.yourLocation.eventgrid.azure.net**. 

    > [!IMPORTANT]
    > Az értékeket idézőjelek nélkül adja meg. A *YAML*-fájlban minden kettőspont után egy szóköz karaktert kell írni. A *YAML*-fájl tartalmának érvényességét bármely online YAML-ellenőrző eszközzel ellenőrizheti, például [ezzel az eszközzel](https://onlineyamltools.com/validate-yaml).

1. Mentse és zárja be a fájlt. Futtassa a következő parancsot a parancsablakban, és jelentkezzen be, amikor a rendszer kéri. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   A parancs létrehozza az Event Grid végpontját. 

   ![Az Event Grid végpontjai](./media/tutorial-facilities-events/dotnet-create-endpoints.png)


## <a name="notify-events-with-logic-app"></a>Eseményértesítések küldése a Logic Appsszel
Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) szolgáltatással automatizált feladatokat hozhat létre a más szolgáltatásokból kapott eseményekhez. Ebben a szakaszban beállítjuk, hogy a Logic Apps értesítő e-maileket hozzon létre a térérzékelőkről átirányított eseményekhez egy [Event Grid-témakör](../event-grid/overview.md) segítségével.

1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs paneljén kattintson az **Erőforrás létrehozása** gombra.

1. Keressen és válasszon ki egy új **Logic Apps**-erőforrást. Kattintson a **Create** (Létrehozás) gombra.

1. Adja meg a logikai alkalmazás **nevét**, majd válassza ki az **előfizetést**, az **erőforráscsoportot** és a **helyet**. Kattintson a **Create** (Létrehozás) gombra.

    ![Logikai alkalmazás létrehozása](./media/tutorial-facilities-events/create-logic-app.png)

1. Miután üzembe helyezte, nyissa meg a logikai alkalmazást, majd a **Logikaialkalmazás-tervező** panelt. 

1. Válassza az **Event Grid-esemény bekövetkezésekor** triggert. Amikor a rendszer kéri, **jelentkezzen be** a bérlőre az Azure-fiókjával. Amikor a rendszer kéri, erősítse meg az Event Gridhez való **hozzáférés engedélyezését**. Kattintson a **Folytatás** gombra.

1. Az **Erőforrás-esemény bekövetkezésekor (előzetes verzió)** ablakban: 
    1. Válassza ki az Event Grid létrehozásához előzőleg használt **előfizetést**.

    1. Válassza a **Microsoft.EventGrid.Topics** lehetőséget az **erőforrás típusaként**.

    1. Az **Erőforrás neve** mezőben válassza ki az Event Grid-erőforrást a legördülő listából.

    ![Logikai alkalmazás létrehozása](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Kattintson az **Új lépés** gombra.

1. A **Művelet kiválasztása** ablakban:
    1. Keressen a *json elemzés* kifejezésre, és válassza a **JSON elemzése** műveletet.

    1. Kattintson a **Tartalom** mezőbe, és válassza a **Törzs** lehetőséget a **Dinamikus tartalom** listából.

    1. Kattintson a **Séma létrehozása hasznosadat-minta használatával** elemre. Illessze be az alábbi JSON formátumú hasznos adatot, majd kattintson a **Kész** gombra.

        ```JSON
        {
        "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
        "subject": "UdfCustom",
        "data": {
          "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
          "ResourceType": "Space",
          "Payload": "\"Air quality is poor.\"",
          "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
        },
        "eventType": "UdfCustom",
        "eventTime": "0001-01-01T00:00:00Z",
        "dataVersion": "1.0",
        "metadataVersion": "1",
        "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
        }
        ```
    
    Ebben a hasznos adatban fiktív értékek szerepelnek. A logikai alkalmazás ennek a hasznosadat-mintának a használatával hozza létre a **sémát**.
    
    ![Logikai alkalmazás, JSON elemzése az Event Grid számára](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Kattintson az **Új lépés** gombra.

1. A **Művelet kiválasztása** ablakban:
    1. Keressen rá a **Feltételvezérlő** lehetőségre a **műveletek** listájában, és válassza ki. 

    1. Kattintson az első **Érték választása** szövegmezőbe, és válassza az **eventType** lehetőséget a **Dinamikus tartalom** listából a **JSON elemzése** ablakban.

    1. Kattintson a második **Érték választása** mezőbe, és válassza az *UdfCustom* lehetőséget.

    ![Logikai alkalmazás, JSON elemzése az Event Grid számára](./media/tutorial-facilities-events/logic-app-condition.png)

1. A **Ha igaz** ablakban:
    1. Kattintson a **Művelet hozzáadása** elemre, és válassza az *Office 365 Outlook* lehetőséget.

    1. A **Műveletek** listában válassza az **E-mail küldése** lehetőséget. Kattintson a **Bejelentkezés** gombra, és adja meg az e-mail-fiókja hitelesítő adatait. Amikor a rendszer kéri, kattintson a **Hozzáférés engedélyezése** elemre.

    1. A **Címzett** mezőben adja meg az e-mail-azonosítóját az értesítések fogadásához. A **Tárgy** mezőbe írja be a *Digital Twins notification for poor air quality in space* szöveget, majd válassza a **TopologyObjectId** lehetőséget a **JSON elemzése** **Dinamikus tartalom** listájában.

    1. Ugyanebben az ablakban, a **Törzs** részben adjon meg a következőhöz hasonló szöveget: *Poor air quality detected in a room, and temperature needs to be adjusted*. Ha kívánja, adjon meg további részleteket a **Dinamikus tartalom** lista elemeivel az alábbiak szerint.

    ![Logikai alkalmazás, e-mail küldése](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Kattintson a **Logikaialkalmazás-tervező** panel tetején a **Mentés** gombra.

1. Az érzékelőadatok szimulálásához lépjen egy parancsablakban a Digital Twins-minta **_device-connectivity_** mappájára, és futtassa a `dotnet run` parancsot.

Néhány perc múlva a logikai alkalmazás elkezdi küldeni az értesítő e-maileket. 

   ![Logikai alkalmazás, e-mail küldése](./media/tutorial-facilities-events/logic-app-notification.png)

Az e-mailek küldésének leállításához lépjen a portálon a **Logikai alkalmazásra**, és nyissa meg az **Áttekintés** panelt. Kattintson a **Letiltás** lehetőségre.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha most be szeretné fejezni az Azure Digital Twins megismerését, nyugodtan törölje az ebben az oktatóanyagban létrehozott erőforrásokat:

1. Az [Azure Portal](http://portal.azure.com) bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, válassza ki a Digital Twins-erőforráscsoportot, és **törölje**.
2. Szükség esetén a mintaalkalmazásokat is törölheti a munkavégzéshez használt gépről. 


## <a name="next-steps"></a>További lépések

A következő oktatóanyagból megtudhatja, hogyan jelenítheti meg az érzékelők adatait, elemezheti a trendeket és veheti észre a rendellenességeket. 
> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Digital Twins-terek eseményeinek vizualizációja és elemzése a Time Series Insights használatával](tutorial-facilities-analyze.md)

Ha folytatja, részletesebben megismerheti az Azure Digital Twins térbeliintelligencia-diagramjait és objektummodelljeit. 
> [!div class="nextstepaction"]
> [A Digital Twins-objektummodellek és a térbeliintelligencia-diagram ismertetése](concepts-objectmodel-spatialgraph.md)