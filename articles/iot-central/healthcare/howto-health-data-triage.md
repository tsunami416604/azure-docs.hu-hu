---
title: Health adatosztályozási irányítópult létrehozása az Azure IoT Central | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Health adatosztályozási irányítópultot az Azure IoT Central alkalmazás-sablonok használatával.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: ed06aef4d494fbdce5a07c5bc50bad9737ba5433
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86497046"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Oktatóanyag: Power BI-szolgáltató irányítópultjának összeállítása



A folyamatos beteg-figyelési megoldás létrehozásakor létrehozhat egy irányítópultot a kórházi ápolási csapat számára a páciensek adatainak megjelenítéséhez. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Power BI valós idejű adatfolyam-irányítópultot a IoT Central folyamatos beteg-figyelési alkalmazás sablonból. Ha a használati eset nem igényli a valós idejű adatelérést, használhatja a [IoT Central Power bi irányítópultot](../core/howto-connect-powerbi.md), amely egyszerűsített telepítési folyamattal rendelkezik. 

>[!div class="mx-imgBorder"]
>![Irányítópult GIF](media/dashboard-gif-3.gif)

Az alapszintű architektúra a következő struktúrát fogja követni:

>[!div class="mx-imgBorder"] 
>![Szolgáltató osztályozása irányítópult](media/dashboard-architecture.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adatok exportálása az Azure IoT Centralból az Azure-ba Event Hubs
> * Power BI folyamatos átviteli adatkészlet beállítása
> * A logikai alkalmazás összekötése az Azure Event Hubs
> * Stream-adatok Power BI a logikai alkalmazásból
> * Valós idejű irányítópult létrehozása a páciensek számára – létfontosságú

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy Azure IoT Central folyamatos beteg monitorozási alkalmazás sablonja. Ha még nem rendelkezik ilyennel, kövesse az [alkalmazás-sablonok üzembe helyezésének](overview-iot-central-healthcare.md)lépéseit.

* Egy Azure [Event Hubs névtér és Event hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

* Az Event hub eléréséhez használni kívánt logikai alkalmazás. A logikai alkalmazás Azure Event Hubs triggerrel való indításához [üres logikai alkalmazásra](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow)van szükség.

* Egy Power BI szolgáltatás-fiók. Ha még nem rendelkezik ilyennel, [létrehozhat egy ingyenes próbaverziós fiókot Power bi szolgáltatáshoz](https://app.powerbi.com/). Ha még nem használta Power BI korábban, hasznos lehet a [Power bi](https://docs.microsoft.com/power-bi/service-get-started)használatának megkezdéséhez.

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Folyamatos adatexportálás beállítása az Azure-ba Event Hubs
Először be kell állítania egy folyamatos adatexportálást az Azure IoT Central-alkalmazás sablonból az előfizetésében található Azure Event hub-ba. Ezt az Azure IoT Central oktatóanyag lépéseit követve végezheti el [Event Hubs exportálásához](https://docs.microsoft.com/azure/iot-central/core/howto-export-data). Ehhez az oktatóanyaghoz csak a telemetria kell exportálni.

## <a name="create-a-power-bi-streaming-dataset"></a>Power BI folyamatos átviteli adatkészlet létrehozása

1. Jelentkezzen be a Power BI-fiókjába.

2. Az előnyben részesített munkaterületen hozzon létre egy új folyamatos átviteli adatkészletet az eszköztár jobb felső sarkában található **+ Létrehozás** gombra kattintva. Létre kell hoznia egy külön adatkészletet minden olyan beteg számára, amelyet szeretne az irányítópulton.

    >[!div class="mx-imgBorder"] 
    >![Folyamatos átviteli adatkészlet létrehozása](media/create-streaming-dataset.png)

3. Válassza az **API** lehetőséget az adatkészlet forrásához.

4. Adjon meg egy **nevet** (például a beteg nevét) az adatkészlethez, majd töltse ki a streamben található értékeket. Az alábbi példát láthatja a folyamatos beteg monitorozási alkalmazás sablonjában a szimulált eszközökről érkező értékek alapján. A példa két betegből áll:

    * Teddy Silvers, akik a Smart térd Zárójelből származó adatokkal rendelkeznek
    * Yesenia Sanford, amely az intelligens vitális javításból származó adatokkal rendelkezik

    >[!div class="mx-imgBorder"] 
    >![Adatkészlet értékeinek megadása](media/enter-dataset-values.png)

Ha többet szeretne megtudni a Power BI lévő adatfolyam-adatkészletekről, olvassa el a következő dokumentumot [valós idejű adatfolyamként Power BIban:](https://docs.microsoft.com/power-bi/service-real-time-streaming).

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>A logikai alkalmazás összekötése az Azure Event Hubs
A logikai alkalmazás Azure Event Hubshoz való összekapcsolásához kövesse a jelen dokumentumban ismertetett utasításokat az [események az azure Event Hubs és a Azure Logic Apps használatával való elküldéséhez](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action). Néhány javasolt paraméter:

|Paraméter|Érték|
|---|---|
|Tartalomtípus|application/json|
|Időköz|3|
|Frequency|Second|

Ennek a lépésnek a végén a Logic app designernek így kell kinéznie:

>[!div class="mx-imgBorder"] 
>![Logic Apps csatlakozik Event Hubs](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Stream-adatok Power BI a logikai alkalmazásból
A következő lépés az, hogy elemezze az Event hub-ról érkező adatokat a korábban létrehozott Power BI adatkészletekben.

1. A művelet megkezdése előtt meg kell ismernie az eszközről az Event hub-ba küldött JSON-adattartalmat. Ehhez tekintse meg ezt a [minta sémát](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#telemetry) , és módosítsa úgy, hogy az megfeleljen a sémának, vagy a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) használatával vizsgálja meg az üzeneteket. Ha a folyamatos beteg monitorozási alkalmazásokat használja, az üzenetek a következőképpen fognak kinézni:

**Smart Vitals – javítási telemetria**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Intelligens térd kapcsos zárójel telemetria**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Tulajdonságok**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

2. Most, hogy megtekintette a JSON-adattartalmakat, térjen vissza a Logic app Designerre, és válassza az **+ új lépés**lehetőséget. A következő lépésként keresse meg és adja hozzá az **inicializálás változót** , és adja meg a következő paramétereket:

    |Paraméter|Érték|
    |---|---|
    |Név|Csatoló neve|
    |Típus|Sztring|

    Kattintson a **Mentés** gombra. 

3. Adjon hozzá egy " **Body** " nevű másik változót **karakterláncként**. A logikai alkalmazás a következő műveleteket fogja hozzáadni:

    >[!div class="mx-imgBorder"]
    >![Változók inicializálása](media/initialize-string-variables.png)
    
4. Válassza az **+ új lépés** lehetőséget, és adjon hozzá egy **elemzési JSON** -műveletet. Nevezze át ezt az **elemzési tulajdonságokra**. A tartalomhoz válassza az Event hub-ból származó **Tulajdonságok** elemet. Válassza a minta hasznos adatok használata lehetőséget a séma alján **való létrehozásához** , majd illessze be a minta adattartalmat a fenti tulajdonságok szakaszból.

5. Ezután válassza a **változó beállítása** műveletet, és frissítse a **csatoló neve** változót a **iothub-Interface-Name** elemre az elemzett JSON-tulajdonságok közül.

6. Vegyen fel egy **felosztott** vezérlőelemet a következő műveletként, és válassza ki a **csatoló neve** változót az on paraméterrel. Ezzel az adatok a megfelelő adatkészletbe lesznek tölcsérben.

7. Az Azure IoT Central alkalmazásban keresse meg az intelligens létfontosságú adatok javítási állapotának adatait, valamint az intelligens térd zárójelek állapotának adatait az **eszköz sablonok** nézetből. Hozzon létre két különböző esetet az egyes interfészekhez tartozó **kapcsolók** vezérléséhez, és nevezze át a vezérlőt megfelelően. Beállíthatja az alapértelmezett esetet **a leállítási vezérlő használatára** , és kiválaszthatja, hogy melyik állapotot szeretné megjeleníteni.

    >[!div class="mx-imgBorder"] 
    >![Felosztás vezérlése](media/split-by-interface.png)

8. A **Smart vitals patch** esetében adjon hozzá egy **elemzési JSON** -műveletet. A tartalomhoz válassza az Event hub-ból származó **tartalmat** . Másolja ki és illessze be a fenti Smart Vitals-javításhoz tartozó minta hasznos adatokat a séma létrehozásához.

9. Adjon hozzá egy **set változót** , és frissítse a **Body** változót az elemzett JSON **törzsével** a 7. lépésben.

10. Vegyen fel egy **feltétel** vezérlőelemet a következő műveletként, és állítsa be a feltételt a **törzs**, a **tartalmaz**, a **HeartRate**. Ezzel a beállítással megadhatja, hogy az intelligens létfontosságú javításokból származó adatok megfelelő készlete legyen az Power BI adatkészlet feltöltése előtt. A 7-9. lépés a következőképpen fog kinézni:

    >[!div class="mx-imgBorder"] 
    >![Intelligens Vitals – feltétel hozzáadása](media/smart-vitals-pbi.png)

11. A feltétel **valódi** esetéhez adjon hozzá egy műveletet, amely meghívja a **sorok hozzáadása egy adatkészlethez** Power bi funkciót. Ehhez be kell jelentkeznie Power BI. A **hamis** eset újra használhatja a **megszakítási** vezérlőt.

12. Válassza ki a megfelelő **munkaterületet**, **adatkészletet**és **táblát**. Képezze le a Power BI adatfolyam-adatkészletének létrehozásakor megadott paramétereket az Event hub-ból származó elemzett JSON-értékekre. A kitöltött műveleteknek így kell kinéznie:

    >[!div class="mx-imgBorder"] 
    >![Sorok hozzáadása a Power BIhoz](media/add-rows-yesenia.png)

13. Az **intelligens térd zárójeles** kapcsoló esetében adjon hozzá egy **elemzési JSON** -műveletet a tartalom elemzéséhez, hasonlóan a 7. lépéshez. Ezután **sorokat adhat hozzá egy adatkészlethez** , hogy frissítse a Teddy Silvers-adatkészletet a Power BIban.

    >[!div class="mx-imgBorder"] 
    >![Intelligens Vitals – feltétel hozzáadása](media/knee-brace-pbi.png)

14. Kattintson a **Save (Mentés** ) gombra, majd futtassa a logikai alkalmazást.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Valós idejű irányítópult létrehozása a páciensek számára – létfontosságú
Most lépjen vissza a Power BIra, és válassza a **+ Létrehozás** lehetőséget egy új **irányítópult**létrehozásához. Adja meg az irányítópult nevét, és kattintson a **create (létrehozás**).

Válassza ki a három pontot a felső navigációs sávon, majd válassza a **+ csempe hozzáadása**lehetőséget.

>[!div class="mx-imgBorder"] 
>![Csempe hozzáadása az irányítópulthoz](media/add-tile.png)

Válassza ki, hogy milyen típusú csempét szeretne felvenni, és testreszabni szeretné az alkalmazást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az erőforrásokat a következő lépésekkel:

1. A Azure Portal törölheti az Event hub-t és Logic Apps a létrehozott erőforrásokat.

2. IoT Central alkalmazásához lépjen az adminisztráció lapra, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

* Tekintse át a [folyamatos beteg monitorozási architektúrával kapcsolatos útmutatót](concept-continuous-patient-monitoring-architecture.md).
