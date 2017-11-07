---
title: "Az Azure IoT Suite és a Logic Apps |} Microsoft Docs"
description: "Egy útmutatót a Logic Apps alkalmazásokat Azure IoT Suite bekötése üzleti folyamat a."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: f4457b44c97fadc58406430fc0f31b3e0bac6682
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>Oktatóanyag: Logikai alkalmazás csatlakoztatása az Azure IoT Suite távoli megfigyelési előre konfigurált megoldás
A [Microsoft Azure IoT Suite] [ lnk-internetofthings] távoli felügyeleti előkonfigurált megoldás használatának gyors megkezdése egy végpont készlet, amely egy IoT-megoldás exemplifies remek módja van. Ez az oktatóanyag bemutatja, hogyan logikai alkalmazás felvétele a Microsoft Azure IoT Suite távoli felügyeleti előkonfigurált megoldás. A lépések bemutatják, hogyan készíthet az IoT-megoldásból még tovább csatlakozva üzleti folyamatokat.

*Ha egy távoli megfigyelési előre konfigurált megoldás kiépítését általános bemutató keres, tekintse meg [oktatóanyag: Megismerkedés a előre konfigurált IoT-megoldások][lnk-getstarted].*

Ez az oktatóanyag megkezdése előtt a következőket:

* A távoli Azure-előfizetése figyelési előre konfigurált megoldás kiépítéséhez.
* Ahhoz, hogy küldjön egy e-mailt, amely az üzleti folyamat elindítja a SendGrid-fiók létrehozása. Regisztrálhat egy ingyenes próbafiók, a [SendGrid](https://sendgrid.com/) kattintva **próbálja szabad**. A próbafiókot regisztrálása után kell létrehoznia egy [API-kulcs](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) a SendGrid, amely engedélyt ad az e-mailek küldését. Az oktatóanyag későbbi részében szüksége az API-kulcs.

Az oktatóanyag teljesítéséhez szüksége van a Visual Studio 2015-öt vagy a Visual Studio 2017 az előkonfigurált megoldás háttérbeli műveletek módosítását.

Feltéve, hogy már megtörtént a távoli figyelésének előre konfigurált megoldást, keresse meg az adott megoldáshoz tartozó erőforráscsoport a [Azure-portálon][lnk-azureportal]. Az erőforráscsoport neve megegyezik a megoldás neve rendelkezik úgy döntött, hogy amikor a távoli felügyeleti megoldás létesített. Az erőforráscsoportot látja a megoldás, kivéve az Azure Active Directory-alkalmazás, amely a klasszikus Azure portálon található összes kiépített Azure-erőforrásokat. Az alábbi képernyőfelvételen szereplő példán látható **erőforráscsoport** egy távoli figyelés panel előre konfigurált megoldást:

![](media/iot-suite-v1-logic-apps-tutorial/resourcegroup.png)

Első lépésként állítsa be a logikai alkalmazást az előkonfigurált megoldás használandó.

## <a name="set-up-the-logic-app"></a>A logikai alkalmazás beállítása
1. Kattintson a **Hozzáadás** tetején található az erőforráscsoport panel az Azure portálon.
2. Keresse meg **logikai alkalmazás**, válassza ki azt, és kattintson a **létrehozása**.
3. Töltse ki a **neve** és ugyanazon **előfizetés** és **erőforráscsoport** használja, ha a távoli felügyeleti megoldás létesített. Kattintson a **Create** (Létrehozás) gombra.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/createlogicapp.png)
4. A telepítés befejeződése után megtekintheti a logikai alkalmazás szerepel az erőforráscsoportban erőforrásként.
5. Kattintson a lehetőségre, és navigáljon a Logic App panelen válassza ki a logikai alkalmazást a **üres logikai alkalmazás** sablon megnyitásához az **Logic Apps Designer**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappsdesigner.png)
6. Válassza ki **kérelem**. Ez a művelet határozza meg, hogy egy adott JSON azonosítójú bejövő HTTP-kérelem hasznos tevékenységéért formázva eseményindítót.
7. A kérelem törzsében JSON-séma illessze be a következő kódot:
   
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
   > Másolhatja az URL-cím a HTTP POST után a logikai alkalmazás menti, de először fel kell venni egy műveletet.
   > 
   > 
