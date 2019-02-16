---
title: Az Azure IoT Central adatok megjelenítése a Power BI-irányítópult |} A Microsoft Docs
description: A Power BI-megoldás az Azure IoT Central használatával az IoT-központ adatok vizualizálását és elemzését.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/15/2019
ms.topic: conceptual
ms.openlocfilehash: 322be1e13662d92a3cb0a805a9ccaacd05928f7d
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328095"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>A Power BI-Irányítópulton az Azure IoT Central adatok megjelenítése és elemzése

*Ez a témakör a rendszergazdák vonatkozik.*

![A Power BI-megoldás folyamat](media/howto-connect-powerbi/iot-continuous-data-export.png)

A Power BI-megoldás az Azure IoT Central használatával hatékony Power BI-irányítópultra a Teljesítményfigyelő IoT-eszköz létrehozása. A Power BI-irányítópulton a következőket teheti:
- Mennyi adatot küldenek, az eszközök idővel nyomon követése
- Hasonlítsa össze a telemetriát, állapotok és az események között adatmennyiség
- Azonosítsa a mértékek rengeteg jelentő eszközök számára
- Tekintse át az eszköz mérések korábbi trendek
- Problémás számos a kritikus eseményeket küldő eszközök azonosítása

Ez a megoldás állítja be a folyamatot, amely beolvassa az adatokat az Azure Blob storage-fiókjában [folyamatos adatexportálás](howto-export-data.md). Ezeket az adatokat az Azure Functions, az Azure Data Factory és Azure SQL Database dolgozza fel, és az adatok átalakítása halad keresztül. A kimenet lehetnek formájában jelenik meg és elemezheti a Power BI-jelentések PBIX-fájlként letölthető. Ezen erőforrások mindegyike jönnek létre az Azure-előfizetésében, így minden egyes összetevő, hogy illeszkedjen az igényeihez szabhatja.

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Első a [Power BI-megoldását az Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) Microsoft AppSource-ból.

## <a name="prerequisites"></a>Előfeltételek
A megoldás beállítása a következőkre van szükség:
- Egy Azure-előfizetéshez való hozzáférés
- Exportált adatok használatával [folyamatos adatexportálás](howto-export-data.md) az IoT-központ alkalmazásból. Azt javasoljuk, hogy bekapcsolja a mérések, eszközök és eszköz-sablon adatfolyam a legtöbbet hozhassa ki a Power BI-irányítópulton.
- A Power BI Desktop (legújabb verzió)
- A Power BI Pro (Ha azt szeretné, az irányítópult megosztása másokkal)

## <a name="reports"></a>Jelentések

Két jelentés automatikusan jönnek létre. 

Az első jelentés eszköz által jelentett mérések állapotelőzményeinek a nézetét mutatja, és a különböző típusú mérések és eszközöket, amelyek a legmagasabb száma mérést elküldött felszámolja.

![A Power BI-jelentésoldalak 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

A második a jelentés az események mélyebb ismertetőinken és hibák és figyelmeztetések jelentett állapotelőzményeinek a nézetét jeleníti meg. Azt is bemutatja, hogy mely eszközök mentorál, az események számát vesszük figyelembe jelent valamint kifejezetten hiba és figyelmeztetési események.

![A Power BI-jelentésoldalak 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>Architektúra
Az Azure Portalon létrehozott erőforrásokat is elérhetők. Minden tartalmát úgy egy erőforráscsoportot.

![Erőforráscsoport nézete az Azure Portalon](media/howto-connect-powerbi/azure-deployment.PNG)

Az egyes erőforrások adatait, és hogyan használt lekéri az alábbiakban ismertetjük.

### <a name="azure-functions"></a>Azure Functions
Az Azure-függvényalkalmazást lekérdezi indul el, minden alkalommal, amikor egy új Blob storage-bA íródnak. A functions bontsa ki a mezőket belül minden egyes mérések, eszközök és eszközök nevű fájlt, és feltölti a több köztes SQL-táblák az Azure Data Factory által használt.

### <a name="azure-data-factory"></a>Azure Data Factory
Az Azure Data Factory csatlakozik az SQL database társított szolgáltatás. Tárolt eljárás tevékenységeket, amelyek az adatok feldolgozására, és tárolja a elemzési táblákat futtatja.

### <a name="azure-sql-database"></a>Azure SQL Database
Ezek a táblák a rendszer automatikusan létrehozza az alapértelmezett jelentések feltöltése. Ismerkedés a Power BI-ban ezek sémák és hozhat létre saját vizualizációkat az adatokon.

| Tábla neve |
|------------|
|[analytics]. [Mérések]|
|[analytics]. [Üzenetek]|
|[szakasz]. [Mérések]|
|[analytics]. [Tulajdonságai]|
|[analytics]. [PropertyDefinitions]|
|[analytics]. [MeasurementDefinitions]|
|[analytics]. [Eszközök]|
|[analytics]. [DeviceTemplates]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>Becsült költségek

Itt látható az Azure-költségek (Azure-függvény, a Data Factory, az Azure SQL) becsült érint. Az összes árak USD-ben. Ne feledje, hogy árak régiónként, így mindig meg kell lekérni a tényleges árak az egyes szolgáltatások legújabb díjai.
A következő Alapértelmezések vannak beállítva, a sablonban (ezt módosíthatja ezeket dolgot első beállítását követően):

- Azure Functions: App Service-csomag S1, $74.40/ hó
- Azure SQL S1, ~$30/month

Javasoljuk, hogy Ismerkedjen meg a különböző díjszabási ajánlatait, és a Teljesítménybeállítások dolog, hogy illeszkedjen az igényeihez.

## <a name="resources"></a>További források

Látogasson el az appsource-ban lekérése a [Power BI-megoldását az Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan jelenítheti meg az adatok a Power bi-ban, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Eszközök kezelése](howto-manage-devices.md)