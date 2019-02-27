---
title: Azure Stream Analytics használatával valós idejű csalásészlelés
description: Ismerje meg, a csalások valós idejű észlelése megoldás létrehozása a Stream Analytics-szel. Valós idejű eseményfeldolgozás az event hub használja.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 5317eb83552b5ea15b2d709f61b456a7a508e9ba
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876177"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Első lépések az Azure Stream Analytics használatával: Csalások valós idejű észlelése

Ez az oktatóanyag ismerteti, hogyan használható az Azure Stream Analytics egy teljes körű ábrája. Az alábbiak végrehajtásának módját ismerheti meg: 

* Bring események streamelése az Azure Event Hubs egy példányát. Ebben az oktatóanyagban egy alkalmazást, amely szimulálja a mobiltelefon metaadatok rekordok adatfolyam fogja használni.

* Összesíti az adatokat, vagy minták keres adatok átalakítására Stream Analytics SQL-szerű lekérdezéseket írni. Látni fogja a lekérdezés használatával megvizsgálja a bejövő streamet, és keresse meg, amely lehet csaló hívásokat.

* Az eredményt elküldik egy kimeneti fogadóba (tárolás), amelyet elemezhet további elemzések számára. Ebben az esetben a gyanús hívásadatokat küldünk az Azure Blob storage.

Ebben az oktatóanyagban a példa az adathamisítások azonnali felismerése telefonhívás-adatok alapján. A célkiszolgálóról technika csalások észlelése, például hitelkártya csalás vagy identitás lopás más típusú esetén is használhatók. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Forgatókönyv: Valós idejű távközlési és SIM csalásészlelés