8. Kattintson a **+ új lépés** a kézi indítási alatt. Kattintson a **művelet hozzáadása**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappcode.png)
9. Keresse meg **SendGrid - e-mail küldése** , és kattintson rá.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappaction.png)
10. Írjon be egy nevet a kapcsolathoz, például **SendGridConnection**, adja meg a **SendGrid API-kulcs** alapteljesítményhez képest, a SendGrid fiók beállítását, majd kattintson az **létrehozása**.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridconnection.png)
11. Saját mindkettőnek e-mail címek hozzáadása a **a** és **való** mezőket. Adja hozzá **távoli figyelési figyelmeztetés [DeviceId]** számára a **tulajdonos** mező. Az a **E-mail törzsének** mezőbe hozzáadása **[DeviceId] jelzett [measurementName] [measuredValue] értékű.** Hozzáadhat **[DeviceId]**, **[measurementName]**, és **[measuredValue]** elemre kattintva a **adatok beszúrásához előző lépéseiből** szakasz.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridaction.png)
12. Kattintson a **mentése** a felső menüben.
13. Kattintson a **kérelem** eseményindító, és másolja a **az URL-címet a Http Post** érték. Az oktatóanyag későbbi részében szüksége az URL-cím.

> [!NOTE]
> A Logic Apps lehetővé teszik a futtatásához [művelet számos különböző típusú] [ lnk-logic-apps-actions] műveletek beleértve az Office 365-ben. 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>A EventProcessor webes feladat beállítása
Ebben a szakaszban az előkonfigurált megoldás a létrehozott logikai alkalmazás csatlakozzon. A feladat végrehajtásához hozzá indul el, a művelet, amely akkor következik be, ha egy eszköz érzékelő érték meghaladja a küszöbértéket a logikai alkalmazás URL-CÍMÉT.

1. A git-ügyfél segítségével klónozza a legújabb verzióját a [azure iot-távoli-ellenőrző github-tárházban][lnk-rmgithub]. Példa:
   
    ```cmd
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. A Visual Studióban nyissa meg a **RemoteMonitoring.sln** a tárházat a helyi másolatát.
3. Nyissa meg a **ActionRepository.cs** fájlt a **infrastruktúra\\tárház** mappát.
4. Frissítés a **actionIds** szótár a **Http Post az URL-címet** , itt megjegyezni a Logic Apps alkalmazást az alábbiak szerint:
   
    ```csharp
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. A módosítások mentése a megoldásban, és zárja be a Visual Studio.

## <a name="deploy-from-the-command-line"></a>Központi telepítése a parancssorból
Ebben a szakaszban központi telepítése a frissített verzióját a távoli figyelési megoldást cserélje le az Azure-ban jelenleg futó verziója.

1. A következő a [fejlesztői beállításról] [ lnk-devsetup] állítsa be a környezetet a telepítési utasításokat.
2. Helyileg telepítéséhez kövesse a [helyi telepítés] [ lnk-localdeploy] utasításokat.
3. Telepíti a felhőbe, és a meglévő felhőalapú üzemelő példány frissítése, kövesse a [felhőalapú üzemelő példány] [ lnk-clouddeploy] utasításokat. Használja a nevét, az eredeti telepítés a központi telepítés nevét. Például ha az eredeti telepítési hívták **demologicapp**, használja a következő parancsot:
   
   ```cmd
   build.cmd cloud release demologicapp
   ```
   
   Ha fut a build parancsfájl, ügyeljen arra, hogy a azonos Azure-fiók, előfizetés, régió, valamint a megoldás létesített használt Active Directory-példányban.

## <a name="see-your-logic-app-in-action"></a>Tekintse meg a Logic Apps alkalmazást működés közben
A távoli felügyeleti előkonfigurált megoldás beállítása alapértelmezés szerint, ha a megoldás két szabályt tartalmaz. Szabályokat is szerepelnek a **SampleDevice001** eszköz:

* Hőmérséklet > 38.00
* Páratartalom > 48.00

A hőmérséklet szabály eseményindítók a **előléptetése riasztás** szabályok eseményindítók művelet és a páratartalom a **SendMessage** művelet. Feltéve, hogy mindkét művelet azonos URL-CÍMÉT használja a **ActionRepository** osztály, vagy a szabály a logic app eseményindítók. Szabályokat is SendGrid segítségével e-mail küldése a **való** cím a riasztás részleteit.

> [!NOTE]
> A logikai alkalmazás továbbra is fennáll, minden alkalommal, amikor a küszöbérték elérése indításához. Felesleges e-mailek elkerülése érdekében tiltsa le a szabályok a megoldás portálon, vagy tiltsa le a logikai alkalmazást a a [Azure-portálon][lnk-azureportal].
> 
> 

Mellett fogadásakor, a logikai alkalmazás futtatásakor a portálon is megtekintheti:

![](media/iot-suite-v1-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>Következő lépések
Most, hogy a logikai alkalmazás már használta az előkonfigurált megoldás csatlakozni az üzleti folyamatokat, hogy többet is megtudhat az előkonfigurált megoldásokat testreszabására szolgáló beállítások:

* [Dinamikus telemetriai adatokat a távoli felügyeleti előkonfigurált megoldás][lnk-dynamic]
* [Eszköz információk metaadatait a távoli felügyeleti előkonfigurált megoldás][lnk-devinfo]

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
