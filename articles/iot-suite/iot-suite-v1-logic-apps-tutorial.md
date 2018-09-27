---
title: Az Azure IoT Suite és Logic Apps |} A Microsoft Docs
description: Hogyan kell kapcsolni a Logic Apps az Azure IoT Suite, az üzleti folyamatok oktatóanyagot.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 4a1db86f4b715533dfea545365eaf66de0574c5e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47106852"
---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>Oktatóanyag: Logikai alkalmazás csatlakoztatása az Azure IoT Suite távoli figyelési előre konfigurált megoldás
A [Microsoft Azure IoT Suite] [ lnk-internetofthings] távoli figyelési előre konfigurált megoldás van egy nagyszerű mód arra, hogy egy teljes körű funkciókészletet, amely exemplifies IoT-megoldás használatának gyors megkezdése. Ez az oktatóanyag végigvezeti a logikai alkalmazás hozzáadása a Microsoft Azure IoT Suite távoli figyelési előre konfigurált megoldás. Ezeket a lépéseket mutatják be, hogyan elvégezhető az IoT-megoldás is egy üzleti folyamat csatlakozással.

*Ha a távoli figyelési előre konfigurált megoldás üzembe helyezni egy általános bemutató keres, tekintse meg [oktatóanyag: Ismerkedés az IoT előre konfigurált megoldások][lnk-getstarted].*

Ebben az oktatóanyagban a Kezdés előtt a következőket kell tennie:

