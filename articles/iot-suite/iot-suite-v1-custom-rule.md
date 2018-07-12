---
title: Egy egyéni szabály létrehozása az Azure IoT Suite-tal |} A Microsoft Docs
description: Hogyan hozzon létre egy egyéni szabályt az IoT Suite előre konfigurált megoldással.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 9bf2a13035de141766fd935966ce18459dccdaab
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590519"
---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>Hozzon létre egy egyéni szabályt a távoli figyelési előre konfigurált megoldásban

## <a name="introduction"></a>Bevezetés

Az előre konfigurált megoldások konfigurálhatja [szabályokat, amelyek indítható el, ha egy telemetria érték, egy eszköz elér egy adott küszöbértéket][lnk-builtin-rule]. [Dinamikus telemetria használata a távoli figyelési előre konfigurált megoldás] [ lnk-dynamic-telemetry] ismerteti, hogyan adhat hozzá egyéni telemetriai értékeket, mint például *ExternalTemperature* a megoldáshoz. Ez a cikk bemutatja, hogyan dinamikus telemetria típusok egyéni szabály létrehozására a megoldásban.

Ez az oktatóanyag egy egyszerű Node.js szimulált eszközt használja, hoznak létre dinamikus telemetriai adatokat küldhet az előre konfigurált megoldás háttérrendszerének. Majd adja hozzá az egyéni szabályok a **RemoteMonitoring** Visual Studio-megoldás és a testreszabott háttérrendszerének üzembe helyezése az Azure-előfizetéshez.

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Aktív Azure-előfizetés. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ: [Ingyenes Azure-fiók létrehozása][lnk_free_trial].
* [NODE.js] [ lnk-node] verzió 0.12.x vagy újabb rendszert egy szimulált eszköz létrehozása.
* A Visual Studio 2015 vagy Visual Studio 2017-ben módosíthatja az előre konfigurált megoldás végén az új szabályokat.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

Jegyezze fel a megoldás nevének a központi telepítés számára is választott. Az oktatóanyag későbbi részében szüksége a megoldás nevét.

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

Miután ellenőrizte, hogy a Node.js-konzolalkalmazást is leállíthatja **ExternalTemperature** telemetriát az előre konfigurált megoldáshoz. Hagyja megnyitva a konzolablakban, mert a Node.js-konzolalkalmazást a megoldáshoz az egyéni szabály hozzáadása után ismét futtatni.

## <a name="rule-storage-locations"></a>A szabály tárolási helyek

Szabályokkal kapcsolatos adatok megőrződnek helye:

* **DeviceRulesNormalizedTable** tábla – Ez a táblázat a megoldás portálja által meghatározott szabályok normalizált hivatkozást. Amikor a megoldás portálja eszköz szabályokat jeleníti meg, a következő táblázat a szabálydefiníciók kérdezi le.
* **DeviceRules** blob – a blob tárolja az összes regisztrált eszközök és az Azure Stream Analytics-feladatok a bemeneti hivatkozásként van definiálva meghatározott összes szabály.
 
Meglévő szabály frissítésekor vagy egy új szabály meghatározásához a megoldás portálján, a tábla- és a blob frissülnek, hogy tükrözze a módosításokat. A table store-ból származik a szabálydefiníciót, megjelenik a portálon, és a szabálydefiníciót a Stream Analytics-feladatok által hivatkozott származik a blobot. 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>Frissítés a RemoteMonitoring Visual Studio-megoldás

A következő lépések bemutatják, hogyan lehet módosítani a RemoteMonitoring Visual Studio-megoldás, hogy egy új szabályt, amely tartalmazza a **ExternalTemperature** a szimulált eszköz által küldött telemetriai adatok:

1. Ha még nem tette meg, klónozza a **azure-iot-remote-monitoring** tárház egy megfelelő helyre a helyi gépen a következő Git-paranccsal:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. A Visual Studióban nyissa meg a RemoteMonitoring.sln fájlt a helyi másolatát a **azure-iot-remote-monitoring** tárház.

