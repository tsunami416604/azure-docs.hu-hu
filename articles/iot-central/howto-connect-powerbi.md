---
title: Az Azure IoT Central adatok megjelenítése a Power BI-irányítópult |} A Microsoft Docs
description: Használja az Azure IoT Central elemzések Power BI megoldássablon az IoT-központ adatok vizualizálását és elemzését.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 6f8bed8aba43d77cdfad49f4fe62a4c7aa2c5ce3
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368069"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>A Power BI-Irányítópulton az Azure IoT Central adatok megjelenítése és elemzése

*Ez a témakör a rendszergazdák vonatkozik.*

![A Power BI-megoldás sablon folyamat](media/howto-connect-powerbi/iot-continuous-data-export.png)

Az Azure IoT Central elemzések Power BI megoldássablon hatékony Power BI-irányítópultra a Teljesítményfigyelő IoT-eszköz létrehozásához használja. A Power BI-irányítópulton a következőket teheti:
- Mennyi adatot küldenek, az eszközök idővel nyomon követése
- Hasonlítsa össze a telemetriát, állapotok és az események között adatmennyiség
- Azonosítsa a mértékek rengeteg jelentő eszközök számára
- Tekintse át az eszköz mérések korábbi trendek
- Problémás számos a kritikus eseményeket küldő eszközök azonosítása

Ez a megoldássablon állítja be a folyamatot, amely beolvassa az adatokat az Azure Blob storage-fiókjában [folyamatos adatexportálás](howto-export-data.md). Ezeket az adatokat az Azure Functions, az Azure Data Factory és Azure SQL Database dolgozza fel, és az adatok átalakítása halad keresztül. A kimenet lehetnek formájában jelenik meg és elemezheti a Power BI-jelentések PBIX-fájlként letölthető. Ezen erőforrások mindegyike jönnek létre az Azure-előfizetésében, így minden egyes összetevő, hogy illeszkedjen az igényeihez szabhatja. Ez a megoldássablon az teljes mértékben nyílt forráskódú, így további információ az architektúra és a megoldás kibővítése meglátogatják a [GitHub-adattárat](https://aka.ms/iotcentralgithubpowerbisolutiontemplate).

## <a name="get-the-azure-iot-central-analytics-solution-templatehttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Első a [Azure IoT Central analitikai megoldássablonját](https://aka.ms/iotcentralpowerbisolutiontemplate) Microsoft AppSource-ból

## <a name="prerequisites"></a>Előfeltételek
A sablon beállítása az alábbiak szükségesek:
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

## <a name="resources"></a>További források

Látogasson el az appsource-ban lekérése a [Azure IoT Central analitikai megoldássablonját](https://aka.ms/iotcentralpowerbisolutiontemplate).

Látogasson el a [GitHub-adattárat](https://aka.ms/iotcentralgithubpowerbisolutiontemplate) további információ az architektúra és kiterjesztheti a megoldást.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan jelenítheti meg az adatok a Power bi-ban, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Eszközök kezelése](howto-manage-devices.md)
