---
title: Oktatóanyag – Stream Analytics feladatok létrehozása és kezelése Azure Portal használatával
description: Ez az oktatóanyag átfogóan bemutatja, hogyan használható az Azure Stream Analytics a csaló hívások elemzésére a telefonhívási streamekben.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/30/2020
ms.openlocfilehash: fef949e9285264ef46fbaed05a4385a15b27e65e
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94354468"
---
# <a name="tutorial-analyze-phone-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Oktatóanyag: telefonhívások elemzése a Stream Analytics és az eredmények megjelenítése Power BI irányítópulton

Ez az oktatóanyag bemutatja, hogyan elemezhet telefonhívási adatokat az Azure Stream Analytics használatával. Az ügyfélalkalmazás által létrehozott telefonhívás-adatok olyan csalárd hívásokat tartalmaznak, amelyeket a Stream Analytics feladatokkal fog szűrni.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Minta telefonhívási adatok létrehozása és elküldése az Azure Event Hubsnak
> * Stream Analytics-feladat létrehozása
> * Feladatbemenet és -kimenet konfigurálása
> * Rosszindulatú hívásokat szűrő lekérdezés definiálása
> * A feladat tesztelése és indítása
> * Eredmények vizualizációja a Power BI-ban

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, hajtsa végre a következő műveleteket:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
* Töltse le a [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) telefonhívás-eseménykészítő alkalmazást a Microsoft letöltőközpontjából, vagy szerezze be a forráskódot a [GitHubról](https://aka.ms/azure-stream-analytics-telcogenerator).
* Szüksége lesz egy Power BI-fiókra.

## <a name="create-an-azure-event-hub"></a>Azure Event Hub létrehozása

Mielőtt a Stream Analytics elemezni tudná a csaló hívások adatstreamjét, el kell küldenie az adatokat az Azure-ba. Ebben az oktatóanyagban az [Azure Event Hubs](../event-hubs/event-hubs-about.md) használatával során fog adatokat küldeni az Azure-ba.

Eseményközpont létrehozásához, majd az adatok az eseményközpontba küldéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
2. Válassza **az erőforrás létrehozása**  >  **eszközök internetes hálózata**  >  **Event Hubs** lehetőséget.

   ![Azure Event hub létrehozása a portálon](media/stream-analytics-manage-job/find-event-hub-resource.png)
3. Töltse ki a **névtér létrehozása** panelt a következő értékekkel:

   |**Beállítás**  |**Ajánlott érték** |**Leírás**  |
   |---------|---------|---------|
   |Név     | asaTutorialEventHub        |  Az eseményközpont névterének azonosítására szolgáló egyedi név.       |
   |Előfizetés     |   \<Your subscription\>      |   Válasszon ki egy Azure-előfizetést, ahol létre kívánja hozni az eseményközpontot.      |
   |Erőforráscsoport     |   MyASADemoRG      |  Válassza az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét.       |
   |Hely     |   USA 2. nyugati régiója      |    Az a hely, ahol az eseményközpont-névtér üzembe helyezhető.     |

4. A többi beállításnál használja az alapértelmezett beállításokat, majd válassza a **felülvizsgálat + létrehozás** lehetőséget. Ezután válassza a **Létrehozás** lehetőséget a telepítés elindításához.

   ![Event hub-névtér létrehozása Azure Portalban](media/stream-analytics-manage-job/create-event-hub-namespace.png)

5. Ha a névtér befejezte a telepítést, lépjen a **minden erőforrás** elemre, és keresse meg a *asaTutorialEventHub* az Azure-erőforrások listájában. A megnyitásához válassza a *asaTutorialEventHub* lehetőséget.

6. Ezután válassza a **+ Event hub** lehetőséget, és adjon meg egy **nevet** az Event hub számára. Állítsa a **partíciók darabszámát** *2* értékre.  A többi beállításnál használja az alapértelmezett beállításokat, majd válassza a **Létrehozás** lehetőséget. Várjon, amíg az üzembe helyezés sikeresen befejeződik.

   ![Event hub-konfiguráció a Azure Portal](media/stream-analytics-manage-job/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Az eseményközponthoz való hozzáférés engedélyezése és kapcsolati sztring beszerzése

Mielőtt egy alkalmazás adatokat küldhet az Azure Event Hubsnak, az eseményközpontnak szüksége van egy szabályzatra megfelelő hozzáférést biztosít. A hozzáférési szabályzat egy kapcsolati sztringet hoz létre, amelyben megtalálhatók az engedélyezési információk.

1. Navigáljon az előző lépésben létrehozott *MyEventHub*. A **Beállítások** területen válassza a **Megosztott elérési szabályzatok** , majd a **+Hozzáadás** elemet.

2. Adja a szabályzatnak a **MyPolicy** nevet, és ellenőrizze, hogy **Kezelés** lehetőség be van-e jelölve. Ezután válassza a **Létrehozás** elemet.

   ![Eseményközpont megosztott elérési házirendjének létrehozása](media/stream-analytics-manage-job/create-event-hub-access-policy.png)

3. A szabályzat létrehozása után kattintson a házirend nevére a szabályzat megnyitásához. Keresse meg a **kapcsolatok karakterláncát – elsődleges kulcs**. A kapcsolódási karakterlánc mellett kattintson a **Másolás** gombra.

   ![A megosztott elérési szabályzat kapcsolati sztringjének mentése](media/stream-analytics-manage-job/save-connection-string.png)

4. Illessze be a kapcsolati sztringet egy szövegszerkesztőbe. A következő szakaszban szükség lesz erre a kapcsolati sztringre.

   A kapcsolati sztring a következőképpen néz ki:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Figyelje meg, hogy a kapcsolati sztring több, pontosvesszővel elválasztott kulcs–érték párt tartalmaz: **Endpoint** , **SharedAccessKeyName** , **SharedAccessKey** és **EntityPath**.

## <a name="start-the-event-generator-application"></a>Az eseménylétrehozó alkalmazás elindítása

A TelcoGenerator alkalmazást úgy kell beállítania az indítása előtt, hogy a korábban beállított Azure Event Hubs-eseményközpontnak küldjön adatokat.

1. Bontsa ki a [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) fájl tartalmát.
2. Nyissa meg a `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` fájlt egy tetszőleges szövegszerkesztőben (több. config fájl található, ezért győződjön meg arról, hogy a megfelelőt nyitja meg.)

3. Frissítse a konfigurációs fájl `<appSettings>` elemét az alábbi részletekkel:

   * Állítsa az *EventHubName* kulcsot a kapcsolati sztringben található EntityPath értékére.
   * Állítsa a *Microsoft. ServiceBus. ConnectionString* kulcs értékét a kapcsolati karakterláncra a EntityPath érték nélkül (ne felejtse el törölni a pontosvesszőt, amely megelőzi azt).

4. Mentse a fájlt.
5. Ezután nyisson meg egy parancsablakot, és lépjen arra a mappára, ahová kicsomagolta a TelcoGenerator alkalmazást. Ezután írja be a következő parancsot:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   A paranccsal a következő paraméterek használhatók:
   * Óránkénti hívásadatrekordok száma.
   * Csalások valószínűségének százalékos aránya, amely azt adja meg, hogy az alkalmazás milyen gyakran szimuláljon csaló hívásokat. A 0,2-es érték azt jelenti, hogy nagyjából a hívások 20%-a fog csalónak tűnni.
   * Időtartam órában, amely azt adja meg, hogy hány óráig fusson az alkalmazás. Az alkalmazást bármikor leállíthatja, ha a parancssorban leállítja a folyamatot ( **CTRL + C** ).

   Néhány másodperc elteltével az alkalmazás elkezdi kijelezni a hívásrekordokat a képernyőn, miközben az eseményközpontba küldi őket. A telefonhívási adatok a következő mezőket tartalmazzák:

   |**Rekord**  |**Definíció**  |
   |---------|---------|
   |CallrecTime    |  A hívási kezdési idejét jelölő időbélyegző.       |
   |SwitchNum     |  A hívás csatlakozásához használt telefonkapcsoló. Ebben a példában a kapcsolók olyan karakterláncok, amelyek a származási országot/régiót (USA, Kína, Egyesült Királyság, Németország vagy Ausztrália) jelölik.       |
   |CallingNum     |  A hívó telefonszáma.       |
   |CallingIMSI     |  Az International Mobile Subscriber Identity (IMSI). Ez a hívó egyedi azonosítója.       |
   |CalledNum     |   A hívott fél telefonszáma.      |
   |CalledIMSI     |  International Mobile Subscriber Identity (IMSI). Ez a hívott fél egyedi azonosítója.       |

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Most, hogy már rendelkezik a hívási események streamjével, létrehozhat egy olyan Stream Analytics-feladatot, amely beolvassa az adatokat az eseményközpontból.

1. Stream Analytics-feladat létrehozásához lépjen az [Azure Portalra](https://portal.azure.com/).

2. Válassza az **erőforrás létrehozása** lehetőséget, és keresse meg **stream Analytics feladatot**. Válassza a **stream Analytics feladatok** csempét, és válassza a * létrehozás * * lehetőséget.

3. Töltse ki az **új stream Analytics feladatok** űrlapot a következő értékekkel:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Feladat neve     |  ASATutorial       |   Az eseményközpont névterének azonosítására szolgáló egyedi név.      |
   |Előfizetés    |  \<Your subscription\>   |   Válassza ki, melyik Azure-előfizetésben kívánja létrehozni a feladatot.       |
   |Erőforráscsoport   |   MyASADemoRG      |   Válassza a **Meglévő használata** lehetőséget, és adjon meg egy új erőforráscsoport-nevet a fiókjának.      |
   |Hely   |    USA 2. nyugati régiója     |      A hely, ahol a feladat üzembe helyezhető. Ajánlott a feladatot és az eseményközpontot ugyanabba a régióba helyezni, az optimális teljesítmény érdekében, továbbá így elkerülheti a régiók közötti adatátvitel díját is.      |
   |Üzemeltetési környezet    | Felhőbeli        |     A Stream Analytics-feladatok a felhőben vagy a peremhálózaton is üzembe helyezhetők. A felhő lehetővé teszi az Azure-felhőbe való üzembe helyezést, az Edge pedig lehetővé teszi, hogy IoT Edge eszközre telepítsen.    |
   |Streamelési egységek     |    1       |      A Streamelési egységek azoknak a számítási erőforrásoknak felelnek meg, amelyek a feladat futtatásához szükségesek. Ez az érték alapértelmezés szerint 1. További információ a streamelési egységek skálázásáról: [A streamelési egységek ismertetése és módosítása](stream-analytics-streaming-unit-consumption.md).      |

4. A többi beállításnál használja az alapértelmezett beállításokat, válassza a **Létrehozás** lehetőséget, és várja meg, amíg a telepítés sikeres lesz.

   ![Azure Stream Analytics-feladat létrehozása](media/stream-analytics-manage-job/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Feladatbemenet konfigurálása

A következő lépés egy bemeneti forrás megadása, amelyből a feladat be tudja olvasni az adatokat az előző szakaszban létrehozott eseményközpontot használva.

1. A Azure Portal nyissa meg a **minden erőforrás** lapot, és keresse meg a *ASATutorial* stream Analytics feladatot.

2. A Stream Analytics-feladathoz tartozó **feladatok topológiája** szakaszban válassza a **bemenetek** lehetőséget.

3. Válassza a **+ Streambemenet hozzáadása** , majd az **Eseményközpont** lehetőséget. Töltse ki a bemeneti űrlapot a következő értékekkel:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Bemeneti alias     |  CallStream       |  Adjon meg egy rövid nevet a bemenet azonosításához. A bemeneti alias csak alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, és 3–63 karakter hosszúságúnak kell lennie.       |
   |Előfizetés    |   \<Your subscription\>      |   Jelölje ki azt az Azure-előfizetést, ahol létrehozta az eseményközpontot. Az eseményközpont ugyanabban az előfizetésben, mint a Stream Analytics-feladat, vagy egy másikban is.       |
   |Eseményközpont-névtér    |  asaTutorialEventHub       |  Válassza ki az előző szakaszban létrehozott eseményközpont-névteret. Az aktuális előfizetésben elérhető összes eseményközpont-névtér megjelenik a legördülő listában.       |
   |Eseményközpont neve    |   MyEventHub      |  Válassza ki az előző szakaszban létrehozott eseményközpontot. Az aktuális előfizetésben elérhető összes eseményközpont megjelenik a legördülő listában.       |
   |Eseményközpont szabályzatának neve   |  MyPolicy       |  Válassza ki az előző lépésben létrehozott megosztott elérési házirendet. Az aktuális előfizetésben elérhető összes eseményközpont-szabályzat megjelenik a legördülő listában.       |

4. A többi beállításnál hagyja meg az alapértelmezett beállításokat, majd válassza a **Mentés** lehetőséget.

   ![Azure Stream Analytics bemenet konfigurálása](media/stream-analytics-manage-job/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Feladatkimenet konfigurálása

Az utolsó lépés egy kimeneti fogadó definiálása, ahol a feladatok írhatják az átalakított adatokat. Ebben az oktatóanyagban kimeneti adatokat hoz létre és vizualizál a Power BI használatával.

1. A Azure Portal nyissa meg az **összes erőforrást** , és válassza ki a *ASATutorial* stream Analytics feladatot.

2. A Stream Analytics feladatok **feladatok topológiája** szakaszában válassza a **kimenetek** lehetőséget.

3. Válassza a **+**  >  **Power bi** hozzáadása elemet. Ezután válassza az **Engedélyezés** lehetőséget, és kövesse az utasításokat a Power bi hitelesítéséhez.

:::image type="content" source="media/stream-analytics-manage-job/authorize-power-bi.png" alt-text="Power BI engedélyezése gomb":::

4. Töltse ki a kimenet űrlapot a következő részletekkel, majd válassza a **Mentés** lehetőséget:

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|
   |Kimeneti alias  |  MyPBIoutput  |
   |Csoport munkaterülete| Saját munkaterület |
   |Adatkészlet neve  |   ASAdataset  |
   |Tábla neve |  ASATable  |
   | Hitelesítési módszer | Felhasználói jogkivonat |

   ![Stream Analytics kimenet konfigurálása](media/stream-analytics-manage-job/configure-stream-analytics-output.png)

   Ez az oktatóanyag a *felhasználói jogkivonat* hitelesítési módját használja. A felügyelt identitás használatához lásd: [a felügyelt identitás használata a Azure stream Analytics-feladatok hitelesítéséhez Power bi](powerbi-output-managed-identity.md).

## <a name="define-a-query-to-analyze-input-data"></a>Lekérdezés meghatározása a bemeneti adatok elemzéséhez

A következő lépés egy átalakítás létrehozása, amely valós időben elemzi az adatokat. Az átalakítási lekérdezés definiálásához használja a [Stream Analytics lekérdezési nyelvet](/stream-analytics-query/stream-analytics-query-language-reference). Az ebben az oktatóanyagban használt lekérdezés észleli a csaló hívásokat a telefon adataiból.

Ebben a példában a csaló hívásokat ugyanaz a felhasználó indítja eltérő helyekről, öt másodperces időközönként. Például ugyanaz a felhasználó nem indíthat szabályosan hívásokat egyszerre az USA-ból és Ausztráliából. A Stream Analytics-feladat átalakítási lekérdezésének megadásához tegye a következőket:

1. A Azure Portal nyissa meg a **minden erőforrás** panelt, és navigáljon a korábban létrehozott **ASATutorial** stream Analytics feladatokhoz.

2. A Stream Analyticsi feladatokhoz tartozó **feladatok topológiája** szakaszban válassza a **lekérdezés** lehetőséget. A lekérdezési ablak felsorolja a feladathoz konfigurált bemeneteket és kimeneteket, és lehetővé teszi, hogy lekérdezést hozzon létre a bemeneti adatfolyam átalakításához.

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

   A csaló hívások kereséséhez a `CallRecTime` érték alapján érdemes önillesztést végrehajtania a streamadatokon. Ezután megkeresheti azokat a hívási rekordokat, amelyekben az `CallingIMSI` érték (az eredeti szám) megegyezik, de az `SwitchNum` érték (a forrás országa/régiója) eltér. Ha JOIN műveletet használ streamadatokon, az illesztésnek korlátoznia kell az egyező sorok közötti maximális időtartamot. Mivel a streamadatok végtelenek, a kapcsolat időkorlátait az **ON** záradékban kell megadni, a [DATEDIFF](/stream-analytics-query/datediff-azure-stream-analytics) függvénnyel.

   Ez a lekérdezés ugyanúgy működik, mint a normál SQL-illesztés, kivéve a **DATEDIFF** függvényt. A lekérdezésben használt **DATEDIFF** függvény csak a Streaming Analyticsben használható, és az `ON...BETWEEN` záradékon belül kell megjelennie.

4. **Mentse** a lekérdezést.

   ![Stream Analytics lekérdezés definiálása a portálon](media/stream-analytics-manage-job/define-stream-analytics-query.png)

## <a name="test-your-query"></a>A lekérdezés tesztelése

A lekérdezéseket a lekérdezés-szerkesztőből is tesztelheti. A lekérdezés teszteléséhez hajtsa végre az alábbi lépéseket:

1. Győződjön meg róla, hogy TelcoGenerator alkalmazás fut, és telefonhívás-rekordokat készít.

2. A lekérdezés teszteléséhez válassza a **Teszt** elemet. A következő eredményeknek kell megjelennie:

   ![Stream Analytics lekérdezési teszt kimenete](media/stream-analytics-manage-job/sample-test-output-restuls.png)

## <a name="start-the-job-and-visualize-output"></a>A feladat elindítása és a kimenet vizualizációja

1. A feladatok elindításához navigáljon a feladatok **áttekintéséhez** , és válassza az **Indítás** lehetőséget.

2. Válassza a **Most** beállítást a feladatkimenet kezdési idejeként, majd válassza az **Indítás** lehetőséget. A feladat állapotát az értesítési sávban tekintheti meg.

3. Miután a feladat sikeresen végre lett hajtva, lépjen a [Power BI](https://powerbi.com/)-ba, és lépjen be munkahelyi vagy iskolai fiókjával. Ha a Stream Analytics-feladatlekérdezés eredményeket ad kimenetként, a korábban létrehozott *ASAdataset* adathalmaz az **Adatkészletek** lapon található.

4. A Power BI-munkaterületen válassza a **+ Létrehozás** lehetőséget egy új, *Fraudulent Calls* nevű irányítópult létrehozásához.

5. Az ablak tetején válassza a **Szerkesztés** lehetőséget, és **adja hozzá a csempét**. Ezután válassza az **Egyedi folyamatos átviteli adatok** , majd a **Tovább** lehetőséget. A **Saját adatkészletek** területen válassza az **ASAdataset** elemet. Válassza a **kártya** lehetőséget a **vizualizáció típusa** legördülő listából, és adjon meg **hamis hívásokat** a **mezőkhöz**. Kattintson a **Tovább** gombra, és nevezze el a csempét, majd kattintson az **Alkalmaz** elemre a csempe létrehozásához.

   ![Irányítópult-csempék létrehozása Power BI](media/stream-analytics-manage-job/create-power-bi-dashboard-tiles.png)

6. Hajtsa végre újra az 5. lépést az alábbi beállításokkal:
   * Amikor a Vizualizáció típusa részhez ér, válassza a Vonaldiagram lehetőséget.
   * Adjon hozzá egy tengelyt, és válassza ki **windowend** lehetőséget.
   * Adjon meg egy értéket, és válassza a **fraudulentcalls** lehetőséget.
   * A **Megjelenítendő időtartomány** beállításnál válassza ki az utolsó 10 percet.

7. A két csempe hozzáadása után az irányítópult az alábbi példában látható módon fog kinézni. Figyelje meg, hogy ha az Event hub Sender Application és a streaming Analytics alkalmazás fut, akkor az Power BI-irányítópult rendszeresen frissül, ahogy új adatként érkeznek.

   ![Eredmények megtekintése Power BI irányítópulton](media/stream-analytics-manage-job/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>A Power BI-irányítópult beágyazása egy webalkalmazásba

Az oktatóanyag ezen részében egy minta [ASP.net](https://asp.net/) webalkalmazást fog használni, amelyet a Power bi csapata hozott létre az irányítópult beágyazásához. További információ az irányítópult beágyazásáról: [Beágyazás a Power BI-ba](/power-bi/developer/embedding).

Az alkalmazás beállításához nyissa meg a [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) GitHub-adattárat, és kövesse a **felhasználó tulajdonában lévő adat** szakasz utasításait (használja a Integration **-Web-App** alszakaszban található átirányítás és Kezdőlap URL-címeket). Mivel az irányítópult példáját használjuk, használja a [GitHub-tárházban](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/)található integrat **-Web-App mintakód-** kódot.
Miután az alkalmazás fut a böngészőjében, az alábbi lépések végrehajtásával ágyazza be a weblapra a korábban létrehozott irányítópultot:

1. Válassza a **bejelentkezés Power bi** lehetőséget, amely hozzáférést biztosít az alkalmazásnak az Power bi-fiókban lévő irányítópultokhoz.

2. Kattintson az **Irányítópultok lekérése** gombra, amely megjeleníti a fiókjában található irányítópultokat egy táblában. Keresse meg a korábban létrehozott irányítópult nevét ( **powerbi-embedded-dashboard** ), és másolja be a megfelelő **EmbedUrl** értéket.

3. Végül illessze be az **EmbedUrl** értéket a megfelelő szövegmezőbe, és válassza az **Irányítópult beágyazása** lehetőséget. Az irányítópult most már megtekinthető egy webalkalmazásba beágyazva.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy Stream Analytics-feladatot, elemezte a bejövő adatokat, és megjelenítette az eredményeket egy Power BI-irányítópulton. A Stream Analytics-feladatokról a következő oktatóanyagban talál további információt:

> [!div class="nextstepaction"]
> [Azure Functions-függvények futtatása Stream Analytics-feladatokban](stream-analytics-with-azure-functions.md)