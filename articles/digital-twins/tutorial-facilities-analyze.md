---
title: 'Oktatóanyag: Azure Digital Twins-környezet eseményeinek elemzése | Microsoft Docs'
description: Ismerje meg, hogyan megjelenítését és elemzését az Azure digitális Twins szóközt, az Azure Time Series Insights, az események ebben az oktatóanyagban szereplő lépések segítségével.
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: f233efc93fa07cc7fc7c904336f01348f4da3f82
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554520"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-by-using-time-series-insights"></a>Oktatóanyag: Megjelenítését és elemzését az Azure digitális Twins tárolóhelyek események a Time Series Insights használatával

Miután az Azure digitális Twins-példány üzembe helyezése, a tárolóhelyek üzembe helyezése, és figyelheti az egyes esetekben egy egyéni függvény végrehajtása, az események és a tárolóhelyek a trendek és rendellenességek kereséséhez származó adatokat jelenítheti meg. 

A [első oktatóanyaga](tutorial-facilities-setup.md), konfigurálta a térbeli grafikonon egy képzeletbeli épület, az a hely, amely tartalmazza a mozgásban lévő adatoknak egyaránt, szén-dioxid- és hőmérséklet érzékelők. A [második oktatóanyagban](tutorial-facilities-udf.md) üzembe helyezte a diagramot és egy felhasználó által meghatározott függvényt. A függvény ezen érzékelő értékek figyeli, és elindítja a megfelelő feltételek értesítések. A helyiségben, üres, és a hőmérséklet és szén-dioxid-szinten nem rendellenes. 

