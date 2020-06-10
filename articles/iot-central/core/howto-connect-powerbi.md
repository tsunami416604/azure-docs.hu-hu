---
title: Az Azure IoT Central-adatPower BI-irányítópulton tárolt adatai megjelenítése | Microsoft Docs
description: Az Azure IoT Central Power BI megoldásával jelenítheti meg és elemezheti a IoT Central adatait.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: a484ab2553aeefbbe8c03ae639bdd667e4d9cb8e
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84661193"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Azure IoT Central-beli adatPower BI-irányítópulton tárolt adatai megjelenítése és elemzése

*Ez a témakör a rendszergazdákra és a megoldás-fejlesztőre vonatkozik.*

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Power BI megoldási folyamat":::

Az Azure IoT Central Power BI megoldásával hatékony Power BI irányítópultot hozhat létre a IoT-eszközök teljesítményének figyeléséhez. A Power BI-irányítópulton a következőket teheti:

- Az eszközök által az idő múlásával küldött adatok számának nyomon követése
- Adatkötetek összehasonlítása különböző telemetria-adatfolyamok között
- Szűrés adott eszközök által elküldve
- Egy táblázat legutóbbi telemetria-adatnézetének megtekintése

Ez a megoldás olyan folyamatot állít be, amely beolvassa az adatokat a [folyamatos adatexportálási](howto-export-data-blob-storage.md) Azure Blob Storage-fiókból. A folyamat Azure Functions, Azure Data Factory és Azure SQL Database használatával dolgozza fel és alakítja át az adatfeldolgozást. megjelenítheti és elemezheti az PBIX-fájlként letöltött Power BI-jelentésekben lévő összes adathalmazt. Az összes erőforrás az Azure-előfizetésében jön létre, így az egyes összetevők igényei szerint testreszabhatók.

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseinek végrehajtásához aktív Azure-előfizetésre van szükség. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

A megoldás beállításához a következő erőforrások szükségesek:

- IoT Central alkalmazás. További információ: [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md).
- Folyamatos adatexportálás a telemetria, eszközök és eszközök Azure Blob Storage-ba való exportálására konfigurálva. További információ: [IoT-információk exportálása az Azure-ba célhelyekre](howto-export-data.md).
  - Győződjön meg arról, hogy csak a IoT Central alkalmazás exportálja az adatait a blob-tárolóba.
  - Az [eszközöknek JSON-kódolású üzeneteket kell küldeniük](../../iot-hub/iot-hub-devguide-messages-d2c.md). Az eszközöknek meg kell adniuk `contentType:application/JSON` és vagy `contentEncoding:utf-8` `contentEncoding:utf-16` vagy vagy `contentEncoding:utf-32` az üzenetrendszer tulajdonságait.
- Power BI Desktop (legújabb verzió). Lásd: [Power bi letöltések](https://powerbi.microsoft.com/downloads/).
- Power BI Pro (ha meg szeretné osztani másokkal az irányítópultot).

## <a name="install"></a>Telepítés

A folyamat beállításához navigáljon az [Azure IoT Central oldal Power bi megoldásához](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) a **Microsoft AppSource** webhelyen. Válassza a **Letöltés most**lehetőséget, és kövesse az utasításokat.

A PBIX-fájl megnyitásakor ügyeljen rá, hogy olvassa el és kövesse a fedél oldalon található utasításokat. Ezek az utasítások azt írják le, hogyan csatlakoztatható a jelentés az SQL-adatbázishoz.

## <a name="report"></a>Jelentés

A PBIX-fájl tartalmazza az **eszközök és a telemetria** jelentést, amely az eszközök által elindított telemetria korábbi nézetét jeleníti meg. Lebontja a különböző típusú telemetria, és megjeleníti az eszközök által eljuttatott legutóbbi telemetria is.

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Eszközök és telemetria-jelentés Power BI":::

## <a name="pipeline-resources"></a>Folyamat erőforrásai

A Azure Portal összes olyan Azure-erőforrását elérheti, amely a folyamatot a folyamaton belül hozza létre. Az összes erőforrás abban a csoportban található, amelyet a folyamat beállításakor hozott létre.

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="Erőforráscsoport Azure Portal nézete":::

A következő lista ismerteti a folyamat egyes erőforrásainak a szerepét:

### <a name="azure-functions"></a>Azure Functions

Az Azure Function alkalmazás minden alkalommal aktiválódik, IoT Central egy új fájlt ír a blob Storage-ba. A függvények kinyerik az adatokat a telemetria, az eszközökből és az eszközök sablonjaiból, hogy feltöltsék a Azure Data Factory által használt köztes SQL-táblákat.

### <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory társított szolgáltatásként kapcsolódik az SQL-adatbázishoz. Tárolt eljárásokat futtat, hogy feldolgozza az adatfeldolgozást, és tárolja azt az elemzési táblákban.

Azure Data Factory 15 percenként fut, hogy átalakítsa a legutóbbi köteget az SQL-táblákba való betöltéshez (ez a jelenlegi minimális szám a **kieséses ablak-triggernél**).

### <a name="azure-sql-database"></a>Azure SQL Database

A Azure Data Factory elemzési táblázatok készletét hozza létre a Power BIhoz. Ezeket a sémákat megtekintheti Power BIban, és felhasználhatja saját vizualizációk létrehozásához.

## <a name="estimated-costs"></a>Becsült költségek

Az [Azure IoT Central Power bi megoldás](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) a Microsoft AppSource webhelyen tartalmaz egy, a központilag telepített erőforrások díjszabására mutató hivatkozást.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan jelenítheti meg az adatait Power BIban, a javasolt következő lépés az [eszközök felügyeletének](howto-manage-devices.md)megismerése.
