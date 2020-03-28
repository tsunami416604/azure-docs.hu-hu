---
title: 'Oktatóanyag: Eszközesemények rögzítése IoT-területről – Azure Digital Twins| Microsoft dokumentumok'
description: Megtudhatja, hogy a jelen oktatóanyagban lévő lépésekkel hogyan kaphat értesítéseket a tereitől az Azure Digital Twins a Logic Appsszel való integrálása révén.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 1cd617204bbc12a99b6ae9e3b55fbc59b0e0578a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75933706"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Oktatóanyag: Értesítések fogadása az Azure digitális twins terek ről a Logic Apps használatával

Miután üzembe helyezte az Azure Digital Twins-példányt, kiépítheti a helyeket, és egyéni függvényeket valósított meg a meghatározott feltételek figyeléséhez, e-mailben értesítheti az irodai rendszergazdát, ha a figyelt feltételek bekövetkeznek.

Az [első oktatóanyagban](tutorial-facilities-setup.md)egy képzeletbeli épület térbeli grafikonját konfigurálta. Az épületben egy helyiség mozgásérzékelőket, szén-dioxidot és hőmérsékletet tartalmaz. A [második oktatóanyagban](tutorial-facilities-udf.md)kiépített e grafikont és egy felhasználó által definiált függvényt, hogy figyelje ezeket az érzékelőértékeket, és értesítéseket váltson ki, amikor a szoba üres, és a hőmérséklet és a szén-dioxid kényelmes tartományban van. 

Ez az oktatóanyag bemutatja, hogyan integrálhatja ezeket az értesítéseket az Azure Logic Appsszel és küldhet e-mailt, amint egy ilyen helyiség elérhetővé válik. Az irodai rendszergazda ezekkel az információkkal segíthet a munkatársaknak lefoglalni a legmegfelelőbb tárgyalótermet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Integrálja az eseményeket az Azure Event Griddel.
> * Események értesítése a Logic Apps segítségével.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már [konfigurálta](tutorial-facilities-setup.md) és [üzembe helyezte](tutorial-facilities-udf.md) az Azure Digital Twins-környezetet. Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik a következőkkel:

- Egy [Azure-fiók](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy futó Digital Twins-példány.
- A munkavégzéshez használt gépre letöltött és kicsomagolt [Digital Twins C#-minták](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- [A .NET Core SDK 2.1.403-as vagy újabb verziója](https://www.microsoft.com/net/download) a fejlesztőgépen a minta futtatásához. Futtassa `dotnet --version` a megfelelő verzió telepítésének ellenőrzéséhez.
- [Office 365-fiók](https://products.office.com/home) értesítési e-mailek küldéséhez.

> [!TIP]
> Használjon egy egyedi digital Twins-példány nevét, ha új példányt létesít.

## <a name="integrate-events-with-event-grid"></a>Események integrálása az Event Griddel

Ebben a szakaszban beállíthatja az [Event Gridet](../event-grid/overview.md) az Azure Digital Twins-példány eseményeinek gyűjtésére, és átirányítja őket egy [eseménykezelőbe,](../event-grid/event-handlers.md) például a Logic Apps-be.

### <a name="create-an-event-grid-topic"></a>Eseményrács témakörének létrehozása

Az [eseményrács témaköre](../event-grid/concepts.md#topics) felületet biztosít a felhasználó által definiált függvény által létrehozott események továbbításához. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A bal oldali panelen válassza az **Erőforrás létrehozása** elemet. 

1. Keressen rá és válassza az **Event Grid-témakör** elemet. Kattintson a **Létrehozás** gombra.

1. Adja meg az Event Grid-témakör **nevét**, és válassza ki az **előfizetést**. Válassza ki a digitális twins-példányhoz használt vagy létrehozott **erőforráscsoportot,** valamint a **Helyet.** Kattintson a **Létrehozás** gombra. 

    [![Eseményrács témakörének létrehozása](./media/tutorial-facilities-events/create-event-grid-topic.png)](./media/tutorial-facilities-events/create-event-grid-topic.png#lightbox)

1. Tallózással keresse meg az erőforráscsoport eseményrácstémakörét, válassza **az Áttekintés**lehetőséget, és másolja a **Témakörvégpont** értékét egy ideiglenes fájlba. Erre az URL-címre a következő szakaszban lesz szüksége. 

1. Válassza **a Hozzáférési kulcsok lehetőséget,** és másolja az **1-** es és **a 2-es kulcsot** egy ideiglenes fájlba. Ezekre az értékekre szüksége lesz a végpont létrehozásához a következő szakaszban.

    [![Eseményrács billentyűi](./media/tutorial-facilities-events/tutorial-event-grid-keys.png)](./media/tutorial-facilities-events/tutorial-event-grid-keys.png#lightbox)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Végpont létrehozása az eseményrács témakörhöz

1. A parancsablakban győződjön meg arról, hogy a Digitális Ikrek minta **használt-gyorsstart\src** mappájában van.

1. Nyissa meg az **actions\createEndpoints.yaml** fájlt a Visual Studio Code szerkesztőjében. Győződjön meg róla, hogy tartalmazza az alábbiakat:

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

1. Cserélje le `<Primary connection string for your Event Grid>` a helyőrzőt az **1- es kulcs**értékére.

1. Cserélje le `<Secondary connection string for your Event Grid>` a helyőrzőt a **2- es kulcs**értékére.

1. Cserélje le a **görbe** helyőrzőit az eseményrács témakörének elérési útjának. Ezt az elérési utat úgy szerezheti **be,** hogy eltávolítja a https:// és a záró erőforrásútvonalakat a **Témakör végponturl-címéről.** Ennek a következő formátumhoz hasonlónak kell lennie: *yourEventGridName.yourLocation.eventgrid.azure.net*.

    > [!IMPORTANT]
    > Az értékeket idézőjelek nélkül adja meg. Győződjön meg arról, hogy a YAML-fájlban lévő kettőspontok után legalább egy szóköz található. A YAML-fájl tartalmát bármely online YAML-érvényesítő, például [ez az eszköz](https://onlineyamltools.com/validate-yaml)segítségével is ellenőrizheti.

1. Mentse és zárja be a fájlt. Futtassa a következő parancsot a parancsablakban, és jelentkezzen be, amikor a rendszer kéri. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Ez a parancs létrehozza az Eseményrács végpontját. 

   [![Az Event Grid végpontjai](./media/tutorial-facilities-events/dotnet-create-endpoints.png)](./media/tutorial-facilities-events/dotnet-create-endpoints.png#lightbox)

## <a name="notify-events-with-logic-apps"></a>Események értesítése a Logic Apps alkalmazásokkal

Az Azure [Logic Apps](../logic-apps/logic-apps-overview.md) szolgáltatás segítségével automatikus feladatokat hozhat létre a más szolgáltatásokból érkező eseményekhez. Ebben a szakaszban beállítja a Logic Apps-t, hogy e-mail-értesítéseket hozzon létre a térbeli érzékelőkről irányított eseményekhez egy [eseményrács-témakör](../event-grid/overview.md)segítségével.

1. Az [Azure Portal](https://portal.azure.com)bal oldali ablaktáblájában válassza az **Erőforrás létrehozása**lehetőséget.

1. Keressen és válasszon ki egy új **Logic Apps**-erőforrást. Kattintson a **Létrehozás** gombra.

1. Adja meg a logikai alkalmazás erőforrás **nevét,** majd válassza ki az **Előfizetés**, **Erőforrás csoport**és **hely lehetőséget.** Kattintson a **Létrehozás** gombra.

    [![Logikai alkalmazások erőforrás létrehozása](./media/tutorial-facilities-events/tutorial-create-logic-app.png)](./media/tutorial-facilities-events/tutorial-create-logic-app.png#lightbox)

1. Nyissa meg a Logic Apps erőforrást üzembe helyezésekor, majd nyissa meg a **Logikai alkalmazás tervezőjének** ablaktábláját. 

1. Jelölje be az Event Grid erőforrásesemény eseményindító **bekövetkezésekor jelölőnégyzetet.** Bontsa ki az **Azure Event Grid** beállítást, és jelentkezzen be a bérlőbe az Azure-fiókjával, amikor a rendszer kéri. Ha a rendszer kéri, válassza a **Hozzáférés engedélyezése** az Eseményrács erőforráshoz lehetőséget. Válassza a **Folytatás** elemet.

1. A **Mikor történik egy erőforrásesemény** ablakban: 
   
   a. Válassza ki az eseményrács témakörének létrehozásához használt **előfizetést.**

   b. Válassza a **Microsoft.EventGrid.Topics** for **Resource Type**lehetőséget.

   c. Válassza ki az Event Grid erőforrást az **Erőforrás neve**legördülő mezőből.

   [![Logikai alkalmazástervező ablaktábla](./media/tutorial-facilities-events/logic-app-resource-event.png)](./media/tutorial-facilities-events/logic-app-resource-event.png#lightbox)

1. Válassza az **Új lépés** gombot.

1. A **Művelet kiválasztása ablakban:**

   a. Keressen a **json elemzés** kifejezésre, és válassza a **JSON elemzése** műveletet.

   b. A **Tartalom** mezőben válassza a **Dinamikus tartalom** lista **Törzs** mezőjét.

   c. Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget. Illessze be a következő JSON-hasznos adatot, majd válassza **a Kész gombot.**

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

    Ebben a hasznos adatban fiktív értékek szerepelnek. A Logic Apps ezt a mintahasznos támrát használja a *séma*létrehozásához.

    [![Logic Apps Elemzés JSON ablak eseményrács](./media/tutorial-facilities-events/logic-app-parse-json.png)](./media/tutorial-facilities-events/logic-app-parse-json.png#lightbox)

1. Válassza az **Új lépés** gombot.

1. A **Művelet kiválasztása ablakban:**

   a. Válassza > feltétel vagy keresési **feltétel** **vezérlése** elemet a **Műveletek** listából. 

   b. Az első **Érték** kiválasztása mezőben válassza az **eventType** elemet a **Parse JSON** ablak **dinamikus tartalomlistájából.**

   c. A második **Érték kiválasztása** mezőbe `UdfCustom`írja be a mezőbe a mezőt.

   [![Kiválasztott feltételek](./media/tutorial-facilities-events/tutorial-logic-app-condition.png)](./media/tutorial-facilities-events/tutorial-logic-app-condition.png#lightbox)

1. A **Ha igaz ablakban:**

   a. Válassza **a Művelet hozzáadása**lehetőséget, majd az Office **365 Outlook**lehetőséget.

   b. A **Műveletek** listában válassza az **E-mail küldése (V2)** lehetőséget. Válassza **a Bejelentkezés** lehetőséget, és használja az e-mail fiók hitelesítő adatait. Ha a rendszer **kéri,** válassza a Hozzáférés engedélyezése lehetőséget.

   c. A **Címzett** mezőben adja meg az e-mail-azonosítóját az értesítések fogadásához. A **Tárgy mezőbe**írja be a digitális twins értesítés szövegét az **űrben lévő rossz levegőminőségről**. Ezután válassza a **TopologyObjectId** elemet a **JSON elemzés** **dinamikus tartalomlistájából.**

   d. Az ugyanabban az ablakban a **Test** mezőbe írja be a következőhöz hasonló szöveget: **A helyiségben rossz levegőminőséget észlel, és a hőmérsékletet módosítani kell.** Nyugodtan dolgozzon ki a **Dinamikus tartalomlista** elemeinek használatával.

   [![Logic Apps "E-mail küldése" kijelölések](./media/tutorial-facilities-events/tutorial-logic-app-send-email.png)](./media/tutorial-facilities-events/tutorial-logic-app-send-email.png#lightbox)

1. Válassza a **Mentés** gombot a **Logikai alkalmazás tervezőjének** ablaktábla tetején.

1. Győződjön meg arról, hogy szimulálja az érzékelő adatait a Digitális twins minta **eszközkapcsolati** mappájában való böngészéssel egy parancsablakban, és fut. `dotnet run`

Néhány perc múlva meg kell kezdeni e-mail értesítések fogadására a Logic Apps erőforrás. 

   [![E-mailes értesítés](./media/tutorial-facilities-events/logic-app-notification.png)](./media/tutorial-facilities-events/logic-app-notification.png#lightbox)

Az e-mailek fogadásának leállításához nyissa meg a Logic Apps erőforrást a portálon, és válassza az **Áttekintő** ablaktáblát. Válassza **a Letiltás**lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezen a ponton le szeretné állítani az Azure Digital Twins felfedezését, nyugodtan törölje az oktatóanyagban létrehozott erőforrásokat:

1. Az [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza az **Összes erőforrás**lehetőséget, válassza ki a Digitális Twins erőforráscsoportot, és válassza a **Törlés**lehetőséget.

    > [!TIP]
    > Ha problémát tapasztalt a Digitális Twins-példány törlésekor, a javítással egy szolgáltatásfrissítés lett elvezetve. Próbálja meg újra kihagyni a példányt.

2. Szükség esetén törölje a mintaalkalmazásokat a munkagépről.

## <a name="next-steps"></a>További lépések

Ha meg szeretné tudni, hogyan jelenítheti meg az érzékelő adatait, hogyan elemezheti a trendeket és a helyszínen lévő anomáliákat, olvassa el a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Digital Twins-terek eseményeinek vizualizációja és elemzése a Time Series Insights használatával](tutorial-facilities-analyze.md)

Az Azure Digital Twins térintelligencia-diagramjairól és objektummodelljeiről is többet tudhat meg:

> [!div class="nextstepaction"]
> [A Digital Twins-objektummodellek és a térbeliintelligencia-diagram ismertetése](concepts-objectmodel-spatialgraph.md)
