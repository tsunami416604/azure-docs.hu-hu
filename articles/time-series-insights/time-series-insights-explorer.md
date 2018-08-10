---
title: Fedezze fel az adatokat az Azure Time Series Insights explorer |} A Microsoft Docs
description: Ez a cikk ismerteti a böngészőben az Azure Time Series Insights explorer segítségével gyorsan megjelenítheti a globális a big data- és az IoT-környezet ellenőrzése.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: dfdc538719b0c7571ba04f4134819d7142f109d3
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629140"
---
# <a name="azure-time-series-insights-explorer"></a>Az Azure Time Series Insights explorer
Ez a cikk ismerteti a különböző funkciók és lehetőségek a Time Series Insights explorer webalkalmazáson belül. Használhatja a Time Series Insights explorer webböngészőben az adatok vizualizációkat hozhat létre.
 
Az Azure Time Series Insights egy teljes körűen felügyelt elemző, tároló és megjelenítő szolgáltatás, amely megkönnyíti IoT-események milliárdjainak párhuzamos felderítését és elemzését. Biztosít az adatokat, ami lehetővé teszi, hogy gyorsan ellenőrizheti IoT-megoldásait, és elkerülheti az üzletmenet szempontjából kritikus fontosságú eszközök költséges leállását, globális nézet készítése. Feltárhatja a rejtett trendeket, azonosíthatja a rendellenességeket, és a kiváltó okainak közel valós időben végezhet. A Time Series Insights explorer jelenleg nyilvános előzetes verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

A Time Series Insights explorer használata előtt a következőket kell tennie:
- Egy Time Series Insights-környezet létrehozása
- Adja meg a hozzáférést a fiókjához a környezetben
- Gyűjthet adatokat, és tárolja az eseményforrás hozzáadása

## <a name="explore-and-query-data"></a>Ismerje meg, illetve adatokat kérdezhet le
Az eseményforrás kapcsolódni a Time Series Insights-környezet percen belül vizsgálatát és az idősoros adatokat lekérdezni.

