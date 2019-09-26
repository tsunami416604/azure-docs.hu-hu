---
title: 'Oktatóanyag: Azure Digital Twins-környezet eseményeinek elemzése | Microsoft Docs'
description: Megtudhatja, hogyan jelenítheti meg és elemezheti az Azure digitális Twins-tárhelyek eseményeit az oktatóanyag lépéseinek használatával Azure Time Series Insightsával.
services: digital-twins
author: alinamstanciu
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/23/2019
ms.author: alinast
ms.openlocfilehash: db62d2209207a807570e971ef4af5f9b10b06cb8
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300063"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-by-using-time-series-insights"></a>Oktatóanyag: Az Azure digitális Twins-tárhelyek eseményeinek megjelenítése és elemzése Time Series Insights használatával

Miután üzembe helyezte az Azure Digital Twins-példányt, kiépítheti a tárhelyeit, és egyéni függvényt valósíthat meg a konkrét feltételek figyeléséhez, megjelenítheti a tárhelyekről érkező eseményeket és az azokban lévő adatokra mutató trendeket és rendellenességeket.

[Az első oktatóanyagban](tutorial-facilities-setup.md)egy képzeletbeli épületben beállította a térbeli gráfot egy olyan helyiséggel, amely mozgásérzékelőket tartalmaz a Motion, a Carbon dioxid és a hőmérséklet számára. A [második oktatóanyagban](tutorial-facilities-udf.md) üzembe helyezte a diagramot és egy felhasználó által meghatározott függvényt. A függvény figyeli ezeket az érzékelő-értékeket és eseményindító-értesítéseket a megfelelő feltételekhez. Ez a hely üres, a hőmérséklet és a szén-dioxid szintje normális.

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Azure Digital Twins-telepítőből érkező értesítéseket és az adatok Azure Time Series Insights használatával. Ezután az érzékelő értékeit az idő múlásával jelenítheti meg. Megkeresheti azokat a trendeket, mint a legtöbbet a legtöbbet, és amelyek a nap legforgalmasabb időpontját használják. Észlelheti a rendellenességeket is, például hogy mely helyiségek stuffier és melegebbek, illetve hogy az épületben lévő területek konzisztensen magas hőmérsékletű értékeket küldenek-e, jelezve a hibás légkondicionálást.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adatfolyamok továbbítása az Azure Event Hubs használatával.
> * Elemzés a Time Series Insights.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már [konfigurálta](tutorial-facilities-setup.md) és [üzembe helyezte](tutorial-facilities-udf.md) az Azure Digital Twins-környezetet. Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik a következőkkel:

- Egy [Azure-fiók](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy futó Digital Twins-példány.
- A munkavégzéshez használt gépre letöltött és kicsomagolt [Digital Twins C#-minták](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- A minta futtatásához [.net Core SDK 2.1.403 vagy újabb verziót](https://www.microsoft.com/net/download) a fejlesztői gépen. A `dotnet --version` futtatásával ellenőrizze, hogy a megfelelő verzió van-e telepítve.

> [!TIP]
> Új példány kiépítés esetén használjon egyedi digitális Twins-példány nevét.

## <a name="stream-data-by-using-event-hubs"></a>Adatfolyam-adatátvitel Event Hubs használatával

Az [Event Hubs](../event-hubs/event-hubs-about.md) szolgáltatással folyamatokat hozhat létre az adatstreamek továbbításához. Ebből a szakaszból megtudhatja, hogyan hozhatja létre az Event hub-összekötőt az Azure digitális ikrek és a Time Series Insights példányok között.

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza az **erőforrás létrehozása**lehetőséget.

1. Keressen rá és válassza ki az **Event Hubs** elemet. Kattintson a **Létrehozás** gombra.

1. Adja meg a Event Hubs névtér **nevét** . Válassza a **standard** lehetőséget az **árképzési szint**, az **előfizetés**, a digitális Twins-példányhoz használt **erőforráscsoport** , valamint a **hely**számára. Kattintson a **Létrehozás** gombra.

1. A Event Hubs névtér üzembe helyezése lapon válassza az **Áttekintés** panelt, majd válassza az **Ugrás az erőforráshoz**lehetőséget.

    [![Névtér Event Hubs az üzembe helyezést követően](./media/tutorial-facilities-analyze/open-event-hub-ns.png)](./media/tutorial-facilities-analyze/open-event-hub-ns.png#lightbox)

1. A Event Hubs névtér **Áttekintés** ablaktábláján kattintson a felül található **Event hub** gombra.
    [![Event hub gomb](./media/tutorial-facilities-analyze/create-event-hub.png)](./media/tutorial-facilities-analyze/create-event-hub.png#lightbox)

1. Adja meg az Event hub **nevét** , majd válassza a **Létrehozás**lehetőséget.

   Az Event hub üzembe helyezése után a Event Hubs névtér **Event Hubs** ablaktábláján **aktív** állapot jelenik meg. Válassza ki ezt az Event hub-t az **Áttekintés** panel megnyitásához.

1. Válassza a Top ( **fogyasztói csoport** ) gombot, és adjon meg egy nevet, például **tsievents** a fogyasztói csoport számára. Kattintson a **Létrehozás** gombra.

    [![Event hub fogyasztói csoport](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)](./media/tutorial-facilities-analyze/event-hub-consumer-group.png#lightbox)

   A fogyasztói csoport létrehozása után megjelenik az Event hub **Áttekintés** paneljének alján található listában.

1. Nyissa meg az Event hub **megosztott hozzáférési szabályzatok** paneljét, és kattintson a **Hozzáadás** gombra. Adja meg a **ManageSend** nevet a szabályzat neveként, győződjön meg róla, hogy az összes jelölőnégyzet be van jelölve, majd válassza a **Létrehozás**lehetőséget.

    [![Event hub kapcsolati karakterláncai](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)](./media/tutorial-facilities-analyze/event-hub-connection-strings.png#lightbox)

1. Nyissa meg a létrehozott ManageSend-szabályzatot, és másolja a **kapcsolódási karakterláncot – elsődleges kulcs** és a **kapcsolódási karakterlánc – másodlagos kulcs** értékét egy ideiglenes fájlba. Ezekre az értékekre szüksége lesz az Event hub végpontjának létrehozásához a következő szakaszban.

### <a name="create-an-endpoint-for-the-event-hub"></a>Végpont létrehozása az Event hub számára

1. Győződjön meg arról, hogy a parancsablakban az Azure digitális Twins minta **Occupancy-quickstart\src** mappájában van.

1. Nyissa meg a szerkesztőben a **actions\createEndpoints.YAML** fájlt. Cserélje le a tartalmát a következő kódra:

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

1. Cserélje le a helyőrzőket `Primary_connection_string_for_your_event_hub` az Event hub **kapcsolati karakterláncának értékére – elsődleges kulcs** . Győződjön meg arról, hogy a következő karakterlánc formátuma a következő:

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Cserélje le a helyőrzőket `Secondary_connection_string_for_your_event_hub` a **kapcsolati sztring – másodlagos kulcs** értékére az Event hub esetében. Győződjön meg arról, hogy a következő karakterlánc formátuma a következő: 

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Cserélje le a helyőrzőket `Name_of_your_Event_Hub` az Event hub nevére.

    > [!IMPORTANT]
    > Az értékeket idézőjelek nélkül adja meg. Győződjön meg arról, hogy legalább egy szóköz karakter szerepel a YAML-fájlban lévő kettőspontok után. A YAML fájl tartalmát bármely online YAML-érvényesítő, például [az eszköz](https://onlineyamltools.com/validate-yaml)használatával is érvényesítheti.

1. Mentse és zárja be a fájlt. Futtassa a következő parancsot a parancssori ablakban, és amikor a rendszer kéri, jelentkezzen be Azure-fiókjával.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Két végpontot hoz létre az Event hub számára.

   [![Event Hubs végpontok](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png#lightbox)

## <a name="analyze-with-time-series-insights"></a>Elemzés a Time Series Insights használatával

1. A [Azure Portal](https://portal.azure.com)bal oldali ablaktábláján válassza az **erőforrás létrehozása**lehetőséget. 

1. Keresse meg és válassza ki a **Time Series Insights** általánosan elérhető (GA) erőforrást. Kattintson a **Létrehozás** gombra.

1. Adja meg a Time Series Insights-példány **nevét**, majd válassza ki az **előfizetését**. Válassza ki a digitális Twins-példányhoz használt **erőforráscsoportot** , valamint a **helyét**. Válassza **a Next (tovább) lehetőséget: Eseményforrás gomb vagy az eseményforrás lap.**

    [![Time Series Insights példány létrehozásához kiválasztott beállítások](./media/tutorial-facilities-analyze/create-tsi.png)](./media/tutorial-facilities-analyze/create-tsi.png#lightbox)

1. Az **eseményforrás** lapon adjon meg egy **nevet**, válassza az **Event hub** lehetőséget a **forrás típusaként**, és győződjön meg arról, hogy a többi érték helyesen van kiválasztva. Válassza az **ManageSend** lehetőséget az **Event hub hozzáférési házirendjének neveként**, majd válassza ki azt a fogyasztói csoportot, amelyet az **Event hub fogyasztói csoportjának**előző szakaszában hozott létre. Válassza az **Áttekintés + létrehozás** lehetőséget.

    [![Az eseményforrás létrehozásához szükséges beállítások](./media/tutorial-facilities-analyze/tsi-event-source.png)](./media/tutorial-facilities-analyze/tsi-event-source.png#lightbox)

1. A **felülvizsgálat + létrehozás** ablaktáblán tekintse át a megadott adatokat, majd válassza a **Létrehozás**lehetőséget.

1. A központi telepítés ablaktáblán válassza ki az imént létrehozott Time Series Insights erőforrást. Ekkor megnyílik a Time Series Insights környezet **Áttekintés** panelje.

1. Válassza az **Ugrás a környezetre** gombra a felső részen. Ha adatelérési figyelmeztetés jelenik meg, nyissa meg az **adatelérési házirendek** panelt Time Series Insights példányához, válassza a **Hozzáadás**lehetőséget, válassza a **közreműködő** szerepkört, és válassza ki a megfelelő felhasználót.

1. A **Ugrás a környezetre** gombra kattintva megnyílik a [Time Series Insights Explorer](../time-series-insights/time-series-insights-explorer.md). Ha nem jelennek meg események, az eszköz eseményeinek szimulálása a digitális Twins **-minta eszköz-kapcsolati** projektjeinek tallózásával és `dotnet run`a futtatásával.

1. Néhány szimulált esemény generálása után térjen vissza a Time Series Insights Explorerrel, és kattintson a felül található frissítés gombra. Meg kell jelennie a szimulált érzékelő adataihoz létrehozott analitikai diagramoknak. 

    [![Diagram a Time Series Insights Explorerben](./media/tutorial-facilities-analyze/tsi-explorer.png)](./media/tutorial-facilities-analyze/tsi-explorer.png#lightbox)

1. A Time Series Insights Explorerben létrehozhat diagramokat és intenzitástérképei a különböző eseményekhez és adatokhoz a szobákból, érzékelőkből és egyéb erőforrásokból. A bal oldalon a **mérték** és a **felosztás** legördülő lista használatával hozhat létre saját vizualizációkat. 

   Válassza például az **események** a **mértékhez** és a **DigitalTwins-SensorHardwareId** a **felosztáshoz**lehetőséget, hogy az egyes érzékelőkhöz hő állítson elő. A hő az alábbi képhez hasonló lesz:

   [![Hő a Time Series Insights Explorerben](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha le szeretné állítani az Azure digitális ikrek felfedezését ezen a ponton kívül, nyugodtan törölheti az oktatóanyagban létrehozott erőforrásokat:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden erőforrás**elemet, válassza ki a digitális Twins erőforráscsoportot, majd válassza a **Törlés**lehetőséget.

    > [!TIP]
    > Ha törli a digitális Twins-példány problémajegyek tapasztal, szolgáltatás frissítése lett állítva a javítás. Ismételje meg a példány törlése.

2. Ha szükséges, törölje a mintául szolgáló alkalmazásokat a munkahelyi gépen.

## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan lehet többet megtudni a térbeli intelligencia gráfokról és az Azure Digital Twins-beli objektummodell-modellekről.

> [!div class="nextstepaction"]
> [A Digital Twins-objektummodellek és a térbeliintelligencia-diagram ismertetése](concepts-objectmodel-spatialgraph.md)
