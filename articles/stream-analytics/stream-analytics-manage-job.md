---
title: 'Oktatóanyag: Stream Analytics-feladat létrehozása és kezelése az Azure Portalon | Microsoft Docs'
description: Ez az oktatóanyag átfogóan bemutatja, hogyan használható az Azure Stream Analytics a csaló hívások elemzésére a telefonhívási streamekben.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/30/2018
ms.openlocfilehash: 9ad4462bba861e2bcc940661242d8801355c2f6c
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240801"
---
# <a name="create-a-stream-analytics-job-to-analyze-phone-call-data-and-visualize-results-in-a-power-bi-dashboard"></a>Stream Analytics-feladat létrehozása telefonhívás-adatok elemzésére és az eredmények Power BI-irányítópulton való vizualizációjára

Ez az oktatóanyag bemutatja, hogyan elemezhet telefonhívási adatokat az Azure Stream Analytics használatával. Az ügyfélalkalmazás által létrehozott telefonhívási adatok csaló hívásokat tartalmaznak, amelyeket egy Stream Analytics-feladat szűr ki.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Minta telefonhívási adatok létrehozása és elküldése az Azure Event Hubsnak  
> * Stream Analytics-feladat létrehozása
> * Feladatbemenet és -kimenet konfigurálása  
> * Rosszindulatú hívásokat szűrő lekérdezés definiálása  
> * A feladat tesztelése és indítása
> * Eredmények vizualizációja a Power BI-ban

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).  
* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).  
* Töltse le a [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) telefonhívás-eseménykészítő alkalmazást a Microsoft letöltőközpontjából, vagy szerezze be a forráskódot a [GitHubról](https://aka.ms/azure-stream-analytics-telcogenerator).  

## <a name="create-an-azure-event-hub"></a>Azure Event Hub létrehozása 

Mielőtt a Stream Analytics elemezni tudná a csaló hívások adatstreamjét, el kell küldenie az adatokat az Azure-ba. Ebben az oktatóanyagban az [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs) használatával során fog adatokat küldeni az Azure-ba.

Eseményközpont létrehozásához, majd az adatok az eseményközpontba küldéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).  
2. Válassza az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **Event Hubs** elemet.  

   ![Azure Event Hub létrehozása](media/stream-analytics-manage-job/find-eh.png)
3. Adja meg az alábbi értékeket a **Névtér létrehozása** panelen:  

   |**Beállítás**  |**Ajánlott érték** |**Leírás**  |
   |---------|---------|---------|
   |Name (Név)     | myEventHubsNS        |  Az eseményközpont névterének azonosítására szolgáló egyedi név.       |
   |Előfizetés     |   \<Az Ön előfizetése\>      |   Válasszon ki egy Azure-előfizetést, ahol létre kívánja hozni az eseményközpontot.      |
   |Erőforráscsoport     |   MyASADemoRG      |  Válassza az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét.       |
   |Hely     |   USA 2. nyugati régiója      |    Az a hely, ahol az eseményközpont-névtér üzembe helyezhető.     |

4. A többi beállításnál hagyja meg az alapértelmezett beállításokat, majd válassza a **Létrehozás** parancsot.  

   ![Eseményközpont-névtér létrehozása](media/stream-analytics-manage-job/create-ehns.png)