* Az Azure-előfizetésben a távoli figyelési előre konfigurált megoldás üzembe helyezése.
* Ahhoz, hogy az üzleti folyamat aktiválása e-mail küldése a SendGrid-fiók létrehozása. Regisztrálhat egy ingyenes próbafiókot, [SendGrid](https://sendgrid.com/) kattintva **próbálja ki ingyenesen**. Az ingyenes próbaverziós fiókját a regisztrálása után kell létrehoznia egy [API-kulcs](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) a SendGrid, amely engedélyt e-mailek küldésére. Az oktatóanyag későbbi részében szüksége az API-kulcs.

Az oktatóanyag elvégzéséhez szüksége van Visual Studio 2015 vagy Visual Studio 2017 az előre konfigurált megoldás háttérrendszere a műveletek módosítását.

Feltételezve, hogy már kiépített távoli figyelési előre konfigurált megoldást, keresse meg az erőforráscsoportot, a megoldás a [az Azure portal][lnk-azureportal]. Az erőforráscsoport neve megegyezik a megoldás nevét azt választotta, amikor a távoli figyelési megoldásban üzembe helyezett. Az erőforráscsoport a megoldáshoz, láthatja az összes kiépített Azure-erőforrásokat. Az alábbi képernyőfelvételen szereplő példán látható **erőforráscsoport** paneljén a távoli figyelési előre konfigurált megoldás:

![](media/iot-suite-v1-logic-apps-tutorial/resourcegroup.png)

Első lépésként állítsa be a logikai alkalmazás az előre konfigurált megoldás használata.

## <a name="set-up-the-logic-app"></a>A logikai alkalmazás beállítása
1. Kattintson a **Hozzáadás** tetején található az erőforráscsoport paneljét az Azure Portalon.
2. Keresse meg **logikai alkalmazás**, válassza ki, majd kattintson **létrehozás**.
3. Töltse ki a **neve** és ugyanaz, mint **előfizetés** és **erőforráscsoport** , a távoli figyelési megoldás üzembe helyezésekor használt. Kattintson a **Create** (Létrehozás) gombra.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/createlogicapp.png)
4. A telepítés befejezését követően megjelenik a logikai alkalmazás szerepel a listán az erőforráscsoportban erőforrásként.
5. Kattintson a logikai alkalmazást, nyissa meg a logikai alkalmazás panelen válassza ki a **üres logikai alkalmazás** sablon megnyitásához a **Logic Apps Designerben**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappsdesigner.png)
6. Válassza ki **kérelem**. Ez a művelet meghatározza, hogy egy bejövő HTTP-kérelem egy adott JSON formátumú-e eseményindítóként hasznos működik.
7. Illessze be a kérelem törzsében JSON-sémája a következő kódot:
   
    ```json
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
   
   > [!NOTE]
   > A HTTP post URL-Címének után a logikai alkalmazás mentette, de először fel kell venni egy műveletet is másolhatja.
   > 
   > 
8. Kattintson a **+ új lépés** a manuális eseményindító. Kattintson a **művelet hozzáadása**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappcode.png)
9. Keresse meg **– e-mail küldése a SendGrid** , és kattintson rá.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappaction.png)
10. Adjon meg egy nevet a kapcsolathoz, például **SendGridConnection**, adja meg a **SendGrid API-kulcs** SendGrid-fiók, és kattintson a létrehozott **létrehozás**.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridconnection.png)
11. A saját, mindkét e-mail-címek hozzáadása a **a** és **való** mezőket. Adjon hozzá **távoli figyelési riasztás [DeviceId]** , a **tulajdonos** mező. Az a **E-mail törzse** adja hozzá **eszköz [DeviceId] [measurementName] jelentett [measuredValue] értékkel.** Hozzáadhat **[DeviceId]**, **[measurementName]**, és **[measuredValue]** parancsra kattintva a **szúr be adatokat az előző lépésekből** a szakasz.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridaction.png)
12. Kattintson a **mentése** a felső menüben.
13. Kattintson a **kérelem** eseményindító, és másolja a **Http Post ezen URL-címen** értéket. Az URL-címet az oktatóanyag későbbi részében szüksége.

> [!NOTE]
> A logikai alkalmazások futtatását teszik [művelet számos különböző típusú] [ lnk-logic-apps-actions] műveletek például az Office 365-ben. 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>Állítsa be az EventProcessor Webjobs-feladat
Ebben a szakaszban csatlakozik az előre konfigurált megoldás a létrehozott logikai alkalmazásra. Ez a feladat végrehajtásához adja hozzá az URL-cím, a művelet akkor aktiválódik, ha egy eszköz érzékelő értéke meghaladja a küszöbértéket, a logikai alkalmazás indítására.

1. A git-kliens használatával klónozza a legújabb verzióját a [azure-iot-remote-monitoring github-adattár][lnk-rmgithub]. Példa:
   
    ```cmd
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. A Visual Studióban nyissa meg a **RemoteMonitoring.sln** a tárház helyi másolatából.
3. Nyissa meg a **ActionRepository.cs** fájlt a **infrastruktúra\\tárház** mappát.
4. Frissítés a **actionIds** szótár a **Http Post ezen URL-címen** feljegyzett a logikai alkalmazás a következőképpen:
   
    ```csharp
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. Mentse a módosításokat a megoldásban, és kilép a Visual Studióból.

## <a name="deploy-from-the-command-line"></a>Központi telepítése a parancssorból
Ebben a szakaszban üzembe helyezi a frissített verziója a távoli figyelési megoldásban cserélje le az Azure-ban jelenleg futó verzió.

1. A következő a [fejlesztési beállítás] [ lnk-devsetup] állítsa be a környezetet a telepítési utasításokat.
2. Helyi üzembe helyezéséhez kövesse a [helyi üzemelő példányának] [ lnk-localdeploy] utasításokat.
3. A felhőben üzembe helyezni, és frissítse a meglévő felhőbeli üzemelő példány, kövesse a [felhőbeli telepítés] [ lnk-clouddeploy] utasításokat. Használja a nevét, az eredeti üzembe helyezés az üzemelő példány neve. Például ha az eredeti üzembe helyezés byla volána **demologicapp**, használja a következő parancsot:
   
   ```cmd
   build.cmd cloud release demologicapp
   ```
   
   Amikor futtatja a felépítési szkriptet, ügyeljen arra, használja az ugyanazon Azure-fiók, előfizetési, régió és Active Directory-példányból, a megoldás üzembe helyezésekor használt.

## <a name="see-your-logic-app-in-action"></a>Tekintse meg a logikai alkalmazás működés közben
A távoli figyelési előre konfigurált megoldás beállítása alapértelmezés szerint, amikor a megoldás kiépítéséhez két szabályokkal rendelkezik. A rendszer szabályokat is a **SampleDevice001** eszköz:

* Hőmérséklet > 38.00
* Páratartalom > 48.00

A hőmérséklet szabály aktiválásakor a **előléptetése riasztás** művelet és a páratartalom szabály eseményindítók a **SendMessage** művelet. Feltéve, hogy mindkét művelet során használt megegyezik a **ActionRepository** osztályhoz, vagy a szabály a logikai alkalmazások eseményindítói. Szabályokat is egy e-mailek küldése a SendGrid használatával a **való** a riasztás részleteit tartalmazó cím.

> [!NOTE]
> A logikai alkalmazás továbbra is a trigger minden alkalommal, amikor a küszöbértéket. Felesleges e-mailek elkerülése érdekében a megoldásportál szabályok letiltása, vagy a logikai alkalmazás letiltása a [az Azure portal][lnk-azureportal].
> 
> 

E-mailek, valamint a logikai alkalmazás futtatásakor a portálon is láthatja:

![](media/iot-suite-v1-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>További lépések
Most, hogy az előre konfigurált megoldás csatlakozni egy üzleti folyamat korábban már használta egy logikai alkalmazást, hogy többet is megtudhat a beállításokat az előre konfigurált megoldások testreszabásához:

* [Dinamikus telemetria használata a távoli figyelési előre konfigurált megoldás][lnk-dynamic]
* [A távoli figyelési előre konfigurált megoldásban az eszköz információk metaadatait][lnk-devinfo]

[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