A távközlési vállalat rendelkezik nagy mennyiségű adat a bejövő hívások. A vállalat úgy kívánja csaló hívások valós időben észlelheti a, hogy értesítse a felhasználókat arról, vagy állítsa le a szolgáltatást egy adott számot. Egy adott típusú SIM csalás magában foglalja a több hívás ugyanazzal az identitással körülbelül egy időben, de a földrajzilag különböző helyeken. Az ilyen típusú csalások észlelése, a vállalat kell megvizsgálja a bejövő telefonszám-rekordok és a meghatározott mintákat keressen – ebben az esetben a különböző országokban megvalósítható körülbelül egy időben indított hívások. A kategóriába tartozó telefonszám rekordokat írt storage további elemzés céljából.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban egy ügyfélalkalmazás által létrehozott minta telefonhívás metaadatok használatával fogjuk szimulálása telefonhívás-adatok. Egy része, amely az alkalmazás a csaló hívások kinézni. 

Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Egy Azure-fiók.
* A hívás-alkalmazást, [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), amely letölthető a Microsoft Download Center. Csomagolja ki ezt a csomagot egy mappába a számítógépen. Ha meg szeretné tekinteni a kódot, és futtassa az alkalmazást a hibakeresőt a forrás, beszerezheti az alkalmazás forráskódjának [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Windows blokkolhatják a letöltött zip-fájlt. Ha nem tömörítse ki, kattintson jobb gombbal a fájlra, majd válassza ki **tulajdonságok**. Ha az "Ez a fájl egy másik számítógépről érkezett és a számítógép védelme érdekében érdemes lehet blokkolni" üzenetet látja, válassza ki a **feloldása** lehetőséget, majd kattintson **alkalmaz**.

Ha azt szeretné, a Streaming Analytics-feladat eredményeinek vizsgálata, is szüksége lesz egy eszköz megtekintése az Azure Blob Storage-tárolókat a tartalmát. Ha a Visual Studio használata esetén használhatja [Azure Tools for Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) vagy [Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Azt is megteheti, mint például az önálló eszközökkel telepítheti [Azure Storage Explorer](https://storageexplorer.com/) vagy [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Hozzon létre egy Azure Event Hubs eseményeket, hogy

Adatfolyam elemzését, *betöltési* azt az Azure-bA. Egy tipikus módja betölteni az adatokat [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md), amellyel másodpercenként több millió feldolgozására és majd fel, és az események adatait tárolja. Ebben az oktatóanyagban létrehoz egy eseményközpontot, és akkor rendelkezik az a hívás-eseménygenerátor alkalmazással elküldi a hívásadatokat ennek az eseményközpontnak. Az event hubs kapcsolatos további információkért tekintse meg a [Azure Service Bus dokumentációja](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Ez az eljárás részletes verzióját, lásd: [hozzon létre egy Event Hubs-névtér és a egy eseményközpontot, az Azure portal használatával](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>A névtér és az eseményközpont létrehozása
Ebben az eljárásban először hozzon létre egy eseményközpont-névteret, és ezután hozzáadhat új eseményközpont adott névtérhez. Eseményközpont-névtér logikailag csoportosítják a kapcsolódó esemény bus példányok szolgálnak. 

1. Jelentkezzen be az Azure Portalon, és kattintson a **erőforrás létrehozása** > **IOT-** > **Eseményközpont**. 

2. Az a **névtér létrehozása** panelen adja meg például a névtér nevét `<yourname>-eh-ns-demo`. A névtér bármilyen nevet használhat, de a név érvényes URL-cím lehet, és Azure-ban egyedinek kell lennie. 
    
3. Válasszon ki egy előfizetést, és hozzon létre vagy válasszon ki egy erőforráscsoportot, majd kattintson **létrehozás**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

4. Ha a névtér telepítése befejeződött, az eseményközpont-névtér található az Azure-erőforrások listája. 

5. Kattintson az új névtéren, és a névtér panelen kattintson a **Eseményközpont**.

   ![Az Eseményközpont felvétele gombra egy új eseményközpont létrehozása](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Nevezze el az új event hubs `asa-eh-frauddetection-demo`. Más nevet is használhat. Ha így tesz, jegyezze fel, mert később szüksége lesz a neve. Nem kell most az event hubs bármilyen egyéb beállításainak megadása.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
 
7. Kattintson a **Create** (Létrehozás) gombra.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Az eseményközponthoz való hozzáférés engedélyezése és kapcsolati sztring beszerzése

Egy folyamat küldhet adatokat egy eseményközpontba, mielőtt az event hubs egy szabályzatot, amely lehetővé teszi, hogy a megfelelő hozzáféréssel kell rendelkeznie. A hozzáférési szabályzat egy kapcsolati sztringet hoz létre, amelyben megtalálhatók az engedélyezési információk.

1.  Az esemény névtér panelen kattintson **az Event Hubs** és kattintson az új eseményközpont neve.

2.  Az event hub paneljén kattintson **megosztott elérési házirendek** majd  **+ &nbsp;Hozzáadás**.

    >[!NOTE]
    >Ellenőrizze, hogy az event hubs, nem az eseményközpont-névtér dolgozik.

3.  Nevű szabályzat hozzáadása `sa-policy-manage-demo` és **jogcím**válassza **kezelés**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  Kattintson a **Create** (Létrehozás) gombra.

5.  A házirend telepítése után kattintson rá a megosztott elérési szabályzatok listájában.

6.  Keresse meg a be az **KAPCSOLATI karakterlánc – elsődleges kulcs** , és kattintson a Másolás gombra a kapcsolati karakterlánc mellett. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  Illessze be a kapcsolati sztringet egy szövegszerkesztőbe. Szükség van a kapcsolati karakterlánc a következő szakaszban rá néhány kisebb módosítások végrehajtása után.

    A kapcsolati karakterláncot a következőhöz hasonló:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    Figyelje meg, hogy a kapcsolati karakterláncot tartalmazza-e több kulcs-érték párok, pontosvesszővel elválasztva: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, és `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Konfigurálja és indítsa el az eseménylétrehozó alkalmazás

A TelcoGenerator alkalmazást a Kezdés előtt konfigurálnia kell azt, hogy a hívásrekordokat küld az event hubs hozott létre.

### <a name="configure-the-telcogenerator-app"></a>A TelcoGenerator alkalmazás konfigurálása

1.  A szerkesztőben, ahol kimásolt kapcsolati karakterláncot, jegyezze fel a `EntityPath` értéket, és távolítsa el a `EntityPath` pár (ne felejtse el eltávolítani a pontosvesszőt is). 

2.  A mappa, ahol kicsomagolt-e a TelcoGenerator.zip fájlt nyissa meg a telcodatagen.exe.config fájlt egy szövegszerkesztőben. (Több .config fájl, ezért ügyeljen arra, hogy, nyissa meg a megfelelőt.)

3.  Az a `<appSettings>` elem:

    * Az értékét állítsa be a `EventHubName` kulcs az eseményközpont neve (azt jelenti, az a entitás elérési útja értékre).
    * Az értékét állítsa be a `Microsoft.ServiceBus.ConnectionString` kulcs kapcsolati karakterláncot. 

    A `<appSettings>` szakasz az alábbi példához hasonlóan fog kinézni. (Az egyértelműség kedvéért a sort és bizonyos karakterek el lettek távolítva az engedélyezési jogkivonatra.)

   ![TelcoGenerator konfigurációs fájl jeleníti meg az eseményközpont nevét és kapcsolati karakterlánca](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4.  Mentse a fájlt. 

### <a name="start-the-app"></a>Indítsa el az alkalmazást
1.  Nyisson meg egy parancsablakot, és módosítsa a mappát, ahol a TelcoGenerator alkalmazást kicsomagolt.
2.  Írja be a következő parancsot:

        ```cmd
        telcodatagen.exe 1000 0.2 2
        ```

    A paraméterek a következők: 

    * EK száma / óra. 
    * SIM-kártya csalás valószínűség: Milyen gyakran százalékában minden hívás, hogy az alkalmazás szimuláljon csaló hívásokat. A 0,2-es érték azt jelenti, hogy nagyjából a hívások 20%-a fog csalónak tűnni.
    * Időtartam (óra). Fusson az alkalmazás órák száma. Is leállíthatja az alkalmazás bármikor a Ctrl + C billentyűkombinációt a parancssorból.

    Néhány másodperc elteltével az alkalmazás elkezdi kijelezni a hívásrekordokat a képernyőn, miközben az eseményközpontba küldi őket.

Néhány, a csalások valós idejű észlelése alkalmazás által használt kulcs mezőt a következők:

|**Rekord**|**Definíció**|
|----------|--------------|
|`CallrecTime`|A hívási kezdési idejét jelölő időbélyegző. |
|`SwitchNum`|A hívás csatlakozásához használt telefonkapcsoló. Ebben a példában a kapcsolók olyan sztringek, amelyek a származási országot jelölik (USA, Kína, Egyesült királyság, Németország vagy Ausztrália). |
|`CallingNum`|A hívó telefonszáma. |
|`CallingIMSI`|Az International Mobile Subscriber Identity (IMSI). Ez az a hívó egyedi azonosítója. |
|`CalledNum`|A hívott fél telefonszáma. |
|`CalledIMSI`|International Mobile Subscriber Identity (IMSI). Ez az a hívás címzett egyedi azonosítója. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Streamelési adatok kezelésére a Stream Analytics-feladat létrehozása

Most, hogy a hívási események streamjével, állíthat egy Stream Analytics-feladatot. A feladat adatokat olvasni az event hubs beállításával. 

### <a name="create-the-job"></a>A feladat létrehozása 

1. Az Azure Portalon kattintson a **erőforrás létrehozása** > **IOT-** > **Stream Analytics-feladat**.

2. Nevezze el a feladat `asa_frauddetection_job_demo`, adjon meg egy előfizetést, erőforráscsoportot és helyet.

    Helyezze el a feladat és az eseményközpont ugyanabban a régióban, a legjobb teljesítmény érdekében érdemes, és hogy a régiók közötti adatátvitel után ne fizessen.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Kattintson a **Create** (Létrehozás) gombra.

    A feladat jön létre, és a portál megjeleníti a feladat részleteit. Semmi nem fut, azonban – a feladat konfigurálására, megkezdése előtt.

### <a name="configure-job-input"></a>Feladatbemenet konfigurálása

1. Az irányítópult vagy a **összes erőforrás** ablaktáblán, keresse meg és válassza ki a `asa_frauddetection_job_demo` Stream Analytics-feladatot. 
2. Az a **áttekintése** szakasz a Stream Analytics-feladat panel, kattintson a **bemeneti** mezőbe.

   ![A Stream Analytics-feladat panel a topológia a beviteli mező](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Kattintson a **streambemenet hozzáadása** válassza **Eseményközpont**. Ezután töltse ki az új bemeneti lap a következő információkat:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Bemeneti alias  |  CallStream   |  Adja meg a feladat bemenetének azonosító nevét.   |
   |Előfizetés   |  \<Az Ön előfizetése\> |  Válassza ki az Azure-előfizetést, amely rendelkezik az Event Hubs hozott létre.   |
   |Event Hubs-névtér  |  asa-eh-ns-demo |  Adja meg az Eseményközpont névterének nevét.   |
   |Eseményközpont neve  | asa-eh-frauddetection-demo | Válassza ki az Event Hub nevét.   |
   |Eseményközpont szabályzatának neve  | asa-policy-manage-demo | Válassza ki a korábban létrehozott hozzáférési szabályzat.   |
    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Kattintson a **Create** (Létrehozás) gombra.

## <a name="create-queries-to-transform-real-time-data"></a>Valós idejű adatok átalakításához lekérdezések létrehozása

Ezen a ponton rendelkezik egy Stream Analytics-feladat egy bejövő streamből beolvasni beállítása. A következő lépés, ha egy lekérdezést, amely elemzi az adatokat valós időben. Stream Analytics egy egyszerű, deklaratív lekérdezési modellt, amely leírja a valós idejű feldolgozásra átalakítások támogatja. A lekérdezések használata SQL-szerű nyelven van néhány konkrét a Stream Analytics extensions. 

Előfordulhat, hogy egy egyszerű lekérdezést csak olvassa el a bejövő adatok. Azonban gyakran létrehozhat olyan lekérdezéseket, amelyek meghatározott adatok vagy a kapcsolatokat, az adatok. Az oktatóanyag jelen szakaszában hozzon létre és tesztelhet számos további elemzés céljából egy bemeneti stream alakíthatja át néhány módszer. 

Az itt létrehozott lekérdezések csak megjeleníti az átalakított adatok a képernyőre. Egy későbbi szakaszban konfigurálja egy kimeneti fogadó és a egy lekérdezést, amely az átalakított adatokat ír ugyanebbe az, hogy a fogadó.

A nyelv kapcsolatos további információkért tekintse meg a [Azure Stream Analytics lekérdezési nyelv leírása](https://msdn.microsoft.com/library/dn834998.aspx).

### <a name="get-sample-data-for-testing-queries"></a>Minta adatbeolvasás a lekérdezések tesztelése

A TelcoGenerator alkalmazást hívásrekordokat küld az event hubs és a Stream Analytics-feladat úgy van konfigurálva, az event hubs olvasni. Egy lekérdezés segítségével győződjön meg arról, hogy helyesen van-e olvasási, hogy a feladat tesztelése. Az Azure-konzolon a lekérdezés teszteléséhez szüksége mintaadatok. Ebben a bemutatóban a mintaadatok fog kinyerése a streamet, amely az event hubs-eseményközpontba érkezik.

1. Győződjön meg arról, hogy TelcoGenerator alkalmazás fut, és hívásrekordokat.
2. A portálon térjen vissza a Stream Analytics-feladat panel. (Ha már bezárta a panelen, a keresési `asa_frauddetection_job_demo` a a **összes erőforrás** ablaktáblán.)
3. Kattintson a **lekérdezés** mezőbe. Azure bemeneteit és kimeneteit a feladat konfigurált sorolja fel, és a egy lekérdezést, amely a bemeneti stream alakíthatja a kimeneti keresztül küldött létrehozását teszi lehetővé.
4. Az a **lekérdezés** ablaktáblán kattintson a Tovább gombra a pontra a `CallStream` adja meg, és válassza ki **mintaadatok bemenetből**.

   ![A Stream Analytics feladat "Mintaadatok a bemenetből" kijelölt bejegyzés mintaadatok használandó menü parancsai](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Állítsa be **perc** 3 majd **OK**. 
    
   ![Bemeneti stream mintavételezi a kiválasztott 3 perc lehetőségei](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Az Azure-minták 3 perc alatt az adatokat a bemeneti streamből, és értesíti, amikor készen áll a mintaadatok. (Ez tart egy rövid ideig.) 

A rendszer ideiglenesen tárolja a mintaadatokat, amelyek akkor érhetők el, amikor meg van nyitva a lekérdezési ablak. Ha bezárja a lekérdezési ablakot, a mintaadatok elvesznek, és új mintaadatkészletet kell létrehoznia. 

Alternatív megoldásként egy .JSON kiterjesztésű fájlt, amely mintaadatokat tartalmaz, kérheti [a Githubról](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), majd töltse fel ezt a .json fájlt, a mintaadatokat használja a `CallStream` bemeneti. 

### <a name="test-using-a-pass-through-query"></a>Tesztelje a továbbított lekérdezés használatával

Ha minden eseményhez archiválni kívánja, a továbbított lekérdezés segítségével olvassa el az esemény az adattartalomban szereplő összes mezőt.

1. A lekérdezési ablakban adja meg a lekérdezés:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Az SQL nem különböztetik meg a kulcsszavak, és szóköz nem jelentős.

    Ebben a lekérdezésben `CallStream` van a bemeneti létrehozásakor megadott aliasnévnek. Ha egy másik aliast, használja inkább ezt a nevet.

2. Kattintson a **teszt**.

    A Stream Analytics-feladat a mintaadatokat a lekérdezés fut, és a kimenet megjeleníti az ablak alján. Az eredmények jelzik, hogy az Event Hubs és a Streaming Analytics-feladat megfelelően vannak konfigurálva. (Feljegyzett, később fog létrehozni, amely a lekérdezés adatokat írhat kimeneti fogadóként.)

   ![Stream Analytics-feladat kimenetét, 73 rekord jön létre](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    A rekordok láthatja a pontos szám függ, hány rekordok rögzítette a 3 perces minta.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>A mezőket egy oszlop leképezése számának csökkentése

Sok esetben az elemzést a bemeneti streamből az oszlopok nem szükséges. Egy lekérdezés segítségével kisebb a visszaadott mezők halmaza, mint a továbbított lekérdezés a projekthez.

1. A kódszerkesztőben lekérdezés módosítása a következőhöz:

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. Kattintson a **teszt** újra. 

   ![Stream Analytics-feladat kimenete kivetítéshez 25 rekordot jeleníti meg.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Régiónként beérkező hívások száma: Átfedésmentes ablak összesítéssel

Tegyük fel, hogy meg szeretné számolni a régiónként beérkező hívások száma. A streamelési adatok, ha szeretne végezni az összesítő függvények, például a leltár, kell szegmens az adatfolyam historikus egységekbe, (mivel a adatfolyam magát gyakorlatilag végtelen). Ehhez egy Stream Analytics használatával [oszlopdefiníció ablakfüggvényt](stream-analytics-window-functions.md). Ezután használhatja ezt az ablakot lévő adatok egy egységként.

Az átalakítás keresi, amelyek nincsenek átfedésben a historikus windows sorozatát – minden ablak egy készlete csoportosíthatók adatokat és összesítés fog rendelkezni. Az ilyen típusú ablak a neve egy *Átfedésmentes ablak*. Az Átfedésmentes ablak belül kérheti a szerint csoportosítva beérkező hívások számát `SwitchNum`, amely az ország, ahol a hívást indító jelöli. 

1. A kódszerkesztőben lekérdezés módosítása a következőhöz:

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    Ez a lekérdezés használ a `Timestamp By` kulcsszót a a `FROM` adja meg, melyik időbélyegmezőt a bemeneti Stream használata a Átfedésmentes ablak záradékot. Ebben az esetben az ablak osztja fel az adatokat a szegmensek a `CallRecTime` rekordokban levő mezőt. (Ha nincs mező van megadva, a leképezési művelet az időpontot használja, amely minden egyes esemény szolgáltatáshoz érkezik, az event hubs. "Érkezési ideje Vs alkalmazás idő" című [Stream Analytics lekérdezési nyelv leírása](https://msdn.microsoft.com/library/azure/dn834998.aspx). 

    A leképezés tartalmaz `System.Timestamp`, minden időszak végén időbélyeggel ad vissza. 

    Adja meg, hogy egy Átfedésmentes ablak használatához, használja a [TUMBLINGWINDOW](https://msdn.microsoft.com/library/dn835055.aspx) működni a `GROUP BY `záradékban. A függvény időegységet (bárhol az egy napra mikromásodperces) és a egy ablakméret (hány egység) kell megadni. Ebben a példában az Átfedésmentes ablak áll 5 másodperces időközökkel, így országonként számát a hívásokat minden 5 másodperc alatt érkezett fog kapni.

2. Kattintson a **teszt** újra. Figyelje meg, hogy az eredmények között, az időbélyegek alapján **WindowEnd** 5 másodperces növekményekben vannak.

   ![Stream Analytics-feladat kimenetét 13 rekord összesítés](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Önillesztés használatával SIM visszaélések észleléséhez

Ebben a példában fontolja meg a hívás, amely ugyanahhoz a felhasználóhoz, de a különböző helyeken származnak, egy másik 5 másodpercen belül kell csaló használat. Például ugyanaz a felhasználó nem indíthat szabályosan hívásokat egyszerre az USA-ból és Ausztráliából. 

Ellenőrizze az ezekben az esetekben, használhatja a streamelési adatok önillesztést önillesztést a stream alapján a `CallRecTime` értéket. Ezután megkeresheti hívás rekordokat, ahol a `CallingIMSI` értéket (a származási szám) megegyezik, de a `SwitchNum` (származási ország) értéke nem ugyanaz.

Streamelési adatok illesztést használ, ha az illesztés bizonyos korlátozások az egyező sorok illesztésnek választhatók el egymástól, időben kell megadnia. (Korábban feljegyzett a streamelt adatokat az gyakorlatilag végtelen.) A kapcsolat ideje határán belül vannak megadva a `ON` a JOIN záradék használatával a `DATEDIFF` függvény. Ebben az esetben a join egy 5 másodperces időköze hívási adatonként alapján történik.

1. A kódszerkesztőben lekérdezés módosítása a következőhöz: 

        ```SQL
        SELECT  System.Timestamp as Time, 
            CS1.CallingIMSI, 
            CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, 
            CS1.SwitchNum as Switch1, 
            CS2.SwitchNum as Switch2 
        FROM CallStream CS1 TIMESTAMP BY CallRecTime 
            JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
            ON CS1.CallingIMSI = CS2.CallingIMSI 
            AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
        WHERE CS1.SwitchNum != CS2.SwitchNum
        ```

    Ez a lekérdezés az alábbiakat kivéve minden SQL való csatlakozás hasonlít a `DATEDIFF` függvényt a JOIN. Ezen verziója `DATEDIFF` csak a Streaming Analyticsben és szerepelniük kell a `ON...BETWEEN` záradékban. A paraméterek a következők: időegységet (ebben a példában másodperc) és az aliasokat a két forrás, az illesztési operátor. Ez különbözik a standard SQL `DATEDIFF` függvény.

    A `WHERE` záradékot tartalmaz a feltételt, amelyet a csaló hívásokat jelzők: az eredeti kapcsolók nem azonosak. 

2. Kattintson a **teszt** újra. 

   ![Stream Analytics-feladat kimenetét a generált önillesztést, látható 6 rekordjában](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Kattintson a **mentése** önillesztést lekérdezés mentéséhez a Streaming Analytics-feladat részeként. (A mintaadatok nem mentse azt.)

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Hozzon létre egy kimeneti fogadóba átalakított adatok tárolásához

Meghatározta az eseménystream egy eseményközpontba, adjon meg, hogy eseményeket és a egy lekérdezést a stream keresztül átalakítás végrehajtásához. Az utolsó lépés az, hogy a feladat egy kimeneti fogadó –, hely, ahol az átalakított adatfolyam írása. 

Kimeneti fogadóként használhatja sok erőforrást – SQL Server-adatbázis, a table storage, a Data Lake storage, a Power BI és akár egy másik eseményközpontot. Ebben az oktatóanyagban az Azure Blob Storage ideális választás későbbi elemzés céljából esemény adatainak összegyűjtése, mivel így alkalmazkodik a teljes strukturálatlan adatmennyiséget tárolni a stream fog írni.

Ha rendelkezik meglévő blob storage-fiókot, használhatja azt. Ebben az oktatóanyagban megtudhatja hogyan hozhat létre egy új tárfiókot.

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob Storage-fiók létrehozása

1. Az Azure Portal bal felső sarkában válassza az **Erőforrás létrehozása** > **Storage** > **Tárfiók** lehetőséget. Töltse ki a Tárfiók feladat oldalán **neve** "asaehstorage", állítsa **hely** állítsa be az "USA keleti régiója", **erőforráscsoport** "asa-eh-ns-rg" (a storage-fiók az állomás beállítása ugyanazt az erőforráscsoportot, a megnövelt teljesítménye a folyamatos átviteli feladatot). A fennmaradó beállításokat alapértelmezett értéken hagyhatja.  

   ![Tárfiók létrehozása az Azure Portalon](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Az Azure Portalon térjen vissza a Stream Analytics-feladat panel. (Ha már bezárta a panelen, a keresési `asa_frauddetection_job_demo` a a **összes erőforrás** ablaktáblán.)

3. Az a **Feladattopológia** területén kattintson a **kimeneti** mezőbe.

4. Az a **kimenetek** ablaktáblán kattintson a **Hozzáadás** , és válassza ki **a Blob storage-**. Ezután adja meg az új kimeneti oldal a következő információkat:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Kimeneti alias  |  CallStream-FraudulentCalls   |  Adja meg a feladat kimenetének azonosító nevét.   |
   |Előfizetés   |  \<Az Ön előfizetése\> |  Válassza ki azt az Azure-előfizetést, amelyhez a létrehozott tárfiók tartozik. A tárfiók tartozhat ugyanahhoz az előfizetéshez, de akár egy másik előfizetéshez is. A példa azt feltételezi, hogy a tárfiók ugyanahhoz az előfizetéshez tartozik. |
   |Tárfiók  |  asaehstorage |  Adja meg a létrehozott tárfiók nevére. |
   |Tároló  | asa-fraudulentcalls-demo | Válassza ki az új létrehozása, és adja meg a tároló nevét. |
    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Kattintson a **Save** (Mentés) gombra. 


## <a name="start-the-streaming-analytics-job"></a>A Stream Analytics-feladat indítása

A feladat most úgy van beállítva. Egy bemeneti (az event hubs), egy átalakítás (a keres csalási célú hívásokat lekérdezés) és -kimenet (a blob storage) megadása. Most már elindíthatja a feladat. 

1. Győződjön meg arról, hogy TelcoGenerator alkalmazás fut-e.

2. A feladatok ablaktáblán kattintson a **Start**. Az a **indítási feladat** ablaktáblán, a feladat kimeneti kezdési ideje, jelölje be **most**. 

   ![A Stream Analytics-feladat indítása](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Az átalakított adatok vizsgálata

Most már rendelkezik egy teljes körű Stream Analytics-feladat. A feladat vizsgálata folyamatban van egy stream telefonhívás-metaadatok, valós idejű csaló telefonhívásokat keres, és ezek csaló hívásokat a tároló felé kapcsolatos információkat írásához. 

Az oktatóanyag elvégzéséhez érdemes tekintse meg a Stream Analytics-feladat által éppen rögzített adatokat. Az adatok Azure Blog Storage van ír tömbökben (fájlok). Minden olyan eszközt, amely beolvassa az Azure Blob Storage is használhatja. Az Előfeltételek szakaszban feljegyzett is használhatja az Azure-bővítmény a Visual Studióban, vagy egy hasonló eszközzel [Azure Storage Explorer](https://storageexplorer.com/) vagy [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

A blob Storage-fájl tartalmának vizsgálata, lásd a következőhöz hasonló:

   ![Az Azure blob storage és a Stream Analytics-kimenet](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincsenek további cikk is folytatja a csalásészlelés forgatókönyvet, és ebben az oktatóanyagban létrehozott erőforrásokat használja. Ha folytatni kívánja, tekintse meg a javaslatok alapján **további lépések**.

Azonban ha elkészült, és a létrehozott erőforrásokra már nincs szüksége, törölheti őket, hogy ne szükségtelen Azure díjak merülhetnek fel. Ebben az esetben javasoljuk, hogy tegye a következőket:

1. A Streaming Analytics-feladat leállítása Az a **feladatok** ablaktáblán kattintson a **leállítása** tetején.
2. Állítsa le a telekommunikációs Eseménygenerátor alkalmazással. A parancssori ablakba, ahol az alkalmazás elindítását nyomja le a Ctrl + C billentyűkombinációt.
3. Ha ebben az oktatóanyagban csak a létrehozott új blob storage-fiókot, törölje azt. 
4. A Streaming Analytics-feladat törlése.
5. Az event hub törlése.
6. Az eseményközpont-névtér törlése.

## <a name="get-support"></a>Támogatás kérése

További segítségre van szüksége, próbálja meg a [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag a következő cikkel továbbra is:

* [Stream Analytics és a Power bi-ban: A valós idejű elemzési irányítópultok, a streamelési adatok](stream-analytics-power-bi-dashboard.md). Ez a cikk bemutatja, hogyan a Stream Analytics-feladat kimenetét telekommunikációs küldendő Power BI valós idejű Vizualizáció és elemzés.

További információ a Stream Analytics általában tanulmányozza a következő cikkeket:

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
