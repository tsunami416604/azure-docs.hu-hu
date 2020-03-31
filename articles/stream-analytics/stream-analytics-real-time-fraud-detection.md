---
title: Valós idejű csalásészlelés az Azure Stream Analytics használatával
description: Ismerje meg, hogyan hozhat létre valós idejű csalásészlelési megoldást a Stream Analytics segítségével. Használjon eseményközpontot valós idejű eseményfeldolgozáshoz.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: c0b2943e1f0d7f2386ec09da03d297a570eede7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276478"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Az Azure Stream Analytics használatának első lépései: Valós idejű csalások észlelése

Ez az oktatóanyag az Azure Stream Analytics használatának végpontok között történő szemléltetése. Az alábbiak végrehajtásának módját ismerheti meg: 

* Streamelési eseményeket hozhat létre az Azure Event Hubs egy példányába. Ebben az oktatóanyagban egy olyan alkalmazást fog használni, amely a mobiltelefon metaadatrekordjainak adatfolyamát szimulálja.

* SQL-szerű Stream Analytics-lekérdezéseket írhat az adatok átalakításához, az adatok összesítéséhez vagy a minták kereséséhez. Látni fogja, hogyan használhat lekérdezést a bejövő adatfolyam vizsgálatához, és megkeresi az esetleg csalárd hívásokat.

* Küldje el az eredményeket egy kimeneti fogadóba (tárolóba), amely további elemzéseket elemezhet. Ebben az esetben elküldi a gyanús hívási adatokat az Azure Blob storage-ba.

Ez az oktatóanyag a telefonos hívások adatai alapján valós idejű csalások észlelésének példáját használja. Az illusztrált technika más típusú csalások felderítésére is alkalmas, mint például a hitelkártya-csalások vagy a személyazonosság-lopás. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Forgatókönyv: A távközlés és a SIM-kártyás csalások valós idejű észlelése

