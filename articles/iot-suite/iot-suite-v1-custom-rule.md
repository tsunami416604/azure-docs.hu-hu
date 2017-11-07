---
title: "Hozzon létre egy egyéni szabály Azure IoT Suite |} Microsoft Docs"
description: "A megoldás IoT Suite előre konfigurált egy egyéni szabály létrehozásának módjáról."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 9bf2a13035de141766fd935966ce18459dccdaab
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>Hozzon létre egy egyéni szabályt a távoli felügyeleti előkonfigurált megoldás

## <a name="introduction"></a>Bevezetés

Az előkonfigurált megoldásokat konfigurálhat [szabályokat, amelyek indul el, ha egy telemetriai érték, egy eszköz eléri a meghatározott][lnk-builtin-rule]. [Dinamikus telemetriai adatokat a távoli figyelési megoldást előre konfigurált] [ lnk-dynamic-telemetry] ismerteti, hogyan értékeket is hozzáadhat egyéni telemetriai adatokat, például a *ExternalTemperature* a megoldáshoz. Ez a cikk bemutatja, hogyan dinamikus telemetriai típusok egyéni szabály létrehozására a megoldásban.

Ebben az oktatóanyagban egy egyszerű Node.js szimulált eszköz használják a dinamikus telemetriai adatokat küldhet az előkonfigurált megoldás háttérrendszerének létrehozásához. Majd adja hozzá az egyéni szabályok a **RemoteMonitoring** Visual Studio megoldás, és a testreszabott háttér telepítse az Azure-előfizetéshez.

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Aktív Azure-előfizetés. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ: [Ingyenes Azure-fiók létrehozása][lnk_free_trial].
* [NODE.js] [ lnk-node] verzió 0.12.x vagy újabb rendszert a szimulált eszköz létrehozásához.
* A Visual Studio 2015-öt vagy a Visual Studio 2017 módosíthatja az előkonfigurált megoldás végén az új szabályokat.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

Jegyezze fel az üzembe helyezéshez választott megoldás nevét. Az oktatóanyag későbbi részében szüksége ezen megoldás neve.

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

Miután ellenőrizte, hogy akkor küld le lehet állítani a Node.js-Konzolalkalmazás **ExternalTemperature** telemetria bekapcsolásával az előkonfigurált megoldás. A konzolablakban tartsa nyitva, mert a Node.js-Konzolalkalmazás a megoldáshoz az egyéni szabály hozzáadása után újra futtatni.

## <a name="rule-storage-locations"></a>A szabály a tárolási helyek

Két külön helyen megőrződjenek szabályokkal kapcsolatos információkat:

* **DeviceRulesNormalizedTable** tábla – Ez a táblázat a megoldás portál által meghatározott szabályok normalizált hivatkozik. A megoldás portal eszköz szabályokat jeleníti meg, amikor lekérdezi a következő táblázat a szabálydefiníciók.
* **DeviceRules** blob – a blob tárolja az összes szabály definiált összes regisztrált eszközökhöz, és adjon meg az Azure Stream Analytics-feladatok hivatkozásként van definiálva.
 
Meglévő szabály frissítésekor, vagy adjon meg egy új szabályt a megoldás portálon, a tábla és a blob frissülnek a módosításokkal. A tábla tároló származik a szabálydefiníciót, megjelenik a portálon, és a Stream Analytics-feladatok által hivatkozott szabálydefiníciója származik a blob. 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>Frissítés a RemoteMonitoring Visual Studio megoldás

A következő lépések bemutatják a módosítása a RemoteMonitoring Visual Studio megoldás egy új szabályt, amely használja felvenni a **ExternalTemperature** a szimulált eszközről küldött telemetriai:

1. Ha még nem tette meg, klónozza a **azure iot-távoli-ellenőrző** tárházat a helyi számítógépen a következő Git-paranccsal megfelelő helyre:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. A Visual Studio, nyissa meg a RemoteMonitoring.sln fájl helyi másolatát a **azure iot-távoli-ellenőrző** tárházba.

3. Nyissa meg a fájlt Infrastructure\Models\DeviceRuleBlobEntity.cs, és adja hozzá egy **ExternalTemperature** tulajdonság az alábbiak szerint:

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. Ugyanebben a fájlban adja hozzá egy **ExternalTemperatureRuleOutput** tulajdonság az alábbiak szerint:

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. Nyissa meg a fájlt Infrastructure\Models\DeviceRuleDataFields.cs, és adja hozzá a következő **ExternalTemperature** követően a meglévő tulajdonság **páratartalom** tulajdonság:

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. Ugyanebben a fájlban frissítse a **_availableDataFields** felvenni metódus **ExternalTemperature** az alábbiak szerint:

    ```csharp
    private static List<string> _availableDataFields = new List<string>
    {                    
        Temperature, Humidity, ExternalTemperature
    };
    ```

