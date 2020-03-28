---
title: 'Oktatóanyag: Események elemzése a Time Series Insights ban – Azure Digital Twins| Microsoft dokumentumok'
description: Ismerje meg, hogyan vizualizálhatja és elemezheti az Azure Digital Twins terekből származó eseményeket az Azure Time Series Insights segítségével az oktatóanyag lépéseivel.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 38bd1755ed87050cf8b91a0a82f6e5f1d2af9db5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75933883"
---
# <a name="tutorial-visualize-and-analyze-events-from-azure-digital-twins-by-using-time-series-insights"></a>Oktatóanyag: Vizualizálhatja és elemezheti az Azure Digital Twins eseményeit a Time Series Insights használatával

Miután üzembe helyezte az Azure Digital Twins-példányt, kiépítheti a tereket, és egyéni függvényt valósított meg a meghatározott feltételek figyelésére, a trendek és anomáliák megkeresése érdekében vizualizálhatja a terekből érkező eseményeket és adatokat.

Az [első bemutatóban](tutorial-facilities-setup.md)egy képzeletbeli épület térbeli grafikonját állította be, egy olyan szobával, amely mozgásérzékelőket, szén-dioxidot és hőmérsékletet tartalmaz. A [második oktatóanyagban](tutorial-facilities-udf.md) üzembe helyezte a diagramot és egy felhasználó által meghatározott függvényt. A függvény figyeli ezeket az érzékelőértékeket, és a megfelelő feltételekről értesítéseket jelenít meg. Vagyis a szoba üres, és a hőmérséklet és a szén-dioxid szintje normális.

Ez az oktatóanyag bemutatja, hogyan integrálhatja az Azure Digital Twins telepítőjéből származó értesítéseket és adatokat az Azure Time Series Insights szolgáltatással. Ezután az érzékelő értékeit idővel megjelenítheti. Meg lehet keresni trendek, mint például melyik szobában kezd a legtöbb használat, és amelyek a legforgalmasabb alkalommal a nap. Észlelheti az olyan anomáliákat is, mint például, hogy mely szobák érzik magukat fülledtebbnek és melegebbnek, vagy hogy az épület egy része következetesen magas hőmérsékleti értékeket küld-e, jelezve a hibás légkondicionálót.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adatok streamelése az Azure Event Hubs használatával.
> * Elemezze a Time Series Insights segítségével.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már [konfigurálta](tutorial-facilities-setup.md) és [üzembe helyezte](tutorial-facilities-udf.md) az Azure Digital Twins-környezetet. Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik a következőkkel:

- Egy [Azure-fiók](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy futó Digital Twins-példány.
- A munkavégzéshez használt gépre letöltött és kicsomagolt [Digital Twins C#-minták](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- [A .NET Core SDK 2.1.403-as vagy újabb verziója](https://www.microsoft.com/net/download) a fejlesztőgépen a minta futtatásához. Futtassa `dotnet --version` a megfelelő verzió telepítésének ellenőrzéséhez.

> [!TIP]
> Használjon egy egyedi digital Twins-példány nevét, ha új példányt létesít.

## <a name="stream-data-by-using-event-hubs"></a>Adatok streamelése az Eseményközpontok használatával

Az Event [Hubs](../event-hubs/event-hubs-about.md) szolgáltatás segítségével létrehozhat egy folyamatot az adatok streameléséhez. Ez a szakasz bemutatja, hogyan hozhat létre az eseményközpontot az Azure Digital Twins és a Time Series Insights-példányok közötti összekötőként.

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A bal oldali panelen válassza az **Erőforrás létrehozása** elemet.

1. Keressen rá és válassza ki az **Event Hubs** elemet. Kattintson a **Létrehozás** gombra.

    [![Eseményközpontok névterének létrehozása](./media/tutorial-facilities-analyze/tutorial-create-event-hubs.png)](./media/tutorial-facilities-analyze/tutorial-create-event-hubs.png#lightbox)

1. Adja meg az Event Hubs **névterének nevét.** Válassza a **Standard** for **Pricing tier**, **az Előfizetés**, a Digitális Twins-példányhoz használt **erőforráscsoportot** és a **Helyet.** Kattintson a **Létrehozás** gombra.

1. Az Event Hubs névtér telepítésében válassza az **Áttekintő** ablaktáblát, majd az **Ugrás az erőforrásra**lehetőséget.

    [![Eseményközpontok névtere a telepítés után](./media/tutorial-facilities-analyze/tutorial-event-hub-ns.png)](./media/tutorial-facilities-analyze/tutorial-event-hub-ns.png#lightbox)

1. Az Eseményközpontok névtér **áttekintése** ablaktáblán válassza az **Eseményközpont** gombot a tetején.
    [![Az Eseményközpont gomb](./media/tutorial-facilities-analyze/tutorial-create-event-hub.png)](./media/tutorial-facilities-analyze/tutorial-create-event-hub.png#lightbox)

1. Adja meg az eseményközpont **nevét,** és válassza a **Létrehozás gombot.**

   Az eseményközpont üzembe helyezése után megjelenik az Eseményközpontok névtér **Aktív** állapotú Eseményközpontok névtér **ének eseményközpontok** ablaktáblájában. Válassza ezt az eseményközpontot az **Áttekintő** ablaktábla megnyitásához.

1. Válassza a **fogyasztói csoport** gombot a tetején, és adjon meg egy nevet, például **tsievents** a fogyasztói csoport. Kattintson a **Létrehozás** gombra.

    [![Az Event Hub fogyasztói csoportja](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)](./media/tutorial-facilities-analyze/event-hub-consumer-group.png#lightbox)

   A fogyasztói csoport létrehozása után megjelenik az eseményközpont **áttekintő** ablaktábla alján található listában.

1. Nyissa meg az eseményközpont **Megosztott hozzáférési házirendek** ablaktábláját, és kattintson a **Hozzáadás** gombra. Adja meg a **ManageSend** értéket házirendnévként, győződjön meg arról, hogy az összes jelölőnégyzet be van jelölve, és válassza a **Létrehozás gombot.**

    [![Eseményközpont kapcsolati sztringjei](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)](./media/tutorial-facilities-analyze/event-hub-connection-strings.png#lightbox)

    > [!TIP]
    > Ellenőrizze, hogy a névtér helyett az eseményközpont-példányhoz hoz-e létre SAS-házirendet.

1. Nyissa meg a létrehozott **ManageSend** házirendet, és másolja a **Kapcsolati karakterlánc – elsődleges kulcs** és **kapcsolati karakterlánc – másodlagos kulcsának** másodlagos kulcsát egy ideiglenes fájlba. Ezekre az értékekre szüksége lesz egy végpont létrehozásához az eseményközpont hoz létre a következő szakaszban.

### <a name="create-an-endpoint-for-the-event-hub"></a>Végpont létrehozása az eseményközponthoz

1. A parancsablakban győződjön meg arról, hogy az Azure Digital Twins minta **használt-gyorsstart\src** mappájában van.

1. Nyissa meg az **actions\createEndpoints.yaml** fájlt egy szerkesztőben. Cserélje le a tartalmát a következő kódra:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    ```

1. Cserélje le `Primary_connection_string_for_your_event_hub` a helyőrzőket a **Kapcsolati karakterlánc értékére –** az eseményközpont elsődleges kulcsára. Győződjön meg arról, hogy a kapcsolati karakterlánc formátuma a következő:

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Cserélje le `Secondary_connection_string_for_your_event_hub` a helyőrzőket a **Kapcsolati karakterlánc értékére –** az eseményközpont másodlagos kulcsára. Győződjön meg arról, hogy a kapcsolati karakterlánc formátuma a következő: 

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Cserélje le `Name_of_your_Event_Hub` a helyőrzőket az Event Hub nevére.

    > [!IMPORTANT]
    > Az értékeket idézőjelek nélkül adja meg. Győződjön meg arról, hogy a YAML-fájlban lévő kettőspontok után legalább egy szóköz található. A YAML-fájl tartalmát bármely online YAML-érvényesítővel, például [ezzel az eszközzel](https://onlineyamltools.com/validate-yaml)is ellenőrizheti.

1. Mentse és zárja be a fájlt. Futtassa a következő parancsot a parancssori ablakban, és amikor a rendszer kéri, jelentkezzen be Azure-fiókjával.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Két végpontot hoz létre az eseményközponthoz.

   [![Az Event Hubs végpontjai](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png#lightbox)

## <a name="analyze-with-time-series-insights"></a>Elemzés a Time Series Insights használatával

1. Az [Azure Portal](https://portal.azure.com)bal oldali ablaktáblájában válassza az **Erőforrás létrehozása**lehetőséget. 

1. Keressen és válasszon ki egy **Általános** rendelkezésre állási idejű (GA) erőforrást. Kattintson a **Létrehozás** gombra.

1. Adja meg a Time Series Insights-példány **nevét**, majd válassza ki az **előfizetését**. Válassza ki a digitális twins-példányhoz használt **erőforráscsoportot** és a **tartózkodási helyét.** Válassza a **Tovább: Eseményforrás** vagy az **Eseményforrás** lapot.

    [![A Time Series Insights-példány okainak létrehozására szolgáló kijelölések](./media/tutorial-facilities-analyze/tutorial-create-tsi-environment.png)](./media/tutorial-facilities-analyze/tutorial-create-tsi-environment.png#lightbox)

1. Az **Eseményforrás** lapon adjon meg egy **Nevet**, válassza az **Eseményközpont** lehetőséget **forrástípusként**, és győződjön meg arról, hogy a többi érték megfelelően van kiválasztva a létrehozott eseményközpontra való hivatkozáshoz. Válassza a **ManageSend** for **Event Hub hozzáférési házirend nevét**, majd válassza ki az Event Hub fogyasztói **csoport**előző szakaszában létrehozott fogyasztói csoportot. Válassza az **Áttekintés + létrehozás** lehetőséget.

    [![Kijelölés eseményforrás létrehozásához](./media/tutorial-facilities-analyze/tutorial-tsi-event-source.png)](./media/tutorial-facilities-analyze/tutorial-tsi-event-source.png#lightbox)

1. A **Véleményezés + Létrehozás** ablaktáblában tekintse át a beírt adatokat, és válassza a **Létrehozás gombot.**

1. A központi telepítés ablaktáblán válassza ki a létrehozott Time Series Insights erőforrást. Megnyitja a Time Series Insights környezet **áttekintő** ablaktábláját.

1. A lap tetején válassza az Ugrás a **környezetre** gombot. Ha adatelérési figyelmeztetést kap, nyissa meg az **Adatelérési szabályzatok** ablaktáblát a Time Series Insights-példányhoz, válassza a **Hozzáadás**lehetőséget, válassza a **Közreműködő** szerepkört, és válassza ki a megfelelő felhasználót.

1. A **"Környezeti információ megnyitása"** gomb megnyitja a [Time Series Insights felfedezőt.](../time-series-insights/time-series-insights-explorer.md) Ha nem jelenít meg eseményeket, szimulálja az eszközeseményeket a Digital Twins minta `dotnet run` **eszközkapcsolati** projektjének böngészésével és a futással.

1. Néhány szimulált esemény létrehozása után lépjen vissza a Time Series Insights-kezelőhöz, és válassza a frissítés gombot a tetején. Megjelennek a szimulált érzékelőadatokhoz létrehozott analitikus diagramok. 

    [![Diagram a Time Series Insights-kezelőben](./media/tutorial-facilities-analyze/tsi-explorer-with-adt-telemetry.png)](./media/tutorial-facilities-analyze/tsi-explorer-with-adt-telemetry.png#lightbox)

1. A Time Series Insights felfedezőben ezután diagramokat és hőtérképeket hozhat létre a szobákból, érzékelőkből és egyéb erőforrásokból származó különböző eseményekhez és adatokhoz. A bal oldalon a **MÉRTÉK** és a **SPLIT BY** legördülő menüvel hozhatja létre saját vizualizációit. 

   Válassza ki például **az Események** a **MEASURE** és **a DigitalTwins-SensorHardwareId** a **SPLIT BY**lehetőséget, hogy az egyes érzékelőkhez hőtérképet hozzon létre. A hőtérkép hasonló lesz a következő képhez:

   [![Hőtérkép a Time Series Insights felfedezőjében](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezen a ponton túl nem szeretné tovább vizsgálni az Azure Digital Twins-eket, törölje az oktatóanyagban létrehozott erőforrásokat:

1. Az [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza az **Összes erőforrás**lehetőséget, válassza ki a Digitális Twins erőforráscsoportot, majd a **Törlés parancsot.**

    > [!TIP]
    > Ha problémát tapasztalt a Digitális Twins-példány törlésekor, a javítással egy szolgáltatásfrissítés lett elvezetve. Próbálja meg újra kihagyni a példányt.

2. Szükség esetén törölje a mintaalkalmazásokat a munkagépről.

## <a name="next-steps"></a>További lépések

A következő cikkből többet is megtudhat az Azure Digital Twins térbeli intelligenciagrafikonjairól és objektummodelljeiről.

> [!div class="nextstepaction"]
> [A Digital Twins-objektummodellek és a térbeliintelligencia-diagram ismertetése](concepts-objectmodel-spatialgraph.md)
