---
title: Testreszabás előre konfigurált megoldások |} Microsoft Docs
description: Hogyan szabhatja testre az Azure IoT Suite előre konfigurált megoldásokat nyújt útmutatást.
services: ''
suite: iot-suite
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: cb5955111cb3954f71f11602042b5153ccee3473
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="customize-a-preconfigured-solution"></a>Előre konfigurált megoldás testreszabása

Az Azure IoT Suite előre konfigurált megoldásai belül a csomaggal együtt működve biztosítanak egy végpont megoldás a szolgáltatások bemutatásához. A kiindulási pont, a különböző helyek, amelyeknek kiterjesztése és a megoldás az adott forgatókönyveket testreszabása vannak. A következő szakaszok ismertetik a közös testreszabási pontok.

## <a name="find-the-source-code"></a>A Forráskód keresése

Az előkonfigurált megoldás forráskódját a következő tárházak a githubon érhető el:

* Távoli figyelése: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* A prediktív karbantartás: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* Csatlakoztatott gyári: [https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

Az előkonfigurált megoldás forráskódját bemutatása a minták és gyakorlatok összessége valósította meg a végpont funkciót egy IoT-megoldás Azure IoT Suite segítségével valósul meg. További információ és a GitHub-adattárak a megoldások üzembe található.

## <a name="change-the-preconfigured-rules"></a>Az előre konfigurált szabályai módosítása

A távoli felügyeleti megoldás tartalmaz három [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) feladatok eszközadatokat, telemetriai adatok és a megoldás szabálylogika kezelésére.

A három stream analytics-feladatok és szintaxisát a részletesen ismerteti a [távoli megfigyelési előre konfigurált megoldás forgatókönyv](iot-suite-v1-remote-monitoring-sample-walkthrough.md). 

Ezeket a feladatokat, közvetlenül az alter logika szerkesztése, vagy adott logika hozzáadása a forgatókönyvéhez. A Stream Analytics-feladatok az alábbiak szerint találja meg:

1. Ugrás a [Azure-portálon](https://portal.azure.com).
2. Nyissa meg az erőforráscsoport neve megegyezik az IoT-megoldásból. 
3. Válassza ki a módosítani kívánt Azure Stream Analytics-feladat. 
4. A feladat leállítása kiválasztásával **leállítása** a parancsok készlete. 
5. A bemenet, a lekérdezés és a kimenetek szerkesztése.
   
    Egy egyszerű módosítása, hogy módosítsa a lekérdezést a **szabályok** használandó feladat egy **"<"** ahelyett, hogy egy **">"**. A megoldás portálra még mindig **">"** amikor szabály szerkesztése, de figyelje meg, hogyan viselkedését tükrözött miatt a változást az alapul szolgáló feladatot.
6. A feladat indítása

> [!NOTE]
> A távoli figyelési irányítópult adatokat, függ, így módosítása a feladat sikertelen lesz az irányítópult okozhat.

## <a name="add-your-own-rules"></a>A saját szabályok hozzáadása

Mellett az előre konfigurált Azure Stream Analytics-feladatok módosítása, az Azure-portál használatával vegyen fel új feladatokat, vagy vegyen fel új lekérdezések meglévő feladatokat.

## <a name="customize-devices"></a>Eszközök testreszabása

Eszközök adott adott esetben a leggyakrabban használt bővítmény tevékenységek egyikét dolgoznak. Többféleképpen eszközökre való munkához. Ezek a metódusok lehetnek a szimulált eszköz helyzetnek megfelelő módosítása, vagy használja a [IoT eszköz SDK] [ IoT Device SDK] csatlakoztassa a fizikai eszközt a megoldás.

Egy részletes útmutató a Hozzáadás, eszközök, tekintse meg a [Iot Suite csatlakozó eszközök](iot-suite-v1-connecting-devices.md) cikk és a [C SDK minta figyelési távoli](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring). Ez a minta tervezték a távoli felügyeleti előkonfigurált megoldás.

### <a name="create-your-own-simulated-device"></a>A saját szimulált eszköz létrehozása

Tartalmazza a [távoli figyelési megoldás forráskódját](https://github.com/Azure/azure-iot-remote-monitoring), .NET szimulátor van. A szimulátor a megoldás részeként üzembe azt, és módosíthatja úgy, hogy a különböző metaadatokat, telemetriai adatokat küldhet és válaszolhat azokra a más parancsok és módszerek.

A távoli felügyeleti előkonfigurált megoldás előre konfigurált szimulátor bocsát ki a hőmérséklet és a páratartalom telemetriai hűtő eszköz szimulálja. Módosíthatja a szimulátor a [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) projektre, ha már ágazik el a GitHub-tárházban.

### <a name="available-locations-for-simulated-devices"></a>A szimulált eszköz elérhető helyről

Alapértelmezés szerint helyek Budapest/Redmond, Washington, az Amerikai Egyesült Államok van. Módosíthatja a következő helyeken és [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>A kívánt tulajdonság frissítéskezelő hozzáadása a szimulátor

Egy eszköz számára a kívánt tulajdonság értékét a megoldás-portálon állíthatja be. Az eszközt, hogy a tulajdonság kezelni felelőssége módosítsa a kérelmet, ha az eszköz olvas be a kívánt tulajdonság értéke. Egy tulajdonság értékének módosítása kívánt tulajdonságon keresztül támogatásához, kell hozzáadnia a kezelő számára a szimulátor.

A szimulátor tartalmazza a kezelők a **SetPointTemp** és **TelemetryInterval** tulajdonságokhoz, frissítheti úgy, hogy a megoldás portálon értékek szükséges.

A következő példa bemutatja a kezelőt a **SetPointTemp** tulajdonság szükséges a **CoolerDevice** osztály:

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

Ez a módszer a telemetriai adatok pont hőmérséklet frissíti, és majd visszajelzést a módosítás az IoT-központ által jelentett tulajdonság beállítását.

A minta az előző példában a következő a saját tulajdonságokat adhat hozzá a saját kezelőit.

Is kötni kell a kívánt tulajdonságot a kezelő az alábbi példában látható módon a **CoolerDevice** konstruktor:

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

Vegye figyelembe, hogy **SetPointTempPropertyName** egy meghatározott "Config.SetPointTemp" konstans.

### <a name="add-support-for-a-new-method-to-the-simulator"></a>A szimulátor hozzáadása egy új módszer támogatása

Testre szabhatja a szimulátor támogatásához új [módszer (közvetlen módszer)][lnk-direct-methods]. Szükséges két fő lépésből áll:

- A szimulátor értesítenie kell az IoT-központ az előre konfigurált megoldásban a metódus adatokkal.
- A szimulátor tartalmaznia kell a metódus hívása kezelésére indításakor, a kódot a **eszközadatok** a solution explorer vagy egy feladat panelen.

A távoli megfigyelési előre konfigurált megoldás által használt *tulajdonságok jelentett* támogatott módszereket küldeni az IoT-központ. A megoldás háttérrendszeréhez fenntart egy listát a metódus meghívásához előzményeit együtt minden eszköz által támogatott összes módszert. Tekintse meg az eszközök, és a megoldás portálon metódusok.

Az IoT hub értesíti, hogy egy eszköz támogatja-e egy metódust, az eszköz részleteit az metódust kell hozzáadnia a **SupportedMethods** csomópont jelentett tulajdonságai:

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

A metódus-aláírás formátuma a következő: `<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>`. Ahhoz például, hogy adja meg a **InitiateFirmwareUpdate** metódus azt várja, egy karakterlánc-paramétert nevű **FwPackageURI**, használja a következő metódus-aláírás:

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

A támogatott paraméter típusainak listáját lásd: a **CommandTypes** osztály az infrastruktúra-projektben.

Töröl egy metódust, adja meg a metódus-aláírás `null` jelentett tulajdonságai.

> [!NOTE]
> A megoldás háttérrendszeréhez csak frissíti a támogatott módszerekkel kapcsolatos információk, amikor megkapja a *eszközadatokat* üzenet az eszközön.

A következő példakód a a **SampleDeviceFactory** osztály a közös projekt bemutatja, hogyan vehet fel felhasználókat listájának **SupportedMethods** az eszköz által küldött jelentett tulajdonságai:

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

A kódrészletet részleteit hozzáadja a **InitiateFirmwareUpdate** módszer, beleértve a megoldás portál és a szükséges metódus paraméterek részleteit megjelenítendő szöveget.

A szimulátor küld jelentett tulajdonságait, beleértve a támogatott módszerek, az IoT hubhoz a szimulátor indításakor listáját.

Adja hozzá a szimulátor kódot az egyes módszerek, amely támogatja a kezelő. Láthatja, hogy a meglévő kezelőket a **CoolerDevice** osztály a Simulator.WebJob projektben. A következő példa bemutatja a kezelőt **InitiateFirmwareUpdate** módszert:

```csharp
public async Task<MethodResponse> OnInitiateFirmwareUpdate(MethodRequest methodRequest, object userContext)
{
    if (_deviceManagementTask != null && !_deviceManagementTask.IsCompleted)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "Device is busy"
        }, 409));
    }

    try
    {
        var operation = new FirmwareUpdate(methodRequest);
        _deviceManagementTask = operation.Run(Transport).ContinueWith(async task =>
        {
            // after firmware completed, we reset telemetry
            var telemetry = _telemetryController as ITelemetryWithTemperatureMeanValue;
            if (telemetry != null)
            {
                telemetry.TemperatureMeanValue = 34.5;
            }

            await UpdateReportedTemperatureMeanValue();
        });

        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "FirmwareUpdate accepted",
            Uri = operation.Uri
        }));
    }
    catch (Exception ex)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = ex.Message
        }, 400));
    }
}
```

Kezelő metódusnevek kell kezdődnie `On` megadott azon metódus neve követ. A **methodRequest** paraméter a metódushívás átadni a megoldás háttérből paramétereket tartalmaz. A visszatérési érték típusúnak kell lennie **feladat&lt;MethodResponse&gt;**. A **BuildMethodResponse** segédprogram módszer létrehozásához nyújt segítséget a visszatérési érték.

A metódus kezelő belül sikerült:

- Aszinkron feladat elindítása.
- A kívánt tulajdonságokat lekérni a *eszköz iker* az IoT-központ.
- Egy egyetlen jelentett tulajdonság használatával frissítse a **SetReportedPropertyAsync** metódust a **CoolerDevice** osztály.
- Hozzon létre több jelentett tulajdonságainak frissítése egy **TwinCollection** példány és hívása a **Transport.UpdateReportedPropertiesAsync** metódust.

Az előző példában a belső vezérlőprogram frissítése a következő lépéseket végzi el:

- Ellenőrzi, hogy az eszköz a belső vezérlőprogram frissítési kérelem fogadására képes-e.
- Aszinkron módon indít el a belső vezérlőprogram frissítési műveletet, és alaphelyzetbe állítja a telemetriai adatokat, a művelet befejezésekor.
- Azonnal visszaadja a "FirmwareUpdate elfogadta" üzenetet annak jelzésére, hogy az eszköz a kérést elfogadták.

### <a name="build-and-use-your-own-physical-device"></a>Hozza létre, és a saját (fizikai) eszköz

A [Azure IoT SDK-k](https://github.com/Azure/azure-iot-sdks) adja meg a szalagtárak számos eszköztípus (nyelv és operációs rendszerek) csatlakozni az IoT-megoldásokhoz.

## <a name="modify-dashboard-limits"></a>Irányítópult korlátok módosítása

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Irányítópult legördülő megjelenő eszközök száma

Az alapértelmezett érték 200. Módosíthatja ezt a számot [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>PIN-kódok Bing térképek vezérlőben megjelenítendő száma

Az alapértelmezett érték 200. Módosíthatja ezt a számot [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Telemetria gráf időszakban

Az alapértelmezett érték 10 perc. Ez az érték módosítható [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="feedback"></a>Visszajelzés

Rendelkezik a testreszabást szeretné lásd a jelen dokumentum az érintett? Adja hozzá a szolgáltatási javaslataikat [User Voice](https://feedback.azure.com/forums/321918-azure-iot), vagy ez a cikk megjegyzés. 

## <a name="next-steps"></a>További lépések

Az előkonfigurált megoldásokat testreszabására vonatkozó beállításokkal kapcsolatos további tudnivalókért lásd:

* [Logikai alkalmazás csatlakoztatása az Azure IoT Suite távoli megfigyelési előre konfigurált megoldás][lnk-logicapp]
* [Dinamikus telemetriai adatokat a távoli felügyeleti előkonfigurált megoldás][lnk-dynamic]
* [Eszköz információk metaadatait a távoli felügyeleti előkonfigurált megoldás][lnk-devinfo]
* [Testre szabhatja, hogy a csatlakoztatott gyári megoldás OPC EE-kiszolgálóinak adatait jeleníti meg][lnk-cf-customize]

[lnk-logicapp]: iot-suite-v1-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-cf-customize]:../iot-accelerators/iot-accelerators-connected-factory-customize.md