7. Nyissa meg a fájlt Infrastructure\Repository\DeviceRulesRepository.cs, és módosítsa a **BuildBlobEntityListFromTableRows** módszert az alábbiak szerint:

    ```csharp
    else if (rule.DataField == DeviceRuleDataFields.Humidity)
    {
        entity.Humidity = rule.Threshold;
        entity.HumidityRuleOutput = rule.RuleOutput;
    }
    else if (rule.DataField == DeviceRuleDataFields.ExternalTemperature)
    {
      entity.ExternalTemperature = rule.Threshold;
      entity.ExternalTemperatureRuleOutput = rule.RuleOutput;
    }
    ```

## <a name="rebuild-and-redeploy-the-solution"></a>Építse újra, és a megoldás újbóli telepítéséhez.

A frissített megoldás most már telepítheti az Azure-előfizetéshez.

1. Nyisson meg egy rendszergazda jogú parancssort, és keresse meg a helyi másolat készítése az azure-iot-távelérési-figyelés tárház gyökérkönyvtárában.

2. A frissített megoldás telepítéséhez futtassa a következő parancs és **{telepítési neve}** nevű, a korábban feljegyzett előkonfigurált megoldás üzembe helyezése:

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>Frissítés a Stream Analytics-feladat

Ha a telepítés befejeződött, a Stream Analytics-feladat az új szabálydefiníciók használandó frissítheti.

1. Az Azure-portálon lépjen az előkonfigurált megoldás erőforrásokat tartalmazó erőforráscsoportot. Ez az erőforráscsoport a telepítés során a megoldáshoz megadott névvel rendelkezik.

2. Keresse meg az {neve}-szabályok Stream Analytics-feladat. 

3. Kattintson a **leállítása** leállítja a Stream Analytics-feladat futtatását. (Várnia kell a folyamatos átviteli feladat leállítása a lekérdezés szerkesztése előtt).

4. Kattintson a **lekérdezés**. Szerkessze a lekérdezést, hogy tartalmazzák a **válasszon** nyilatkozata **ExternalTemperature**. A következő példa bemutatja a teljes lekérdezés az új **válasszon** utasítást:

    ```
    WITH AlarmsData AS 
    (
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Temperature' as ReadingType,
         Stream.Temperature as Reading,
         Ref.Temperature as Threshold,
         Ref.TemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Humidity' as ReadingType,
         Stream.Humidity as Reading,
         Ref.Humidity as Threshold,
         Ref.HumidityRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'ExternalTemperature' as ReadingType,
         Stream.ExternalTemperature as Reading,
         Ref.ExternalTemperature as Threshold,
         Ref.ExternalTemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.ExternalTemperature IS NOT null AND Stream.ExternalTemperature > Ref.ExternalTemperature
    )
     
    SELECT *
    INTO DeviceRulesMonitoring
    FROM AlarmsData
     
    SELECT *
    INTO DeviceRulesHub
    FROM AlarmsData
    ```

5. Kattintson a **mentése** a frissített szabály lekérdezés módosításához.

6. Kattintson a **Start** újra futtatni a Stream Analytics-feladat elindításához.

## <a name="add-your-new-rule-in-the-dashboard"></a>Az irányítópult az új szabály hozzáadása

Mostantól hozzáadhatja a **ExternalTemperature** szabály egy eszközre, a megoldás irányítópultjának.

1. Navigáljon a megoldás portálra.

2. Keresse meg a **eszközök** panel.

3. Keresse meg az egyéni eszközt létrehozott által küldött **ExternalTemperature** telemetriai adatok és a a **eszközadatok** panelen, kattintson a **szabály hozzáadása**.

4. Válassza ki **ExternalTemperature** a **adatmező**.

5. Állítsa be **küszöbérték** 56. Kattintson a **mentéséhez és a szabályok megtekintése**.

6. Térjen vissza az irányítópulton a riasztás előzményeinek megtekintése.

7. A konzolablakban nyitva maradt, indítsa el a Node.js-Konzolalkalmazás megkezdéséhez küldése **ExternalTemperature** telemetriai adatokat.

8. Figyelje meg, hogy a **riasztási előzmények** táblázat új riasztások az új szabály indítását.
 
## <a name="additional-information"></a>További információ

Az operátor módosítása  **>**  bonyolultabb és túllép ebben az oktatóanyagban leírt lépéseket. Módosíthatja a Stream Analytics-feladat bármilyen tetszés operátor használata, jelezve, hogy a megoldás portálon operátor napjainkban összetettebb feladat. 

## <a name="next-steps"></a>Következő lépések
Most, hogy korábban már látott egyéni szabályok létrehozásához, többet is megtudhat az előkonfigurált megoldások:

- [Logikai alkalmazás csatlakoztatása az Azure IoT Suite távoli megfigyelési előre konfigurált megoldás][lnk-logic-app]
- [Eszköz információk metaadatait a távoli figyelési megoldást előre konfigurált][lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-v1-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-v1-logic-apps-tutorial.md