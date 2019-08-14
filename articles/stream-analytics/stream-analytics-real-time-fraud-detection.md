---
title: A csalások valós idejű észlelése Azure Stream Analytics használatával
description: Megtudhatja, hogyan hozhat létre valós idejű csalások elleni észlelési megoldást Stream Analytics használatával. A valós idejű események feldolgozásához használja az Event hub-t.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 19c9448b6a743302eb81bb208444336d6435f114
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947047"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>A Azure Stream Analytics használatának első lépései: Csalások valós idejű észlelése

Ez az oktatóanyag egy teljes körű illusztrációt biztosít a Azure Stream Analytics használatáról. Az alábbiak végrehajtásának módját ismerheti meg: 

* A folyamatos átviteli eseményeket az Azure Event Hubs-példányba viszi. Ebben az oktatóanyagban egy olyan alkalmazást fog használni, amely szimulálja a Mobile-Phone metaadat-rekordok streamjét.

* SQL-szerű Stream Analytics-lekérdezések írása adatok átalakításához, adatok összesítéséhez vagy mintázatok kereséséhez. Megtudhatja, hogyan használhatja a lekérdezéseket a bejövő adatfolyam vizsgálatára, és keressen olyan hívásokat, amelyek hamisak lehetnek.

* Küldje el az eredményeket egy kimeneti fogadóba (tárhelyre), amelyet elemezni tud a további elemzésekhez. Ebben az esetben a gyanús hívást az Azure Blob Storage szolgáltatásba küldi.

Ez az oktatóanyag a valós idejű csalások észlelésének példáját használja a telefonhívási adatmennyiség alapján. Az illusztrált technika más típusú csalások észlelésére is alkalmas, például a hitelkártya-csalások vagy a személyazonosság-lopás. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Forgatókönyv: Távközlés és SIM-csalások észlelése valós időben

