---
title: 'A Stream Analytics: Valós idejű csalások felderítéséhez |} Microsoft Docs'
description: Útmutató a valós idejű csalások észlelése megoldás létrehozása a Stream Analytics. Egy eseményközpont használható a valós idejű esemény feldolgozása.
keywords: anomáliadetektálás, a csalások felderítéséhez, a valós idejű anomáliadetektálás
services: stream-analytics
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: c10dd53f-d17a-4268-a561-cb500a8c04eb
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: sngun
ms.openlocfilehash: a622b7851f52f65efe4450191c2cf65e73e816bc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Az Azure Stream Analytics első lépéseiben: valós idejű csalások felderítése

Ez az oktatóanyag illusztrálja végpont Azure Stream Analytics segítségével. Az alábbiak végrehajtásának módját ismerheti meg: 

* Kapcsolja a streaming eseményeket az Azure Event Hubs egy példányát. Ebben az oktatóanyagban egy alkalmazást, amely azt adja meg, amely a mobiltelefon metaadat-bejegyzéseket adatfolyam szimulálja fogja használni.

* Adatok, szoftverfelügyeleti, vagy csak a következő minták Stream Analytics SQL-szerű lekérdezéseket írhat. Egy lekérdezés segítségével megvizsgálja a bejövő streamből, és keresse meg, előfordulhat, hogy rosszindulatúak hívások jelenik meg.

* Az eredményeket elküldik kimeneti fogadóként (tárolás), amely további betekintést nyerjen elemezheti. Ebben az esetben a gyanús hívás adatokat fog küldeni Azure Blob Storage tárolóban.

Ebben az oktatóanyagban a telefonhívás-adatok alapján valós idejű csalások felderítéséhez példa használjuk. De az eljárás azt mutatja be olyan is alkalmas más típusú csalások felderítéséhez, például a hitelkártya csalás vagy azonosító adatokkal való visszaéléseket. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Forgatókönyv: Távközlési és SIM csalások felderítéséhez valós időben

A távközlési vállalat rendelkezik nagy mennyiségű bejövő hívások adatait. A vállalat szeretne csalárd hívások valós idejű észlelheti az, hogy a felhasználók értesítése, vagy állítsa le az adott szolgáltatást. Egy adott típusú SIM csalás ugyanazzal az identitással körülbelül egy időben, de a földrajzilag különböző helyeken több hívás magában foglalja. Az ilyen típusú csalások észlelése, a vállalatnak foglalkoznia kell a megvizsgálja a bejövő phone rögzíti, és keresse meg adott minták – ebben az esetben az hívásokra a különböző országokban körülbelül egy időben. A telefon azt jelzi, hogy ebbe a kategóriába tartozik kerülnek tárolási további elemzés céljából.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a telefonhívás-adatok szimulálása lesz egy ügyfél-alkalmazással, amely mintát telefonhívás metaadatokat hoz létre. Néhány rekordot, amely az alkalmazás létrehozza a csalárd hívások tűnik. 

Mielőtt elkezdené, győződjön meg arról, hogy a következő:

