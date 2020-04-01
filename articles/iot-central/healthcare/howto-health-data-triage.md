---
title: Hozzon létre egy állapotadat-osztályozási irányítópultot az Azure IoT Central szolgáltatással | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre egy állapotadat-osztályozási irányítópultot az Azure IoT Central alkalmazássablonjaival.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 99b27ec53d955079b5f73986408e698955c0969b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77021644"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Oktatóanyag: Power BI-szolgáltató irányítópultjának létrehozása



A folyamatos betegfigyelési megoldás létrehozásakor létrehozhat egy irányítópultot is egy kórházi ápolócsapat számára a betegadatok megjelenítéséhez. Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy Power BI valós idejű streamelési irányítópultot az IoT Central folyamatos betegfigyelési alkalmazássablonjából.

>[!div class="mx-imgBorder"]
>![Műszerfal GIF](media/dashboard-gif-3.gif)

Az alapvető architektúra követi ezt a struktúrát:

>[!div class="mx-imgBorder"] 
>![Szolgáltató osztályozási irányítópultja](media/dashboard-architecture.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adatok exportálása az Azure IoT Central szolgáltatásból az Azure Event Hubs-ba
> * Power BI streamelési adatkészlet beállítása
> * A logikai alkalmazás csatlakoztatása az Azure Event Hubs-hoz
> * Adatok streamelése a Power BI-ba a Logic App alkalmazásból
> * Valós idejű műszerfal létrehozása a betegek létfontosságú aktaszámára

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy Azure IoT Central folyamatos betegfigyelésalkalmazás-sablon. Ha még nem rendelkezik ilyen, az [alkalmazássablon központi telepítésével](overview-iot-central-healthcare.md)teendő lépéseket hajthatja végre.

* Az Azure [Event Hubs névtere és az Event Hub.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

* A logikai alkalmazás, amely szeretné elérni az Event Hub. A logikai alkalmazás Azure-eseményközpontok eseményindítóval való elindításához [egy üres logikai alkalmazásra](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow)van szükség.

* Power BI szolgáltatásfiók. Ha még nem rendelkezik ilyen, [létrehozhat egy ingyenes próbafiókot a Power BI szolgáltatáshoz.](https://app.powerbi.com/) Ha korábban még nem használta a Power BI-t, hasznos lehet az Első lépések a [Power BI-val](https://docs.microsoft.com/power-bi/service-get-started)( Első lépések a Power BI- val ) kapcsolatban .

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Folyamatos adatexportálás beállítása az Azure Event Hubs szolgáltatásba
Először be kell állítania egy folyamatos adatexportálást az Azure IoT Central alkalmazássablonból az Azure Event Hubba az előfizetésében. Ehhez kövesse az Azure IoT Central event [hubs-exportálási](https://docs.microsoft.com/azure/iot-central/core/howto-export-data)oktatóanyagának lépéseit. Csak az oktatóanyag alkalmazásában kell exportálnia a telemetriai adatokat.

## <a name="create-a-power-bi-streaming-dataset"></a>Power BI streamelési adatkészlet létrehozása

1. Jelentkezzen be a Power BI-fiókjába.

2. A kívánt munkaterületen hozzon létre egy új streamelési adatkészletet az eszköztár jobb felső sarkában található **+ Create** gombra kattintva. Minden olyan beteghez külön adatkészletet kell létrehoznia, amelyet az irányítópulton szeretne.

    >[!div class="mx-imgBorder"] 
    >![Streamelési adatkészlet létrehozása](media/create-streaming-dataset.png)

3. Válassza az **API-t** az adatkészlet forrásához.

4. Adja meg az adatkészlet **nevét** (például egy beteg nevét), majd töltse ki az értékeket az adatfolyamból. A szimulált eszközökről érkező értékek alapján az alábbi példa látható a folyamatos betegfigyelési alkalmazássablonban. A példában két beteg van:

    * Teddy Silvers, aki az adatokat a Smart Knee Brace
    * Yesenia Sanford, aki adatokat a Smart Vitals Patch

    >[!div class="mx-imgBorder"] 
    >![Adatkészletértékek megadása](media/enter-dataset-values.png)

Ha többet szeretne megtudni a Power BI adatfolyam-adatfolyam-továbbítási adatkészleteiről, olvassa el ezt a dokumentumot [valós idejű streamelésközben a Power BI-ban.](https://docs.microsoft.com/power-bi/service-real-time-streaming)

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>A logikai alkalmazás csatlakoztatása az Azure Event Hubs-hoz
A logic app azure-eseményközpontokhoz való csatlakoztatásához kövesse az ebben a dokumentumban ismertetett utasításokat az [Események küldése az Azure Event Hubs és](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action)az Azure Logic Apps szolgáltatással kapcsolatban. Íme néhány javasolt paraméter:

|Paraméter|Érték|
|---|---|
|Tartalomtípus|application/json|
|Intervallum|3|
|Frequency|Másodperc|

A lépés végén a Logikai alkalmazástervezőnek így kell kinéznie:

>[!div class="mx-imgBorder"] 
>![A Logic Apps az Eseményközpontokhoz csatlakozik](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Adatok streamelése a Power BI-ba a Logic App alkalmazásból
A következő lépés az Event Hubról érkező adatok elemzésével történik, hogy azokat a korábban létrehozott Power BI-adatkészletekbe továbbíthassa.

1. Ehhez meg kell értenie a JSON hasznos, amely az eszközről az Event Hub. Ehhez nézd meg ezt a [mintasémát,](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#telemetry) és módosítsa azt, hogy megfeleljen a sémának, vagy a [Service Bus explorer](https://github.com/paolosalvatori/ServiceBusExplorer) segítségével vizsgálja meg az üzeneteket. Ha folyamatos betegfigyelő alkalmazásokat használ, az üzenetek a következőkre fognak kinézni:

**Smart Vitals Patch telemetriai adatok**

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

**Intelligens térdmerevítő telemetria**

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

2. Most, hogy megvizsgálta a JSON hasznos adatait, menjen vissza a Logic App Designer-hez, és válassza a **+ Új lépés lehetőséget.** Következő lépésként keressen rá a Keresésre, és adja hozzá az **Inicializálás változót,** és adja meg a következő paramétereket:

    |Paraméter|Érték|
    |---|---|
    |Név|Kapcsolat neve|
    |Típus|Sztring|

    Kattintson a **Mentés** gombra. 

3. Adjon hozzá egy **másik, Szövegtörzs** karakterláncként nevű **változót.** A logikai alkalmazás ezeket a műveleteket adja hozzá:

    >[!div class="mx-imgBorder"]
    >![Változók inicializálása](media/initialize-string-variables.png)
    
4. Válassza a **+ Új lépés** lehetőséget, és adjon hozzá egy **Parse JSON** műveletet. Nevezze át ezt **elemzési tulajdonságokra.** A tartalomhoz válassza az Event Hubról érkező **tulajdonságok lehetőséget.** Válassza a Minta hasznos adat használata a **séma létrehozásához** az alján lehetőséget, és illessze be a minta hasznos adatát a fenti Tulajdonságok szakaszból.

5. Ezután válassza a **Változó beállítása** műveletet, és frissítse a **felület neve** változót az **iothub-interface-name-nel** az elemző JSON-tulajdonságokból.

6. Adjon hozzá egy **osztott** vezérlőt a következő műveletként, és válassza a **Kapcsolat neve** változót On paraméterként. Ezzel a segítségével az adatokat a megfelelő adatkészletbe irányíthatja.

7. Az Azure IoT Central alkalmazásban keresse meg a felület nevét a Smart Vitals patch állapotadatok és a Smart Knee Brace egészségügyi adatok at the **Device Templates** view. Hozzon létre két különböző esetet a **kapcsolóvezérléshez** az egyes illesztőkapcsolatok nevéhez, és nevezze át megfelelően a vezérlőt. Beállíthatja, hogy az Alapértelmezett eset a **Vezérlő leállítása beállítást** használja, és megadhatja, hogy milyen állapotot szeretne megjeleníteni.

    >[!div class="mx-imgBorder"] 
    >![Megosztásvezérlő](media/split-by-interface.png)

8. A **Smart Vitals Patch** tokhoz adjon hozzá egy **Parse JSON** műveletet. A Tartalom hoz válassza az Event Hubról érkező **Tartalom** lehetőséget. Másolja és illessze be a fenti Smart Vitals patch mintarakományát a séma létrehozásához.

9. Adjon hozzá egy **Set változó** műveletet, és frissítse a **Törzs** változót a **Testtel** az elemzésáltal ossze JSON-ból a 7.

10. Add hozzá a **feltételvezérlőt** a következő műveletként, és állítsd a Feltétel feltételt **a Body**, **tartalmazza**, **HeartRate**. Ez biztosítja, hogy a Power BI-adatkészlet feltöltése előtt a Megfelelő adatkészletet hozza létre a Smart Vitals patch-ből. A 7-9.

    >[!div class="mx-imgBorder"] 
    >![A Smart Vitals állapotot ad](media/smart-vitals-pbi.png)

11. A Feltétel **Igaz** esetéhez adjon hozzá egy műveletet, amely meghívja a **Sorok hozzáadása egy adatkészlethez** Power BI funkciót. Ehhez be kell jelentkeznie a Power BI-ba. A **Hamis** eset ismét **használhatja** a Terminate vezérlőt.

12. Válassza ki a megfelelő **munkaterületet**, **adatkészletet**és **táblát.** Rendelje hozzá a Power BI-ban a streamelési adatkészlet létrehozásakor megadott paramétereket az Event Hubról származó, az elemzésben szereplő JSON-értékekhez. A kitöltött műveleteknek így kell kinézniük:

    >[!div class="mx-imgBorder"] 
    >![Sorok hozzáadása a Power BI-hoz](media/add-rows-yesenia.png)

13. A **Smart Knee Brace** kapcsoló esetében adjon hozzá egy **Parse JSON** műveletet a tartalom elemzéséhez, hasonlóan a 7. Ezután **sorokhozzáadása egy adatkészlethez** a Teddy Silvers adatkészlet frissítéséhez a Power BI-ban.

    >[!div class="mx-imgBorder"] 
    >![A Smart Vitals állapotot ad](media/knee-brace-pbi.png)

14. Nyomja **le a Mentés gombot,** majd futtassa a logikai alkalmazást.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Valós idejű műszerfal létrehozása a betegek létfontosságú aktaszámára
Most lépjen vissza a Power BI-hoz, és válassza **a + Létrehozás** lehetőséget új **irányítópult**létrehozásához. Adjon nevet az irányítópultnak, és nyomja le a **Create (Létrehozás) lehetőséget.**

Jelölje ki a felső navigációs sávon a három pont, majd a **+ Mozaik hozzáadása**lehetőséget.

>[!div class="mx-imgBorder"] 
>![Csempe hozzáadása az irányítópulthoz](media/add-tile.png)

Válassza ki, hogy milyen típusú csempét szeretne hozzáadni, és úgy szabhatja testre az alkalmazást, ahogy szeretné.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje az erőforrásokat a következő lépésekkel:

1. Az Azure Portalon törölheti az Event Hub és a Logic Apps által létrehozott erőforrásokat.

2. Az IoT Central alkalmazás esetén lépjen a Felügyelet fülre, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

* Tekintse át a [betegek folyamatos monitorozási architektúrájának útmutatását.](concept-continuous-patient-monitoring-architecture.md)
