---
title: Az Event Hubs Azure Stream Analytics használatával adatokat feldolgozni |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan az Azure Stream Analytics-feladat használatával az Azure event hub adatainak feldolgozásához.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: f179687b0983e145244e228a3d3b06b4eabead48
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723414"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Dolgozza fel az adatokat az eseményközpontból, az Azure Stream Analytics használatával
Az Azure Stream Analytics szolgáltatás megkönnyíti a képes feldolgozni a folyamat, és elemezheti a streamelt adatokat az Azure Event Hubsból, így hatékony elemzéseket valós idejű műveleteket hajthat végre. Ez az integráció lehetővé teszi, hogy gyorsan létrehozhat egy gyakori elérési út elemzése folyamatot. Az Azure portal segítségével megjelenítheti a bejövő adatokat, és a egy Stream Analytics-lekérdezés írása. Ha készen áll a lekérdezést, továbbléphet csupán néhány kattintással az éles környezetbe. 

## <a name="key-benefits"></a>Főbb előnyök
Az alábbiakban az Azure Event Hubs és az Azure Stream Analytics-integráció előnyei: 
- **Adatok villámnézete** – megtekintheti a bejövő adatokat az eseményközpontból, az Azure Portalon.
- **A lekérdezés tesztelése** – a transzformációs lekérdezés előkészítése, és a tesztelés közben közvetlenül az Azure Portalon. A lekérdezési nyelvi szintaxisát lásd [Stream Analytics lekérdezési nyelv](/stream-analytics-query/built-in-functions-azure-stream-analytics) dokumentációját.
- **Éles üzembe helyezése a lekérdezés** – telepíthet a lekérdezés éles környezetben hoz létre, és a egy Azure Stream Analytics-feladat indítása folyamatban van.

## <a name="end-to-end-flow"></a>Teljes körű folyamatot

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Keresse meg a **Event Hubs-névtér** , majd lépjen a **eseményközpont**, amely a bejövő adatokat tartalmaz. 
1. Válassza ki **dolgozza fel az adatokat** az event hub oldalon.  

    ![Folyamat adat csempe](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Válassza ki **Intéző** a a **engedélyezése az események valós idejű elemzési** csempére. 

    ![Válassza ki a Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Megjelenik egy lekérdezési lap már be van állítva a következő mezők értékekkel:
    1. A **eseményközpont** a lekérdezés bemenetként.
    1. Minta **SQL-lekérdezés** SELECT utasítással. 
    1. Egy **kimeneti** alias tekintse meg a teszt eredményeit. 

        ![Lekérdezésszerkesztő](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Ez a szolgáltatás első alkalommal való használatakor ezen a lapon hozzon létre egy fogyasztói csoportot és az eseményközpont-szabályzat a bejövő adatok előnézetének megtekintéséhez engedélyt kér.
1. Válassza ki **létrehozás** a a **bemeneti előzetes** ablaktáblán az előző képen látható módon. 
1. Azonnal látni fogja a legfrissebb ezen a lapon a bejövő adatok pillanatképet.
    - A szerializálás írja be az adatokat a program automatikusan észlel (JSON vagy CSV). Manuálisan módosíthatja azt is, JSON vagy CSV/avro-hoz.
    - Megtekintheti, hogy a bejövő adatokat, a tábla vagy nyers formátumban. 
    - Ha a látható adatok nem érvényes, jelölje be **frissítése** a legújabb események megtekintéséhez. 

        Íme egy példa az adatok a **táblázatos formátumú**:   ![A táblázatos formában eredmények](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Íme egy példa az adatok a **fájlt nyers formátumban**: 

        ![Eredmények nyers formátumban](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Válassza ki **Testovat dotaz** látják a pillanatfelvételt a lekérdezés a teszt eredményeit, hogy a **teszteredmények** fülre. Az eredmények is letöltheti.

    ![Lekérdezés eredményei](./media/process-data-azure-stream-analytics/test-results.png)
1. Írhat saját lekérdezését, az adatok átalakításához. Lásd: [Stream Analytics lekérdezési nyelv leírása](/stream-analytics-query/stream-analytics-query-language-reference).
1. A lekérdezés tesztelését, és az éles környezetbe, válassza ki áthelyezni kívánt **telepítés lekérdezés**. A lekérdezés üzembe helyezéséhez hozzon létre az Azure Stream Analytics-feladat, amelyen a kimenetét állítsa a feladat, és indítsa el a feladatot. Stream Analytics-feladat létrehozása, adja meg a feladat nevét, és válassza ki **létrehozás**.

      ![Azure Stream Analytics-feladat létrehozása](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Javasoljuk, hogy hozzon létre egy fogyasztói csoportot, és minden egyes új Azure Stream Analytics-feladat az Event Hubs-oldalon létrehozott egy szabályzatot. Fogyasztói csoportok csak öt egyidejű olvasók, lehetővé teszik, hogy nyújtó dedikált fogyasztói csoportot minden egyes feladathoz kerülni ezt a határértéket meghaladó az esetlegesen felmerülő hibákat. Dedikált házirend lehetővé teszi, hogy a kulcs rotálása vagy tagadhatók más erőforrások befolyásolása nélkül. 
1. A Stream Analytics-feladat most létrejön, ahol a lekérdezési megegyezik, amelyek tesztelése, és a bemeneti az eseményközpont. 

9.  A folyamat befejeződik, állítsa be a **kimeneti** a lekérdezést, és válassza a **Start** elindítani a feladatot.

    > [!NOTE]
    > A feldolgozás indítása előtt ne felejtse el a outputalias cserélje az Azure Stream Analytics szolgáltatásban létrehozott kimeneti nevével.

      ![Állítsa be a kimenetet, és indítsa el a feladatot](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Ismert korlátozások
A lekérdezés tesztelése, miközben a vizsgálati eredmények körülbelül 6 másodperc betöltése igénybe vehet. A teljesítmény, a tesztelési dolgozunk. Azonban ha éles környezetben üzembe helyezett Azure Stream Analytics lesz subsecond késés.

## <a name="streaming-units"></a>Streamelési egységek
Az Azure Stream Analytics-feladat alapértelmezés szerint három streamelési egységek (su). Szükség esetén módosítsa ezt a beállítást, jelölje be **méretezési** a bal oldali menüben lévő a **Stream Analytics-feladat** oldal az Azure Portalon. Streamelési egységekkel kapcsolatos további információkért lásd: [ismertetése és módosítása a folyamatos átviteli egységek](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![A folyamatos átviteli egységek méretezése](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>További lépések
Stream Analytics-lekérdezések kapcsolatos további információkért lásd: [Stream Analytics lekérdezési nyelve](/stream-analytics-query/built-in-functions-azure-stream-analytics)