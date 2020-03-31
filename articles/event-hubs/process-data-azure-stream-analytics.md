---
title: Adatok feldolgozása az Event Hubs Azure-ból a Stream Analytics használatával | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan dolgozhatja fel az azure-beli eseményközpontból származó adatokat egy Azure Stream Analytics-feladat használatával.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69991952"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Adatok feldolgozása az eseményközpontból az Azure Stream Analytics használatával 
Az Azure Stream Analytics szolgáltatás megkönnyíti az Azure Event Hubs-ból származó streamelési adatok betöltését, feldolgozását és elemzését, lehetővé téve a hatékony elemzéseket a valós idejű műveletek megvalósításához. Ez az integráció lehetővé teszi, hogy gyorsan hozzon létre egy elérési út elemzési folyamat. Az Azure Portal segítségével vizualizálhatja a bejövő adatokat, és írhat egy Stream Analytics-lekérdezést. Miután a lekérdezés készen áll, néhány kattintással áthelyezheti éles környezetbe. 

## <a name="key-benefits"></a>Főbb előnyök
Az Azure Event Hubs és az Azure Stream Analytics-integráció legfontosabb előnyei: 
- **Adatok előnézete** – Megtekintheti a bejövő adatokat egy eseményközpontból az Azure Portalon.
- **Tesztelje a lekérdezést** – Készítsen elő egy átalakítási lekérdezést, és tesztelje közvetlenül az Azure Portalon. A lekérdezési nyelv szintaxisát a [Stream Analytics lekérdezési nyelv dokumentációjában](/stream-analytics-query/built-in-functions-azure-stream-analytics) találja.
- **A lekérdezés üzembe helyezése éles környezetben** – A lekérdezés üzembe helyezésével éles környezetben egy Azure Stream Analytics-feladat létrehozásával és elindításával.

## <a name="end-to-end-flow"></a>Végpontok között folyik

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 
1. Nyissa meg az **Event Hubs névterét,** majd keresse meg az **eseményközpontot,** amely a bejövő adatokat is rendelkezik. 
1. Válassza **az Adatok feldolgozása** lehetőséget az eseményközpont lapján.  

    ![Adatcsempe feldolgozása](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Válassza a **Felfedezés** lehetőséget az **Események valós idejű elemzési adatainak engedélyezése** csempén. 

    ![Válassza a Stream Analytics lehetőséget](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Megjelenik egy lekérdezési lap, amelyen a következő mezőkhöz már beállított értékek vannak beállítva:
    1. Az **eseményközpont** a lekérdezés bemeneteként.
    1. Példa **SQL-lekérdezésselect** utasítással. 
    1. A lekérdezési teszt eredményekre hivatkozó **kimeneti** alias. 

        ![Lekérdezésszerkesztő](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Ha első alkalommal használja ezt a funkciót, ez a lap az Ön engedélyét kéri egy fogyasztói csoport és egy szabályzat létrehozásához az eseményközpont számára a bejövő adatok megtekintéséhez.
1. Válassza a **Létrehozás gombot** a **Bemeneti betekintő** ablaktáblában az előző képen látható módon. 
1. Ezen a lapon azonnal megjelenik a legfrissebb bejövő adatok pillanatképe.
    - A szerializálás típusa az adatok automatikusan észleli (JSON/CSV). Manuálisan is módosíthatja JSON/CSV/AVRO-ra.
    - A bejövő adatok at táblázat formátumban vagy nyers formátumban tekintheti meg. 
    - Ha a megjelenített adatok nem aktuálisak, a legújabb események megtekintéséhez válassza a **Frissítés** lehetőséget. 

        Íme egy példa a **táblázat formátumú adatokra:** ![Eredmények a táblázat formátumban](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Íme egy példa a **nyers formátumú adatokra:** 

        ![Eredmények a nyers formátumban](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Válassza **a Lekérdezés tesztelése** lehetőséget a lekérdezés teszteredményeinek pillanatképét a **Teszteredmények** lapon. Az eredményeket is letöltheti.

    ![Lekérdezés eredményeinek tesztelése](./media/process-data-azure-stream-analytics/test-results.png)
1. Saját lekérdezés beírása az adatok átalakításához. Lásd: [Stream Analytics lekérdezési nyelv – hivatkozás](/stream-analytics-query/stream-analytics-query-language-reference).
1. Miután tesztelte a lekérdezést, és át szeretné helyezni éles környezetbe, válassza **a Lekérdezés telepítése**lehetőséget. A lekérdezés üzembe helyezéséhez hozzon létre egy Azure Stream Analytics-feladatot, ahol beállíthatja a feladatot, és elindíthatja a feladatot. Stream Analytics-feladat létrehozásához adja meg a feladat nevét, és válassza a **Létrehozás gombot.**

      ![Azure Stream Analytics-feladat létrehozása](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Azt javasoljuk, hogy hozzon létre egy fogyasztói csoportot és egy szabályzatot minden új Azure Stream Analytics-feladathoz, amelyet az Event Hubs lapról hoz létre. A fogyasztói csoportok csak öt egyidejű olvasót engedélyeznek, így minden feladathoz külön fogyasztói csoport biztosítása elkerüli az e korlát túllépéséből eredő hibákat. A dedikált házirend lehetővé teszi a kulcs elforgatását vagy az engedélyek visszavonását anélkül, hogy más erőforrásokat befolyásolna. 
1. A Stream Analytics-feladat most jön létre, ahol a lekérdezés ugyanaz, amit tesztelt, és a bemeneti az eseményközpont. 

9.  A folyamat befejezéséhez állítsa be a lekérdezés **kimenetét,** és válassza az **Indítás** gombot a feladat elindításához.

    > [!NOTE]
    > A feladat megkezdése előtt ne felejtse el lecserélni a outputalias-t az Azure Stream Analytics-ben létrehozott kimeneti névre.

      ![Kimenet beállítása és a feladat indítása](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Ismert korlátozások
A lekérdezés tesztelése közben a teszteredmények körülbelül 6 másodpercet vesz igénybe. Azon dolgozunk, hogy javítsuk a tesztelés teljesítményét. Éles környezetben üzembe helyezéskor azonban az Azure Stream Analytics másodperc alatti késéssel fog rendelkezni.

## <a name="streaming-units"></a>Streamelési egységek
Az Azure Stream Analytics-feladat alapértelmezés szerint három streamelési egység (SUs). A beállítás módosításához válassza a **Méretezés** parancsot az Azure Portalon a **Stream Analytics feladatlapján** a bal oldali menüben. Ha többet szeretne megtudni a streamelési egységekről, olvassa [el a Streamelési egységek megértése és beállítása](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Streamelési egységek méretezése](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a Stream Analytics-lekérdezésekről, olvassa el a [Stream Analytics lekérdezési nyelvének lekérdezési nyelve](/stream-analytics-query/built-in-functions-azure-stream-analytics)