A távközlési vállalat nagy mennyiségű adattal rendelkezik a bejövő hívásokhoz. A vállalat valós időben szeretné észlelni a csalárd hívásokat, hogy értesíthessék az ügyfeleket, vagy leállíthassák a szolgáltatást egy adott számra vonatkozóan. A SIM-kártyás csalások egyik típusa több, azonos személyazonosságú hívással jár, körülbelül ugyanabban az időben, de földrajzilag különböző helyeken. Az ilyen típusú csalások észleléséhez a vállalatnak meg kell vizsgálnia a bejövő telefonhívások adatait, és meg kell keresnie a konkrét mintákat – ebben az esetben a különböző országokban/régiókban egyidejűleg kezdeményezett hívásokesetében. Az ebbe a kategóriába tartozó telefonrekordokat a rendszer a későbbi elemzéshez a tárolóba írja.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a telefonhívási adatokat egy olyan ügyfélalkalmazás használatával szimulálhatja, amely minta telefonhívás-metaadatokat hoz létre. Az alkalmazás által gyártott rekordok egy része csalárd hívásoknak tűnik. 

Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Egy Azure-fiók.
* A Call-event generator alkalmazás, [a TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), amely letölthető a Microsoft letöltőközpontjából. Csomagolja ki a csomagot a számítógép egyik mappájába. Ha meg szeretné tekinteni a forráskódot, és futtatni szeretné az alkalmazást egy hibakeresőben, beszerezheti az alkalmazás forráskódját a [GitHubról.](https://aka.ms/azure-stream-analytics-telcogenerator) 

    >[!NOTE]
    >Előfordulhat, hogy a Windows blokkolja a letöltött .zip fájlt. Ha nem tudja kicsomagolni, kattintson a jobb gombbal a fájlra, és válassza a **Tulajdonságok parancsot.** Ha a "Ez a fájl egy másik számítógépről származik, és a számítógép védelme érdekében le van tiltva" üzenet jelenik meg, jelölje be a **Letiltás feloldása** lehetőséget, majd kattintson az **Alkalmaz gombra.**

Ha meg szeretné vizsgálni a Streaming Analytics-feladat eredményeit, egy Azure Blob Storage-tároló tartalmának megtekintéséhez is szüksége van egy eszközre. A Visual Studio használata esetén használhatja [az Azure Tools for Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) vagy a Visual Studio Cloud Explorer [alkalmazást.](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) Másik lehetőségként telepítheti az önálló eszközöket, például [az Azure Storage Explorert](https://storageexplorer.com/) vagy [a Ceruleant.](https://www.cerebrata.com/products/cerulean/features/azure-storage) 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Hozzon létre egy Azure Event Hubs betöltési események

Az adatfolyam elemzéséhez *bekell dolgoznia* az Azure-ba. Az adatok betöltésének tipikus módja az [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)használata, amely lehetővé teszi több millió esemény másodpercenkénti betöltését, majd az eseményadatok feldolgozását és tárolását. Ebben az oktatóanyagban hozzon létre egy eseményközpontot, majd a hívásesemény-generátor alkalmazás hívásadatokat küld jön az adott eseményközpontba. Az eseményközpontokról az [Azure Service Bus dokumentációjában](https://docs.microsoft.com/azure/service-bus/)olvashat bővebben.

>[!NOTE]
>Az eljárás részletesebb verziójáért olvassa el az [Event Hubs névtér létrehozása és egy eseményközpont létrehozása az Azure Portal használatával című témakört.](../event-hubs/event-hubs-create.md) 

### <a name="create-a-namespace-and-event-hub"></a>Névtér és eseményközpont létrehozása
Ebben az eljárásban először hozzon létre egy eseményközpont-névteret, majd adjon hozzá egy eseményközpontot a névtérhez. Az eseményközpont-névterek a kapcsolódó eseménybusz-példányok logikai csoportosítására szolgálnak. 

1. Jelentkezzen be az Azure Portalon, és kattintson az **erőforrás létrehozása** a képernyő bal felső részén.

2. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** és válassza a **csillag (`*`)** lehetőséget az **Eseményközpontok** elem mellett az **Analytics** kategóriában. Ellenőrizze, hogy az **Event Hubs** hozzá lett-e adva a **KEDVENCEK mappába** a bal oldali navigációs menüben. 

   ![Eseményközpontok keresése](./media/stream-analytics-real-time-fraud-detection/select-event-hubs-menu.png)

3. A bal oldali navigációs menüben válassza a **KEDVENCEK** csoport **Eseményközpontok parancsát,** majd az eszköztáron a **Hozzáadás** parancsot.

   ![Hozzáadás gomb](./media/stream-analytics-real-time-fraud-detection/event-hubs-add-toolbar.png)

4. A **Névtér létrehozása** ablaktáblán adjon meg `<yourname>-eh-ns-demo`egy névtérnevet, például . A névtérhez bármilyen nevet használhat, de a névnek érvényesnek kell lennie egy URL-címhez, és egyedinek kell lennie az Azure-ban. 
    
5. Jelöljön ki egy előfizetést, hozzon létre vagy válasszon erőforráscsoportot, majd kattintson a **Létrehozás gombra.**

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

6. Ha a névtér telepítése befejeződött, keresse meg az eseményközpont névterét az Azure-erőforrások listájában. 

7. Kattintson az új névtérre, majd a névtér ablaktábláján kattintson az **Eseményközpont**elemre.

   ![Az Eseményközpont hozzáadása gomb új eseményközpont létrehozásához](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
8. Nevezze el az `asa-eh-frauddetection-demo`új eseményközpontot . Más nevet is használhat. Ha igen, jegyezze fel, mert később szüksége lesz a névre. Nem kell, hogy más beállításokat az eseményközpont most.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
9. Kattintson **a Létrehozás gombra.**

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Az eseményközponthoz való hozzáférés engedélyezése és kapcsolati sztring beszerzése

Ahhoz, hogy egy folyamat adatokat küldhessen egy eseményközpontba, az eseményközpontnak rendelkeznie kell egy megfelelő hozzáférést lehetővé. A hozzáférési szabályzat egy kapcsolati sztringet hoz létre, amelyben megtalálhatók az engedélyezési információk.

1. Az eseménynévtér ablaktáblájában kattintson az **Eseményközpontok** elemre, majd az új eseményközpont nevére.

2. Az eseményközpont ablaktábláján kattintson a **Megosztott hozzáférési házirendek** elemre, majd a ** + &nbsp;Hozzáadás**gombra.

    > [!NOTE]
    > Győződjön meg arról, hogy az eseményközponttal dolgozik, nem az eseményközpont névterével.

3. Adjon hozzá `asa-policy-manage-demo` egy nevű házirendet, és a **Jogcím**csoporthoz válassza **a Kezelés lehetőséget.**

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4. Kattintson **a Létrehozás gombra.**

5. A házirend üzembe helyezése után kattintson rá a megosztott hozzáférési házirendek listájában.

6. Keresse meg a **CONNECTION STRING-PRIMARY KEY** feliratú mezőt, és kattintson a kapcsolati karakterlánc melletti másolásgombra. 

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7. Illessze be a kapcsolati sztringet egy szövegszerkesztőbe. Szüksége van erre a kapcsolati karakterláncra a következő szakaszhoz, miután néhány kisebb szerkesztést végzett rajta.

    A kapcsolati karakterlánc így néz ki:

    `Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo`

    Figyelje meg, hogy a kapcsolati karakterlánc több kulcs-érték `Endpoint` `SharedAccessKeyName`párt `SharedAccessKey`tartalmaz, pontosvesszővel elválasztva: , , , és `EntityPath`.  


## <a name="configure-and-start-the-event-generator-application"></a>Az eseménygenerátor-alkalmazás konfigurálása és indítása

A TelcoGenerator alkalmazás indítása előtt konfigurálnia kell azt, hogy hívásrekordokat küldjön a létrehozott eseményközpontnak.

### <a name="configure-the-telcogenerator-app"></a>A TelcoGenerator alkalmazás konfigurálása

1. Abban a szerkesztőben, ahová a kapcsolati `EntityPath` karakterláncot másolta, jegyezze fel az értéket, majd távolítsa el a `EntityPath` párt (ne felejtse el eltávolítani az azt megelőző pontosvesszőt). 

2. Abban a mappában, ahol kicsomagolta a TelcoGenerator.zip fájlt, nyissa meg a telcodatagen.exe.config fájlt egy szerkesztőben. (Több .config fájl is van, ezért győződjön meg róla, hogy a megfelelőfájlt nyitja meg.)

3. Az `<appSettings>` elemben:

   * Állítsa be a `EventHubName` kulcs értékét az eseményközpont nevéhez (azaz az entitás elérési útjának értékéhez).
   * Állítsa be a `Microsoft.ServiceBus.ConnectionString` kulcs értékét a kapcsolati karakterlánchoz. 

   A `<appSettings>` szakasz a következő példához hasonlóan fog kinézni:

    ```xml
    <appSettings>
     <!-- Service Bus specific app setings for messaging connections -->
     <add key="EventHubName" value="asa-eh-ns-demo"/>
     <add key="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://asa-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=GEcnTKf2//1MRn6SN1A2u0O76MP9pj3v0Ccyf1su4Zo="/>
   </appSettings>
    ```
 
4. Mentse a fájlt. 

### <a name="start-the-app"></a>Az alkalmazás indítása

1. Nyisson meg egy parancsablakot, és váltson arra a mappára, ahol a TelcoGenerator alkalmazás kivan csomagolva.

2. Írja be a következő parancsot:

    ```console
    telcodatagen.exe 1000 0.2 2
    ```

   A paraméterek a következők: 

   * A CDR-ek száma óránként. 
   * SIM-kártya csalásának valószínűsége: Milyen gyakran, az összes hívás százalékában, hogy az alkalmazás szimulálja a csalárd hívást. A 0,2-es érték azt jelenti, hogy nagyjából a hívások 20%-a fog csalónak tűnni.
   * Időtartam órákban. Az alkalmazás futtatásának óráinak száma. Az alkalmazást bármikor leállíthatja, ha a ctrl+C billentyűkombinációt nyomja meg a parancssorból.

   Néhány másodperc elteltével az alkalmazás elkezdi kijelezni a hívásrekordokat a képernyőn, miközben az eseményközpontba küldi őket.

A valós idejű csalásészlelési alkalmazásban használt kulcsmezők közül néhány a következő:

|**Record**|**Meghatározás**|
|----------|--------------|
|`CallrecTime`|A hívási kezdési idejét jelölő időbélyegző. |
|`SwitchNum`|A hívás csatlakozásához használt telefonkapcsoló. Ebben a példában a kapcsolók olyan karakterláncok, amelyek a származási országot/régiót képviselik (USA, Kína, Egyesült Királyság, Németország vagy Ausztrália). |
|`CallingNum`|A hívó telefonszáma. |
|`CallingIMSI`|Az International Mobile Subscriber Identity (IMSI). Ez a hívó egyedi azonosítója. |
|`CalledNum`|A hívott fél telefonszáma. |
|`CalledIMSI`|International Mobile Subscriber Identity (IMSI). Ez a hívás címzettjének egyedi azonosítója. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Stream Analytics-feladat létrehozása a streamelési adatok kezeléséhez

Most, hogy rendelkezik a hívásesemények adatfolyamával, beállíthat egy Stream Analytics-feladatot. A feladat a beállított eseményközpontból olvassa be az adatokat. 

### <a name="create-the-job"></a>A feladat létrehozása 

1. Az Azure Portalon kattintson az > **Erőforrás-internet-adatforgalom** > **streamanalytics-feladatának** **létrehozása**elemre.

2. Nevezze el `asa_frauddetection_job_demo`a feladatot, adjon meg egy előfizetést, erőforráscsoportot és helyet.

    Érdemes a feladatot és az eseményközpontot ugyanabban a régióban elhelyezni a legjobb teljesítmény érdekében, hogy ne fizessen az adatok régiók közötti átviteléért.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Kattintson **a Létrehozás gombra.**

    A feladat létrejön, és a portál megjeleníti a feladat részleteit. Még semmi sem fut – a feladat indítása előtt konfigurálnia kell a feladatot.

### <a name="configure-job-input"></a>Feladatbemenet konfigurálása

1. Az irányítópulton vagy a **Minden erőforrás** `asa_frauddetection_job_demo` ablaktáblán keresse meg és jelölje ki a Stream Analytics-feladatot. 
2. A Stream Analytics **feladatablakátáttekintő** szakaszban kattintson a **Bevitel mezőre.**

   ![Beviteli mező a Topológia csoportban a Streaming Analytics feladatablakában](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Kattintson **az Adatfolyam-bevitel hozzáadása** gombra, és válassza **az Eseményközpont lehetőséget.** Ezután töltse ki az Új beviteli lapot a következő információkkal:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Bemeneti alias  |  CallStream   |  Adjon meg egy nevet a feladat bevitelének azonosításához.   |
   |Előfizetés   |  \<Az Ön előfizetése\> |  Válassza ki azt az Azure-előfizetést, amely rendelkezik a létrehozott Event Hub használatával.   |
   |Event Hubs-névtér  |  asa-eh-ns-demo |  Adja meg az Event Hub névtér nevét.   |
   |Eseményközpont neve  | asa-eh-csalásdetection-demo | Válassza ki az Event Hub nevét.   |
   |Eseményközpont szabályzatának neve  | asa-policy-manage-demo | Válassza ki a korábban létrehozott hozzáférési szabályzatot.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Kattintson **a Létrehozás gombra.**

## <a name="create-queries-to-transform-real-time-data"></a>Lekérdezések létrehozása valós idejű adatok átalakításához

Ezen a ponton van egy Stream Analytics-feladat beállítása a bejövő adatfolyam olvasására. A következő lépés egy olyan lekérdezés létrehozása, amely valós időben elemzi az adatokat. A Stream Analytics egy egyszerű, deklaratív lekérdezési modellt támogat, amely a valós idejű feldolgozás transzformációit ismerteti. A lekérdezések egy SQL-szerű nyelvet használnak, amely rendelkezik a Stream Analytics néhány bővítményével. 

Egy egyszerű lekérdezés előfordulhat, hogy csak olvasni az összes bejövő adatokat. Gyakran azonban olyan lekérdezéseket hoz létre, amelyek konkrét adatokat vagy az adatokban lévő kapcsolatokat keresnek. Ebben a szakaszban az oktatóanyag, hozzon létre, és tesztelje a több lekérdezést, hogy megtanulják, hogyan lehet átalakítani a bemeneti adatfolyam elemzésre. 

Az itt létrehozott lekérdezések csak az átalakított adatokat jelenítik meg a képernyőn. Egy későbbi szakaszban egy kimeneti fogadót és egy lekérdezést konfigurál, amely az átalakított adatokat az adott fogadóba írja.

Ha többet szeretne megtudni a nyelvről, olvassa el az [Azure Stream Analytics lekérdezési nyelvének útmutatója című témakört.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

### <a name="get-sample-data-for-testing-queries"></a>Mintaadatok bekésezése lekérdezések teszteléséhez

A TelcoGenerator alkalmazás hívásrekordokat küld az eseményközpontba, és a Stream Analytics-feladat úgy van beállítva, hogy az eseményközpontból olvasson. A feladat teszteléséhez lekérdezéssel ellenőrizheti, hogy megfelelően olvassa-e. Egy lekérdezés teszteléséhez az Azure konzolon, mintaadatokra van szüksége. Ebben a forgatókönyvben kinyeri a mintaadatokat az eseményközpontba érkező adatfolyamból.

1. Győződjön meg arról, hogy a TelcoGenerator alkalmazás fut, és hívásrekordokat készít.
2. A portálon térjen vissza a Streaming Analytics feladatablaktáblához. (Ha bezárta az ablaktáblát, keressen `asa_frauddetection_job_demo` rá a Minden **erőforrás** ablaktáblán.)
3. Kattintson a **Lekérdezés** mezőre. Az Azure felsorolja a bemenetek és kimenetek, amelyek a feladathoz konfigurálva vannak, és lehetővé teszi, hogy hozzon létre egy lekérdezést, amely lehetővé teszi a bemeneti adatfolyam átalakítását, ahogy az a kimenetre.
4. A **Lekérdezés** ablaktáblán kattintson a `CallStream` bemenet melletti poklokra, majd válassza **a Mintaadatok a bemenetből lehetőséget.**

   ![Menübeállítások a mintaadatok használatára a Streaming Analytics feladatbejegyzéshez, a "Mintaadatok bemenetből" lehetőség kiválasztásával](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Állítsa a **Perc 3-at** 3-ra, majd kattintson **az OK**gombra. 
    
   ![A mintavételi adatfolyam mintavételezési lehetőségei 3 perc alatt kiválasztva](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Az Azure mintát 3 perc nyi adat a bemeneti adatfolyamból, és értesíti Önt, ha a mintaadatok készen áll. (Ez rövid időt vesz igénybe.) 

A rendszer ideiglenesen tárolja a mintaadatokat, amelyek akkor érhetők el, amikor meg van nyitva a lekérdezési ablak. Ha bezárja a lekérdezési ablakot, a mintaadatok elvesznek, és új mintaadatkészletet kell létrehoznia. 

Alternatív megoldásként beszerezhet egy .json fájlt, amely mintaadatokat tartalmazó [a GitHubról,](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)majd feltöltheti `CallStream` ezt a .json fájlt a bemenetminta-adatként való használatra. 

### <a name="test-using-a-pass-through-query"></a>Tesztelés átamenő lekérdezéssel

Ha minden eseményt archiválni szeretne, átamenő lekérdezéssel elolvashatja az esemény hasznos adatának összes mezőjét.

1. A lekérdezésablakban adja meg a következő lekérdezést:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Az SQL-hez hasonlóan a kulcsszavak nem érzékenyek a kis- és nagybetűkre, és a szóközök sem jelentősek.

    Ebben a `CallStream` lekérdezésben az alias, amelyet a bemenet létrehozásakor megadott. Ha más aliast használt, használja inkább ezt a nevet.

2. Kattintson a **Teszt gombra.**

    A Stream Analytics-feladat futtatja a lekérdezést a mintaadatokkal, és megjeleníti a kimenetet az ablak alján. Az eredmények azt mutatják, hogy az Event Hub és a Streaming Analytics feladat megfelelően van konfigurálva. (Mint megjegyezte, később létrehoz egy kimeneti fogadó, amely a lekérdezés képes adatokat írni.)

   ![Stream Analytics-feladat kimenete, amely 73 létrehozott rekordot jelenít meg](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    A látott rekordok pontos száma attól függ, hogy hány rekordot rögzített a 3 perces minta.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>A mezők számának csökkentése oszlopvetület használatával

Sok esetben az elemzés nem kell az összes oszlopot a bemeneti stream. A lekérdezéssel kisebb visszaadott mezőket vetíthet ki, mint az átadó lekérdezésben.

1. Módosítsa a kódszerkesztőlekérdezését a következőkre:

    ```SQL
    SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
    FROM 
        CallStream
    ```

2. Kattintson ismét **a Teszt** gombra. 

   ![A Stream Analytics-projekt kimenete 25 rekordot mutat](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Bejövő hívások száma régiónként: Aggregációval rendelkező ablak bukdácsolása

Tegyük fel, hogy régiónként meg szeretné számolni a bejövő hívások számát. A streamelési adatokban, ha azt szeretné, hogy az összesített függvények, mint a számlálás, a szegmens az adatfolyam időbeli egységek (mivel az adatfolyam maga gyakorlatilag végtelen). Ezt a Streaming Analytics [ablakfunkcióval](stream-analytics-window-functions.md)teszi. Ezután egységként dolgozhat az ablakon belüli adatokkal.

Ehhez az átalakításhoz olyan időbeli ablakok sorozatát szeretné ellátni, amelyek nem fedik át egymást – minden ablak különálló, csoportosítható és összesíthető adathalmazt fog rendelkezni. Az ilyen típusú ablakot *tumbling ablaknak*nevezzük. A Tumbling ablakban a bejövő hívások számát a , `SwitchNum`a csoportba csoportosítva kaphatja meg, amely azt az országot/régiót jelöli, ahonnan a hívás származik. 

1. Módosítsa a kódszerkesztőlekérdezését a következőkre:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Ez a `Timestamp By` lekérdezés a `FROM` záradékban szereplő kulcsszó segítségével határozza meg, hogy a bemeneti adatfolyam melyik időbélyegmezőjét használja a Tumbling ablak definiálására. Ebben az esetben az ablak az adatokat `CallRecTime` az egyes rekordok mezője szerint szegmensekre osztja. (Ha nincs megadva mező, az ablakos művelet azt az időt használja, amikor az egyes események megérkeznek az eseményközpontba. Lásd az "Érkezési idő vs alkalmazás idő" a [Stream Analytics lekérdezési nyelvi referencia](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). 

    A vetítés `System.Timestamp`tartalmazza a , amely visszaadja az egyes ablakok végének időbélyegét. 

    Ha meg szeretné adni, hogy tumbling ablakot kíván használni, `GROUP BY` használja a [tumblingwindow](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) függvényt a záradékban. A függvényben meg kell adni egy időegységet (egy mikroszekundumtól egy napig) és egy ablakméretet (hány egységet). Ebben a példában a Tumbling ablak 5 másodperces időközönként áll, így országonként/régiónként i.

2. Kattintson ismét **a Teszt** gombra. Az eredmények ben figyelje meg, hogy a **WindowEnd** alatti időbélyegek 5 másodperces lépésekben vannak.

   ![Stream Analytics-feladat kimenete 13 rekordot megjelenítő összesítéshez](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Sim-csalások felderítése önillesztés sel

Ebben a példában a csalárd használatot olyan hívásoknak tekintheti, amelyek ugyanattól a felhasználótól, de egymástól 5 másodpercen belül különböző helyeken történnek. Például ugyanaz a felhasználó nem indíthat szabályosan hívásokat egyszerre az USA-ból és Ausztráliából. 

Ezeknek az eseteknek a ellenőrzéséhez használhatja a streamelési adatok önillesztését, hogy az `CallRecTime` érték alapján önmagához csatlakozzon. Ezután megkeresheti azokat `CallingIMSI` a hívásrekordokat, ahol az érték `SwitchNum` (az eredeti szám) megegyezik, de az érték (származási ország/régió) nem azonos.

Ha streamelési adatokkal csatlakozik, az illesztésnek meg kell adnia bizonyos korlátokat arra vonatkozóan, hogy az egyező sorok mennyi ideig választhatók el egymástól. (Amint azt korábban megjegyeztük, a streamelési adatok gyakorlatilag végtelenek.) A kapcsolat időhatárait az illesztés `ON` záradékában, a `DATEDIFF` függvény használatával adják meg. Ebben az esetben az illesztés a hívási adatok 5 másodperces időközén alapul.

1. Módosítsa a kódszerkesztőlekérdezését a következőkre: 

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

    Ez a lekérdezés olyan, mint `DATEDIFF` bármely SQL-illesztés, kivéve az illesztésben lévő függvényt. Ez a `DATEDIFF` verzió a streaming analytics, és `ON...BETWEEN` meg kell jelennie a záradékban. A paraméterek egy időegység (másodperc ebben a példában) és az illesztés két forrásának aliasai. Ez eltér a szabványos `DATEDIFF` SQL függvénytől.

    A `WHERE` záradék tartalmazza azt a feltételt, hogy a csalárd hívást megjelöli: az eredeti kapcsolók nem azonosak. 

2. Kattintson ismét **a Teszt** gombra. 

   ![Stream Analytics-feladat kimenete önillesztéshez, amely 6 létrehozott rekordot jelenít meg](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Kattintson a **Mentés** gombra az önillesztéses lekérdezés mentéséhez a Streaming Analytics feladat részeként. (Nem menti a mintaadatokat.)

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Kimeneti fogadó létrehozása az átalakított adatok tárolásához

Megadott egy eseményfolyamot, egy eseményközpont-bemenetet az események betöltéséhez, és egy lekérdezést az adatfolyamon keresztüli átalakításhoz. Az utolsó lépés egy kimeneti fogadó meghatározása a feladathoz, azaz egy hely, ahová az átalakított adatfolyamot írhatja. 

Számos erőforrást használhat kimeneti fogadóként – SQL Server-adatbázist, táblatárolást, Data Lake-tárolót, Power BI-t és még egy másik eseményközpontot. Ebben az oktatóanyagban az adatfolyamot az Azure Blob Storage-ba fogja írni, amely egy tipikus választás eseményadatok későbbi elemzéshez való gyűjtésére, mivel a strukturálatlan adatokat fogadja el.

Ha rendelkezik egy meglévő blob storage-fiókkal, használhatja azt. Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre új tárfiókot.

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob Storage-fiók létrehozása

1. Az Azure Portal bal felső sarkában válassza az Erőforrás > **tárterület-fiók****Storage** >  **létrehozása lehetőséget.** Töltse ki a storage-fiók feladat lap **neve** beállítása "asaehstorage", **Hely** beállítása "USA keleti része", **Erőforrás csoport** beállítása "asa-eh-ns-rg" (a tárfiók ugyanabban az erőforráscsoportban, mint a streamelési feladat a nagyobb teljesítmény érdekében). A fennmaradó beállításokat alapértelmezett értéken hagyhatja.  

   ![Tárfiók létrehozása az Azure Portalon](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Az Azure Portalon térjen vissza a Streaming Analytics feladatablaktáblához. (Ha bezárta az ablaktáblát, keressen `asa_frauddetection_job_demo` rá a Minden **erőforrás** ablaktáblán.)

3. A **Feladattopológia csoportban** kattintson a **Kimenet** mezőre.

4. A **Kimenetek** ablaktáblán kattintson a **Hozzáadás** gombra, és válassza a **Blob Storage lehetőséget.** Ezután töltse ki az Új kimenet ilapot a következő információkkal:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Kimeneti alias  |  CallStream-csalárd hívások   |  Adjon meg egy nevet a feladat kimenetének azonosításához.   |
   |Előfizetés   |  \<Az Ön előfizetése\> |  Válassza ki azt az Azure-előfizetést, amelyhez a létrehozott tárfiók tartozik. A tárfiók tartozhat ugyanahhoz az előfizetéshez, de akár egy másik előfizetéshez is. A példa azt feltételezi, hogy a tárfiók ugyanahhoz az előfizetéshez tartozik. |
   |Tárfiók  |  asaehstorage között |  Adja meg a létrehozott tárfiók nevét. |
   |Tároló  | asa-fraudulentcalls-demo | Válassza az Új létrehozása lehetőséget, és adjon meg egy tárolónevet. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Kattintson a **Mentés** gombra. 


## <a name="start-the-streaming-analytics-job"></a>A Streaming Analytics feladat indítása

A feladat most konfigurálva van. Megadott egy bemenetet (az eseményközpontot), egy átalakítást (a csalárd hívásokat keresőket) és egy kimenetet (blob storage). Most már elkezdheti a munkát. 

1. Ellenőrizze, hogy fut-e a TelcoGenerator alkalmazás.

2. A feladat ablaktáblán kattintson a **Start**gombra. A **Feladat indítása** ablaktáblán a Feladat kimeneti kezdési időpontja esetén válassza **a Now (Most) lehetőséget.** 

   ![A Stream Analytics feladat indítása](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Az átalakított adatok vizsgálata

Most már teljes Streaming Analytics-feladatkal rendelkezik. A feladat a telefonhívások metaadatainak vizsgálata, a csalárd telefonhívások valós idejű keresésével, valamint a tárhelyre irányuló csalárd hívásokadatainak írásával. 

Az oktatóanyag befejezéséhez érdemes megtekinteni a Streaming Analytics feladat által rögzített adatokat. Az adatok írása folyamatban van az Azure Blog Storage adattömbökben (fájlokban). Bármilyen eszközt használhat, amely az Azure Blob Storage-t olvassa. Amint azt az Előfeltételek szakaszban megjegyeztük, használhatja az Azure-bővítményeket a Visual Studio-ban, vagy használhat egy olyan eszközt, mint [az Azure Storage Explorer](https://storageexplorer.com/) vagy a [Cerulean.](https://www.cerebrata.com/products/cerulean/features/azure-storage) 

Amikor megvizsgálja egy fájl tartalmát a blob storage-ban, a következőhez hasonló tetsző ek jelennek meg:

   ![Azure blobstorage streaming analytics-kimenettel](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Vannak további cikkek, amelyek továbbra is a csalás észlelési forgatókönyv, és használja az ebben az oktatóanyagban létrehozott erőforrásokat. Ha folytatni szeretné, olvassa el a **következő lépések**című témakörben található javaslatokat.

Ha azonban végzett, és nincs szüksége a létrehozott erőforrásokra, törölheti őket, hogy ne merüljön fel szükségtelen Azure-díjak. Ebben az esetben javasoljuk, hogy tegye a következőket:

1. Állítsa le a Streaming Analytics-feladatot. A **Feladatok** ablaktáblán kattintson a **leállítás** gombra a tetején.
2. Állítsa le a Telco Generator alkalmazást. Abban a parancsablakban, ahol elindította az alkalmazást, nyomja le a Ctrl+C billentyűkombinációt.
3. Ha létrehozott egy új blob storage-fiókot csak ebben az oktatóanyagban, törölje azt. 
4. Törölje a Streaming Analytics feladatot.
5. Törölje az eseményközpontot.
6. Törölje az eseményközpont névtere.

## <a name="get-support"></a>Támogatás kérése

További segítségért próbálja ki az [Azure Stream Analytics fórumot.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések

Ezt az oktatóanyagot a következő cikkel folytathatja:

* [Stream Analytics és Power BI: Valós idejű elemzési irányítópult az adatfolyam-továbbításhoz.](stream-analytics-power-bi-dashboard.md) Ez a cikk bemutatja, hogyan küldheti el a Stream Analytics-feladat TelCo kimenetét a Power BI-nak valós idejű vizualizációés elemzés hez.

A Stream Analytics szolgáltatásról általában az alábbi cikkekben talál további információt:

* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
