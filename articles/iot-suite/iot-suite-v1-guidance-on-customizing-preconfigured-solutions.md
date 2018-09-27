---
title: Testreszabása előre konfigurált megoldások |} A Microsoft Docs
description: Az Azure IoT Suite előre konfigurált megoldások testreszabásáról nyújt útmutatást.
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
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47106848"
---
# <a name="customize-a-preconfigured-solution"></a>Előre konfigurált megoldás testreszabása

Az előre konfigurált megoldások az Azure IoT Suite-tal megadott együtt dolgozik azon, hogy egy teljes körű megoldást nyújthat a csomag szolgáltatásai mutatják be. A kiindulási pontról nincsenek különböző helyeken, amelyben bővítheti és testre szabhatja a megoldást bizonyos forgatókönyvek esetén. A következő szakaszok ismertetik ezeket a gyakori testreszabási pontokat.

## <a name="find-the-source-code"></a>Keresse meg a forráskódot

Előre konfigurált megoldás forráskódját a következő tárházban a githubon érhető el:

* Távoli figyelés: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Prediktív karbantartás: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* Csatlakoztatott gyár: [https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

A forráskódot, előre konfigurált megoldások bemutatása, minták és gyakorlatok IoT-megoldás Azure IoT Suite segítségével teljes körű funkciójának megvalósításához használt biztosítunk. Készíthet és helyezhet üzembe a GitHub-adattárában megoldások további tájékoztatást talál.

## <a name="change-the-preconfigured-rules"></a>Az előre konfigurált szabályok módosítása

A távoli figyelési megoldás tartalmaz három [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) feladatok kezelésére, eszköz adatai, a telemetria és a megoldás szabálylogika.

A három stream analytics-feladatok és a szintaxis csoportfában ismertetett a [előre konfigurált távoli figyelési megoldás bemutatója](iot-suite-v1-remote-monitoring-sample-walkthrough.md). 

Ezeket a feladatokat, közvetlenül az alter, a logikai szerkesztése, vagy adott logika hozzáadása a forgatókönyvhöz. A Stream Analytics-feladatok módon találhatja meg:

1. Lépjen a [az Azure portal](https://portal.azure.com).
2. Keresse meg az erőforráscsoport-neve megegyezik az IoT-megoldás. 
3. Válassza ki a módosítani szeretné az Azure Stream Analytics-feladat. 
4. Állítsa le a feladatot kiválasztásával **leállítása** a parancsok készlete. 
5. A bemenetek, lekérdezés és kimenetek szerkesztéséhez.
   
    Egy egyszerű módosítást, hogy a lekérdezés módosítása a **szabályok** használandó feladat egy **"<"** helyett egy **">"**. Továbbra is látható a megoldás portálja **">"** amikor egy szabály szerkesztése, de figyelje meg, hogyan viselkedését az alapul szolgáló feladat változása miatt van tükrözve.
6. A feladat indítása

> [!NOTE]
> A távoli figyelési irányítópult adott adatok függ, ezért sikertelen lesz az irányítópult módosítása a feladatok okozhat.

## <a name="add-your-own-rules"></a>A saját szabályok hozzáadása

Vegyen fel új feladatokat, vagy új lekérdezések hozzáadása meglévő feladatok mellett az előre konfigurált Azure Stream Analytics-feladatok módosítása, használhatja az Azure Portalon.

## <a name="customize-devices"></a>Eszközök testre szabása

A leggyakoribb bővítmény tevékenységek egyikét az adott forgatókönyvnek eszközök működik. Többféleképpen eszközökre való munkához. Ezek a metódusok lehetnek egy szimulált eszközt a forgatókönyvnek megfelelően módosítása, vagy használja a [IoT eszközoldali SDK] [ IoT Device SDK] a fizikai eszköz a megoldáshoz történő csatlakoztatásáról.

Egy lépésenkénti útmutató, amellyel az eszközök hozzáadása, lásd: a [Iot Suite-összekötő eszközök](iot-suite-v1-connecting-devices.md) cikkben és a [távoli megfigyelés C SDK minta](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring). Ez a minta használata a távoli figyelési előre konfigurált megoldás célja.

### <a name="create-your-own-simulated-device"></a>A saját szimulált eszköz létrehozása

Tartalmazza a [távoli figyelési megoldás forráskódja](https://github.com/Azure/azure-iot-remote-monitoring), van egy .NET-szimulátort. A szimulátor a megoldás részeként üzembe helyezett egy és, akkor módosíthatja, hogy küldjön különböző metaadatokat, telemetriai adatokat, és az azokra való más parancsok és metódusok.

A távoli figyelési előre konfigurált megoldásban az előre konfigurált szimulátor egy gyors megtérülés küldő eszközt szimulál hőmérséklettel és páratartalommal kapcsolatos telemetriai adatokat küldenek. A szimulátort a módosítható a [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) projektre, amikor már elágaztatott GitHub-adattárat.

### <a name="available-locations-for-simulated-devices"></a>A szimulált eszközök az elérhető helyek

A alapértelmezett olyan helyek, a Seattle/Redmond, Washington, az Amerikai Egyesült Államok. Módosíthatja a helyek [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>A szimulátor egy kívánt tulajdonságot frissítéskezelő hozzáadása

Eszköz kívánt tulajdonságának értékét a megoldásportálon állíthatja be. Változáskérés feladata az eszköz kezeléséhez a tulajdonságot, amikor az eszköz kér le a kívánt tulajdonság értéke. Hozzáadni kívánt tulajdonságon keresztül tulajdonság értékének módosítása támogatása, a kezelő hozzáadása a szimulátor kell.

A szimulátor kezelőit a következőkhöz tartalmaz a **SetPointTemp** és **TelemetryInterval** tulajdonságok megadásával frissíthető kívánt értékeket a megoldás portálján.

Az alábbi példa bemutatja a kezelő a **SetPointTemp** kívánt tulajdonság a **CoolerDevice** osztály:

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

Ez a módszer a telemetriai adatok porthőmérséklet frissíti, és majd jelentést a módosítás vissza az IoT hubhoz a jelentett tulajdonságok beállításával.

A minta az előző példában követve adhat hozzá a saját kezelőit a saját tulajdonságok.

Meg kell is a kívánt tulajdonság kötése a kezelőnek az az alábbi példában látható módon a **CoolerDevice** konstruktort:

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

Vegye figyelembe, hogy **SetPointTempPropertyName** egy konstans definiálva "Config.SetPointTemp".

### <a name="add-support-for-a-new-method-to-the-simulator"></a>A szimulátor egy új módszer támogatása

Testre szabhatja a szimulátor támogatása érdekében új [metódus (közvetlen módszer)][lnk-direct-methods]. Szükséges két fő lépésből áll:

- A szimulátor értesítenie kell a kívánt metódus részleteit az előre konfigurált megoldásban az IoT hubon.
- A szimulátor tartalmaznia kell kezelni a metódushívás hívhat meg a kódot a **eszközadatok** a megoldáskezelőben vagy egy feladat panelen.

A távoli figyelési előre konfigurált megoldás *jelentett tulajdonságokként* támogatott módszereket küldése az IoT hubnak. A megoldás háttérrendszere tárolja mindazokat a módszereket, valamint megpróbálkozni előzményeit minden egyes eszköz által támogatott listáját. Az eszközökkel kapcsolatos információk megtekintéséhez, és a megoldás portálján metódusokat hívhat meg.

Az IoT hub értesíti, hogy egy eszköz támogatja a módszert, az eszköz kívánt metódus részleteit hozzá kell adnia a **SupportedMethods** csomópontja a jelentett tulajdonságok:

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

A metódus aláírásához formátuma a következő: `<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>`. Adja meg például a **InitiateFirmwareUpdate** metódust vár egy karakterlánc-paramétert nevű **FwPackageURI**, használja a következő podpis metody:

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

Támogatott paramétertípusok listájáért tekintse meg a **CommandTypes** osztály az infrastruktúra-projektben.

Töröl egy metódust, állítsa be a podpis metody `null` a jelentett tulajdonságok.

> [!NOTE]
> A megoldás háttérrendszere csak frissíti a támogatott módszerekről, amikor kap egy *eszközadatokat* üzenetet az eszközről.

Az alábbi kódmintát a a **SampleDeviceFactory** osztály az a közös projektet mutatja be egy olyan metódus hozzáadása a listához, **SupportedMethods** az eszköz által jelentett tulajdonságok:

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

Ez a kódrészlet hozzáad részleteit a **InitiateFirmwareUpdate** metódus szöveg megjelenítéséhez a megoldásportál és a metódus szükséges paraméterek részleteit.

A szimulátor jelentett tulajdonságokat, beleértve a támogatott metódusok, amikor a szimulátor elindít az IoT hub küldi.

Adja hozzá a kezelő az egyes módszerek támogatja a szimulátorkódot. Láthatja, hogy a meglévő kezelőket a **CoolerDevice** osztály a Simulator.WebJob projektben. Az alábbi példa bemutatja a kezelő **InitiateFirmwareUpdate** módszer:

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

Metódust kezelő kell kezdődniük `On` metódus neve követ. A **methodRequest** paraméter tartalmazza a metódus meghívásának a megoldás háttérrendszere az átadott paramétereket. A visszaadott érték típusúnak kell lennie **feladat&lt;MethodResponse&gt;**. A **BuildMethodResponse** segédprogram metódus segítségével létrehozhatja az a visszaadott érték.

A metódus kezelő belül sikerült:

- Indítsa el egy aszinkron feladatot.
- A kívánt tulajdonságokat lekérni a *ikereszköz* az IoT hubon.
- Egy egyetlen a jelentett tulajdonságok használatával frissítse a **SetReportedPropertyAsync** metódus az a **CoolerDevice** osztály.
- Több jelentett tulajdonságok frissítéséhez hozzon létre egy **TwinCollection** -példány és hívása a **Transport.UpdateReportedPropertiesAsync** metódust.

Az előző példában a belső vezérlőprogram frissítése a következő lépéseket hajtja végre:

- Ellenőrzi, hogy az eszközt a belső vezérlőprogram frissítési kérelem fogadására képes-e.
- Aszinkron módon kezdeményez a belsővezérlőprogram-frissítési művelet, és alaphelyzetbe állítja a telemetriai adatokat, a művelet befejezésekor.
- Azonnal visszaadja a "FirmwareUpdate elfogadta" üzenetet jelzi, hogy az eszköz által a kérést elfogadták.

### <a name="build-and-use-your-own-physical-device"></a>Létrehozhat és használhat saját (fizikai) eszköz

A [Azure IoT SDK-k](https://github.com/Azure/azure-iot-sdks) kódtárakat biztosít számos eszköztípus (nyelvet és operációs rendszerek) kapcsolódás IoT-megoldásokban.

## <a name="modify-dashboard-limits"></a>Irányítópult korlátok módosítása

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Irányítópult legördülő menüben megjelenő eszközök száma

Az alapértelmezett érték 200. Ez a szám módosítható [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>A Bing térkép vezérlőelemben megjeleníthető megőrződő PIN kódok számát

Az alapértelmezett érték 200. Ez a szám módosítható [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Telemetria graph időtartammal

Az alapértelmezett érték 10 perc. Módosíthatja ezt az értéket [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="feedback"></a>Visszajelzés

Rendelkezik egy testreszabási szeretné lásd a jelen dokumentum az érintett? Adja hozzá a szolgáltatási javaslataikat [User Voice](https://feedback.azure.com/forums/321918-azure-iot), vagy amelyekhez megjegyzést ezen a cikken. 

## <a name="next-steps"></a>További lépések

Az előre konfigurált megoldások testreszabásához beállításokkal kapcsolatos további tudnivalókért lásd:

* [Logikai alkalmazás csatlakoztatása az Azure IoT Suite távoli figyelési előre konfigurált megoldás][lnk-logicapp]
* [Dinamikus telemetria használata a távoli figyelési előre konfigurált megoldás][lnk-dynamic]
* [A távoli figyelési előre konfigurált megoldásban az eszköz információk metaadatait][lnk-devinfo]
* [Testre szabhatja, hogy a csatlakoztatott gyár megoldás az OPC UA-kiszolgálók adatait jeleníti meg][lnk-cf-customize]

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