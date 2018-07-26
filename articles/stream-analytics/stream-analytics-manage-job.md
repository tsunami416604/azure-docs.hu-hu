---
title: 'Oktatóanyag: Stream Analytics-feladat létrehozása és kezelése az Azure Portallal | Microsoft Docs'
description: Ez az oktatóanyag átfogóan bemutatja, hogyan használható az Azure Stream Analytics a csaló hívások elemzésére a telefonhívási streamekben.
services: stream-analytics
author: sidramadoss
ms.author: sidram
manager: kfile
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.openlocfilehash: 39b39a1d00c91e0ff114a28c13da0d4b6920ec13
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186230"
---
# <a name="create-a-stream-analytics-job-to-analyze-phone-call-data-and-visualize-results-in-a-power-bi-dashboard"></a>Stream Analytics-feladat létrehozása telefonhívás-adatok elemzésére és az eredmények Power BI-irányítópulton való vizualizációjára
 
Ez az oktatóanyag bemutatja, hogyan elemezhet egy ügyfélalkalmazás által létrehozott minta-telefonhívást az Azure Stream Analytics segítségével. Az ügyfélalkalmazás által létrehozott telefonhívás-adatok csaló hívásokat tartalmaznak, és meghatározunk egy Stream Analytics-feladatot az ilyen hívások szűréséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Minta telefonhívás-adatok létrehozása és elküldése az Azure Event Hubsnak  
> * Stream Analytics-feladat létrehozása   
> * A feladat bemenetének és kimenetének konfigurálása  
> * Rosszindulatú hívásokat szűrő lekérdezés definiálása  
> * A feladat tesztelése és indítása  
> * Eredmények vizualizációja a Power BI-ban 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).  
* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).  
* Töltse le a [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) telefonhívás-eseménykészítő alkalmazást a Microsoft letöltőközpontjából, vagy szerezze be a forráskódot a [GitHubról](https://aka.ms/azure-stream-analytics-telcogenerator).  

## <a name="create-an-azure-event-hub"></a>Azure Event Hub létrehozása 

Mielőtt a Stream Analytics elemezni tudná a csaló hívások adatstreamjét, el kell küldenie az adatokat az Azure-ba. Ebben az oktatóanyagban az [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs) használatával során fog adatokat küldeni az Azure-ba. Ebben az oktatóanyagban létre fog hozni egy eseményközpontot, és az eseménygenerátor alkalmazással elküldi a hívásadatokat ennek az eseményközpontnak. Az eseményközpont létrehozásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra.  
2. Válassza az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **Event Hubs** elemet.  

   ![Eseményközpont keresése](media/stream-analytics-manage-job/find-eh.png)
3. Adja meg az alábbi értékeket a **Névtér létrehozása** ablaktáblán:  

   |**Beállítás**  |**Ajánlott érték** |**Leírás**  |
   |---------|---------|---------|
   |Name (Név)     | myEventHubNS        |  Az eseményközpont névterének azonosítására szolgáló egyedi név.       |
   |Előfizetés     |   \<Az Ön előfizetése\>      |   Válasszon ki egy Azure-előfizetést, ahol létre kívánja hozni az eseményközpontot.      |
   |Erőforráscsoport     |   MyASADemoRG      |  Válassza az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét.       |
   |Hely     |   USA 2. nyugati régiója      |    Az a hely, ahol az eseményközpont-névtér üzembe helyezhető.     |

4. A többi beállításnál hagyja meg az alapértelmezett beállításokat, majd válassza a **Létrehozás** parancsot.  

   ![Eseményközpont-névtér létrehozása](media/stream-analytics-manage-job/create-ehns.png)

5. Ha a névtér telepítése befejeződött, lépjen a **Minden erőforrás** területre > keresse meg a myEventHubNS elemet az Azure-erőforrások listájában > majd válassza ki a megnyitásához.  
6. Ezután válassza a **+Event Hub** > **Név** elemet, és adja a MyEventHub nevet az eseményközpontnak. Más nevet is használhat. A többi beállításnál használja az alapértelmezett értékeket, majd válassza a **Létrehozás** parancsot, és várja meg, amíg az üzembe helyezés befejeződik.

   ![Eseményközpont létrehozása](media/stream-analytics-manage-job/create-eh.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Az eseményközponthoz való hozzáférés engedélyezése és kapcsolati sztring beszerzése

Mielőtt egy alkalmazás adatokat küldhet az Azure Event Hubsnak, az eseményközpontnak szüksége van egy szabályzatra megfelelő hozzáférést biztosít. A hozzáférési szabályzat egy kapcsolati sztringet hoz létre, amelyben megtalálhatók az engedélyezési információk.

1. Lépjen az előző lépésben létrehozott **Event Hubs**-elemre, amelynek a MyEventHub nevet adta > válassza a **Megosztott elérési házirendek** elemet az eseményközpont ablaktábláján > válassza a **+Hozzáadás** lehetőséget.  
2. Állítsa a szabályzat nevét **Mypolicy** értékre > válassza a **Kezelés** elemet > válassza a **Létrehozás** lehetőséget.  

   ![Eseményközpont megosztott elérési házirendjének létrehozása](media/stream-analytics-manage-job/create-ehpolicy.png)

3. A szabályzat üzembe helyezése után válassza ki a megnyitásához, keresse meg a **Kapcsolati sztring – elsődleges kulcs** elemet, és válassza a kapcsolati sztring mellett található **Másolás** elemet.  
4. Illessze be a kapcsolati sztringet egy szövegszerkesztőbe. A következő szakaszban szükség lesz erre a kapcsolati sztringre.  

   A kapcsolati sztring a következőképpen néz ki:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>` 

   Figyelje meg, hogy a kapcsolati sztring több, pontosvesszővel elválasztott kulcs–érték párt tartalmaz: Endpoint, SharedAccessKeyName, SharedAccessKey és EntityPath.  

5. Távolítsa el az EntityPath párt a kapcsolati sztringből (ne felejtse el törölni az előtte lévő pontosvesszőt is).

## <a name="start-the-event-generator-application"></a>Az eseménylétrehozó alkalmazás elindítása

A TelcoGenerator alkalmazást úgy kell beállítania az indítása előtt, hogy a korábban beállított Azure Event Hubs-eseményközpontnak küldjön adatokat.

1. Bontsa ki a [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) fájl tartalmát.  
2. Nyissa meg a `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` fájlt egy tetszőleges szövegszerkesztőben (Több .config fájl található, ezért győződjön meg róla, hogy a megfelelőt nyitja meg.)  

3. Frissítse a konfigurációs fájl <appSettings> elemét az alábbi részletekkel:

   * Állítsa az EventHubName kulcsot a kapcsolati sztringben található EntityPath értékére.  
   * A Microsoft.ServiceBus.ConnectionString kulcs értékét állítsa a kapcsolati sztring értékére az EntityPath értéke nélkül, amely az előző szakasz 5. lépésében kapott érték.

4. Mentse a fájlt.  
5. Ezután nyisson egy parancsablakot, váltson arra a mappára, ahová kicsomagolta a TelcoGenerator alkalmazást, és írja be a következő parancsot:

   ```
   telcodatagen.exe 1000 .2 2
   ```

   A paranccsal a következő paraméterek használhatók:
   * **Óránkénti hívásadatrekordok száma**.  
   * **Csalások valószínűségének százalékos aránya** – ez azt adja meg, hogy az alkalmazás milyen gyakran szimuláljon csaló hívásokat. A .2 érték azt jelenti, hogy nagyjából a hívások 20%-a fog csalónak tűnni.  
   * **Időtartam órában** – ez azt adja meg, hogy hány óráig fusson az alkalmazás. Az alkalmazást bármikor leállíthatja úgy is, ha a parancssorban leállítja a folyamatot (Ctrl+C).

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

1. Lépjen az Azure Portalra egy Stream Analytics-feladat létrehozásához  

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

   ![Feladat létrehozása](media/stream-analytics-manage-job/create-a-job.png)   

4. A többi beállításnál használja az alapértelmezett értékeket, majd válassza a **Létrehozás** parancsot, és várja meg, amíg az üzembe helyezés befejeződik.

## <a name="configure-job-input"></a>Feladatbemenet konfigurálása

A következő lépés egy bemeneti forrás megadása, amelyből a feladat be tudja olvasni az adatokat. A jelen oktatóanyag esetében azt az eseményközpontot használja, amelyet az előző részben bemenetként létrehozott. A feladat bemenetének konfigurálásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg az Azure Portalról az **Összes erőforrás** panelt, és keresse meg az ASATutorial Stream Analytics-feladatot.  

2. A Stream Analytics feladatpanelének **Feladattopológia** szakaszában válassza a **Bemenetek** lehetőséget.  

3. Válassza ki **+Streambemenet hozzáadása** lehetőséget (a referenciabemenet a statikus referenciaadatokra vonatkozik, amelyeket ebben az oktatóanyagban nem fog használni), majd az **Eseményközpont** lehetőséget, és töltse ki az ablaktáblát az alábbi értékekkel:  

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Bemeneti alias     |  CallStream       |  Adjon meg egy rövid nevet a bemenet azonosításához. A bemeneti alias csak alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, és 3–63 karakter hosszúságúnak kell lennie.       |
   |Előfizetés    |   \<Az Ön előfizetése\>      |   Jelölje ki azt az Azure-előfizetést, ahol létrehozta az eseményközpontot. Az eseményközpont ugyanabban az előfizetésben, mint a Stream Analytics-feladat, vagy egy másikban is.       |
   |Eseményközpont-névtér    |  MyEventHubNS       |  Válassza ki az előző szakaszban létrehozott eseményközpont-névteret. Az aktuális előfizetésben elérhető összes eseményközpont-névtér megjelenik a legördülő listában.       |
   |Eseményközpont neve    |   MyEventHub      |  Válassza ki az előző szakaszban létrehozott eseményközpontot. Az aktuális előfizetésben elérhető összes eseményközpont megjelenik a legördülő listában.       |
   |Eseményközpont szabályzatának neve   |  Mypolicy       |  Válassza ki az előző lépésben létrehozott megosztott elérési házirendet. Az aktuális előfizetésben elérhető összes eseményközpont-szabályzat megjelenik a legördülő listában.       |

   ![Bemenet konfigurálása](media/stream-analytics-manage-job/configure-input.png) 

4. A többi beállításnál használja az alapértelmezett értékeket, majd válassza a **Mentés** parancsot, és várja meg, amíg az üzembe helyezés befejeződik.

## <a name="configure-job-output"></a>Feladatkimenet konfigurálása 

Az utolsó lépés egy kimeneti fogadó megadása a feladatnak, ahová az átalakított adatokat írhatja. A jelen oktatóanyagban a Power BI-t fogja használni az eredménykimenetek tárolásához és az adatok vizualizációjához. A feladat kimenetének konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az Azure Portalról nyissa meg az **Összes erőforrás** panelt, majd az ASATutorial Stream Analytics-feladatot.  

2. A Stream Analytics-feladat panel **Feladattopológia** szakaszában válassza a **Kimenetek** lehetőséget.  

3. Válassza ki **+Hozzáadás** > **Power BI** lehetőséget, és töltse ki az űrlapot a következő adatokkal (megadhat egy rövid nevet a Kimeneti alias, az Adatkészlet neve és a Tábla neve tulajdonságokhoz, ahogy a táblázatban látható), majd válassza az **Engedélyezés** lehetőséget:  

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|---------|
   |Kimeneti alias  |  MyPBIoutput  |
   |Adatkészlet neve  |   ASAdataset  | 
   |Tábla neve |  ASATable  | 

   ![Kimenet konfigurálása](media/stream-analytics-manage-job/configure-output.png)  

4. Miután kiválasztotta az **Engedélyezés** lehetőséget, megjelenik egy előugró ablak, ahol meg kell adnia a hitelesítő adatait a Power BI-fiókja hitelesítéséhez. A sikeres hitelesítés után válassza a **Mentés** lehetőséget a beállítások mentéséhez. 

## <a name="define-a-query-to-analyze-input-data"></a>Lekérdezés meghatározása a bemeneti adatok elemzéséhez

Miután sikeresen beállította a Stream Analytics-feladatot a beérkező adatfolyam olvasására, a következő lépés egy átalakítás létrehozása, amely valós időben elemzi az adatokat. Az átalakítási lekérdezés definiálásához használja a [Stream Analytics lekérdezési nyelvet](https://msdn.microsoft.com/library/dn834998.aspx). Ebben az oktatóanyagban egy olyan lekérdezést hoz létre, amely észleli a csaló hívásokat a telefon adataiból. 

Ebben a példában azokat a hívásokat vesszük csaló hívásnak, amelyek ugyanattól a felhasználótól, de eltérő helyekről származnak, és a hívások közötti időtartam öt másodperc. Például ugyanaz a felhasználó nem indíthat szabályosan hívásokat egyszerre az USA-ból és Ausztráliából. A Stream Analytics-feladat átalakítási lekérdezésének megadásához hajtsa végre az alábbi lépéseket:

1. Az Azure Portalról nyissa meg az **Összes erőforrás** panelt, majd a korábban létrehozott **ASATutorial** Stream Analytics-feladatot.  

2. A Stream Analytics-feladat panel **Feladattopológia** szakaszában válassza a **Lekérdezés** lehetőséget. Az előugró ablak felsorolja a feladathoz konfigurált bemeneteket és kimeneteket, és a segítségével létrehozhat egy lekérdezést a bemeneti stream átalakításához.  

3. Ezután cserélje le a szerkesztőben lévő lekérdezést az alábbi adatokra. Ez a lekérdezés önillesztést hajt végre 5 másodpercnyi hívási adatonként:

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

   A rosszindulatú hívások kereséséhez a `CallRecTime` érték alapján érdemes önillesztést végrehajtania a streamadatokon. Ezután megkeresheti azokat a hívásrekordokat, amelyekben a `CallingIMSI` érték (a származási szám) megegyezik, de a `SwitchNum` érték (származási ország) különbözik. Ha JOIN műveletet használ streamadatokon, az illesztésnek korlátoznia kell az egyező sorok közötti maximális időtartamot. Mivel a streamadatok végtelenek, a kapcsolat időkorlátait az ON záradékban kell megadni, a [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) függvénnyel. 

   Ez a lekérdezés a hagyományos SQL-illesztésekhez hasonlít, kivéve a DATEDIFF függvényt. A lekérdezésben használt DATEDIFF függvény csak a Streaming Analyticsben használható, és a `ON...BETWEEN` záradékon belül kell megjelennie.  

4. **Mentse** a lekérdezést.  

   ![lekérdezés meghatározása](media/stream-analytics-manage-job/define-query.png) 

## <a name="test-your-query"></a>A lekérdezés tesztelése

A lekérdezésszerkesztőben létrehozott lekérdezéseket tesztelheti, amihez mintaadatok szükségesek. Ebben a bemutatóban az eseményközpontba beérkező telefonhívásstreamből fogja kinyerni a mintaadatokat. A lekérdezés teszteléséhez hajtsa végre az alábbi lépéseket:

1. Győződjön meg róla, hogy TelcoGenerator alkalmazás fut, és telefonhívás-rekordokat készít.  

2. A **Lekérdezés** panelen válassza ki a CallStream bemenet melletti pontokat, majd válassza a **Mintaadatok bemenetből** lehetőséget. Ekkor megnyílik egy ablaktábla, amelyen megadhatja a bemeneti streamből beolvasni kívánt mintaadatok mennyiségét.  

   ![Bemeneti mintaadatok](media/stream-analytics-manage-job/sample-input-data.png)  

3. Állítsa a **Percek** beállítást 3 értékre, majd válassza az **OK** lehetőséget. A rendszer három percnyi adatmintát vesz a bemeneti streamből, és értesítést küld, amikor készen állnak a mintaadatok. A mintavételezés állapotát az értesítési sávon tekintheti meg. 

   A rendszer ideiglenesen tárolja a mintaadatokat, amelyek akkor érhetők el, amikor meg van nyitva a lekérdezési ablak. Ha bezárja a lekérdezési ablakot, a mintaadatok elvesznek, és új mintaadatkészletet kell létrehoznia. Alternatív megoldásként a [GitHubról](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/telco.json) beszerezhet egy mintaadatokat tartalmazó .json fájlt, majd feltöltheti ezt a .json fájlt, hogy a benne lévő mintaadatokat használja a CallStream bemenetéhez.  

4. Válassza a **Tesztelés** lehetőséget a lekérdezés teszteléséhez. A kimeneti eredmények a következő képernyőképen láthatóhoz hasonlóan jelennek meg:  

   ![Tesztkimenet](media/stream-analytics-manage-job/test-output.png)

## <a name="start-the-job-and-visualize-output"></a>A feladat elindítása és a kimenet vizualizációja

1. A feladat indításához nyissa meg a feladat **Áttekintés** ablaktábláját, és válassza az **Indítás** lehetőséget.  

2. Válassza a **Most** beállítást a feladatkimenet kezdési idejeként, majd válassza az **Indítás** lehetőséget. A feladat pár perc múlva elindul, és megtekintheti az állapotát az értesítési sávban.  

3. Miután a feladat sikeresen lezajlott, nyissa meg a [Powerbi.com](https://powerbi.com/) webhelyet, és lépjen be a munkahelyi vagy iskolai fiókjával. Ha a Stream Analytics-feladatlekérdezés eredményeket ad, akkor láthatja, hogy már létre is jött az adatkészlete. Az **Adatkészletek** lapra lépve láthat egy ASAdataset nevű adatkészletet.  

4. Válassza a **+Létrehozás** lehetőséget a munkaterületén. Hozzon létre egy új irányítópultot, és adja neki a Csaló hívások nevet. Két csempét fog hozzáadni ehhez az irányítópulthoz, amelyekből az egyik a csaló hívások előfordulását fogja számolni, a másik pedig egy vonaldiagramos vizualizációt fog megjeleníteni.  

5. Az ablak tetején válassza a **Csempe hozzáadása** lehetőséget > válassza az **Egyedi streamadatok** lehetőséget > Tovább > válassza az **ASAdataset** elemet > Vizualizáció típusaként válassza a **Kártya** lehetőséget > a Mezők beállításnál válassza a **fraudulentcalls** lehetőséget. Válassza a **Tovább** lehetőséget > adjon nevet a csempének, és kattintson az **Alkalmaz** elemre.  

   ![Csempék létrehozása](media/stream-analytics-manage-job/create-tiles.png)

6. Hajtsa végre újra 4. lépést az alábbi beállításokkal:
   * Amikor a Vizualizáció típusa részhez ér, válassza a Vonaldiagram lehetőséget.  
   * Adjon hozzá egy tengelyt, és válassza ki **windowend** lehetőséget.  
   * Adjon meg egy értéket, és válassza a **fraudulentcalls** lehetőséget.  
   * A **Megjelenítendő időtartomány** beállításnál válassza ki az utolsó 10 percet.  

7. Az irányítópult a képernyőképen láthatóan fog kinézni, miután mind két csempe hozzá lett adva. Látható, hogy az eseményközpont küldőalkalmazása és a Streaming Analytics alkalmazás fut, a Power BI-irányítópult pedig rendszeresen frissül, amint új adatok érkeznek.  

   ![Power BI-eredmények](media/stream-analytics-manage-job/power-bi-results.png)

## <a name="embedding-your-powerbi-dashboard-in-a-web-application"></a>A Power BI-irányítópult beágyazása egy webalkalmazásba

Az oktatóanyag ezen részében egy [ASP.NET](http://asp.net/) minta-webalkalmazást fog használni, amelyet a Power BI csapata készített az irányítópultja beágyazásához. További információ az irányítópult beágyazásáról: [Beágyazás a Power BI-ba](https://docs.microsoft.com/power-bi/developer/embedding).

Ebben az oktatóanyagban „a felhasználó az adatok tulajdonosa” alkalmazás lépéseit követjük. Az alkalmazás beállításához keresse fel a [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) Github-adattárat, és kövesse a **Felhasználó az adatok tulajdonosa** szakaszt (használja az **integrate-dashboard-web-app** alszakaszban található átirányítási és a kezdőlap URL-t). Mivel az irányítópult-alapú példát alkalmazzuk, használja a [GitHub-adattárban](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User Owns Data/integrate-dashboard-web-app) található integrate-dashboard-web-app mintakódot.
Miután az alkalmazás fut a böngészőjében, az alábbi lépések végrehajtásával ágyazza be a weblapra a korábban létrehozott irányítópultot:

1. Válassza a **Bejelentkezés a Power BI-ba** lehetőséget, amely engedélyezi az alkalmazásnak a Power BI-fiókjában lévő irányítópultokhoz való hozzáférést.  

2. Kattintson az **Irányítópultok lekérése** gombra, amely megjeleníti a fiókjában található irányítópultokat egy táblában. Keresse meg a korábban létrehozott irányítópult nevét (powerbi-embedded-dashboard), és másolja be a megfelelő **EmbedUrl** értéket.  

3. Végül illessze be az **EmbedUrl** értéket a megfelelő szövegmezőbe, és válassza az **Irányítópult beágyazása** lehetőséget. Az irányítópult most már megtekinthető egy webalkalmazásba beágyazva.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy Stream Analytics-feladatot, elemezte a bejövő adatokat, és megjelenítette az eredményeket egy Power BI-irányítópulton. A Stream Analytics-feladatokról a következő oktatóanyagban talál további információt:

> [!div class="nextstepaction"]
> [Azure Functions-függvények futtatása Stream Analytics-feladatokban](stream-analytics-with-azure-functions.md)