1. Elindítja, nyissa meg a [Time Series Insights explorer](https://insights.timeseries.azure.com/) az webböngészőben, és válassza az ablak bal oldalán környezet. Hozzáféréssel rendelkező összes környezet betűrend szerinti sorrendben vannak felsorolva.

2. Miután, válasszon ki egy környezetet, vagy használja a **FROM** és **TO** konfigurációk a lap tetején, vagy kattintson és húzza át a kívánt azt az időtartományt.  Kattintson a Nagyító ikonra a jobb felső sarokban, vagy kattintson a jobb gombbal a kiválasztott időtartomány keresztül, és válassza ki **keresési**.  

3. Is frissítheti rendelkezésre állási automatikusan percenkénti kiválasztásával a **automatikus a** gombra.  Vegye figyelembe, hogy az "Automatikus – a" gomb csak a rendelkezésre állási diagram, a fő Vizualizáció a tartalom nem vonatkozik.

4. Figyelje meg, hogy az Azure-felhő ikon továbblép a környezet az Azure Portalon.

   ![Time Series Insights-környezet](media/time-series-insights-explorer/explorer1.png)

5. Ezután megjelenik egy diagram, amely az összes esemény száma a kiválasztott időtartam során.  Itt számos olyan vezérlőt rendelkezik:

    **Editor Panel feltételek**: A kifejezés hely, ahol lekérdezheti, ha a környezetben.  Lehetővé teszi, hogy a képernyő bal oldalán található 
      - **Mérték**: A legördülő lista látható összes numerikus oszlopot (Double típusú értékekkel)
      - **Felosztás által**: legördülő menü mutatja az kategorikus oszlopok (karakterlánc)
      - Lépcsőzetes interpoláció engedélyezheti, minimális és maximális megjelenítése, és állítsa be a következő és mérhető, a Vezérlőpult y tengely.  Emellett módosíthatja-e megjelenített adatok a száma, átlagos vagy az adatok összege.
      - Ugyanazt az x tengelyen megtekintéséhez legfeljebb öt feltételeket adhat hozzá.  Használja a **másolási legördülő** gombra, és adjon hozzá egy további kifejezést, vagy kattintson a **Hozzáadás** friss kifejezés hozzáadása gombra.
     
        ![Kifejezések szerkesztő panel](media/time-series-insights-explorer/explorer2.png)

      - **Predikátum**: A predikátum lehetővé teszi, hogy gyorsan szűrheti az eseményeket, az alább felsorolt operandusok használatával. Keresés kiválasztása/kattintva végrehajtása, ha a predikátum lesz alapján automatikusan frissülnek, hogy a keresés.      Operand támogatott típusok a következők:

         |Művelet  |Támogatott típusok  |Megjegyzések  |
         |---------|---------|---------|
         |<, >, <=, >=     |  Double, DateTime, időtartam       |         |
         |=, !=, <>     | String, Bool, Double, DateTime, az időtartam, NULL értékű        |         |
         |INDIA     | String, Bool, Double, DateTime, az időtartam, NULL értékű        |  Az összes operandusok azonos típusú legyen vagy NULL állandó.        |
         |RENDELKEZIK     | Sztring        |  Csak állandó karakterlánc-literálnak engedélyezett jobb oldalán. Üres karakterlánc, és a NULL nem engedélyezettek.       |

      - **Példák a lekérdezések**
      
         ![Példa a lekérdezésekre](media/time-series-insights-explorer/explorer9.png)

6. A **intervallumának mérete** csúszka eszköz lehetővé teszi a azonos timespan keresztül adataikkal időközök nagyítás.  Ez biztosítja, hogy idő nagy webterületek trendeket jelenít meg az zökkenőmentes szeletek le legyen az ezredmásodperc alatt maradnak, így megjelenik az adatok részletes, nagy felbontású darabok közötti mozgás még pontosabb irányítását. A csúszka alapértelmezett kiindulási pontjaként a kijelölésből; az adatok a legoptimálisabb nézet van beállítva terheléselosztási megoldás, a lekérdezés sebessége és a granularitási.

7. A **ecset idő** eszköz megkönnyíti a kezdőlapról kiindulva egy azt az időtartományt a másikra, és intuitív felhasználói felület középpontban a zökkenőmentes áthelyezése között időintervallumok.

8. A **mentése** parancs lehetővé teszi az aktuális lekérdezés mentéséhez és a környezet más felhasználókkal való megosztás esetében engedélyezi azt. Használatával **nyílt**, láthatja az összes a mentett lekérdezések és bármely más felhasználóinak van hozzáférése környezetek megosztott lekérdezések. 

   ![Lekérdezések](media/time-series-insights-explorer/explorer3.png)

9. A **perspektíva nézet** eszköz legfeljebb négy egyedi lekérdezések egyidejű nézetét jeleníti meg. A perspektíva megtekintése gombra a diagram jobb felső sarkában találhatja meg.  

   ![Perspektíva megtekintése](media/time-series-insights-explorer/explorer4.png)

10. A **diagram** lehetővé teszi, hogy vizuálisan tárhatja fel adatait. Diagramkezelő eszközök a következők:

   - Válassza ki/kattintson, amely lehetővé teszi, hogy egy adott időtartam vagy egy adatsort kijelölés.  
   - Időn belül span a kijelölést, nagyítás vagy események áttekintése.  
   - Adatsorok feloszthatja az adatsorozat más oszlop alapján, az adatsorozat hozzáadása új kifejezésként, csak a kijelölt adatsorozat megjelenítése, a kiválasztott adatsorozat kizárása, ping paranccsal a sorozat, és ismerje meg a események a kiválasztott sorozat.
   - A szűrő területen, a diagram bal is tekintse meg az összes megjelenített adatsor és átrendezése érték vagy a neve, az összes adatsor vagy kifejezetten a rögzített vagy nem rögzített adatsorozat megjelenítése.  Meg is egyetlen adatsor kijelölése és az adatsorozat felosztása egy másik oszlop alapján, az adatsorozat hozzáadása új kifejezésként, csak a kijelölt adatsorozat megjelenítése, kihagyása a kiválasztott sorozat, rögzítheti a sorozat, vagy események tallózása a kiválasztott sorozatból.
   - Megtekintésekor több használati egyszerre, a verem, halomszétszedési beállítások, adatsorra vonatkozó további adatok, és használja az azonos y tengely összes a kifejezéseket, a diagram jobb felső sarokban található gombokra.
 
   ![Diagram](media/time-series-insights-explorer/explorer5.png) 

11. A **intenzitástérkép** segítségével gyorsan azonosíthatják a egyedi vagy rendellenes adatsor az egy adott lekérdezésre. Csak egy keresési kifejezést, egy intenzitástérkép jeleníthetők meg.    

   ![Intenzitástérkép](media/time-series-insights-explorer/explorer6.png)

12. **Események**: Ha úgy dönt, Fedezze fel az eseményeket, amikor kiválasztja, vagy kattintson a jobb gombbal fent, az események panel szeretné elérhetővé tenni.  Itt láthatja az összes a nyers események és exportálása az eseményeket JSON vagy CSV-fájlként. Vegye figyelembe, hogy a Time Series Insights minden nyers adatot tárol.

   ![Események](media/time-series-insights-explorer/explorer7.png)

13. Kattintson a **STATS** után felfedezése elérhetővé, minták és oszlopstatisztikák események fülre.  

   - **Minták**: Ez a funkció proaktív módon elérhetővé teszi a kiválasztott adatterületen a legtöbb statisztikailag minták. Ön nem kell megérteni, milyen minták legtöbb indokolják időt és energiát események ezer meg ez mentesíti. További a Time Series Insights lehetővé teszi a közvetlenül belevágjon folytatja az elemzést végző statisztikailag mintákkal. Ez a funkció menthet vizsgálatot előzményadatokat is hasznos lehet. 

   - **Oszlopstatisztikák**: oszlopstatisztikák diagramkészítési és a táblákat, amelyek az egyes oszlopok kiválasztott adatsorozat adatainak felosztania az adott időtartam biztosítanak.  
 
      ![STATISZTIKA](media/time-series-insights-explorer/explorer8.png) 

Most láthatta a különböző funkciók és lehetőségek a Time Series Insights explorer webalkalmazáson belül. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
>[A Time Series Insights-környezet a problémák diagnosztizálása és megoldása](time-series-insights-diagnose-and-solve-problems.md)
