---
title: "A távoli felügyeleti megoldás - Azure testreszabása |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan érheti el a távoli felügyeleti előkonfigurált megoldás forráskódját."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/07/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 0e15193975b3142f604c2f3c0391b21d6fb1fac1
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="customize-the-remote-monitoring-preconfigured-solution"></a>A távoli felügyeleti előkonfigurált megoldás testreszabása

Ez a cikk ismerteti, hogyan hozzáférni a forráskódot, és a távoli figyelésének testreszabása, előre konfigurált megoldás. A cikk ismerteti:

* A GitHub-adattárak, amely a forráskódot, és a mikroszolgáltatások az előkonfigurált megoldás alkotó forrásokat tartalmazza.
* Írja be például az új eszköz hozzáadása a testreszabási szabhatják.

## <a name="project-overview"></a>Projekt áttekintése

### <a name="implementations"></a>Megvalósítások

A távoli felügyeleti megoldás .NET és a Java-megvalósítások rendelkezik. Mindkét megoldások hasonló funkciókat biztosít, és az azonos alapul szolgáló Azure-szolgáltatás támaszkodnak. A legfelső szintű GitHub-adattárak itt található:

* [.NET-megoldás](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java-megoldás](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

### <a name="microservices"></a>Mikroszolgáltatások

Ha érdekli a megoldás egy adott szolgáltatása, minden egyes mikroszolgáltatási a GitHub-adattárak végezheti el. Minden mikroszolgáltatási valósítja meg a megoldást funkciót egy másik része. Általános architektúrája kapcsolatos további információkért lásd: [megoldásarchitektúra távoli megfigyelési előre konfigurált](iot-suite-remote-monitoring-sample-walkthrough.md).

Ez a táblázat minden egyes nyelvekhez tartozó mikroszolgáltatási aktuális rendelkezésre állását foglalja össze:

<!-- please add links for each of the repos in the table, you can find them here https://github.com/Azure/azure-iot-pcs-team/wiki/Repositories-->

| Mikroszolgáltatási      | Leírás | Java | .NET |
| ----------------- | ----------- | ---- | ---- |
| Webes felhasználói felület            | Webalkalmazás távoli figyelési megoldást igényelnek. Felhasználói felület React.js keretrendszer használatával valósít meg. | [N/A(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) | [N/A(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) |
| Az IoT Hub-kezelő   | Az IoT Hub kommunikációt kezeli.        | [Rendelkezésre álló](https://github.com/Azure/iothub-manager-java) | [Rendelkezésre álló](https://github.com/Azure/iothub-manager-dotnet)   |
| Authentication    |  Kezeli az Azure Active Directory-integráció.  | Még nem érhető el | [Rendelkezésre álló](https://github.com/Azure/pcs-auth-dotnet)   |
| Eszköz szimulálása | Kezeli a szimulált eszköz készletét. | Még nem érhető el | [Rendelkezésre álló](https://github.com/Azure/device-simulation-dotnet)   |
| Telemetria         | Elérhetővé teszi telemetriát a felhasználói felületen. | [Rendelkezésre álló](https://github.com/Azure/device-telemetry-java) | [Rendelkezésre álló](https://github.com/Azure/device-telemetry-dotnet)   |
| Telemetria ügynök   | Elemzi a telemetriai adatok adatfolyam tárolja az üzeneteket az Azure IoT Hub és meghatározott szabályok szerint riasztásokat állít elő.  | [Rendelkezésre álló](https://github.com/Azure/telemetry-agent-java) | [Rendelkezésre álló](https://github.com/Azure/telemetry-agent-dotnet)   |
| Felhasználói felület Config         | A felhasználói felület a konfigurációs adatok kezeli. | [Rendelkezésre álló](https://github.com/azure/pcs-ui-config-java) | [Rendelkezésre álló](https://github.com/azure/pcs-ui-config-dotnet)   |
| Tárolási adapter   |  Kezeli a társzolgáltatás interakció.   | [Rendelkezésre álló](https://github.com/azure/pcs-storage-adapter-java) | [Rendelkezésre álló](https://github.com/azure/pcs-storage-adapter-dotnet)   |
| Fordított proxy     | Egy egyedi végpontot keresztül felügyelt módon tesz elérhetővé a saját erőforrásokat. | Még nem érhető el | [Rendelkezésre álló](https://github.com/Azure/reverse-proxy-dotnet)   |

A Java megoldásokkal jelenleg használ, a .NET hitelesítés, a szimuláció és a fordított proxy mikroszolgáltatások létrehozására. Ezek mikroszolgáltatások helyébe Java-verziót, amint azok elérhetővé válnak.

## <a name="presentation-and-visualization"></a>Bemutató és -megjelenítésre

A következő szakaszok ismertetik a távoli felügyeleti megoldás bemutató és képi megjelenítéseket réteg beállításait:

### <a name="change-the-logo-in-the-ui"></a>Módosítsa a felhasználói felületen az embléma

Az alapértelmezett telepítési a Contoso vállalat neve és embléma használ a felhasználói felületen. A felhasználói felületi elemei a vállalat nevének és embléma megjelenítendő módosítása:

1. Az alábbi parancs segítségével a webes felhasználói felületén tárház klónozása:

    ```cmd/sh
    git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
    ```

1. A vállalatnév módosításához nyissa meg a `src/common/lang.js` fájlt egy szövegszerkesztőben.

1. A fájlban keresse meg a következő sort:

    ```js
    CONTOSO: 'Contoso',
    ```

1. Cserélje le `Contoso` a vállalat nevével. Példa:

    ```js
    CONTOSO: 'YourCo',
    ```

1. Mentse a fájlt.

1. Az embléma frissítéséhez adja hozzá az új SVG-fájl a `assets/icons` mappát. A meglévő embléma a `assets/icons/Contoso.svg` fájlt.

1. Nyissa meg a `src/components/layout/leftNav/leftNav.js` fájlt egy szövegszerkesztőben.

1. A fájlban keresse meg a következő sort:

    ```js
    import ContosoIcon from '../../../assets/icons/Contoso.svg';
    ```

1. Cserélje le `Contoso.svg` a nevet, a embléma fájlt. Példa:

    ```js
    import ContosoIcon from '../../../assets/icons/YourCo.svg';
    ```

1. A fájlban keresse meg a következő sort:

    ```js
    alt="ContosoIcon"
    ```

1. Cserélje le `ContosoIcon` rendelkező a `alt` szöveg. Példa:

    ```js
    alt="YourCoIcon"
    ```

1. Mentse a fájlt.

1. A módosítások ellenőrzéséhez futtathatja a módosított `webui` a helyi számítógépen. Megtudhatja, hogyan lehet létrehozni, és futtassa a `webui` megoldás helyileg, lásd: [Build, futtatása és a vizsgálati helyileg](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/README.md#build-run-and-test-locally) a a `webui` GitHub tárház információs fájl.

1. A módosítások központi telepítéséhez lásd: a [fejlesztői útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide).

<!--

### Add a new KPI to the Dashboard page

The following steps describe how to add a new KPI to display on the **Dashboard** page. The new KPI shows information about the number of alarms with specific status values as a pie chart:

1. Step 1

1. Step 2
-->

### <a name="customize-the-map"></a>A térkép testreszabása

Tekintse meg a [testreszabás térkép](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) a lapot a Githubon a leképezési összetevők a megoldásban.

<!--
### Customize the telemetry chart

See the [Customize telemetry chart](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of the telemetry chart components in the solution.

### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

### Duplicate an existing control

To duplicate an existing UI element such as a chart or alert, see the [Duplicate a control](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub.

-->

### <a name="other-customization-options"></a>Egyéb testreszabási lehetőségek

További módosításához a bemutató és képi megjelenítéseket réteg a távoli felügyeleti megoldás, szerkesztheti a kódot. A megfelelő GitHub-adattárak a következők:

* [UIConfig (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [UIConfig (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Az Azure rendszerű számítógépek távoli megfigyelési WebUI](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="device-connectivity-and-streaming"></a>Eszközkapcsolatok és adatfolyamként történő továbbítását

A következő szakaszok ismertetik az eszköz kapcsolat és a távoli felügyeleti megoldás a folyamatos átviteli réteg beállításait. [Eszközmodellek](https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models) írják le, az eszköz- és telemetria a megoldásban. Eszközmodellek szimulált és a fizikai eszközök használhatja.

A fizikai eszköz megvalósítási példát talál [csatlakoztassa az eszközt a távoli felügyeleti előkonfigurált megoldás](iot-suite-connecting-devices-node.md).

Ha használ egy _fizikai eszköz_, meg kell adnia az ügyfélalkalmazás az eszköz modell, amely az eszköz metaadatait és telemetria-meghatározást tartalmaz.

Az alábbi szakaszok ismertetik a szimulált eszköz eszközmodellek használata:

### <a name="add-a-telemetry-type"></a>A telemetria-típus hozzáadása

A Contoso bemutató megoldásban a eszköztípusok esetében adja meg mindegyik eszköztípus küldő telemetriai adatok. A további telemetriai típusoknak a megadásához, hogy egy eszköz küldhet telemetriai definíciók metaadat, a megoldás. Ha ezt a formátumot használja, az irányítópult-t használ fel a telemetriát és a rendelkezésre álló metódusok dinamikusan, és nem kell módosítani a felhasználói felületen. Azt is megteheti módosíthatja az eszköz típus definíciója a megoldásban.

További információt az egyéni telemetriai hozzáadása a _eszköz szimulátor_ mikroszolgáltatási, lásd: [a megoldás tesztelése szimulált eszközökkel](iot-suite-remote-monitoring-test.md).

### <a name="add-a-device-type"></a>Eszköz-típus hozzáadása

A Contoso bemutató megoldás egyes minta eszköztípusok határozza meg. A megoldás lehetővé teszi, hogy az adott alkalmazás követelményeknek megfelelő egyéni eszköztípus adható meg. Például a vállalat használhatja egy ipari átjárót az elsődleges eszköz, a megoldás csatlakozik.

Az eszköz egy pontos ábrázolását létrehozásához módosítania kell a megfelelő az eszköz követelményei az eszközön futó alkalmazást.

További információt az új eszköz típust a _eszköz szimulátor_ mikroszolgáltatási, lásd: [a megoldás tesztelése szimulált eszközökkel](iot-suite-remote-monitoring-test.md).

### <a name="define-custom-methods-for-simulated-devices"></a>A szimulált eszköz egyéni módszerek meghatározása

A szimulált eszköz egyéni módszerek definiálása a távoli felügyeleti megoldás, lásd: [Eszközmodellek](https://github.com/Azure/device-simulation-dotnet/wiki/%5BAPI-Specifications%5D-Device-Models) a GitHub-tárházban.

<!--
#### Using the simulator service

TODO: add steps for the simulator microservice here
-->

#### <a name="using-a-physical-device"></a>Fizikai eszköz használata

A fizikai eszközök módszerek és a feladatok végrehajtásához a következő IoT-központ cikkekben talál:

* [Ismertetés és az IoT-központ közvetlen metódusok](../iot-hub/iot-hub-devguide-direct-methods.md).
* [Több eszközön feladatok ütemezése](../iot-hub/iot-hub-devguide-jobs.md).

### <a name="other-customization-options"></a>Egyéb testreszabási lehetőségek

Az eszköz kapcsolat és a távoli felügyeleti megoldás a folyamatos átviteli réteg további módosításához, szerkesztheti a kódot. A megfelelő GitHub-adattárak a következők:

* [Telemetriát (.NET)](https://github.com/Azure/device-telemetry-dotnet)
* [Telemetriát (Java)](https://github.com/Azure/device-telemetry-java)
* [Telemetria ügynök (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [Telemetria ügynök (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="data-processing-and-analytics"></a>Adatfeldolgozás és -elemzés

<!--
The following sections describe options to customize the data processing and analytics layer in the remote monitoring solution:

### Rules and actions

See the [Customize rules and actions](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to customize the rules and actions in solution.


### Other customization options
-->

Adatok feldolgozása és a távoli felügyeleti megoldás analytics réteg módosítása, szerkesztheti a kódot. A megfelelő GitHub-adattárak a következők:

* [Telemetria ügynök (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [Telemetria ügynök (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="infrastructure"></a>Infrastruktúra

<!--
The following sections describe options for customizing the infrastructure services in the remote monitoring solution:

### Change storage

The default storage service for the remote monitoring solution is Cosmos DB. See the [Customize storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses.

### Change log storage

The default storage service for logs is Cosmos DB. See the [Customize log storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses for logging.

### Other customization options
-->

Az infrastruktúra a távoli felügyeleti megoldás módosítása, szerkesztheti a kódot. A megfelelő GitHub-adattárak a következők:

* [IOT hubbal Manager (.NET)](https://github.com/Azure/iothub-manager-dotnet)
* [IOT hubbal Manager (Java)](https://github.com/Azure/iothub-manager-java)
* [Tárolóadapter (.NET)](https://github.com/Azure/pcs-storage-adapter-dotnet)
* [Tárolóadapter (Java)](https://github.com/Azure/pcs-storage-adapter-java)

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta segítségével testre szabhatja az előkonfigurált megoldás rendelkezésre álló erőforrásokról.

Fogalmi kapcsolatos további információkért a távoli felügyeleti előkonfigurált megoldás lásd: [távoli figyelési architektúrája](iot-suite-remote-monitoring-sample-walkthrough.md)

A távoli felügyeleti megoldás testreszabásával kapcsolatos további információkért lásd:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->