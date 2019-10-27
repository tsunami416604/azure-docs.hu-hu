---
title: Az Azure IoT Central-adatPower BI-irányítópulton tárolt adatai megjelenítése | Microsoft Docs
description: Az Azure IoT Central Power BI megoldásával jelenítheti meg és elemezheti a IoT Central adatait.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: de22b4431da61af9dbd22ccc024cbd58b6ae4a89
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954304"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Azure IoT Central-beli adatPower BI-irányítópulton tárolt adatai megjelenítése és elemzése

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

*Ez a témakör a rendszergazdákra vonatkozik.*

![Power BI megoldási folyamat](media/howto-connect-powerbi/iot-continuous-data-export.png)

Az Azure IoT Central Power BI megoldásával hatékony Power BI irányítópultot hozhat létre a IoT-eszközök teljesítményének figyeléséhez. A Power BI-irányítópulton a következőket teheti:
- Az eszközök által az idő múlásával küldött adatok számának nyomon követése
- Adatmennyiség összehasonlítása a telemetria, az állapotok és az események között
- A sok mérést jelentő eszközök azonosítása
- Az eszközök mérésének korábbi trendjeinek megfigyelése
- Olyan problémás eszközök azonosítása, amelyek sok kritikus eseményt küldenek

Ez a megoldás állítja be azt a folyamatot, amely az Azure Blob Storage-fiókban lévő adatok [folyamatos adatexportálásból](howto-export-data-blob-storage.md)való elvégzését végzi. Ezek az adatfolyamatok Azure Functions, Azure Data Factory és Azure SQL Database az adatfeldolgozásra és-átalakításra. A kimenet megjeleníthető és elemezhető egy Power BI jelentésben, amelyet PBIX fájlként tölthet le. Az összes ilyen erőforrás az Azure-előfizetésében jön létre, így az egyes összetevők igényei szerint testreszabhatók.

> [!Note] 
> Az Azure IoT Central Power BI megoldása olyan IoT Central alkalmazásokkal működik, amelyek nem támogatják a IoT Plug and Play (előzetes verziójú alkalmazások ma)

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Szerezze [be az Azure IoT Central Power bi-megoldását](https://aka.ms/iotcentralpowerbisolutiontemplate) Microsoft AppSource.

## <a name="prerequisites"></a>Előfeltételek
A megoldás beállításához a következők szükségesek:
- Hozzáférés egy Azure-előfizetéshez
- IoT Central IoT-Plug and Playt nem támogató alkalmazás (az előzetes verziójú alkalmazások ma)
- Folyamatos adatexportálás az Azure Blob Storage-be a IoT Central alkalmazásból
    - Győződjön meg arról, hogy az adatformátum Avro
    - Javasoljuk, hogy kapcsolja be a méréseket, eszközöket és az eszközök sablonjait, hogy a lehető legtöbbet hozza ki a Power BI irányítópulton.
    - Ismerje meg [, hogyan állíthatja be a folyamatos adatexportálást](howto-export-data-blob-storage.md)
- Power BI Desktop (legújabb verzió)
- Power BI Pro (ha az irányítópultot másokkal is meg szeretné osztani)

## <a name="reports"></a>Jelentések

Két jelentés automatikusan létrejön. 

Az első jelentés az eszközök által jelentett mérések korábbi nézetét jeleníti meg, és lebontja a különböző típusú méréseket és eszközöket, amelyek a legnagyobb mennyiségű mérést elküldötték.

![Power BI 1. jelentés lapja](media/howto-connect-powerbi/template-page1-hasdata.PNG)

A második jelentés az események mélyebb betekintését mutatja be, és a jelzett hibák és figyelmeztetések előzményeit jeleníti meg. Azt is megmutatja, hogy mely eszközök jelentik be a legtöbb eseményt, valamint konkrétan a hibák és a figyelmeztető események számát.

![Power BI 2. jelentés lapja](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>Architektúra
Az összes létrehozott erőforrás a Azure Portalban érhető el. Mindennek egy erőforráscsoport alatt kell lennie.

![Az erőforráscsoport Azure Portal-nézete](media/howto-connect-powerbi/azure-deployment.PNG)

Az egyes erőforrások sajátosságait és a használatuk módját az alábbiakban ismertetjük.

### <a name="azure-functions"></a>Azure Functions
Az Azure Function alkalmazás minden alkalommal aktiválódik, amikor egy új fájlt ír a blob Storage-ba. A függvények kinyerik a mezőket az egyes méréseken, eszközökön és az eszközök sablonjain, és több köztes SQL-táblázatot töltenek fel Azure Data Factory.

### <a name="azure-data-factory"></a>Azure Data Factory
A Azure Data Factory társított szolgáltatásként kapcsolódik az SQL-adatbázishoz. Futtatja a tárolt eljárási tevékenységeket, amelyek feldolgozzák az adatfeldolgozást és tárolják azokat az elemzési táblákban.

### <a name="azure-sql-database"></a>Azure SQL Database
Ezek a táblák automatikusan létrejönnek az alapértelmezett jelentések feltöltéséhez. Fedezze fel ezeket a sémákat a Power BIban, és saját vizualizációkat hozhat létre ezen az adatain.

| Tábla neve |
|------------|
|[Analitika]. Mérések|
|[Analitika]. Üzenetek|
|[szakasz]. Mérések|
|[Analitika]. Tulajdonságok|
|[Analitika]. [PropertyDefinitions]|
|[Analitika]. [MeasurementDefinitions]|
|[Analitika]. Eszközök|
|[Analitika]. [DeviceTemplates]|
|[dbo]. dátum|
|[dbo]. Változáskövetési|

## <a name="estimated-costs"></a>Becsült költségek

Itt látható az Azure-költségek (Azure-függvény, Data Factory, Azure SQL) becsült értéke. Az árak USD-ben vannak. Ne feledje, hogy az árak régiónként változnak, ezért mindig tekintse meg az egyes szolgáltatások legújabb árait, hogy megkapják a tényleges árakat.
A következő alapértelmezett beállítások vannak beállítva a sablonban (ezeket a beállítások beállítása után módosíthatja):

- Azure Functions: App Service S1 csomag, $74.40/hó
- Azure SQL S1, ~ $30/hó

Javasoljuk, hogy ismerkedjen meg a különböző díjszabási lehetőségekkel és az igényeinek megfelelően.

## <a name="resources"></a>Segédanyagok és eszközök

Látogasson el a AppSource-re az [Azure IoT Central Power bi megoldásának](https://aka.ms/iotcentralpowerbisolutiontemplate)beszerzéséhez.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan jelenítheti meg az adatait Power BIban, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Eszközök kezelése](howto-manage-devices.md)