A távközlési vállalatok nagy mennyiségű adattal rendelkeznek a bejövő hívásokhoz. A vállalat valós időben szeretné felderíteni a csalárd hívásokat, hogy értesítéseket küldjön az ügyfeleknek, vagy leállítsa a szolgáltatást egy adott számra. Az egyfajta SIM-csalás több hívást is magában foglal ugyanazon identitás körül, de földrajzilag különböző helyszíneken. Az ilyen típusú csalások észleléséhez a vállalatnak meg kell vizsgálnia a beérkező telefonos rekordokat, és meg kell keresnie bizonyos mintákat – ebben az esetben a különböző országokban/régiókban egy időben végrehajtott hívásokat. Az ebbe a kategóriába tartozó telefonos rekordok a további elemzés céljából a tárolóba kerülnek.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a telefonhívási adatokat szimulálja egy olyan ügyfélalkalmazás használatával, amely minta telefonhívás-metaadatokat hoz létre. Az alkalmazás által előállított rekordok némelyike hamis hívásokat eredményez. 

Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Egy Azure-fiók.
* A [TelcoGenerator. zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)fájlt, amely a Microsoft letöltőközpontból tölthető le. Csomagolja ki a csomagot a számítógép egyik mappájába. Ha szeretné megtekinteni a forráskódot, és egy hibakeresőben futtatja az alkalmazást, akkor a Githubról szerezheti be az [](https://aka.ms/azure-stream-analytics-telcogenerator)alkalmazás forráskódját. 

    >[!NOTE]
    >Előfordulhat, hogy a Windows letiltja a letöltött. zip fájlt. Ha nem tudja kibontani, kattintson a jobb gombbal a fájlra, és válassza a **Tulajdonságok**lehetőséget. Ha a "Ez a fájl egy másik számítógépről érkezett, és előfordulhat, hogy a számítógép védelmének megakadályozása" üzenet jelenik meg, válassza a **Tiltás feloldása** lehetőséget, majd kattintson az **alkalmaz**gombra.

Ha meg szeretné vizsgálni a streaming Analytics-feladatok eredményeit, az Azure Blob Storage-tároló tartalmának megtekintéséhez eszközre is szüksége lesz. Ha a Visual studiót használja, használhatja [a Visual Studio vagy a](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) [Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer)Azure Tools eszközt is. Alternatív megoldásként olyan önálló eszközöket is telepíthet, mint például a [Azure Storage Explorer](https://storageexplorer.com/) vagy a [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Azure-Event Hubs létrehozása események betöltéséhez

Az adatfolyamok elemzéséhez az Azure -ba kerül. Az adatok betöltésének tipikus módja az [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)használata, amely másodpercenként akár több millió eseményt is felhasználhat, majd feldolgozhatja és tárolhatja az események adatait. Ebben az oktatóanyagban létre fog hozni egy Event hub-t, majd a Call-Event Generator alkalmazásnak meg kell küldenie a hívási adatközpontot. További információ az Event hubokról: [Azure Service Bus dokumentáció](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Az eljárás részletesebb változata: [Event Hubs névtér és az Event hub létrehozása a Azure Portal használatával](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>A névtér és az eseményközpont létrehozása
Ebben az eljárásban először létre kell hoznia egy Event hub-névteret, majd hozzá kell adnia egy Event hub-t a névtérhez. Az Event hub-névterek a kapcsolódó Event Bus-példányok logikai csoportosítására szolgálnak. 

1. Jelentkezzen be a Azure Portalba, és kattintson az **erőforrás** > létrehozása**eszközök internetes hálózata** > **Event hub**elemre. 

2. A **névtér létrehozása** panelen adja meg a névtér nevét, például `<yourname>-eh-ns-demo`:. Bármilyen nevet használhat a névtérhez, de a névnek érvényesnek kell lennie az URL-címhez, és egyedinek kell lennie az Azure-ban. 
    
3. Válasszon ki egy előfizetést, hozzon létre vagy válasszon ki egy erőforráscsoportot, majd kattintson a **Létrehozás**gombra.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

4. Ha a névtér befejezte a telepítést, keresse meg az Event hub-névteret az Azure-erőforrások listájában. 

5. Kattintson az új névtérre, majd a névtér ablaktáblán kattintson az **Event hub**elemre.

   ![Az Event hub hozzáadása gomb új Event hub létrehozásához](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Nevezze el az új Event `asa-eh-frauddetection-demo`hubot. Más nevet is használhat. Ha így tesz, jegyezze fel, mert később szüksége lesz erre a névre. Jelenleg nem kell megadnia az Event hub egyéb beállításait.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
 
7. Kattintson a **Create** (Létrehozás) gombra.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Az eseményközponthoz való hozzáférés engedélyezése és kapcsolati sztring beszerzése

Ahhoz, hogy egy folyamat hozzáférhessen az adatközponthoz, az Event hub-nak rendelkeznie kell egy olyan házirenddel, amely lehetővé teszi a megfelelő hozzáférést. A hozzáférési szabályzat egy kapcsolati sztringet hoz létre, amelyben megtalálhatók az engedélyezési információk.

1.  Az esemény-névtér ablaktáblán kattintson a **Event Hubs** elemre, majd kattintson az új Event hub nevére.

2.  Az Event hub ablaktáblán kattintson a **megosztott elérési házirendek** elemre, majd a  **+ &nbsp;Hozzáadás**gombra.

    >[!NOTE]
    >Győződjön meg arról, hogy az Event hub-t használja, nem az Event hub-névteret.

3.  Adja hozzá a és `sa-policy-manage-demo` a **jogcím**nevű szabályzatot, majd válassza a **kezelés**lehetőséget.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  Kattintson a **Create** (Létrehozás) gombra.

5.  Miután telepítette a házirendet, kattintson rá a megosztott hozzáférési házirendek listájában.

6.  Keresse meg a kapcsolódási **karakterlánc – elsődleges kulcs** jelölőnégyzetet, és kattintson a kapcsolódási karakterlánc melletti Másolás gombra. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  Illessze be a kapcsolati sztringet egy szövegszerkesztőbe. A következő szakaszhoz szükség van erre a kapcsolódási sztringre, miután elvégezte néhány kis módosítást.

    A következőhöz hasonló a kapcsolatok karakterlánca:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    Figyelje meg, hogy a kapcsolatok karakterlánca több kulcs-érték párokat tartalmaz, pontosvesszővel elválasztva `EntityPath`: `Endpoint` `SharedAccessKey`, `SharedAccessKeyName`, és.  

## <a name="configure-and-start-the-event-generator-application"></a>Az Event Generator alkalmazás konfigurálása és elindítása

Mielőtt elkezdené a TelcoGenerator alkalmazást, úgy kell konfigurálnia, hogy a hívási rekordokat a létrehozott Event hubhoz küldje.

### <a name="configure-the-telcogenerator-app"></a>A TelcoGenerator alkalmazás konfigurálása

1. A szerkesztőben, ahová a kapcsolódási karakterláncot másolta, jegyezze fel `EntityPath` az értéket, majd távolítsa `EntityPath` el a párt (ne felejtse el eltávolítani a pontosvesszőt, amely megelőzi azt). 

2. A TelcoGenerator. zip fájl kibontása mappában Nyissa meg a telcodatagen. exe. config fájlt egy szerkesztőben. (Több. config fájl is van, ezért mindenképpen nyissa meg a megfelelőt.)

3. A `<appSettings>` elemben:

   * Állítsa a `EventHubName` kulcs értékét az Event hub nevére (vagyis az entitás elérési útjának értékére).
   * Állítsa a `Microsoft.ServiceBus.ConnectionString` kulcs értékét a kapcsolódási karakterláncra. 

   A `<appSettings>` szakasz az alábbi példához hasonlóan fog kinézni. (Az egyértelműség érdekében a vonalak beburkoltak, és néhány karakter el lett távolítva az engedélyezési jogkivonatból.)

   ![A TelcoGenerator konfigurációs fájlja az Event hub nevét és a kapcsolati karakterláncot jeleníti meg](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4. Mentse a fájlt. 

### <a name="start-the-app"></a>Az alkalmazás elindítása
1.  Nyisson meg egy parancssorablakot, és váltson arra a mappára, ahol a TelcoGenerator alkalmazás ki van csomagolva.

2.  Írja be a következő parancsot:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   A paraméterek a következők: 

   * CdR száma óránként. 
   * SIM-kártya csalásának valószínűsége: Az összes hívás százalékaként az alkalmazásnak hamis hívást kell szimulálnia. A 0,2-es érték azt jelenti, hogy nagyjából a hívások 20%-a fog csalónak tűnni.
   * Időtartam órában. Azon órák száma, ameddig az alkalmazásnak futnia kell. Az alkalmazást bármikor leállíthatja, ha a parancssorban a CTRL + C billentyűkombinációt lenyomva tartja.

   Néhány másodperc elteltével az alkalmazás elkezdi kijelezni a hívásrekordokat a képernyőn, miközben az eseményközpontba küldi őket.

Az ebben a valós idejű csalások észlelése alkalmazásban használt legfontosabb mezők a következők:

|**Rekord**|**Definíció**|
|----------|--------------|
|`CallrecTime`|A hívási kezdési idejét jelölő időbélyegző. |
|`SwitchNum`|A hívás csatlakozásához használt telefonkapcsoló. Ebben a példában a kapcsolók olyan karakterláncok, amelyek a származási országot/régiót (USA, Kína, Egyesült Királyság, Németország vagy Ausztrália) jelölik. |
|`CallingNum`|A hívó telefonszáma. |
|`CallingIMSI`|Az International Mobile Subscriber Identity (IMSI). Ez a hívó egyedi azonosítója. |
|`CalledNum`|A hívott fél telefonszáma. |
|`CalledIMSI`|International Mobile Subscriber Identity (IMSI). Ez a hívás címzettjének egyedi azonosítója. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Stream Analytics-feladatok létrehozása a folyamatos átviteli adatok kezeléséhez

Most, hogy elvégezte a hívási események streamjét, beállíthat egy Stream Analytics feladatot. A művelet beolvassa az Ön által beállított Event hub adatait. 

### <a name="create-the-job"></a>A feladat létrehozása 

1. A Azure Portal kattintson az **erőforrás** > létrehozása**eszközök internetes hálózata** > **stream Analytics**feladatokra.

2. Nevezze el a `asa_frauddetection_job_demo`feladatot, adjon meg egy előfizetést, egy erőforráscsoportot és egy helyet.

    Érdemes a feladatot és az Event hub-t ugyanabban a régióban elhelyezni a legjobb teljesítmény érdekében, és így nem kell fizetnie a régiók közötti adatátvitel során.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Kattintson a **Create** (Létrehozás) gombra.

    A rendszer létrehozza a feladatot, és a portál megjeleníti a feladatok részleteit. A művelet még nem fut, de a művelet elkezdése előtt konfigurálnia kell a feladatot.

### <a name="configure-job-input"></a>Feladatbemenet konfigurálása

1. Az irányítópulton vagy a **minden erőforrás** ablaktáblán keresse meg és válassza `asa_frauddetection_job_demo` ki a stream Analytics feladatot. 
2. A Stream Analytics feladatok ablaktábla **Áttekintés** szakaszában kattintson a **beviteli** mezőre.

   ![Beviteli mező a topológia területen a streaming Analytics-feladatok ablaktáblán](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Kattintson a **stream-bemenet hozzáadása** elemre, és válassza az **Event hub**elemet. Ezután töltse ki az új bemeneti oldalt az alábbi adatokkal:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Bemeneti alias  |  CallStream   |  Adja meg a feladat bemenetének azonosító nevét.   |
   |Subscription   |  \<Az Ön előfizetése\> |  Válassza ki azt az Azure-előfizetést, amelyhez a létrehozott Event hub tartozik.   |
   |Event Hubs-névtér  |  asa-eh-ns-demo |  Adja meg az Event hub-névtér nevét.   |
   |Eseményközpont neve  | asa-eh-frauddetection-demo | Válassza ki az Event hub nevét.   |
   |Eseményközpont szabályzatának neve  | asa-policy-manage-demo | Válassza ki a korábban létrehozott hozzáférési szabályzatot.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Kattintson a **Create** (Létrehozás) gombra.

## <a name="create-queries-to-transform-real-time-data"></a>Lekérdezések létrehozása a valós idejű adatértékek átalakításához

Ezen a ponton egy Stream Analytics feladatsorral beolvasható egy bejövő adatfolyam. A következő lépés egy olyan lekérdezés létrehozása, amely valós időben elemzi az adatelemzést. A Stream Analytics egy egyszerű, deklaratív lekérdezési modellt támogat, amely leírja a valós idejű feldolgozás átalakításait. A lekérdezések olyan SQL-szerű nyelvet használnak, amely a Stream Analyticsra vonatkozó egyes bővítményekkel rendelkezik. 

Előfordulhat, hogy egy egyszerű lekérdezés csak az összes bejövő adatbevitelt olvassa. Előfordulhat azonban, hogy olyan lekérdezéseket hoz létre, amelyek konkrét vagy az adatkapcsolatot keresnek. Az oktatóanyag ezen szakaszában több lekérdezést is létrehozhat és tesztel, így megtanulhatja, hogyan alakíthat át egy bemeneti adatfolyamot elemzésre. 

Az itt létrehozott lekérdezések csak az átalakított adatképernyőt fogják megjeleníteni. Egy későbbi szakaszban egy kimeneti fogadót és egy olyan lekérdezést fog konfigurálni, amely az átalakított adatokat az adott fogadóba írja.

Ha többet szeretne megtudni a nyelvről, tekintse meg a [Azure stream Analytics lekérdezés nyelvi referenciáját](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="get-sample-data-for-testing-queries"></a>Mintaadatok beolvasása lekérdezések teszteléséhez

A TelcoGenerator alkalmazás hívási rekordokat küld az Event hub-nak, és a Stream Analyticsi feladata az Event hub-ról való olvasásra van konfigurálva. A lekérdezés segítségével tesztelheti a feladatot, és ellenőrizheti, hogy helyesen olvas-e el. A lekérdezéseknek az Azure-konzolon való teszteléséhez mintaadatok szükségesek. Ebben az útmutatóban az Event hub-ba érkező adatstreamből Kinyeri a mintaadatok adatait.

1. Győződjön meg arról, hogy a TelcoGenerator alkalmazás fut, és hozzon létre hívási rekordokat.
2. A portálon térjen vissza a streaming Analytics-feladatok ablaktáblára. (Ha bezárta a panelt, `asa_frauddetection_job_demo` keresse meg a **minden erőforrás** ablaktáblán.)
3. Kattintson a **lekérdezés** mezőre. Az Azure felsorolja a feladathoz konfigurált bemeneteket és kimeneteket, és lehetővé teszi egy olyan lekérdezés létrehozását, amely lehetővé teszi a bemeneti adatfolyam átalakítását, ahogy az a kimenetre lesz küldve.
4. A **lekérdezés** ablaktáblán kattintson a `CallStream` bemenet melletti pontokra, majd válassza a **mintaadatok a bemenetből**lehetőséget.

   ![A streaming Analytics-feladathoz tartozó mintaadatok használatára szolgáló menüpontok, a "mintaadatok a bemenetből" lehetőséggel](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Állítsa be a **percet** 3 értékre, majd kattintson **az OK**gombra. 
    
   ![A mintavétel bemeneti adatfolyamának beállításai 3 perc kiválasztásával](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Az Azure 3 perces adatokat ad a bemeneti adatfolyamból, és értesítést küld, ha a mintaadatok készen állnak. (Ez rövid ideig tart.) 

A rendszer ideiglenesen tárolja a mintaadatokat, amelyek akkor érhetők el, amikor meg van nyitva a lekérdezési ablak. Ha bezárja a lekérdezési ablakot, a mintaadatok elvesznek, és új mintaadatkészletet kell létrehoznia. 

Másik lehetőségként beszerezhet egy olyan. JSON fájlt, amely a [githubról származó](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)mintaadatok használatával rendelkezik, majd feltölti azt. JSON fájlt, amelyet mintaadatokként használhat a `CallStream` bemenet számára. 

### <a name="test-using-a-pass-through-query"></a>Tesztelés átmenő lekérdezés használatával

Ha minden eseményt archiválni szeretne, használhat egy áteresztő lekérdezést, amely az esemény hasznos adataiban lévő összes mezőt beolvassa.

1. A lekérdezési ablakban adja meg a következő lekérdezést:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Az SQL-hez hasonlóan a kulcsszavak nem megkülönböztetik a kis-és nagybetűket, és a szóköz nem jelentős.

    Ebben a lekérdezésben `CallStream` az az alias, amelyet a bemenet létrehozásakor adott meg. Ha más aliast használt, használja helyette a nevet.

2. Kattintson a **teszt**gombra.

    A Stream Analyticsi feladatsor futtatja a lekérdezést a mintaadatok között, és megjeleníti a kimenetet az ablak alján. Az eredmények azt jelzik, hogy az Event hub és a streaming Analytics-feladatok megfelelően vannak konfigurálva. (Ahogy azt is említettük, később létrehoz egy kimeneti fogadót, amelyet a lekérdezés adatokat tud írni.)

   ![Stream Analytics a feladatok kimenete, amely a 73-es rekordot generálta](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    A megjelenő rekordok pontos száma attól függ, hogy hány rekordot rögzít a rendszer a 3 perces mintában.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Mezők számának csökkentése oszlop kivetítésével

Sok esetben az elemzéshez nem szükséges a bemeneti adatfolyam összes oszlopa. A lekérdezéssel a visszaadott mezők kisebb készletét lehet feltervezni, mint az áteresztő lekérdezésben.

1. Módosítsa a lekérdezést a Kódszerkesztő alkalmazásban a következőre:

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. Kattintson ismét a **teszt** elemre. 

   ![A kivetítéshez Stream Analytics feladatok kimenete 25 rekordot mutat](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Bejövő hívások száma régiónként: Ablak kibontása összesítéssel

Tegyük fel, hogy régiónként szeretné megszámolni a bejövő hívások számát. A folyamatos átviteli adatok esetében, ha olyan összesítő függvényeket kíván végrehajtani, mint például a számlálás, az adatfolyamot időbeli egységbe kell osztania (mivel maga az adatfolyam gyakorlatilag végtelen). Ezt egy streaming Analytics- [ablak függvény](stream-analytics-window-functions.md)használatával végezheti el. Ezután használhatja az adott ablakban található, egységként szolgáló adatmennyiséget.

Ehhez az átalakításhoz olyan időbeli Windows-sorozatot szeretne használni, amely nem fedi át az átfedést – minden ablaknak külön adatkészlete lesz, amelyet csoportosíthatjuk és összesíteni lehet. Az ilyen típusú ablakokat a rendszer kieséses *ablaknak*nevezzük. A kiesési ablakban a bejövő hívások `SwitchNum`száma látható, amely az országot vagy régiót jelöli, ahol a hívás származik. 

1. Módosítsa a lekérdezést a Kódszerkesztő alkalmazásban a következőre:

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    Ez a lekérdezés a `Timestamp By` `FROM` záradékban található kulcsszót használja annak megadásához, hogy a bemeneti adatfolyam melyik timestamp mezőjében használja a kiesési időszakot. Ebben az esetben az ablak az egyes rekordok `CallRecTime` mezői szerint osztja szét az adatok szegmenseit. (Ha nincs megadva mező, az ablakos művelet azt az időpontot használja, amikor az egyes események megérkeznek az Event hub-ba. Tekintse meg a "megérkezési idő vs Application Time" kifejezést [stream Analytics lekérdezési nyelvi referenciában](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). 

    A vetítés magában `System.Timestamp`foglalja, amely az egyes ablak végének időbélyegét adja vissza. 

    Annak megadásához, hogy egy feltételt tartalmazó ablakot kíván használni, használja a [TUMBLINGWINDOW](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) függvényt a `GROUP BY` záradékban. A függvényben meg kell adnia egy időegységet (bárhol a másodperctől egy napra) és egy ablak méretével (hány egység). Ebben a példában a kihelyező ablak 5 másodperces intervallumokból áll, így minden 5 másodpercenkénti hívás esetén az ország/régió alapján kell számolni.

2. Kattintson ismét a **teszt** elemre. Az eredmények között figyelje meg, hogy a **WindowEnd** alatti időbélyegek 5 másodperces növekményekben vannak.

   ![A feladatok kimenetének Stream Analytics a 13 rekordot ábrázoló összesítéshez](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>SIM-csalások észlelése Önillesztés használatával

Ebben a példában úgy gondolja, hogy a hamis használat olyan hívás, amely ugyanabból a felhasználóból származik, de egy másik helyen, 5 másodpercen belül. Például ugyanaz a felhasználó nem indíthat szabályosan hívásokat egyszerre az USA-ból és Ausztráliából. 

Az ilyen esetek ellenőrzéséhez használhatja a folyamatos átviteli adatátvitelt úgy, hogy a streamet saját magához csatlakoztassa az `CallRecTime` érték alapján. Ezután megkeresheti azokat a hívási rekordokat, amelyekben `CallingIMSI` az érték (az eredeti szám) megegyezik, de a `SwitchNum` (származási ország/régió) értéke nem ugyanaz.

Ha adatfolyam-továbbítási adatokkal csatlakozik, az illesztésnek bizonyos korlátozásokat kell biztosítania, hogy az egyező sorok mennyi idő alatt legyenek elkülönítve. (Ahogy korábban már említettük, a folyamatos átviteli adattovábbítás gyakorlatilag végtelen.) A kapcsolat időkorlátja a JOIN `ON` záradékában van megadva a `DATEDIFF` függvény használatával. Ebben az esetben az illesztés a hívási adatmennyiség 5 másodperces intervallumán alapul.

1. Módosítsa a lekérdezést a Kódszerkesztő alkalmazásban a következőre: 

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

    Ez a lekérdezés bármilyen SQL-csatlakoztatáshoz hasonló, `DATEDIFF` kivéve a JOIN függvényt. A jelen verziója `DATEDIFF` a streaming Analytics szolgáltatásra vonatkozik, és meg kell jelennie a `ON...BETWEEN` záradékban. A paraméterek egy időegység (a példában szereplő másodpercek) és az illesztés két forrásainak aliasai. Ez eltér a szabványos SQL `DATEDIFF` -függvénytől.

    A `WHERE` záradék tartalmazza azt a feltételt, amely a hamis hívást megjelöli: a kezdeményező kapcsolók nem egyeznek. 

2. Kattintson ismét a **teszt** elemre. 

   ![Stream Analytics feladatok kimenete önillesztéshez, 6 rekordot generálva](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Kattintson a **Save (Mentés** ) gombra az önillesztési lekérdezés mentéséhez a streaming Analytics-feladatok részeként. (Nem menti a mintaadatok mentését.)

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Kimeneti fogadó létrehozása az átalakított adatmennyiségek tárolásához

Definiált egy esemény-adatfolyamot, egy Event hub-bemenetet az események betöltéséhez, valamint egy lekérdezést, amely az adatfolyamon keresztül végez átalakítást. Az utolsó lépés egy kimeneti fogadó definiálása a feladatokhoz – vagyis az a hely, ahol az átalakított adatfolyamot be kell írni. 

Számos erőforrást használhat kimeneti mosogatóként – egy SQL Server adatbázist, egy Table Storage-t, Data Lake tárterületet, Power BIt és még egy másik Event hub-t. Ebben az oktatóanyagban a streamet az Azure Blob Storageba fogja írni, amely az esemény-információk későbbi elemzéshez való összegyűjtésére szolgál, mivel a strukturálatlan adatokat is tartalmazza.

Ha rendelkezik meglévő blob Storage-fiókkal, ezt használhatja. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre új Storage-fiókot.

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob Storage-fiók létrehozása

1. Az Azure Portal bal felső sarkában válassza az **Erőforrás létrehozása** > **Storage** > **Tárfiók** lehetőséget. Töltse ki a Storage-fiók feladata lapot "asaehstorage" értékre, az "East us" értékre állítva, az **erőforráscsoport** pedig az "ASA-eh-NS-RG" értékre van állítva (a nagyobb teljesítmény érdekében tárolja a Storage-fiókot ugyanabban az erőforráscsoporthoz, mint a folyamatos átviteli feladatok esetében) . A fennmaradó beállításokat alapértelmezett értéken hagyhatja.  

   ![Storage-fiók létrehozása Azure Portal](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. A Azure Portal térjen vissza a streaming Analytics-feladatok ablaktáblára. (Ha bezárta a panelt, `asa_frauddetection_job_demo` keresse meg a **minden erőforrás** ablaktáblán.)

3. A **feladatok topológiája** szakaszban kattintson a **kimenet** mezőre.

4. A **kimenetek** ablaktáblán kattintson a **Hozzáadás** elemre, és válassza a **blob Storage**elemet. Ezután töltse ki az új kimeneti oldalt az alábbi adatokkal:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Kimeneti alias  |  CallStream-FraudulentCalls   |  Adja meg a feladat kimenetének azonosító nevét.   |
   |Subscription   |  \<Az Ön előfizetése\> |  Válassza ki azt az Azure-előfizetést, amelyhez a létrehozott tárfiók tartozik. A tárfiók tartozhat ugyanahhoz az előfizetéshez, de akár egy másik előfizetéshez is. A példa azt feltételezi, hogy a tárfiók ugyanahhoz az előfizetéshez tartozik. |
   |Tárfiók  |  asaehstorage |  Adja meg a létrehozott Storage-fiók nevét. |
   |Tároló  | asa-fraudulentcalls-demo | Válassza az új létrehozása elemet, és adja meg a tároló nevét. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Kattintson a **Save** (Mentés) gombra. 


## <a name="start-the-streaming-analytics-job"></a>A streaming Analytics-feladatok elindítása

A feladatot már konfigurálták. Megadott egy bemenetet (az Event hub-t), egy átalakítást (a hamis hívásokat megkereső lekérdezést), valamint egy kimenetet (blob Storage). Most már elindíthatja a feladatot. 

1. Győződjön meg arról, hogy a TelcoGenerator alkalmazás fut.

2. A feladatok ablaktáblán kattintson a **Start**gombra. A **kezdési feladatok** ablaktáblán, a feladatok kimenetének kezdési ideje beállításnál válassza a **most**lehetőséget. 

   ![A Stream Analytics-feladatok elindítása](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Az átalakított adatelemzések vizsgálata

Most már rendelkezik egy teljes streaming Analytics-feladatokkal. A feladattal a telefonhívási metaadatok streamjét vizsgálja, valós időben keresve a csalárd telefonhívásokat, és adatokat kell írnia a tárolóval kapcsolatos csalárd hívásokról. 

Az oktatóanyag elvégzéséhez érdemes megtekinteni a streaming Analytics-feladatok által rögzített adatok körét. Az adathalmazok (fájlok) az Azure blog Storage-ba kerülnek. Bármilyen eszközt használhat, amely az Azure Blob Storaget olvassa. Ahogy az előfeltételek szakaszban is látható, használhatja az Azure-bővítményeket a Visual Studióban, vagy használhat olyan eszközt, mint a [Azure Storage Explorer](https://storageexplorer.com/) vagy a [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Amikor megvizsgál egy fájl tartalmát a blob Storage-ban, a következőhöz hasonló jelenik meg:

   ![Azure Blob Storage streaming Analytics-kimenettel](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Vannak további cikkek, amelyek továbbra is a csalások észlelésére szolgálnak, és az ebben az oktatóanyagban létrehozott erőforrásokat használják. Ha folytatni szeretné a lépéseket, tekintse meg a **következő lépések**című témakörben található javaslatokat.

Ha azonban elkészült, és nincs szüksége a létrehozott erőforrásokra, törölheti őket, hogy ne kelljen szükségtelen Azure-díjakat fizetnie. Ebben az esetben javasoljuk, hogy tegye a következőket:

1. Állítsa le a streaming Analytics-feladatot. A **feladatok** ablaktáblán kattintson felül a **Leállítás** gombra.
2. Állítsa le a távközlési létrehozó alkalmazást. A parancssorablakban, ahol elindította az alkalmazást, nyomja le a CTRL + C billentyűkombinációt.
3. Ha csak ehhez az oktatóanyaghoz hozott létre egy új blob Storage-fiókot, törölje azt. 
4. Törölje a streaming Analytics-feladatot.
5. Az Event hub törlése.
6. Törölje az Event hub-névteret.

## <a name="get-support"></a>Támogatás kérése

További segítségre van szüksége, próbálja meg a [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

Ezt az oktatóanyagot a következő cikk használatával folytathatja:

* [Stream Analytics és Power BI: Valós idejű elemzési irányítópult a folyamatos átvitelhez](stream-analytics-power-bi-dashboard.md). Ez a cikk bemutatja, hogyan küldheti el a Stream Analytics-feladatok távközlési kimenetét Power BI valós idejű vizualizációk és elemzések céljából.

Az általános Stream Analyticsról a következő cikkekben talál további információt:

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
