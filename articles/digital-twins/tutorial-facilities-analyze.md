---
title: Azure Digital Twins-környezet eseményeinek elemzése | Microsoft Docs
description: Az oktatóanyag lépéseit követve megtanulhatja, hogyan jelentheti meg és elemezheti az Azure Digital Twins-terek eseményeit az Azure Time Series Insights használatával.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 1366cafe5d2c526e86905c108b9c7b865aac8f69
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323300"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-using-time-series-insights"></a>Oktatóanyag: Az Azure Digital Twins-terek eseményeinek megjelenítése és elemzése a Time Series Insights használatával

Miután üzembe helyezte az Azure Digital Twins-példányt, kiépítette a tereket, és megvalósított egyéni függvényeket adott feltételek monitorozására, a terekből beérkező események és adatok megjelenítésével keresheti a trendeket és a rendellenességeket. 

Az [első oktatóanyagban](tutorial-facilities-setup.md) egy képzeletbeli épület térbeli diagramját konfigurálta, amelynek egyik helyiségében mozgás-, széndioxid- és hőmérséklet-érzékelők voltak. A [második oktatóanyagban](tutorial-facilities-udf.md) üzembe helyezte a diagramot és egy felhasználó által meghatározott függvényt. A függvény az érzékelők értékeit monitorozza, és értesítéseket aktivál a megfelelő feltételek teljesülése esetén, azaz ha egy helyiség üres, és a hőmérséklet és a szén-dioxid szintje elfogadható. Ez az oktatóanyag bemutatja, hogyan integrálhatja a Digital Twins-környezetből érkező értesítéseket és adatokat az Azure Time Series Insightsszal. Ezután megjelenítheti az érzékelők értékeit az időben, és trendeket kereshet, például hogy melyik helyiséget használják a legtöbbet, melyik a legforgalmasabb napszak és hasonlók. Emellett a rendellenességeket is észlelheti, például hogy melyik helyiségek fülledtebbek vagy melegebbek, vagy hogy az épület mely területein magasabb mindig a hőmérséklet, ami a légkondicionálás meghibásodására utalhat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adatok streamelése az Event Hubs használatával
> * Elemzés a Time Series Insights használatával

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már [konfigurálta](tutorial-facilities-setup.md) és [üzembe helyezte](tutorial-facilities-udf.md) az Azure Digital Twins-környezetet. Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik a következőkkel:
- Egy [Azure-fiók](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy futó Digital Twins-példány.
- A munkavégzéshez használt gépre letöltött és kicsomagolt [Digital Twins C#-minták](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- A [.NET Core SDK 2.1.403-as vagy újabb](https://www.microsoft.com/net/download) verziója a fejlesztői gépen a minta futtatására. A `dotnet --version` parancs futtatásával ellenőrizze, hogy a megfelelő verzió van-e telepítve. 


## <a name="stream-data-using-event-hubs"></a>Adatok streamelése az Event Hubs használatával
Az [Event Hubs](../event-hubs/event-hubs-about.md) szolgáltatással folyamatokat hozhat létre az adatok streamelésére. Ebben a részben bemutatjuk, hogyan hozhat létre egy eseményközpontot, amely összekötőként szolgál a Digital Twins- és a TSI-példány között.

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. A bal oldali navigációs panelen kattintson az **Erőforrás létrehozása** elemre. 

1. Keressen rá és válassza ki az **Event Hubs** elemet. Kattintson a **Create** (Létrehozás) gombra.

1. Adja meg az Event Hubs-névtér **nevét**, válassza a *Standard* **tarifacsomagot**, válassza ki az **előfizetést**, a Digital Twins-példányhoz használt **erőforráscsoportot**, valamint a **helyet**. Kattintson a **Create** (Létrehozás) gombra. 

1. Miután üzembe helyezte, lépjen az Event Hubs-névtér *üzemelő példányára*, és kattintson a névtérre az **ERŐFORRÁS** alatt.

    ![Event Hubs-névtér](./media/tutorial-facilities-analyze/open-event-hub-ns.png)


1. Az Event Hubs-névtérben kattintson az **Áttekintés** panelre, majd felül az **Eseményközpont** gombra. 
    ![Event Hub](./media/tutorial-facilities-analyze/create-event-hub.png)

1. Adja meg az eseményközpont **nevét**, és kattintson a **Létrehozás** gombra. Miután üzembe helyezte az eseményközpontot, az megjelenik az Event Hubs-névtér **Eseményközpontok** panelén *Aktív* **ÁLLAPOTTAL**. Az eseményközpontra kattintva nyissa meg annak **Áttekintés** paneljét.

1. Kattintson felül a **Fogyasztói csoport** gombra, és adjon meg egy nevet a fogyasztói csoport számára, például *tsievents*. Kattintson a **Create** (Létrehozás) gombra.
    ![Eseményközpont fogyasztói csoportja](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   Miután létrejött a fogyasztói csoport, megjelenik az eseményközpont **Áttekintés** panelének alján lévő listában. 

1. Nyissa meg az eseményközpont **Megosztott elérési szabályzatok** panelét, és kattintson a **Hozzáadás** gombra. **Hozzon létre** egy szabályzatot *ManageSend* néven, és jelölje be mindegyik jelölőnégyzetet. 

    ![Eseményközpont kapcsolati sztringjei](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. Nyissa meg az imént létrehozott *ManageSend* szabályzatot, és másolja a **Kapcsolati sztring – elsődleges kulcs** és a **Kapcsolati sztring – másodlagos kulcs** értékeit egy ideiglenes fájlba. Ezekre az értékekre az eseményközpont végpontjának létrehozásához lesz szükség a következő részben.

### <a name="create-endpoint-for-the-event-hub"></a>Eseményközpont végpontjának létrehozása

1. A parancsablakban ellenőrizze, hogy a Digital Twins-minta **_occupancy-quickstart\src** mappájában van-e.

1. Nyissa meg az **_actions\createEndpoints.yaml_** fájlt egy szerkesztőben. Cserélje le a tartalmát a következő kódra:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    ```

1. Cserélje le a `Primary_connection_string_for_your_event_hub` helyőrzőket az eseményközpont **Kapcsolati sztring – elsődleges kulcs** értékére. Ügyeljen rá, hogy a kapcsolati sztring a következő formátumban legyen megadva:
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
```

1. Cserélje le a `Secondary_connection_string_for_your_event_hub` helyőrzőket az eseményközpont **Kapcsolati sztring – másodlagos kulcs** értékére. Ügyeljen rá, hogy a kapcsolati sztring a következő formátumban legyen megadva: 
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
```

1. Cserélje le a `Name_of_your_Event_Hubs_namespace` helyőrzőket az Event Hubs-névtér nevére.

    > [!IMPORTANT]
    > Az értékeket idézőjelek nélkül adja meg. A *YAML*-fájlban minden kettőspont után egy szóköz karaktert kell írni. A *YAML*-fájl tartalmának érvényességét bármely online YAML-ellenőrző eszközzel ellenőrizheti, például [ezzel az eszközzel](https://onlineyamltools.com/validate-yaml).


1. Mentse és zárja be a fájlt. Futtassa a következő parancsot a parancssori ablakban, és amikor a rendszer kéri, jelentkezzen be Azure-fiókjával.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```
   
   A parancs két végpontot hoz létre az eseményközponthoz.

   ![Az Event Hubs végpontjai](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Elemzés a Time Series Insights használatával

1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs paneljén kattintson az **Erőforrás létrehozása** gombra. 

1. Keressen a **Time Series Insights** kifejezésre, és válasszon ki egy új Time Series Insights-erőforrást. Kattintson a **Create** (Létrehozás) gombra.

1. Adja meg a Time Series Insights-példány **nevét**, majd válassza ki az **előfizetését**. Válassza ki a Digital Twins-példányhoz használt **erőforráscsoportot**, valamint a **helyet**. Kattintson a **Create** (Létrehozás) gombra.

    ![TSI létrehozása](./media/tutorial-facilities-analyze/create-tsi.png)

1. Miután üzembe helyezte a Time Series Insights-környezetet, nyissa meg, majd nyissa meg a környezet **Eseményforrások** panelét is. Kattintson felül a **Hozzáadás** gombra egy fogyasztói csoport hozzáadásához.

1. Az **Új eseményforrás** panelen adja meg a **nevet**, és ellenőrizze, hogy a többi érték megfelelően van-e beállítva. Az **Eseményközpont szabályzatának neve** mezőben válassza a *ManageSend* lehetőséget, majd az **Eseményközpont fogyasztói csoport** mezőben válassza az előző szakaszban létrehozott *fogyasztói csoportot*. Kattintson a **Create** (Létrehozás) gombra.

    ![TSI-eseményforrás](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. Nyissa meg a Time Series Insights-környezet **Áttekintés** panelét, és kattintson felül az **Ugrás a környezethez** gombra. Ha megjelenik egy *adat-hozzáférési figyelmeztetés*, nyissa meg a TSI-példány **Adathozzáférési szabályzatok** panelét, kattintson a **Hozzáadás** gombra, válassza a **Közreműködő** szerepkört, és válassza ki a megfelelő felhasználót.

1. Az **Ugrás a környezethez** gombra kattintva megnyílik a [Time Series Insights Explorer](../time-series-insights/time-series-insights-explorer.md). Ha nem jelennek meg benne események, eszközesemények szimulálásához lépjen a Digital Twins-minta **_device-connectivity_** projektjére, és futtassa a `dotnet run` parancsot.

1. Miután néhány szimulált esemény létrejött, lépjen vissza a Time Series Insights Explorerre, és kattintson felül a Frissítés gombra. Meg kell jelennie a szimulált érzékelőadatokhoz létrehozott elemzési diagramoknak. 

    ![TSI Explorer](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. A Time Series Explorerben ezután létrehozhat diagramokat és intenzitástérképeket a helyiségek, érzékelők és egyéb erőforrások különböző eseményeire és adataira vonatkozóan. A bal oldalon a **MÉRTÉK** és a **FELOSZTÁSI SZEMPONT** legördülő mezőkre kattintva saját megjelenítéseket hozhat létre. Például a **MÉRTÉK** mezőben az *Események*, a **FELOSZTÁSI SZEMPONT** mezőben pedig a *DigitalTwins-SensorHardwareId* lehetőséget kiválasztva egy, az alábbihoz hasonló intenzitástérképet hozhat létre az egyes érzékelőkhöz:

    ![TSI Explorer](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha most be szeretné fejezni az Azure Digital Twins megismerését, nyugodtan törölje az ebben az oktatóanyagban létrehozott erőforrásokat:

1. Az [Azure Portal](http://portal.azure.com) bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, válassza ki a Digital Twins-erőforráscsoportot, és **törölje**.
2. Szükség esetén a mintaalkalmazásokat is törölheti a munkavégzéshez használt gépről. 


## <a name="next-steps"></a>További lépések

A következő cikkből részletesebben megismerheti az Azure Digital Twins térbeliintelligencia-diagramjait és objektummodelljeit. 
> [!div class="nextstepaction"]
> [A Digital Twins-objektummodellek és a térbeliintelligencia-diagram ismertetése](concepts-objectmodel-spatialgraph.md)