Ez az oktatóanyag bemutatja, hogyan integrálhatja az értesítések és az Azure digitális Twins beállítása az Azure Time Series Insights származó adatokat. Idővel majd jelenítheti meg az érzékelő értékeket. Trendek kereshet például melyik szoba kezd a legtöbb használatát, és amelyek a nap legforgalmasabb időpontok. Is észlelheti a rendellenességeket, mely termek stuffier és hotter miatt, vagy egy adott területre az épület küld e folyamatosan magas hőmérsékleti értékek forrása, például hibás légkondicionálást jelzi.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Stream-adatok az Azure Event Hubs használatával.
> * Time Series insights elemzése.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már [konfigurálta](tutorial-facilities-setup.md) és [üzembe helyezte](tutorial-facilities-udf.md) az Azure Digital Twins-környezetet. Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik a következőkkel:
- Egy [Azure-fiók](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy futó Digital Twins-példány.
- A munkavégzéshez használt gépre letöltött és kicsomagolt [Digital Twins C#-minták](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- [.NET core SDK 2.1.403 verzió vagy újabb](https://www.microsoft.com/net/download) a fejlesztői gépen, a minta futtatásához. Futtatás `dotnet --version` , győződjön meg arról, hogy telepítve van-e a megfelelő verziót. 


## <a name="stream-data-by-using-event-hubs"></a>Stream-adatokat az Event Hubs használatával
Használhatja a [az Event Hubs](../event-hubs/event-hubs-about.md) szolgáltatás létrehoz egy folyamatot az adatok továbbításához. Ez a szakasz bemutatja, hogyan hozhat létre az event hub, az összekötő az Azure digitális Ikrekhez és a Time Series Insights-példányok között.

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza ki a **erőforrás létrehozása**. 

1. Keressen rá és válassza ki az **Event Hubs** elemet. Kattintson a **Létrehozás** gombra.

1. Adjon meg egy **neve** az Event Hubs-névtér esetében. Válasszon **Standard** a **tarifacsomag**, a **előfizetés**, a **erőforráscsoport** a digitális Twins példány használt és a **hely**. Kattintson a **Létrehozás** gombra. 

1. Az Event Hubs névtér központi telepítés esetén válassza ki a névtér **erőforrás**.

    ![Event Hubs-névtér üzembe helyezés után](./media/tutorial-facilities-analyze/open-event-hub-ns.png)


1. Az Event Hubs-névtér **áttekintése** panelen válassza a **Eseményközpont** gombra az oldal tetején. 
    ![Event Hub gomb](./media/tutorial-facilities-analyze/create-event-hub.png)

1. Adjon meg egy **neve** az eseményközpont, és válassza ki a **létrehozás**. 

   Az event hubs üzembe helyezését követően megjelenik a **az Event Hubs** az Event Hubs-névtér ablakában egy **aktív** állapotát. Az eseményközpont megnyitásához válassza a **áttekintése** ablaktáblán.

1. Válassza ki a **fogyasztói csoportot** gombra az oldal tetején, és írjon be egy nevet, például **tsievents** a felhasználói csoport. Kattintson a **Létrehozás** gombra.
    ![Eseményközpont fogyasztói csoportja](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   A fogyasztói csoport létrehozása után megjelenik a lista alján, az event hubs **áttekintése** ablaktáblán. 

1. Nyissa meg a **megosztott elérési házirendek** panelje az eseményközpont, és válassza ki a **Hozzáadás** gombra. Adja meg **ManageSend** a szabályzat neveként, ellenőrizze, hogy az összes jelölőnégyzet legyen bejelölve, majd válassza ki **létrehozás**. 

    ![Eseményközpont kapcsolati sztringjei](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. Nyissa meg a létrehozott ManageSend szabályzatot, és másolja az értékeket a **kapcsolati karakterlánc – elsődleges kulcs** és **kapcsolati karakterlánc – másodlagos kulcs** egy ideiglenes fájlba. Ezeket az értékeket egy végpontot az eseményközpont létrehozásához a következő szakaszban lesz szüksége.

### <a name="create-an-endpoint-for-the-event-hub"></a>Event hub-végpont létrehozása

1. A parancssori ablakba, ellenőrizze, hogy használja-e a **foglaltsága-quickstart\src** az Azure digitális Twins minta mappát.

1. Nyissa meg a fájlt **actions\createEndpoints.yaml** a saját szerkesztőben. Cserélje le a tartalmát a következő kódra:

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

1. Cserélje le a zárójelben `Primary_connection_string_for_your_event_hub` értékét **kapcsolati karakterlánc – elsődleges kulcs** az event hubs számára. Győződjön meg arról, hogy ez a kapcsolati karakterlánc formátuma a következő:

   ```
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Cserélje le a zárójelben `Secondary_connection_string_for_your_event_hub` értékét **kapcsolati karakterlánc – másodlagos kulcs** az event hubs számára. Győződjön meg arról, hogy ez a kapcsolati karakterlánc formátuma a következő: 

   ```
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Cserélje le a `Name_of_your_Event_Hubs_namespace` helyőrzőket az Event Hubs-névtér nevére.

    > [!IMPORTANT]
    > Az értékeket idézőjelek nélkül adja meg. Ellenőrizze, hogy van legalább egy szóköz karakter után a kettőspont a YAML-fájlt. Minden online YAML-érvényesítő használatával is ellenőrizheti a YAML-fájl tartalmának [ezzel az eszközzel](https://onlineyamltools.com/validate-yaml).


1. Mentse és zárja be a fájlt. Futtassa a következő parancsot a parancssori ablakban, és amikor a rendszer kéri, jelentkezzen be Azure-fiókjával.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```
   
   Az eseményközpont két végpontot hoz létre.

   ![Az Event Hubs végpontjai](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Elemzés a Time Series Insights használatával

1. A bal oldali panelen, a [az Azure portal](https://portal.azure.com)válassza **erőforrás létrehozása**. 

1. Keressen a **Time Series Insights** kifejezésre, és válasszon ki egy új Time Series Insights-erőforrást. Kattintson a **Létrehozás** gombra.

1. Adja meg a Time Series Insights-példány **nevét**, majd válassza ki az **előfizetését**. Válassza ki a **erőforráscsoport** a digitális Twins példány használt és a **hely**. Kattintson a **Létrehozás** gombra.

    ![A Time Series Insights-példányok létrehozását szolgáló kiválasztások](./media/tutorial-facilities-analyze/create-tsi.png)

1. A példány üzembe helyezését követően nyissa meg a Time Series Insights-környezetet, és nyissa meg a **eseményforrások** ablaktáblán. Válassza ki a **Hozzáadás** tetején egy felhasználói csoport hozzáadása gombot.

1. Az **Új eseményforrás** panelen adja meg a **nevet**, és ellenőrizze, hogy a többi érték megfelelően van-e beállítva. Válassza ki **ManageSend** a **eseményközpont szabályzatának neve**, majd válassza ki a fogyasztói csoportot, amely az előző szakaszban létrehozott **eseményközpontbeli fogyasztói csoport**. Kattintson a **Létrehozás** gombra.

    ![Eseményforrás létrehozása szolgáló kiválasztások](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. Nyissa meg a **áttekintése** a Time Series Insights-környezethez, és válassza a panel a **Ugrás a környezet** gombra az oldal tetején. Ha megjelenik egy figyelmeztetés adatok elérése, nyissa meg a **az adathozzáférési házirendek** panelen a Time Series Insights-példány, válassza **hozzáadása**, jelölje be **közreműködői** szerepkört, és válassza ki a megfelelő felhasználó.

1. A **Ugrás a környezet** gombra kattintva megnyílik a [Time Series Insights explorer](../time-series-insights/time-series-insights-explorer.md). Bármely esemény nem jelenik meg, ha a navigáljon az eszköz események szimulálása a **eszközkapcsolattal** digitális Twins minta, és futó projekt `dotnet run`.

1. Néhány szimulált események generálása után térjen vissza a Time Series Insights explorer, és kattintson a frissítés gombra az oldal tetején. Analitikus diagramok hoz létre a szimulált érzékelőadatok kell megjelennie. 

    ![A Time Series Insights Explorer mutató részletes diagram](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. A Time Series Insights explorer majd létrehozhat diagramokat és intenzitástérképek különböző események és az adatok a a termek eszközök, érzékelők és más erőforrások. A bal oldalon, használja a **MÉRTÉK** és **felosztás által** legördülő listák hozhatja létre saját vizualizációkat. 

   Válassza ki például **események** a **MÉRTÉK** és **DigitalTwins-SensorHardwareId** a **felosztás által**kattintva létrehozhat egy intenzitástérkép minden az érzékelők. Az intenzitástérkép, az alábbi képhez hasonló lesz:

   ![A Time Series Insights Explorer Intenzitástérkép](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az Azure digitális Twins felfedezése a túlra le szeretné, nyugodtan törölje az ebben az oktatóanyagban létrehozott erőforrásokat:

1. A bal oldali menüben lévő a [az Azure portal](http://portal.azure.com)válassza **összes erőforrás**, a digitális Twins erőforráscsoportot, majd válassza ki és **törlése**.

    > [!TIP]
    > Ha törli a digitális Twins-példány problémajegyek tapasztal, szolgáltatás frissítése lett állítva a javítás. Ismételje meg a példány törlése.

2. Ha szükséges, törölje a munkahelyi számítógépen mintaalkalmazásból. 


## <a name="next-steps"></a>További lépések

Nyissa meg a következő cikkben tájékozódhat a térbeli intelligencia grafikonokkal és az Azure digitális Twins objektummodellt. 
> [!div class="nextstepaction"]
> [A Digital Twins-objektummodellek és a térbeliintelligencia-diagram ismertetése](concepts-objectmodel-spatialgraph.md)

