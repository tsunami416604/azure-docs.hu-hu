---
title: "A csatlakoztatott gyári megoldás - Azure testreszabása |} Microsoft Docs"
description: "Az előre konfigurált csatlakoztatott gyári megoldás beállításainak testreszabása leírását."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 09b146740413e74e3030bf3a6cb660a3cfabd239
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Testre szabhatja, hogy a csatlakoztatott gyári megoldás OPC EE-kiszolgálóinak adatait jeleníti meg

A csatlakoztatott gyári megoldás összesíti, és a megoldás kapcsolódó OPC EE-kiszolgálók adatait jeleníti meg. Keresse meg, és parancsainak elküldését a OPC EE-kiszolgálók a megoldásban. Az OPC UA architektúráról a [Csatlakoztatott gyár – GYIK](iot-suite-faq-cf.md) fejezetben talál további információt.

Összesített adatai a megoldás például a teljes berendezések hatékonyságát (OEE) és fő teljesítménymutatók (KPI-k), megtekinthető az irányítópulton a gyári, a sor és az állomás szintjén. Az alábbi képernyőfelvételen látható OEE és KPI értékeit a **szerelvény** állomás, a **termelési sor 1**, a a **München** gyári:

![A megoldás OEE és a KPI értékének – példa][img-oee-kpi]

A megoldás lehetővé teszi a OPC EE-kiszolgálókról, úgynevezett adott adatelemek részletes információk megtekintése *állomások*. Az alábbi képernyőfelvételen látható függvényében egy adott állomásról előállított elemek száma:

![Felvétel az előállított elemek száma.][img-manufactured-items]

A diagramokon választásakor ismerheti meg az adatokat további időt adatsorozat Insights (ÁME):

![Adatokba idő adatsorozat Insights segítségével][img-tsi]

Ez a cikk ismerteti:

- Hogyan adatokat szeretné elérhetővé tenni a különböző nézetek a megoldásban.
- Hogyan szabhatja testre a módját a megoldás adatait jeleníti meg.

## <a name="data-sources"></a>Adatforrások

A csatlakoztatott gyári megoldás a megoldás kapcsolódó OPC EE-kiszolgálók adatait jeleníti meg. Az alapértelmezett telepítés magában foglalja a gyári szimuláció futtató több OPC EE. A saját OPC EE-kiszolgálót is hozzáadhat, amelyek [átjárón keresztül csatlakozni] [ lnk-connect-cf] a megoldáshoz.

A csatlakoztatott OPC EE-kiszolgáló küldhet a megoldás az irányítópulton adatelemek tallózással:

1. Keresse meg a **jelöljön ki egy OPC EE** megtekintése:

    ![Navigáljon a válassza egy OPC EE-kiszolgáló megtekintése][img-select-server]

1. Válasszon kiszolgálót, majd kattintson a **Connect**. Kattintson a **folytatása** Ha a biztonsági figyelmeztetés jelenik meg.

    > [!NOTE]
    > Ez a figyelmeztetés csak egyszer jelenik meg minden olyan kiszolgálón, és a megoldás irányítópult és a kiszolgáló közötti megbízhatósági kapcsolatot létesít.

1. A kiszolgáló elküldheti a megoldáshoz adatelemek tallózhatnak. A megoldáshoz küldött útjuk zöld pipa jelzi:

    ![Közzétett elemek][img-published]

1. Ha Ön egy *rendszergazda* a megoldást választhatja ki elérhetővé a csatlakoztatott gyári megoldásban adatelemet közzététele. A rendszergazdák is adatelemek értékének módosítása és hívható meg metódus a OPC EE-kiszolgálón.

## <a name="map-the-data"></a>Az adatok leképezése

A csatlakoztatott gyári megoldás le, és a közzétett adatelemeket összesíti a OPC EE-kiszolgálóról a különböző nézetek a megoldásban. Ha a megoldás a csatlakoztatott gyári megoldás központilag telepíti az Azure-fiókjával. A JSON-fájl, a Visual Studio csatlakoztatott gyári megoldásban ez leképezési információkat tárolja. Megtekintheti és módosíthatja a Visual Studio megoldás a csatlakoztatott gyárban JSON-konfigurációs fájlt. A módosítást követően központilag telepítheti a megoldás.

A konfigurációs fájlt is használhatja:

- A meglévő szimulált előállítók, éles sorok és állomások szerkesztése.
- Valós OPC EE-kiszolgálókhoz, amelyek a megoldással az adatok leképezése.

Klónozza a csatlakoztatott gyári Visual Studio megoldás egy példányát, a következő git paranccsal:

`git clone https://github.com/Azure/azure-iot-connected-factory.git`

A fájl **ContosoTopologyDescription.json** a leképezés a OPC EE-kiszolgáló az elemeket a nézetek meghatározása a csatlakoztatott gyári megoldás irányítópulton. A konfigurációs fájlban található a **Contoso\Topology** mappájában a **WebApp** projektet a Visual Studio-megoldásban.

A JSON-fájl tartalmának gyári, termelési sor és állomás csomópontok hierarchikus vannak rendezve. Ezt a hierarchiát a navigációs hierarchia a csatlakoztatott gyári irányítópult határozza meg. A hierarchia minden egyes csomóponton értékek meghatározzák, hogy az irányítópulton megjelenő információkat. Például a JSON-fájl tartalmazza a következő értékek München a beépített:

