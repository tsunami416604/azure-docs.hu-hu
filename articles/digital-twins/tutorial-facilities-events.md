---
title: 'Oktatóanyag: eszközök eseményeinek rögzítése egy IoT-területről – Azure digitális Twins | Microsoft Docs'
description: Megtudhatja, hogy a jelen oktatóanyagban lévő lépésekkel hogyan kaphat értesítéseket a tereitől az Azure Digital Twins a Logic Appsszel való integrálása révén.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: 492fa7f4989a40ea1d5ec91a4fbf4dbbe79ef6ce
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383261"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Oktatóanyag: értesítések fogadása Azure digitális Twins-tárhelyekről Logic Apps használatával

Miután üzembe helyezte az Azure Digital Twins-példányt, kiépítheti a tárhelyeit, és egyéni függvényeket valósíthat meg bizonyos feltételek figyeléséhez, e-mailben értesítheti az Office-rendszergazdát a figyelt feltételek bekövetkezésekor.

[Az első oktatóanyagban](tutorial-facilities-setup.md)egy képzeletbeli épületben a térbeli gráfot konfigurálta. Az épületben található egyik helyiség a mozgással, a széndioxidmal és a hőmérséklettel kapcsolatos érzékelőket tartalmaz. [A második oktatóanyagban](tutorial-facilities-udf.md)kiépített egy gráfot és egy felhasználó által definiált függvényt, amellyel figyelheti ezeket az érzékelő értékeket, és aktiválhatja az értesítéseket, ha a szoba üres, és a hőmérséklet és a szén-dioxid kényelmes tartományban van. 

Ez az oktatóanyag bemutatja, hogyan integrálhatja ezeket az értesítéseket az Azure Logic Appsszel és küldhet e-mailt, amint egy ilyen helyiség elérhetővé válik. Az irodai rendszergazda ezekkel az információkkal segíthet a munkatársaknak lefoglalni a legmegfelelőbb tárgyalótermet.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Események integrálása a Azure Event Gridsal.
> * Események értesítése Logic Appsokkal.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már [konfigurálta](tutorial-facilities-setup.md) és [üzembe helyezte](tutorial-facilities-udf.md) az Azure Digital Twins-környezetet. Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik a következőkkel:

- Egy [Azure-fiók](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy futó Digital Twins-példány.
- A munkavégzéshez használt gépre letöltött és kicsomagolt [Digital Twins C#-minták](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- A minta futtatásához [.net Core SDK 2.1.403 vagy újabb verziót](https://www.microsoft.com/net/download) a fejlesztői gépen. A `dotnet --version` futtatásával ellenőrizze, hogy telepítve van-e a megfelelő verzió.
- [Office 365](https://products.office.com/home) -fiók az értesítő e-mailek küldéséhez.

> [!TIP]
> Új példány kiépítés esetén használjon egyedi digitális Twins-példány nevét.

## <a name="integrate-events-with-event-grid"></a>Események integrálása az Event Griddel

Ebben a szakaszban be kell állítania [Event Grid](../event-grid/overview.md) az Azure Digital Twins-példány eseményeinek gyűjtésére, és átirányíthatja őket egy [eseménykezelőre](../event-grid/event-handlers.md) , például a Logic Appsra.

### <a name="create-an-event-grid-topic"></a>Event Grid-témakör létrehozása

Az [Event Grid-témakör](../event-grid/concepts.md#topics) egy felületet biztosít a felhasználó által definiált függvény által generált események útválasztásához. 

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

1. A bal oldali panelen válassza az **Erőforrás létrehozása** elemet. 

1. Keressen rá és válassza az **Event Grid-témakör** elemet. Kattintson a **Létrehozás** gombra.

1. Adja meg az Event Grid-témakör **nevét**, és válassza ki az **előfizetést**. Válassza ki a digitális Twins-példányhoz használt vagy létrehozott **erőforráscsoportot** , valamint a **helyet**. Kattintson a **Létrehozás** gombra. 

    [Event Grid-témakör ![létrehozása](./media/tutorial-facilities-events/create-event-grid-topic.png)](./media/tutorial-facilities-events/create-event-grid-topic.png#lightbox)

1. Tallózással keresse meg az Event Grid-témakört az erőforráscsoporthoz, válassza az **Áttekintés**lehetőséget, és másolja a **témakör-végpont** értékét egy ideiglenes fájlba. Ezt az URL-címet a következő szakaszban kell megadnia. 

1. Válassza a **hozzáférési kulcsok**lehetőséget, és másolja az **1** . és a **2** . kulcsot egy ideiglenes fájlba. Ezekre az értékekre szüksége lesz a végpont létrehozásához a következő szakaszban.

    [Event Grid kulcsok ![](./media/tutorial-facilities-events/event-grid-keys.png)](./media/tutorial-facilities-events/event-grid-keys.png#lightbox)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Végpont létrehozása az Event Grid-témakörhöz

1. A parancsablakban ellenőrizze, hogy a digitális ikrek minta **Occupancy-quickstart\src** mappájában van-e.

1. Nyissa meg a **actions\createEndpoints.YAML** fájlt a Visual Studio Code Editorban. Győződjön meg róla, hogy tartalmazza az alábbiakat:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: <Primary connection string for your Event Grid>
      secondaryConnectionString: <Secondary connection string for your Event Grid>
      path: <Event Grid Topic Name without https:// and /api/events, e.g. eventgridname.region.eventgrid.azure.net>
    ```

1. Cserélje le a helyőrzőt `<Primary connection string for your Event Grid>` az **1. kulcs**értékére.

1. Cserélje le a helyőrzőt `<Secondary connection string for your Event Grid>` a **2. kulcs**értékére.

1. Cserélje le az **elérési út** helyőrzőjét az Event Grid-témakör elérési útjára. Ezt az elérési utat úgy érheti el, ha eltávolítja a **https://** és a záró erőforrás elérési útját a **témakör végpont** URL-címéből. Ennek a következő formátumhoz hasonlónak kell lennie: *yourEventGridName.yourLocation.eventgrid.azure.net*.

    > [!IMPORTANT]
    > Az értékeket idézőjelek nélkül adja meg. Győződjön meg arról, hogy legalább egy szóköz karakter szerepel a YAML-fájlban lévő kettőspontok után. A YAML-fájl tartalmát bármely online YAML-érvényesítő, például [az eszköz](https://onlineyamltools.com/validate-yaml)használatával is érvényesítheti.

1. Mentse és zárja be a fájlt. Futtassa a következő parancsot a parancsablakban, és jelentkezzen be, amikor a rendszer kéri. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Ez a parancs létrehozza a végpontot a Event Grid számára. 

   [Event Grid ![végpontok](./media/tutorial-facilities-events/dotnet-create-endpoints.png)](./media/tutorial-facilities-events/dotnet-create-endpoints.png#lightbox)

## <a name="notify-events-with-logic-apps"></a>Események értesítése Logic Apps

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) szolgáltatással automatizált feladatokat hozhat létre más szolgáltatásokból érkező eseményekhez. Ebben a szakaszban Logic Apps beállíthatja, hogy a rendszer hogyan hozzon létre e-mail-értesítéseket a térbeli érzékelőktől átirányított eseményekhez, az [Event Grid-témakör](../event-grid/overview.md)segítségével.

1. A [Azure Portal](https://portal.azure.com)bal oldali ablaktábláján válassza az **erőforrás létrehozása**lehetőséget.

1. Keressen és válasszon ki egy új **Logic Apps**-erőforrást. Kattintson a **Létrehozás** gombra.

1. Adja meg a logikai alkalmazás erőforrásának **nevét** , majd válassza ki az **előfizetését**, az **erőforráscsoportot**és a **helyet**. Kattintson a **Létrehozás** gombra.

    [Logic Apps erőforrás létrehozása ![](./media/tutorial-facilities-events/create-logic-app.png)](./media/tutorial-facilities-events/create-logic-app.png#lightbox)

1. Az üzembe helyezéskor nyissa meg Logic Apps erőforrását, majd nyissa meg a **Logic app Designer** panelt. 

1. Válassza ki, hogy **mikor történjen egy Event Grid erőforrás-esemény** eseményindító. Ha a rendszer kéri, jelentkezzen be a bérlőbe az Azure-fiókjával. Ha a rendszer kéri, válassza a **hozzáférés engedélyezése** a Event Grid erőforráshoz lehetőséget. Válassza a **Folytatás** elemet.

1. Az **erőforrás-esemény bekövetkezésekor (előzetes verzió)** ablakban: 
   
   a. Válassza ki az Event Grid-témakör létrehozásához használt **előfizetést** .

   b. Válassza ki a **Microsoft. EventGrid.** **Resource típusú**témákat.

   c. Válassza ki a Event Grid erőforrást az **Erőforrás neve**legördülő listából.

   [![Logic app Designer panel](./media/tutorial-facilities-events/logic-app-resource-event.png)](./media/tutorial-facilities-events/logic-app-resource-event.png#lightbox)

1. Kattintson az **új lépés** gombra.

1. A **művelet kiválasztása** ablakban:

   a. Keressen a **json elemzés** kifejezésre, és válassza a **JSON elemzése** műveletet.

   b. A **tartalom** mezőben válassza a **törzs** lehetőséget a **dinamikus tartalmak** listájából.

   c. Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget. Illessze be a következő JSON-adattartalmat, majd válassza a **kész**lehetőséget.

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

    Ebben a hasznos adatban fiktív értékek szerepelnek. Logic Apps ezt a mintát használja a *séma*létrehozásához.

    [![a Event Grid JSON-ablakának elemzése Logic Apps](./media/tutorial-facilities-events/logic-app-parse-json.png)](./media/tutorial-facilities-events/logic-app-parse-json.png#lightbox)

1. Kattintson az **új lépés** gombra.

1. A **művelet kiválasztása** ablakban:

   a. Válassza a **vezérlés > a feltétel** vagy a keresési **feltétel** elemet a **műveletek** listából. 

   b. Az első **válasszon egy értéket** szövegmezőben válassza a **EventType** lehetőséget a **dinamikus tartalmak** listájából a **JSON-elemzési** ablakhoz.

   c. A második **válasszon egy értéket** szövegmezőbe írja be a `UdfCustom`elemet.

   [![kiválasztott feltételek](./media/tutorial-facilities-events/logic-app-condition.png)](./media/tutorial-facilities-events/logic-app-condition.png#lightbox)

1. A **Ha igaz** ablakban:

   a. Válassza **a művelet hozzáadása**lehetőséget, és válassza az **Office 365 Outlook**lehetőséget.

   b. A **műveletek** listából válassza az **E-mail küldése (v2)** lehetőséget. Válassza a **Bejelentkezés** lehetőséget, és használja az e-mail-fiókja hitelesítő adatait. Ha a rendszer kéri, válassza a **hozzáférés engedélyezése** lehetőséget.

   c. A **Címzett** mezőben adja meg az e-mail-azonosítóját az értesítések fogadásához. A **Tárgy**mezőben adja meg a szöveges **digitális ikrek értesítését a térben található gyenge levegőminőség tekintetében**. Ezután válassza a **TopologyObjectId** lehetőséget a **dinamikus tartalmak** listájából a **JSON elemzéséhez**.

   d. Az ugyanabban az ablakban található **törzs** területen adja meg a következőhöz hasonló szöveget: **a rendszer a helyiségben észlelt gyenge levegőminőség észlelését és a hőmérsékletet is módosítani**kell. A **dinamikus tartalmak** listájából származó elemek használatával dolgozhat.

   [![Logic Apps "e-mail küldése" választás](./media/tutorial-facilities-events/logic-app-send-email.png)](./media/tutorial-facilities-events/logic-app-send-email.png#lightbox)

1. Kattintson a **Save (Mentés** ) gombra a **Logic app Designer** panel tetején.

1. Győződjön meg arról, hogy az érzékelőt úgy szimulálja, hogy a parancssorablakban megkeresi a digitális Twins **-minta eszköz kapcsolati** mappáját, és `dotnet run`futtat.

Néhány percen belül el kell indítania az e-mailes értesítéseket ebből a Logic Apps erőforrásból. 

   [e-mail-értesítés ![](./media/tutorial-facilities-events/logic-app-notification.png)](./media/tutorial-facilities-events/logic-app-notification.png#lightbox)

Az e-mailek fogadásának leállításához nyissa meg az Logic Apps erőforrást a portálon, és válassza az **Áttekintés** ablaktáblát. Válassza a **Letiltás**lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, ezen a ponton felfedezése az Azure digitális Twins leállítására, nyugodtan ebben az oktatóanyagban létrehozott erőforrások törléséhez:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden erőforrás**elemet, válassza ki a digitális Twins-erőforráscsoportot, és válassza a **Törlés**lehetőséget.

    > [!TIP]
    > Ha törli a digitális Twins-példány problémajegyek tapasztal, szolgáltatás frissítése lett állítva a javítás. Ismételje meg a példány törlése.

2. Ha szükséges, törölje a mintául szolgáló alkalmazásokat a munkahelyi gépen.

## <a name="next-steps"></a>Következő lépések

A következő oktatóanyagban megtudhatja, hogyan jelenítheti meg az érzékelők adatait, elemezheti a trendeket és a helyszíni rendellenességeket:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Digital Twins-terek eseményeinek vizualizációja és elemzése a Time Series Insights használatával](tutorial-facilities-analyze.md)

További információt a térbeli intelligencia diagramjairól és az Azure Digital Twins-beli objektummodell-modellekről a következőket is megtudhat:

> [!div class="nextstepaction"]
> [A Digital Twins-objektummodellek és a térbeliintelligencia-diagram ismertetése](concepts-objectmodel-spatialgraph.md)