5. Ha a névtér üzembe helyezése befejeződött, lépjen a **Minden erőforrás** területre > keresse meg a *myEventHubsNS* elemet az Azure-erőforrások listájában. Válassza ki a *myEventHubsNS* elemet a megnyitásához.  
6. Ezután válassza az **+Eseményközpont** lehetőséget, és a **Név** mezőbe írja be a *MyEventHub* vagy egy Ön által választott nevet. A többi beállításnál hagyja meg az alapértelmezett beállításokat, majd válassza a **Létrehozás** parancsot. Várjon, amíg az üzembe helyezés sikeresen befejeződik.

   ![Eseményközpont létrehozása](media/stream-analytics-manage-job/create-eh.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Az eseményközponthoz való hozzáférés engedélyezése és kapcsolati sztring beszerzése

Mielőtt egy alkalmazás adatokat küldhet az Azure Event Hubsnak, az eseményközpontnak szüksége van egy szabályzatra megfelelő hozzáférést biztosít. A hozzáférési szabályzat egy kapcsolati sztringet hoz létre, amelyben megtalálhatók az engedélyezési információk.

1. Lépjen az előző szakaszban létrehozott, *MyEventHub* nevű eseményközponthoz. A **Beállítások** területen válassza a **Megosztott elérési szabályzatok**, majd a **+Hozzáadás** elemet.

2. Adja a szabályzatnak a **MyPolicy** nevet, és ellenőrizze, hogy **Kezelés** lehetőség be van-e jelölve. Ezután kattintson a **Létrehozás** elemre.  

   ![Eseményközpont megosztott elérési házirendjének létrehozása](media/stream-analytics-manage-job/create-ehpolicy.png)

3. A szabályzat létrehozása után válassza ki a szabályzatot a megnyitásához, és keresse meg a **Kapcsolati sztring – elsődleges kulcs** elemet. Kattintson a kapcsolati sztring melletti **másolás** gombra.

   ![A megosztott elérési szabályzat kapcsolati sztringjének mentése](media/stream-analytics-manage-job/save-connection-string.png)

4. Illessze be a kapcsolati sztringet egy szövegszerkesztőbe. A következő szakaszban szükség lesz erre a kapcsolati sztringre.  

   A kapcsolati sztring a következőképpen néz ki:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>` 

   Figyelje meg, hogy a kapcsolati sztring több, pontosvesszővel elválasztott kulcs–érték párt tartalmaz: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** és **EntityPath**.  

5. Távolítsa el az **EntityPath** párt a kapcsolati sztringből az előtte található pontosvesszővel együtt.

## <a name="start-the-event-generator-application"></a>Az eseménylétrehozó alkalmazás elindítása

A TelcoGenerator alkalmazást úgy kell beállítania az indítása előtt, hogy a korábban beállított Azure Event Hubs-eseményközpontnak küldjön adatokat.

1. Bontsa ki a [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) fájl tartalmát.  
2. Nyissa meg a `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` fájlt egy tetszőleges szövegszerkesztőben (Több .config fájl található, ezért győződjön meg róla, hogy a megfelelőt nyitja meg.)  

3. Frissítse a konfigurációs fájl <appSettings> elemét az alábbi részletekkel:

   * Állítsa az *EventHubName* kulcsot a kapcsolati sztringben található EntityPath értékére.  
   * A *Microsoft.ServiceBus.ConnectionString* kulcs értékét állítsa a kapcsolati sztring értékére az EntityPath értéke nélkül.

4. Mentse a fájlt.  
5. Ezután nyisson meg egy parancsablakot, és lépjen arra a mappára, ahová kicsomagolta a TelcoGenerator alkalmazást. Ezután írja be a következő parancsot:

   ```
   telcodatagen.exe 1000 0.2 2
   ```

   A paranccsal a következő paraméterek használhatók:
   * Óránkénti hívásadatrekordok száma.  
   * Csalások valószínűségének százalékos aránya, amely azt adja meg, hogy az alkalmazás milyen gyakran szimuláljon csaló hívásokat. A 0,2-es érték azt jelenti, hogy nagyjából a hívások 20%-a fog csalónak tűnni.  
   * Időtartam órában, amely azt adja meg, hogy hány óráig fusson az alkalmazás. Az alkalmazást bármikor leállíthatja úgy is, ha a parancssorban leállítja a folyamatot (**Ctrl+C**).

   Néhány másodperc elteltével az alkalmazás elkezdi kijelezni a hívásrekordokat a képernyőn, miközben az eseményközpontba küldi őket. A telefonhívási adatok a következő mezőket tartalmazzák:

   |**Rekord**  |**Definíció**  |
   |---------|---------|
   |CallrecTime    |  A hívási kezdési idejét jelölő időbélyegző.       |
   |SwitchNum     |  A hívás csatlakozásához használt telefonkapcsoló. Ebben a példában a kapcsolók olyan sztringek, amelyek a származási országot jelölik (USA, Kína, Egyesült királyság, Németország vagy Ausztrália).       |
   |CallingNum     |  A hívó telefonszáma.       |
   |CallingIMSI     |  Az International Mobile Subscriber Identity (IMSI). Ez a hívó egyedi azonosítója.       |
   |CalledNum     |   A hívott fél telefonszáma.      |
   |CalledIMSI     |  International Mobile Subscriber Identity (IMSI). Ez a hívott fél egyedi azonosítója.       |

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Most, hogy már rendelkezik a hívási események streamjével, létrehozhat egy olyan Stream Analytics-feladatot, amely beolvassa az adatokat az eseményközpontból.

1. Stream Analytics-feladat létrehozásához lépjen az [Azure Portalra](https://portal.azure.com/).  

2. Válassza az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **Stream Analytics-feladat** elemet.

3. Adja meg az alábbi értékeket az **Új Stream Analytics-feladat** ablaktáblán:  

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Feladat neve     |  ASATutorial       |   Az eseményközpont névterének azonosítására szolgáló egyedi név.      |
   |Előfizetés    |  \<Az Ön előfizetése\>   |   Válassza ki, melyik Azure-előfizetésben kívánja létrehozni a feladatot.       |
   |Erőforráscsoport   |   MyASADemoRG      |   Válassza a **Meglévő használata** lehetőséget, és adjon meg egy új erőforráscsoport-nevet a fiókjának.      |
   |Hely   |    USA 2. nyugati régiója     |      A hely, ahol a feladat üzembe helyezhető. Ajánlott a feladatot és az eseményközpontot ugyanabba a régióba helyezni, az optimális teljesítmény érdekében, továbbá így elkerülheti a régiók közötti adatátvitel díját is.      |
   |Üzemeltetési környezet    | Felhő        |     A Stream Analytics-feladatok a felhőben vagy a peremhálózaton is üzembe helyezhetők. A Felhő beállítással az Azure Cloudban, a Peremhálózat pedig IoT Edge-eszközökön helyezheti üzembe a feladatot.    |
   |Streamelési egységek     |    1       |      A streamelési egységek a feladatok végrehajtásához felhasznált számítási erőforrásokat jelölik. Alapértelmezés szerint ez az érték 1. További információ a streamelési egységek skálázásáról: [A streamelési egységek ismertetése és módosítása](stream-analytics-streaming-unit-consumption.md).      |   

4. A többi beállításnál használja az alapértelmezett értékeket, majd válassza a **Létrehozás** parancsot, és várja meg, amíg az üzembe helyezés befejeződik.

   ![Feladat létrehozása](media/stream-analytics-manage-job/create-a-job.png)

## <a name="configure-job-input"></a>Feladatbemenet konfigurálása

A következő lépés egy bemeneti forrás megadása, amelyből a feladat be tudja olvasni az adatokat az előző szakaszban létrehozott eseményközpontot használva.

1. Az Azure Portalon nyissa meg a **Minden erőforrás** panelt, és keresse meg az *ASATutorial* Stream Analytics-feladatot.  

2. A Stream Analytics feladatpanelének **Feladattopológia** szakaszában válassza a **Bemenetek** lehetőséget.  

3. Válassza a **+ Streambemenet hozzáadása**, majd az **Eseményközpont** lehetőséget. Adja meg az alábbi értékeket a panelen:  

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Bemeneti alias     |  CallStream       |  Adjon meg egy rövid nevet a bemenet azonosításához. A bemeneti alias csak alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, és 3–63 karakter hosszúságúnak kell lennie.       |
   |Előfizetés    |   \<Az Ön előfizetése\>      |   Jelölje ki azt az Azure-előfizetést, ahol létrehozta az eseményközpontot. Az eseményközpont ugyanabban az előfizetésben, mint a Stream Analytics-feladat, vagy egy másikban is.       |
   |Eseményközpont-névtér    |  myEventHubsNS       |  Válassza ki az előző szakaszban létrehozott eseményközpont-névteret. Az aktuális előfizetésben elérhető összes eseményközpont-névtér megjelenik a legördülő listában.       |
   |Eseményközpont neve    |   MyEventHub      |  Válassza ki az előző szakaszban létrehozott eseményközpontot. Az aktuális előfizetésben elérhető összes eseményközpont megjelenik a legördülő listában.       |
   |Eseményközpont szabályzatának neve   |  Mypolicy       |  Válassza ki az előző lépésben létrehozott megosztott elérési házirendet. Az aktuális előfizetésben elérhető összes eseményközpont-szabályzat megjelenik a legördülő listában.       |

4. A többi beállításnál hagyja meg az alapértelmezett beállításokat, majd válassza a **Mentés** lehetőséget.

   ![Bemenet konfigurálása](media/stream-analytics-manage-job/configure-input.png)

## <a name="configure-job-output"></a>Feladatkimenet konfigurálása 

Az utolsó lépés egy kimeneti fogadó megadása a feladatnak, ahová az átalakított adatokat írhatja. Ebben az oktatóanyagban kimeneti adatokat hoz létre és vizualizál a Power BI használatával.

1. Az Azure Portalon nyissa meg a **Minden erőforrás** panelt, majd az *ASATutorial* Stream Analytics-feladatot.  

2. A Stream Analytics-feladat panel **Feladattopológia** szakaszában válassza a **Kimenetek** lehetőséget.  

3. Válassza a **+ Hozzáadás** > **Power BI** lehetőséget. Töltse ki az űrlapot a következő értékekkel, majd válassza az **Engedélyezés** lehetőséget:  

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|---------|
   |Kimeneti alias  |  MyPBIoutput  |
   |Adatkészlet neve  |   ASAdataset  | 
   |Tábla neve |  ASATable  | 

   ![Kimenet konfigurálása](media/stream-analytics-manage-job/configure-output.png)  

4. Miután kiválasztotta az **Engedélyezés** lehetőséget, megjelenik egy előugró ablak, ahol meg kell adnia a hitelesítő adatait a Power BI-fiókja hitelesítéséhez. A sikeres hitelesítés után válassza a **Mentés** lehetőséget a beállítások mentéséhez. 

## <a name="define-a-query-to-analyze-input-data"></a>Lekérdezés meghatározása a bemeneti adatok elemzéséhez

A következő lépés egy átalakítás létrehozása, amely valós időben elemzi az adatokat. Az átalakítási lekérdezés definiálásához használja a [Stream Analytics lekérdezési nyelvet](https://msdn.microsoft.com/library/dn834998.aspx). Az ebben az oktatóanyagban használt lekérdezés észleli a csaló hívásokat a telefon adataiból.

Ebben a példában a csaló hívásokat ugyanaz a felhasználó indítja eltérő helyekről, öt másodperces időközönként. Például ugyanaz a felhasználó nem indíthat szabályosan hívásokat egyszerre az USA-ból és Ausztráliából. A Stream Analytics-feladat átalakítási lekérdezésének megadásához tegye a következőket:

1. Az Azure Portalon nyissa meg a **Minden erőforrás** panelt, majd navigáljon a korábban létrehozott, **ASATutorial** nevű Stream Analytics-feladathoz.  

2. A Stream Analytics-feladat panel **Feladattopológia** szakaszában válassza a **Lekérdezés** lehetőséget. A lekérdezési ablak felsorolja a feladathoz konfigurált bemeneteket és kimeneteket, és a segítségével létrehozhat egy lekérdezést a bemeneti stream átalakításához.  

3. Cserélje le a szerkesztőben lévő lekérdezést az alábbi lekérdezésre, amely önillesztést hajt végre 5 másodpercnyi hívási adatonként:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   A csaló hívások kereséséhez a `CallRecTime` érték alapján érdemes önillesztést végrehajtania a streamadatokon. Ezután megkeresheti azokat a hívásrekordokat, amelyekben a `CallingIMSI` érték (a származási szám) megegyezik, de a `SwitchNum` érték (származási ország) különbözik. Ha JOIN műveletet használ streamadatokon, az illesztésnek korlátoznia kell az egyező sorok közötti maximális időtartamot. Mivel a streamadatok végtelenek, a kapcsolat időkorlátait az **ON** záradékban kell megadni, a [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) függvénnyel. 

   Ez a lekérdezés a hagyományos SQL-illesztésekhez hasonlít, kivéve a **DATEDIFF** függvényt. A lekérdezésben használt **DATEDIFF** függvény csak a Streaming Analyticsben használható, és az `ON...BETWEEN` záradékon belül kell megjelennie.  

4. **Mentse** a lekérdezést.  

   ![Stream Analytics-lekérdezés meghatározása](media/stream-analytics-manage-job/define-query.png)

## <a name="test-your-query"></a>A lekérdezés tesztelése

A lekérdezésszerkesztőben létrehozott lekérdezéseket mintaadatokkal tesztelheti. A lekérdezés teszteléséhez hajtsa végre az alábbi lépéseket:

1. Győződjön meg róla, hogy TelcoGenerator alkalmazás fut, és telefonhívás-rekordokat készít.  

2. A **Lekérdezés** panelen válassza ki a *CallStream* bemenet melletti pontokat, majd válassza a **Mintaadatok bemenetből** lehetőséget. 

3. Állítsa a **Percek** beállítást 3 értékre, majd válassza az **OK** lehetőséget. A rendszer három percnyi adatmintát vesz a bemeneti streamből, és jelzi, amikor készen állnak a mintaadatok. A mintavételezés állapotát az értesítési sávon tekintheti meg. 

   A rendszer ideiglenesen tárolja a mintaadatokat, amelyek akkor érhetők el, amikor meg van nyitva a lekérdezési ablak. Ha bezárja a lekérdezési ablakot, a mintaadatok elvesznek, és új mintaadatkészletet kell létrehoznia, ha tesztet kíván végrehajtani. Alternatív megoldásként használhat a [GitHubról](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) származó, mintaadatokat tartalmazó JSON-fájlt, majd feltöltheti ezt a JSON-fájlt, hogy a benne lévő mintaadatokat használja a *CallStream* bemenethez.

   ![Bemeneti mintaadatok](media/stream-analytics-manage-job/sample-input-data.png)

4. A lekérdezés teszteléséhez válassza a **Teszt** elemet. A következő eredményeknek kell megjelennie:  

   ![Tesztkimenet](media/stream-analytics-manage-job/test-output.png)

## <a name="start-the-job-and-visualize-output"></a>A feladat elindítása és a kimenet vizualizációja

1. A feladat indításához lépjen a feladat **Áttekintés** paneljére, és válassza az **Indítás** lehetőséget.  

2. Válassza a **Most** beállítást a feladatkimenet kezdési idejeként, majd válassza az **Indítás** lehetőséget. A feladat állapotát az értesítési sávban tekintheti meg.  

3. Miután a feladat sikeresen végre lett hajtva, lépjen a [Power BI](https://powerbi.com/)-ba, és lépjen be munkahelyi vagy iskolai fiókjával. Ha a Stream Analytics-feladatlekérdezés eredményeket ad kimenetként, a korábban létrehozott *ASAdataset* adathalmaz az **Adatkészletek** lapon található.  

4. A Power BI-munkaterületen válassza a **+ Létrehozás** lehetőséget egy új, *Fraudulent Calls* nevű irányítópult létrehozásához.  

5. Válassza a **Csempe felvétele** lehetőséget az ablak tetején. Ezután válassza az **Egyedi folyamatos átviteli adatok**, majd a **Tovább** lehetőséget. A **Saját adatkészletek** területen válassza az **ASAdataset** elemet. A **Megjelenítés típusa** legördülő menüben válassza a **Kártya** elemet, és a **Mezők** panelhez adja hozzá a **fraudulentcalls** elemet. Kattintson a **Tovább** gombra, és nevezze el a csempét, majd kattintson az **Alkalmaz** elemre a csempe létrehozásához.  

   ![Csempék létrehozása](media/stream-analytics-manage-job/create-tiles.png)

6. Hajtsa végre újra a 4. és az 5. lépést az alábbi beállításokkal:
   * Amikor a Vizualizáció típusa részhez ér, válassza a Vonaldiagram lehetőséget.  
   * Adjon hozzá egy tengelyt, és válassza ki **windowend** lehetőséget.  
   * Adjon meg egy értéket, és válassza a **fraudulentcalls** lehetőséget.  
   * A **Megjelenítendő időtartomány** beállításnál válassza ki az utolsó 10 percet.  

7. A két csempe hozzáadása után az irányítópult az alábbi példában látható módon fog kinézni. Látható, hogy ha az eseményközpont küldőalkalmazása és a Streaming Analytics alkalmazás fut, a Power BI-irányítópult rendszeresen frissül, amint új adatok érkeznek.  

   ![Power BI-eredmények](media/stream-analytics-manage-job/power-bi-results.png)

## <a name="embedding-your-powerbi-dashboard-in-a-web-application"></a>A Power BI-irányítópult beágyazása egy webalkalmazásba

Az oktatóanyag ezen részében egy [ASP.NET](https://asp.net/) minta-webalkalmazást fog használni, amelyet a Power BI csapata készített az irányítópultja beágyazásához. További információ az irányítópult beágyazásáról: [Beágyazás a Power BI-ba](https://docs.microsoft.com/power-bi/developer/embedding).

Az alkalmazás beállításához keresse fel a [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) GitHub-adattárat, és kövesse a **Felhasználó az adatok tulajdonosa** szakasz utasításait (használja az **integrate-dashboard-web-app** alszakaszban található átirányítási és a kezdőlap URL-t). Mivel az irányítópult-alapú példát alkalmazzuk, használja a [GitHub-adattárban](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-dashboard-web-app) található **integrate-dashboard-web-app** mintakódot.
Miután az alkalmazás fut a böngészőjében, az alábbi lépések végrehajtásával ágyazza be a weblapra a korábban létrehozott irányítópultot:

1. Válassza a **Bejelentkezés a Power BI-ba** lehetőséget, amely engedélyezi az alkalmazásnak a Power BI-fiókjában lévő irányítópultokhoz való hozzáférést.  

2. Kattintson az **Irányítópultok lekérése** gombra, amely megjeleníti a fiókjában található irányítópultokat egy táblában. Keresse meg a korábban létrehozott irányítópult nevét (**powerbi-embedded-dashboard**), és másolja be a megfelelő **EmbedUrl** értéket.  

3. Végül illessze be az **EmbedUrl** értéket a megfelelő szövegmezőbe, és válassza az **Irányítópult beágyazása** lehetőséget. Az irányítópult most már megtekinthető egy webalkalmazásba beágyazva.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy Stream Analytics-feladatot, elemezte a bejövő adatokat, és megjelenítette az eredményeket egy Power BI-irányítópulton. A Stream Analytics-feladatokról a következő oktatóanyagban talál további információt:

> [!div class="nextstepaction"]
> [Azure Functions-függvények futtatása Stream Analytics-feladatokban](stream-analytics-with-azure-functions.md)