```json
"Guid": "73B534AE-7C7E-4877-B826-F1C0EA339F65",
"Name": "Munich",
"Description": "Braking system",
"Location": {
    "City": "Munich",
    "Country": "Germany",
    "Latitude": 48.13641,
    "Longitude": 11.57754
},
"Image": "munich.jpg"
```

A nevét, leírását és helyen jelennek meg ebben a nézetben az irányítópulton:

![Az irányítópult München adatok][img-munich]

Minden egyes gyári, a termelési sor és a állomás van az image tulajdonság. Ezeket a JPEG-fájlok találhatók a **Content\img** mappájában a **WebApp** projekt. A kép fájlok megjelenítése a csatlakoztatott gyári irányítópulton.

Minden állomás több részletes meghatározó tulajdonságok biztosítása a leképezés a OPC EE adatelemek tartalmazza. A következő szakaszok ismertetik ezeket a tulajdonságokat:

### <a name="opcuri"></a>OpcUri

A **OpcUri** értéke az EE OPC-alkalmazás, amely egyedileg azonosítja a OPC EE-kiszolgáló URI. Például a **OpcUri** a szerelvény állomás éles sor 1 München a következőhöz hasonló, a következő érték: **urn: scada2194:ua:munich:productionline0:assemblystation**.

Az URI-azonosítók a csatlakoztatott OPC EE-kiszolgálók a megoldás irányítópultjának tekintheti meg:

![Nézet OPC EE kiszolgáló URI-azonosítók][img-server-uris]

### <a name="simulation"></a>Szimuláció

Az információk a **szimuláció** csomópont csak a OPC EE szimuláció, amelyen a OPC EE-kiszolgálókon alapértelmezés szerint törlődnek az. A valós OPC EE-kiszolgáló nem szolgál.

### <a name="kpi1-and-kpi2"></a>Kpi1 és Kpi2

Ezek a csomópontok ismertetik, hogyan hozzájárul az adatok az állomásról a két KPI érték az irányítópulton. Alapértelmezett telepítésében ezen KPI értékei óránként egységek és óránként kWh. A megoldás kiszámítja a KPI vales állomás szintjén, és összesíti azokat az éles sor és a gyári szintjén.

Egyes KPI-KET egy minimális, maximális és célértékéhez rendelkezik. Minden KPI értéket adhat meg riasztási műveletek végrehajtásához a csatlakoztatott gyári megoldás. Az alábbi kódrészletben láthatja a KPI-definíciókat a szerelvény állomás éles sor München 1:

```json
"Kpi1": {
  "Minimum": 150,
  "Target": 300,
  "Maximum": 600
},
"Kpi2": {
  "Minimum": 50,
  "Target": 100,
  "Maximum": 200,
  "MinimumAlertActions": [
    {
      "Type": "None"
    }
  ]
}
```

Az alábbi képernyőfelvételen a KPI adatainak megjelenítése az irányítópulton.

![Az irányítópult KPI-információk][lnk-kpi]

### <a name="opcnodes"></a>OpcNodes

A **OpcNodes** csomópontok azonosíthatja a közzétett adatelemeket a OPC EE-kiszolgálóról, és adja meg, hogyan kell feldolgozni az adatokat.

A **NodeId** érték azonosítja az adott OPC EE NodeID a OPC EE-kiszolgálóról. A szerelvény állomás gyártási sor München 1 első csomópontjának értéke **ns = 2, i = 385**. A **NodeId** érték határozza meg, az elem olvasni a OPC EE-kiszolgálóról, és a **melynek** biztosít egy felhasználóbarát nevet a az irányítópulton az adatok.

Más csomópontokra társított értékek a következő táblázat tartalmazza:

| Érték | Leírás |
| ----- | ----------- |
| Találati pontosság  | A KPI-t és OEE értékek hozzájárul az adatok. |
| Műveleti kód     | Hogyan összesíti az adatokat. |
| egység      | Az irányítópult használatához egység.  |
| Látható    | Hogy megjelenjenek-e ezt az értéket az irányítópulton. Egyes értékek számítások szerepel, de nem jelenik meg.  |
| Maximális    | A maximális érték, amely a riasztást az irányítópulton. |
| MaximumAlertActions | A riasztás válasz műveletet. Például egy vonatkozó parancs küldése a állomáshoz. |
| ConstValue | A számításhoz használt konstans érték. |

## <a name="deploy-the-changes"></a>A módosítások telepítése

Miután végzett a módosítások elvégzése az **ContosoTopologyDescription.json** fájlt, újra kell telepítenie a csatlakoztatott gyári megoldás az Azure-fiókjába.

A **azure iot-csatlakoztatott-gyári** tárház tartalmaz egy **build.ps1** PowerShell parancsprogram, amellyel újraépítéséhez és a megoldás üzembe helyezéséhez.

## <a name="next-steps"></a>Következő lépések

További információ a csatlakoztatott gyári előre konfigurált megoldásról elolvasni a következő cikkeket:

* [Előre konfigurált csatlakoztatott gyár megoldás – bemutató][lnk-rm-walkthrough]
* [A beépített csatlakoztatott egy átjáró üzembe helyezéséhez][lnk-connect-cf]
* [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions]
* [Csatlakoztatott gyár – GYIK](iot-suite-faq-cf.md)
* [GYAKORI KÉRDÉSEK][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-faq]: iot-suite-v1-faq.md