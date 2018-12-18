---
title: 'Oktatóanyag: Az Azure digitális Twins területről események rögzítése |} A Microsoft Docs'
description: Megtudhatja, hogy a jelen oktatóanyagban lévő lépésekkel hogyan kaphat értesítéseket a tereitől az Azure Digital Twins a Logic Appsszel való integrálása révén.
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: a52a3be8c3023893569e95b566a18c032be26459
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556016"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Oktatóanyag: Az Azure digitális Twins tárolóhelyek értesítéseket fogadjon a Logic Apps használatával

Miután az Azure digitális Twins-példány üzembe helyezése, a tárolóhelyek üzembe helyezése, és adott feltételek figyelése egyéni funkciók megvalósításához, értesítheti az e-mailen keresztül office felügyeleti, ha a figyelt események történnek. 

A [első oktatóanyaga](tutorial-facilities-setup.md), konfigurálta a térbeli grafikonon egy képzeletbeli épület. Egy hely az épület a mozgásban lévő adatoknak egyaránt, szén-dioxid-és hőmérséklet érzékelők tartalmazza. A [a második oktatóanyagban](tutorial-facilities-udf.md), Ön által üzembe helyezett, a graph és a egy felhasználó által definiált függvény ezeket az értékeket érzékelő figyelése, és ha észlelnek a helyiségben üres, és a hőmérséklet és szén-dioxid-eseményindító értesítések egy kényelmes címtartományba tartoznak. 

Ez az oktatóanyag bemutatja, hogyan integrálhatja ezeket az értesítéseket az Azure Logic Appsszel és küldhet e-mailt, amint egy ilyen helyiség elérhetővé válik. Az irodai rendszergazda ezekkel az információkkal segíthet a munkatársaknak lefoglalni a legmegfelelőbb tárgyalótermet. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Események integrálása az Azure Event Griddel.
> * Értesítse a Logic Apps-események.
    
## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már [konfigurálta](tutorial-facilities-setup.md) és [üzembe helyezte](tutorial-facilities-udf.md) az Azure Digital Twins-környezetet. Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik a következőkkel:
- Egy [Azure-fiók](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy futó Digital Twins-példány.
- A munkavégzéshez használt gépre letöltött és kicsomagolt [Digital Twins C#-minták](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- [.NET core SDK 2.1.403 verzió vagy újabb](https://www.microsoft.com/net/download) a fejlesztői gépen, a minta futtatásához. Futtatás `dotnet --version` , győződjön meg arról, hogy telepítve van-e a megfelelő verziót. 
- Office 365-fiók értesítési e-mailek küldéséhez.

## <a name="integrate-events-with-event-grid"></a>Események integrálása az Event Griddel 
Ebben a szakaszban a beállítása [Event Grid](../event-grid/overview.md) események gyűjtésére az Azure digitális Twins-példány, és átirányítja őket, hogy egy [eseménykezelő](../event-grid/event-handlers.md) például a Logic Apps.

### <a name="create-an-event-grid-topic"></a>Hozzon létre egy event grid-témakör
Egy [event grid-témakör](../event-grid/concepts.md#topics) irányíthatja a felhasználó által definiált függvény által létrehozott eseményeket felületet biztosít. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza ki a **erőforrás létrehozása**. 

1. Keressen rá és válassza az **Event Grid-témakör** elemet. Kattintson a **Létrehozás** gombra.

1. Adja meg az Event Grid-témakör **nevét**, és válassza ki az **előfizetést**. Válassza ki a **erőforráscsoport** használt vagy a digitális Twins példány létrehozott és a **hely**. Kattintson a **Létrehozás** gombra. 

    ![Hozzon létre egy event grid-témakör](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Keresse meg az erőforráscsoportból, válassza ki az event grid-témakör **áttekintése**, és másolja az értéket a **téma végpontja** egy ideiglenes fájlba. Az URL-cím a következő szakaszban lesz szüksége. 

1. Válassza ki **hozzáférési kulcsok**, és másolja át **kulcs 1** és **kulcs 2** egy ideiglenes fájlba. Szüksége lesz ezekre az értékekre a következő szakaszban a végpont létrehozásához.

    ![Event Grid-kulcsok](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Az event grid-témakör a végpont létrehozása

1. A parancssori ablakba, ellenőrizze, hogy használja-e a **foglaltsága-quickstart\src** a digitális Twins minta mappát.

1. Nyissa meg a fájlt **actions\createEndpoints.yaml** a Visual Studio Code szerkesztőben. Győződjön meg róla, hogy tartalmazza az alábbiakat:

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

1. Cserélje le az `Event_Grid_Topic_Path` helyőrzőt az Event Grid-témakör elérési útjára. Az elérési út lekérése eltávolításával **https://** és a záró erőforrás az elérési út a **téma végpontja** URL-CÍMÉT. Ennek a következő formátumhoz hasonlónak kell lennie: *yourEventGridName.yourLocation.eventgrid.azure.net*. 

    > [!IMPORTANT]
    > Az értékeket idézőjelek nélkül adja meg. Ellenőrizze, hogy van legalább egy szóköz karakter után a kettőspont a YAML-fájlt. Minden online YAML-érvényesítő használatával is ellenőrizheti a YAML-fájl tartalmának [ezzel az eszközzel](https://onlineyamltools.com/validate-yaml).

1. Mentse és zárja be a fájlt. Futtassa a következő parancsot a parancsablakban, és jelentkezzen be, amikor a rendszer kéri. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Ez a parancs a végpont az Event Gridhez hoz létre. 

   ![Az Event Grid végpontjai](./media/tutorial-facilities-events/dotnet-create-endpoints.png)


## <a name="notify-events-with-logic-apps"></a>A Logic Apps események értesítése
Használhatja a [Azure Logic Apps](../logic-apps/logic-apps-overview.md) szolgáltatás más szolgáltatások fogadott események automatizált feladatok létrehozása. Ebben a szakaszban beállíthatja a Logic Apps e-mail-értesítések segítségével. a térbeli érzékelők átirányítani események létrehozása egy [event grid-témakör](../event-grid/overview.md).

1. A bal oldali panelen, a [az Azure portal](https://portal.azure.com)válassza **erőforrás létrehozása**.

1. Keressen és válasszon ki egy új **Logic Apps**-erőforrást. Kattintson a **Létrehozás** gombra.

1. Adjon meg egy **neve** a logikai alkalmazás-erőforrást, majd válassza ki a **előfizetés**, **erőforráscsoport**, és **hely**. Kattintson a **Létrehozás** gombra.

    ![A Logic Apps-erőforrások létrehozása](./media/tutorial-facilities-events/create-logic-app.png)

1. Nyissa meg a Logic Apps-erőforrások telepítése, és nyissa meg a **Logikaialkalmazás-Tervező** ablaktáblán. 

1. Válassza az **Event Grid-esemény bekövetkezésekor** triggert. Jelentkezzen be a bérlő az Azure-fiókkal amikor a rendszer kéri. Válassza ki **hozzáférést** az Event Grid-erőforrás, amikor a rendszer kéri. Válassza a **Folytatás** elemet.

1. Az a **(előzetes verzió) erőforrás-esemény bekövetkezésekor** ablakban: 
   
   a. Válassza ki a **előfizetés** az event grid-témakör létrehozásához használt.

   b. Válassza ki **Microsoft.EventGrid.Topics** a **erőforrástípus**.

   c. Az Event Grid erőforrás válassza a legördülő listából a **erőforrásnév**.

   ![Logic App Designerben panel](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Válassza ki a **új lépés** gombra.

1. Az a **válasszon ki egy műveletet** ablakban:
    
   a. Keressen a **json elemzés** kifejezésre, és válassza a **JSON elemzése** műveletet.

   b. Az a **tartalom** mezőben válassza **törzs** származó a **dinamikus tartalom** listája.

   c. Válassza ki **adattartalom létrehozni a sémát használja minta**. Illessze be a következő JSON-adattartalom, és válassza ki **kész**.

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
    
    Ebben a hasznos adatban fiktív értékek szerepelnek. A Logic Apps a hasznosadat-minta alapján hoz létre egy *séma*.
    
    ![Logic Apps – JSON elemzése ablak az Event Gridhez](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Válassza ki a **új lépés** gombra.

1. Az a **válasszon ki egy műveletet** ablakban:

   a. Keresse és jelölje ki **feltétel vezérlő** származó a **műveletek** listája. 

   b. Az első **válasszon egy értéket** szövegbeviteli mezőben válasszon ki **eventType** származó a **dinamikus tartalom** listázása a **JSON elemzése** ablak.

   c. A második **válasszon egy értéket** szöveget adja meg **UdfCustom**.

   ![Kijelölt feltétel](./media/tutorial-facilities-events/logic-app-condition.png)

1. Az a **ha igaz** ablakban:
   
   a. Válassza ki **művelet hozzáadása**, és válassza ki **Office 365 Outlook**.

   b. Az a **műveletek** listáról válassza ki **e-mail küldése**. Válassza ki **jelentkezzen be a** és az e-mail-fiók hitelesítő adataival. Válassza ki **hozzáférést** amikor a rendszer kéri.

   c. A **Címzett** mezőben adja meg az e-mail-azonosítóját az értesítések fogadásához. A **tulajdonos**, írja be a szöveget **digitális Twins értesítési területen a gyenge légi minőségi**. Válassza ki **TopologyObjectId** származó a **dinamikus tartalom** listázása **JSON elemzése**.

   d. A **törzs** ugyanabban az ablakban írja be a szöveget ehhez hasonló: **Gyenge légi minőségi szoba észlelt, és hőmérséklet kell módosítani**. Nyugodtan dolgoznak ki az elemet a **dinamikus tartalom** listája.

   ![Logic Apps "E-mail küldése" beállításokat](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Válassza ki a **mentése** gombot a felső részén a **Logikaialkalmazás-Tervező** ablaktáblán.

1. Ügyeljen arra, hogy navigáljon az érzékelőktől kapott adatok szimulálása a **eszközkapcsolattal** mappát a digitális Twins minta egy parancsablakot, és futó `dotnet run`.

Néhány perc alatt akkor el kell e-mail értesítések fogadása a Logic Apps-erőforrásból. 

   ![E-mailes értesítés](./media/tutorial-facilities-events/logic-app-notification.png)

E-mailek leállítását, nyissa meg a Logic Apps-erőforrások a portálon, és válassza ki a **áttekintése** ablaktáblán. Válassza ki **letiltása**.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, ezen a ponton felfedezése az Azure digitális Twins leállítására, nyugodtan ebben az oktatóanyagban létrehozott erőforrások törléséhez:

1. A bal oldali menüben lévő a [az Azure portal](http://portal.azure.com), jelölje be **összes erőforrás**, a digitális Twins erőforráscsoportot, válassza ki és **törlése**.

    > [!TIP]
    > Ha törli a digitális Twins-példány problémajegyek tapasztal, szolgáltatás frissítése lett állítva a javítás. Ismételje meg a példány törlése.

2. Ha szükséges, törölje a munkahelyi számítógépen mintaalkalmazásból. 


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan jelenítheti meg az érzékelő adatokat, trendeket és azonosíthatja a rendellenességeket elemzése, lépjen a következő oktatóanyaggal: 
> [!div class="nextstepaction"]
> [Oktatóanyag: Segítségével vizualizálhatja és elemezheti a Time Series Insights használatával az Azure digitális Twins tárolóhelyek eseményei](tutorial-facilities-analyze.md)

Emellett többet is megtudhat a térbeli intelligencia gráfok és az Azure digitális Twins objektummodellt: 
> [!div class="nextstepaction"]
> [A Digital Twins-objektummodellek és a térbeliintelligencia-diagram ismertetése](concepts-objectmodel-spatialgraph.md)