3. Nyissa meg a fájlt Infrastructure\Models\DeviceRuleBlobEntity.cs, és adjon hozzá egy **ExternalTemperature** tulajdonság az alábbiak szerint:

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

5. Nyissa meg a fájlt Infrastructure\Models\DeviceRuleDataFields.cs, és adja hozzá a következő **ExternalTemperature** után a meglévő tulajdonság **páratartalom** tulajdonság:

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. Ugyanebben a fájlban frissítse a **_availableDataFields** metódus tartalmazza **ExternalTemperature** módon:

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

## <a name="rebuild-and-redeploy-the-solution"></a>Építse újra, és a megoldás újbóli üzembe helyezéséhez.

Most már telepítheti a frissített megoldás az Azure-előfizetéshez.

1. Nyisson meg egy rendszergazda jogú parancssort, és keresse meg az azure-iot-remote-monitoring adattár helyi példányának gyökérmappájában.

2. A frissített megoldás üzembe helyezéséhez futtassa az alábbi parancs le **{központi telepítés neve}** az előre konfigurált megoldás üzembe helyezése, amely a korábban feljegyzett nevére:

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>A Stream Analytics-feladat frissítése

Az üzembe helyezés befejeződése után frissítheti a Stream Analytics-feladat az új szabály definíciók használatára.

1. Az Azure Portalon keresse meg az erőforráscsoportot, amely tartalmazza az előre konfigurált megoldás erőforrások. Ez az erőforráscsoport a megoldás az üzembe helyezés során megadott névvel rendelkezik.

2. Keresse meg az {üzemelő példány neve}-szabályok Stream Analytics-feladatot. 

3. Kattintson a **leállítása** leállítani a Stream Analytics-feladat futtatását. (Meg kell várnia a folyamatos átviteli feladat leállítása, a lekérdezés szerkesztése előtt).

4. Kattintson a **lekérdezés**. Szerkessze a lekérdezést, hogy tartalmazza a **kiválasztása** nyilatkozata **ExternalTemperature**. A következő minta bemutatja a teljes lekérdezést az új **kiválasztása** utasítást:

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

5. Kattintson a **mentése** a frissített szabály lekérdezés módosítása.

6. Kattintson a **Start** újra futtatni a Stream Analytics-feladat elindításához.

## <a name="add-your-new-rule-in-the-dashboard"></a>Az irányítópulton az új szabály hozzáadása

Most hozzáadhatja a **ExternalTemperature** szabály egy eszközre a megoldás irányítópultján.

1. Keresse meg a megoldás portálja.

2. Keresse meg a **eszközök** panel.

3. Keresse meg az egyéni eszközt hozott létre, amely küld **ExternalTemperature** telemetriai és a a **eszközadatok** panelen, kattintson a **szabály hozzáadása**.

4. Válassza ki **ExternalTemperature** a **adatmező**.

5. Állítsa be **küszöbérték** 56. Kattintson a **mentés és szabályok megtekintése**.

6. Térjen vissza az irányítópultra való a riasztások előzményei.

7. A konzolablakban, marad nyitva, indítsa el a Node.js-Konzolalkalmazás küldésének megkezdéséhez **ExternalTemperature** telemetriai adatokat.

8. Figyelje meg, hogy a **riasztások előzményei** táblázat új riasztások az új szabály aktiválásakor.
 
## <a name="additional-information"></a>További információ

Az operátor módosítása **>** összetettebb, és ebben az oktatóanyagban ismertetett lépéseket is. Módosíthatja a Stream Analytics-feladat bármilyen operátor szeretné használni, amíg a megoldás portálja operátora tükrözve összetettebb feladat. 

## <a name="next-steps"></a>További lépések
Most, hogy megtudhatta, hogyan hozhat létre egyéni szabályok, további információ az előre konfigurált megoldások:

- [Logikai alkalmazás csatlakoztatása az Azure IoT Suite távoli figyelési előre konfigurált megoldás][lnk-logic-app]
- [Eszköz információk metaadatait a távoli figyelési előre konfigurált megoldás][lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-v1-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-v1-logic-apps-tutorial.md