* Egy Azure-fiók.
* A hívás-esemény készítő alkalmazást. Ez a letöltés kaphat a [TelcoGenerator.zip fájl](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) a Microsoft Download Center webhelyről. Bontsa ki a csomagot egy mappába a számítógépen. Ha meg szeretné tekinteni a forrás-kód és az alkalmazás futtatásához a hibakereső, az alkalmazás forráskódjának kaphat [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Windows blokkolhatják a letöltött zip-fájlt. Ha Ön nem bontsa ki azt, kattintson jobb gombbal a fájlra, és válassza ki **tulajdonságok**. Ha a "ezt a fájlt egy másik számítógépről érkezett és a számítógép védelme érdekében blokkolhatja" üzenet jelenik meg, válassza ki a **Unblock** lehetőséget, majd kattintson a **alkalmaz**.

Ha meg szeretné vizsgálni a Streaming Analytics-feladat eredményének, is szüksége egy eszközt az Azure Blob Storage-tároló tartalmának megtekintése. Ha a Visual Studio használata esetén használhatja [Azure Tools for Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) vagy [Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Másik lehetőségként telepítheti önálló eszközök, például a [Azure Tártallózó](http://storageexplorer.com/) vagy [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Létrehozása az Azure event hubs betöltési események

Adatfolyam elemzéséhez, *betöltési* Azure be azt. Egy tipikus módja adatok [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md), amely lehetővé teszi több millió eseményt másodpercenként és majd feldolgozni, és az eseményadatok tárolására. Ebben az oktatóanyagban létrehoz egy eseményközpontot, és már rendelkezik a hívás adatokat küldeni, hogy az event hubs hívás-esemény készítő alkalmazást. Az event hubs kapcsolatban bővebben lásd: a [Azure Service Bus-dokumentáció](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Ez az eljárás részletes verziójához, lásd: [hozzon létre egy Event Hubs névtér és egy eseményközpontot, az Azure portál használatával](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>A névtér és az event hub létrehozása
Ebben az eljárásban először létre kell hoznia egy event hub névtér, és ezután hozzáadhat új eseményközpont tartalmazni. Event hub névterek logikailag csoportosítják a kapcsolódó esemény bus példányok szolgálnak. 

1. Jelentkezzen be az Azure-portálon, majd kattintson a **hozzon létre egy erőforrást** > **az eszközök internetes hálózatát** > **Eseményközpont**. 

2. Az a **névtér létrehozása** panelen adja meg például a névtér nevét `<yourname>-eh-ns-demo`. A névtér egy tetszőleges nevet is használhat, de a neve nem érvényes URL- és Azure között egyedinek kell lennie. 
    
3. Válasszon egy előfizetést, és hozzon létre vagy válasszon egy erőforráscsoportot, majd kattintson **létrehozása**. 

    ![Az event hub névtér létrehozása](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png)
 
4. A névtér telepítése befejeződött, amikor az event hub névtér található az Azure-erőforrások listája. 

5. Kattintson az új névtéren, és a névtér ablaktáblán kattintson **Eseményközpont**.

    ![Az Eseményközpont hozzáadása gombra egy új eseményközpont létrehozása ](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Az új eseményközpont neve `sa-eh-frauddetection-demo`. Használhat egy másik nevet. Ha így tesz, jegyezze fel, mert később szüksége nevét. Bármely más vonatkozó beállítások megadása az event hubs most nem kell.

    ![Egy új eseményközpont létrehozása panel](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png)
    
 
7. Kattintson a **Create** (Létrehozás) gombra.
### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Az event hubs hozzáférést, és a kapcsolati karakterlánc beolvasása

Egy folyamat adatokat küldhet egy eseményközpontot, az event hubs egy házirendet, amely lehetővé teszi, hogy a megfelelő hozzáféréssel kell rendelkeznie. A hozzáférési házirendet hoz létre egy kapcsolati karakterláncot, amely tartalmazza az engedélyezési adatok.

1.  Az esemény névtér ablaktáblán kattintson **Event Hubs** és kattintson az új eseményközpont nevét.

2.  Az event hub ablaktáblán kattintson **megosztott elérési házirendek** majd  **+ &nbsp;Hozzáadás**.

    >[!NOTE]
    >Ellenőrizze, hogy az event hubs, nem az event hub névtér dolgozik.

3.  Nevű házirend hozzáadása `sa-policy-manage-demo` és **jogcím**, jelölje be **kezelése**.

    ![Az új esemény központi hozzáférési házirend létrehozása panel](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png)
 
4.  Kattintson a **Create** (Létrehozás) gombra.

5.  A házirend telepítése után kattintson rá a megosztott elérési házirendek listájában.

6.  A mezőben található **KAPCSOLATI karakterlánc elsődleges kulcs** , majd kattintson a Másolás gombra mellett a kapcsolati karakterláncot. 
    
    ![A hozzáférési házirendben az elsődleges kapcsolódási karakterlánc kulcs másolása](./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png)
 
7.  A kapcsolati karakterlánc illessze be a szövegszerkesztőben. Ez a kapcsolati karakterlánc szükséges ahhoz, hogy a következő szakaszban után néhány kisebb módosításokat végez.

    A kapcsolati karakterlánc így néz ki:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=sa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=sa-eh-frauddetection-demo

    Figyelje meg, hogy a kapcsolati karakterlánc tartalmaz-e több kulcs-érték párok, pontosvesszővel elválasztva: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, és `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Konfigurálni és elindítani az esemény-készítő alkalmazás

Mielőtt elkezdené a TelcoGenerator alkalmazást, akkor konfigurálja úgy, hogy hívás rekordok elküldi a létrehozott eseményközpont.

### <a name="configure-the-telcogeneratorapp"></a>A TelcoGeneratorapp konfigurálása

1.  A szerkesztőben, ahol kimásolt kapcsolati karakterlánc, jegyezze fel a `EntityPath` értékét, és távolítsa el a `EntityPath` pár (ne feledje, az azt megelőző pontosvessző eltávolítása). 

2.  A mappában, amelyben unzipped-e a TelcoGenerator.zip fájlt nyissa meg a telcodatagen.exe.config fájlt valamelyik szerkesztőben. (Nincs több .config kiterjesztésű fájl, ezért ügyeljen arra, hogy nyissa meg a megfelelőt.)

3.  Az a `<appSettings>` elem:

    * Állítsa a `EventHubName` kulcs az eseményközpont neveként (Ez azt jelenti, hogy az entitás elérési út értékre).
    * Állítsa a `Microsoft.ServiceBus.ConnectionString` kulcs a kapcsolati karakterlánc módosításait. 

    A `<appSettings>` szakasz az alábbihoz hasonlóan fog kinézni. (Az átláthatóság érdekében azt becsomagolt a sorokat és néhány karaktert a az engedélyezési jogkivonat.)

    ![Megjeleníti a event hub nevét és a kapcsolati karakterlánc TelcoGenerator app konfigurációs fájl](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4.  Mentse a fájlt. 

### <a name="start-the-app"></a>Indítsa el az alkalmazást
1.  Nyisson meg egy parancsablakot, és módosítsa a mappát, ahol a TelcoGenerator app tömörítetlen.
2.  Írja be a következő parancsot:

        telcodatagen.exe 1000 .2 2

    A paraméterek a következők: 

    * Óránként EK számát. 
    * SIM kártyával csalás valószínűség: Milyen gyakran összes hívást, hogy az alkalmazás kell szimulálása a csalárd hívás százalékában. .2 érték azt jelenti, amely körülbelül 20 %-a hívás rekordok fog kinézni csalárd.
    * Hossza (óra). Az alkalmazás fusson órák száma. Is leállíthatja, az alkalmazás bármikor parancssori Ctrl + C billentyűt lenyomásával.

    Néhány másodpercen belül az alkalmazás indítása telefonhívás rekordjainak megjelenítéséhez a képernyőn, az event hubs küldi azokat.

A valós idejű csalások észlelése alkalmazásban használt kulcsmezők a következő:

|**Record**|**Meghatározása**|
|----------|--------------|
|`CallrecTime`|A hívás időbélyegzőjét kezdési időpontja. |
|`SwitchNum`|A telefon kapcsoló használatával kapcsolódni a hívást. Az ebben a példában a kapcsolók a következők karakterláncok, amelyek megfelelnek a származási (Egyesült Államok, Kína, UK, Németország vagy Ausztrália). |
|`CallingNum`|A hívó telefonszámát. |
|`CallingIMSI`|A nemzetközi mobil előfizető Identity (IMSI). Ez az a hívó egyedi azonosítóját. |
|`CalledNum`|A hívás címzettje telefonszámát. |
|`CalledIMSI`|Nemzetközi mobil előfizető Identity (IMSI). Ez az a hívás címzettje egyedi azonosítóját. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>A streamelési adatok kezelésében a Stream Analytics-feladat létrehozása

Most, hogy egy az események streamjét a hívást, beállíthat egy Stream Analytics-feladat. A feladat adatokat olvasni az Ön által beállított eseményközpontba. 

### <a name="create-the-job"></a>A feladat létrehozása 

1. Az Azure portálon kattintson **hozzon létre egy erőforrást** > **az eszközök internetes hálózatát** > **Stream Analytics-feladat**.

2. A feladat neve `sa_frauddetection_job_demo`, adjon meg egy előfizetési, erőforráscsoportot és helyet.

    Jó ötlet helyezhető el a feladatot, és az event hubs a legjobb teljesítmény érdekében ugyanabban a régióban, és így nem kell fizetnie régiók közötti adattovábbításra.

    ![Új Stream Analytics-feladat létrehozása](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png)

3. Kattintson a **Create** (Létrehozás) gombra.

    A feladat jön létre, és a portál megjeleníti a feladat részleteit. Semmi sem fut-e még, azonban – meg kell adnia a feladat, mielőtt indíthatók el.

### <a name="configure-job-input"></a>Feladat bemeneti konfigurálása

1. Az irányítópult vagy a **összes erőforrás** ablaktáblán, megkeresheti, és válassza ki a `sa_frauddetection_job_demo` Stream Analytics-feladat. 
2. Az a **feladat topológia** szakasz a Stream Analytics-feladat panelen kattintson a **bemeneti** mezőbe.

    ![A topológia beviteli mezőt a Streaming Analytics-feladat panelen](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Kattintson a  **+ &nbsp;Hozzáadás** , és ezután adja meg ezeket az értékeket tartalmazó ablaktábla:

    * **A bemeneti alias**: a nevet használja `CallStream`. Ha más nevet használjon, jegyezze fel a, mert később szüksége.
    * **Adatforrás típusa**: válasszon **adatfolyam**. (**Referenciaadatok** statikus keresési adatokat, amelyeket az oktatóanyag nem használ hivatkozik.)
    * **Forrás**: válasszon **eseményközpont**.
    * **Beállítás importálása**: válasszon **eseményközpont használható a jelenlegi előfizetés**. 
    * **Service bus-névtér**: válassza ki a korábban létrehozott event hub névteret (`<yourname>-eh-ns-demo`).
    * **Az Event hubs**: válassza ki a korábban létrehozott eseményközpont (`sa-eh-frauddetection-demo`).
    * **Eseményközpont házirend neve**: válassza ki a korábban létrehozott házirend (`sa-policy-manage-demo`).

    ![Új bemeneti Streaming Analytics-feladat létrehozása](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png)

4. Kattintson a **Create** (Létrehozás) gombra.

## <a name="create-queries-to-transform-real-time-data"></a>Valós idejű adatok átalakítására lekérdezések létrehozása

Ezen a ponton rendelkezik egy Stream Analytics-feladat bejövő adatfolyam olvasására beállítása. A következő lépés, ha a valós idejű adatok átalakítást. Ehhez létrehozhat egy lekérdezést. A Stream Analytics egy egyszerű, deklaratív lekérdezési modellel, amely leírja a valós idejű feldolgozással átalakítások támogatja. A lekérdezések használata egy SQL-szerű nyelv, amely néhány meghatározott stream Analytics-bővítményt. 

Egy egyszerű lekérdezést csak lehet, hogy a bejövő adatokat olvasni. Azonban Ön gyakran lekérdezéseket hozhat létre keresse meg a meghatározott adatok vagy az adatok kapcsolatok. Az oktatóanyag ezen részében és ezek tesztelésével több lekérdezések megtudhatja, hogyan egy bemeneti adatfolyam elemzéshez alakíthatja át. 

Az itt létrehozott lekérdezések csak megjeleníti az átalakított adatok a képernyőre. Egy későbbi szakasz ismerteti konfigurálhatja a kimeneti fogadóként és egy lekérdezést, amely az átalakított adatokat ír az adott fogadó.

A nyelvi kapcsolatos további tudnivalókért tekintse meg a [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/dn834998.aspx).

### <a name="get-sample-data-for-testing-queries"></a>Tesztelési lekérdezések minta-adatok beolvasása

A TelcoGenerator app hívás rekordot küld az event hubs, és a Stream Analytics-feladat úgy van beállítva, az event hubs olvasni. Egy lekérdezés segítségével győződjön meg arról, hogy helyesen van-e olvasási feladat ellenőrzése. Az Azure-konzolon a lekérdezés teszteléséhez mintaadatokat kell. Ennél a bemutatónál mintaadatok lesz kinyerése az adatforrásból az event hubs az adatfolyam.

1. Győződjön meg arról, hogy a TelcoGenerator alkalmazás fut, és termelő hívást rögzíti.
2. A portálon térjen vissza a Streaming Analytics-feladat panelen. (Ha korábban bezárta a ablaktáblán, keresse meg a `sa_frauddetection_job_demo` a a **összes erőforrás** ablaktáblán.)
3. Kattintson a **lekérdezés** mezőbe. Azure a be- és kimenetekkel, a feladathoz beállított sorolja fel, és lehetővé teszi, hogy hozzon létre egy lekérdezést, amely lehetővé teszi, hogy a bemeneti adatfolyam átalakítása a kimeneti keresztül küldött.
4. Az a **lekérdezés** ablaktáblán kattintson a Tovább gombra a pont a `CallStream` adja meg, és válassza ki **az adatokat a bemeneti**.

    ![A mintaadatokat használja a Streaming Analytics feladat bejegyzést, mintaadatokkal"bemeneti" kiválasztott menüpont](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)

    Ekkor megnyílik egy ablak, amely lehetővé teszi, hogy adja meg, mennyi mintaadatok beszerzéséhez definiált mennyi ideig a bemeneti adatfolyam olvasására.

5. Állítsa be **perc** 3 majd **OK**. 
    
    ![A bemeneti adatfolyam mintavételi a "3 perc" kiválasztott beállítások.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure-minták a bemeneti adatfolyam adatait 3 perc alatt érkezett, és értesíti, amikor készen áll a mintaadatok. (Ez időt vesz igénybe egy rövid ideig.) 

A mintaadatok ideiglenesen tárolja, és áll rendelkezésre, míg a lekérdezési ablakban nyissa meg a rendelkezik. Ha bezárja a lekérdezési ablakban, a minta adatait a rendszer törli, és konfigurálnia kell egy új mintaadatok létrehozása. 

Alternatív megoldásként egy .JSON kiterjesztésű fájlt, amely rendelkezik a mintaadatok azt kaphat [a Githubról](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), majd töltse fel az adott .JSON kiterjesztésű fájlt kívánja használni, mint a mintaadatok az `CallStream` bemeneti. 

### <a name="test-using-a-pass-through-query"></a>Csatlakoztatott lekérdezéssel tesztelése

Ha archiválni szeretné minden esemény, átadó lekérdezés segítségével olvassa el a tartalom az esemény található összes mezőhöz.

1. Adja meg a lekérdezést a lekérdezési ablakban:

        SELECT 
            *
        FROM 
            CallStream

    >[!NOTE]
    >Az SQL kulcsszavak nem kis-és nagybetűket, és szóköz nincs jelentős eltérés.

    Ebben a lekérdezésben `CallStream` a bemeneti létrehozásakor adott aliasként. Ha egy másik aliast, használja az ezen a néven.

2. Kattintson a **teszt**.

    A Stream Analytics-feladat a lekérdezés a mintaadatok fut, és a kimenet megjelenítése a az ablak alján. Ez jelzi, hogy az event hubs és a Streaming Analytics-feladat helyesen vannak konfigurálva. (Amint azt később fog létrehozni a lekérdezés adatokat írhat kimeneti fogadóként.)

    ![Stream Analytics-feladat kimeneti, előállított 73 rekordok megjelenítése](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Látja rekordok teljes száma a rekordok rögzítette a 3 perces minta függ.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Egy oszlop projekció használata mezők számának csökkentése

Sok esetben az elemzési bemeneti adatfolyamból összes oszlop nem szükséges. Egy lekérdezés segítségével meg kevesebb mint a továbbított lekérdezést visszaadott mezők projektre.

1. Módosítsa a lekérdezést a kód szerkesztése a következő:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
        FROM 
            CallStream

2. Kattintson a **teszt** újra. 

    ![Stream Analytics-feladat kimeneti kivetítéshez, 25 rekordot generált megjelenítő](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Count bejövő hívások régió: az összesítő Átfedésmentes ablak

Tegyük fel, hogy régiónként beérkező hívások számát. A streamelési adatok, ha el kíván végezni, például a leltárt, az aggregátumfüggvények kell az adatfolyam szegmentálja a historikus egységekbe (mivel az adatfolyamot magát gyakorlatilag végtelen). Ehhez használja a Streaming Analytics [ablak függvény](stream-analytics-window-functions.md). Majd dolgozhat adott ablakra adatok egységként.

Ez a transzformáció keresi, amely nem fedi át a historikus windows sorozatát – minden ablak adatokat csoportosíthatja, és összesítés diszkrét készlete fog rendelkezni. Az ilyen típusú ablak neve egy *Átfedésmentes ablak* . Az Átfedésmentes ablak belül kaphat szerint csoportosítva a bejövő hívások számát `SwitchNum`, amely az országot, ahol a hívást indító jelenti. 

1. Módosítsa a lekérdezést a kód szerkesztése a következő:

        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Ez a lekérdezés használja a `Timestamp By` kulcsszót a `FROM` mely időbélyegző mező megadása a bemeneti adatfolyam adhat meg az Átfedésmentes ablak a záradékot. Ebben az esetben az ablak szegmensek által az adatok felosztja a `CallRecTime` rekordokban levő mező. (Ha nincs megadva, a leképezési művelet a használja, amely minden esemény érkezik az eseményközpontba. "Érkezési idő Visual Studio alkalmazás idő" című [Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx). 

    A leképezési tartalmaz `System.Timestamp`, az egyes időszak végén az időbélyeg adja vissza, amely. 

    Adja meg, hogy szeretné-e használni a Átfedésmentes ablak, használhatja a [TUMBLINGWINDOW](https://msdn.microsoft.com/library/dn835055.aspx) működni a `GROUP BY `záradékban. A függvény egy időegységet (bárhol az egy napra mikroszekundum) és egy ablak mérete (darabszám) kell megadni. Ebben a példában az Átfedésmentes ablak áll 5 másodperces időközönként, ország száma a hívások minden 5 másodperc alatt érkezett elérhetővé válik.

2. Kattintson a **teszt** újra. Figyelje meg, hogy az eredmények között, az időbélyegek **WindowEnd** 5 másodperces lépésekben vannak.

    ![Stream Analytics-feladat kimeneti összesítési, előállított 13 rekordok megjelenítése](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Önillesztés használatával SIM csalások észlelése

Ebben a példában azt is érdemes megfontolni csalárd használati hívások, amely ugyanahhoz a felhasználóhoz, de különböző helyeken egy másik 5 másodpercen belül kell. Például ugyanaz a felhasználó nem szabályosan hívás az USA és Ausztrália egyszerre. 

Ellenőrizze az ezekben az esetekben, használhatja a streamelési adatok önillesztés önillesztést az adatfolyam alapján a `CallRecTime` érték. Majd keressen a hívás where rögzíti a `CallingIMSI` (származó szám) értéke azonos, de a `SwitchNum` (származási) értéke nem ugyanaz.

Használatakor a csatlakozzon egy adatfolyam-adatok, az illesztés biztosítania kell néhány milyen távolságban egyező sorok vonatkozó korlátozások időben választhatók el egymástól. (Ahogy azt korábban említettük, a streamelési adatok az gyakorlatilag végtelen.) A kapcsolat ideje határain belül vannak megadva a `ON` az illesztési záradék használatával a `DATEDIFF` függvény. Ebben az esetben az illesztés egy 5 másodperces időköze hívás adatok alapján történik.

1. Módosítsa a lekérdezést a kód szerkesztése a következő: 

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

    Ez a lekérdezés olyan, mint bármely SQL illesztési kivételével a `DATEDIFF` az illesztés függvényt. Egy verziója `DATEDIFF` , amely Streaming Analytics, és azt meg kell jelenniük a `ON...BETWEEN` záradékban. Egy időegységet (ebben a példában másodperc) és a csatlakozás a két forrásból aliasok paraméterei. (Ez eltér a szokásos SQL `DATEDIFF` függvény.) 

    A `WHERE` záradék tartalmaz a feltételt, amely a csalárd hívás: a származó kapcsolók a következők nem ugyanaz. 

2. Kattintson a **teszt** újra. 

    ![Stream Analytics-feladat kimeneti generált önillesztés, látható 6 rekordok](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Kattintson a **Save** (Mentés) gombra. Ezzel az önálló join lekérdezés esetén a Streaming Analytics-feladat részeként takaríthat meg. (A mintaadatok nem menti azt.)

    ![A Stream Analytics-feladat mentése](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png)

## <a name="create-an-output-sink-to-store-transformed-data"></a>Hozzon létre egy kimeneti fogadóját átalakított adatok tárolásához

Meghatározta az eseménystream, az eseményközpontok a betöltési események és az adatfolyam keresztül átalakítás végrehajtásához egy lekérdezést adjon meg. Az utolsó lépése az, hogy adja meg a feladat kimeneti fogadóként – Ez azt jelenti, hogy a hely, az átalakított adatfolyam írni. 

Kimeneti mosdók is használhatja sok erőforrást – SQL Server-adatbázis, a table storage, Data Lake-tároló, a Power BI és még egy másik eseményközpont. Ebben az oktatóanyagban az Azure Blob Storage egy tipikus választott eseményadatok újabb elemzés, mivel így alkalmazkodik a strukturálatlan adatok gyűjtéséhez az adatfolyam fog írni.

Ha egy meglévő blob storage-fiókot, használhatja, amely. Ebben az oktatóanyagban mutat be, hogyan hozzon létre egy új tárfiókot, csak a jelen oktatóanyag.

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob Storage-fiók létrehozása

1. Az Azure portálon térjen vissza a Streaming Analytics-feladat panelen. (Ha korábban bezárta a ablaktáblán, keresse meg a `sa_frauddetection_job_demo` a a **összes erőforrás** ablaktáblán.)
2. Az a **feladat topológia** területen kattintson a **kimeneti** mezőbe. 
3. Az a **kimenetek** ablaktáblán kattintson a  **+ &nbsp;Hozzáadás** , és ezután adja meg ezeket az értékeket tartalmazó ablaktábla:

    * **A kimeneti alias**: a nevet használja `CallStream-FraudulentCalls`. 
    * **Gyűjtése**: válasszon **Blob-tároló**.
    * **Importálási lehetőségeit**: válasszon **használja a jelenlegi előfizetés blob-tároló**.
    * **A tárfiók**. Válassza ki **hozzon létre új tárfiókot.**
    * **A tárfiók** (második mezőben). Adja meg `YOURNAMEsademo`, ahol `YOURNAME` a neve, vagy egy másik egyedi karakterlánc. A név csak kisbetűket és számokat használható, és Azure között egyedinek kell lennie. 
    * **Tároló**. Adja meg `sa-fraudulentcalls-demo`.
    A tárfiók nevét és a tároló neve együttesen arra használják, adjon meg egy URI-t a blob-tároló, ehhez hasonló: 

    `http://yournamesademo.blob.core.windows.net/sa-fraudulentcalls-demo/...`
    
    ![Ablaktáblán "Új kimeneti" Stream Analytics-feladat](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png)
    
4. Kattintson a **Create** (Létrehozás) gombra. 

    Azure tárolási fiókot hoz létre, és automatikusan létrehoz egy kulcsot. 

5. Zárja be a **kimenetek** ablaktáblán. 

## <a name="start-the-streaming-analytics-job"></a>A Streaming Analytics-feladat indítása

A feladat konfigurálva van. A megadott bemeneti (az event hubs), átalakítás (a csalárd hívások keres lekérdezés) és egy kimeneti (blob-tároló). Most elindíthatja a feladatot. 

1. Ellenőrizze, fut-e a TelcoGenerator alkalmazást.

2. Kattintson a feladatok ablaktáblában **Start**.

    ![A Stream Analytics-feladat indítása](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start-output.png)

3. Az a **indítási feladat** ablaktáblán, a feladat kimenete kezdési időpontja, jelölje be a **most**. 

4. Kattintson a **Start**. 

    ![A Stream Analytics-feladat "Feladat start" ablak](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start-job-blade.png)

    Azure értesíti, amikor a feladat elindult, és a feladatok ablaktáblán az állapot jelenik meg **futtató**.

    ![Stream Analytics-feladat állapotát, a "Fut" megjelenítése](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-running-status.png)
    

## <a name="examine-the-transformed-data"></a>Vizsgálja meg az átalakított adatok

Most már rendelkezik egy teljes Streaming Analytics-feladathoz. A feladat vizsgálata folyamatban van a telefonhívás metaadatok adatfolyam, valós idejű csalárd telefonhívásokat keres, és ezek tárolási csalárd hívásainak kapcsolatos adatok írásakor. 

Az oktatóanyag elvégzéséhez, érdemes megtekinteni az adatokat a Streaming Analytics-feladat által. Az adattömbök (fájlok) az adatok írása Azure Blog Storage. Bármely Azure Blob Storage olvasó eszközt is használhatja. Amint az Előfeltételek című rész, használhatja a Visual Studio Azure-bővítményeket, vagy használhatja egy eszköz, például [Azure Tártallózó](http://storageexplorer.com/) vagy [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction). 

A blob Storage tárolóban fájl tartalmának vizsgálja meg, lásd a következő hasonlót:

![Az Azure blob storage Streaming Analytics kimenet](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Jelenleg áll, folytassa a csalások észlelés forgatókönyv, és ebben az oktatóanyagban létrehozott erőforrások használatára, amely további cikkek. Ha folytatja, tekintse meg a javaslatok alapján **további lépések** később.

Azonban ha elkészült, és nincs szükség a létrehozott erőforrások, törölheti őket, hogy a szükségtelen Azure költségek. Ebben az esetben javasoljuk, hogy tegye a következőket:

1. A Streaming Analytics-feladat leállítása. Az a **feladatok** ablaktáblán kattintson a **leállítása** tetején.
2. Állítsa le a Telco készítő alkalmazást. A parancssori ablakban, ahol az alkalmazás indításakor nyomja le a Ctrl + C.
3. Ha új blob storage-fiók ebben az oktatóanyagban az imént létrehozott, törölje azt. 
4. Törölje a Streaming Analytics-feladathoz.
5. Törölje az eseményközpontba.
6. Törölje a event hub névteret.

## <a name="get-support"></a>Támogatás kérése

Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag a következő továbbra is:

* [A Stream Analytics és a Power BI: az adatfolyamként történő adatok valós idejű elemzési irányítópult](stream-analytics-power-bi-dashboard.md). Ez a cikk bemutatja, hogyan küldhet a Stream Analytics-feladat eredményének TelCo Power bi-bA valós idejű képi megjelenítés és elemzésére.

További információ a Stream Analytics általában, lásd: ezek a cikkek:

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
