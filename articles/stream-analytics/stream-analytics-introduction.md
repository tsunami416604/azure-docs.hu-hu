---
title: "A Stream Analytics bemutatása | Microsoft Docs"
description: "Ismerje meg a Stream Analytics nevű felügyelt szolgáltatást, amely segít valós időben elemezni az eszközök internetes hálózatáról (IoT) származó streamadatokat."
keywords: "szolgáltatásként kínált elemzés, felügyelt szolgáltatások, streamfeldolgozás, streamelemzés, mi a stream analytics"
services: stream-analytics
documentationcenter: 
author: jenniehubbard
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/08/2017
ms.author: jhubbard
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 5081ecaf569aef9e2f99cc27e91c4b64a25b0deb
ms.contentlocale: hu-hu
ms.lasthandoff: 08/11/2017

---

# <a name="what-is-stream-analytics"></a>Mi a Stream Analytics?

Az Azure Stream Analytics teljes körűen felügyelt eseményfeldolgozó motor, amellyel valós idejű elemző számítások állíthatók be adatfolyamokra. Az adatok származhatnak egyebek között eszközöktől, érzékelőktől, webhelyektől, közösségi hírcsatornákról, alkalmazásoktól vagy infrastruktúra-rendszerektől. 

## <a name="what-can-i-do-with-stream-analytics"></a>Mire használhatom a Stream Analytics szolgáltatást?

A Stream Analytics használatával az eszközöktől vagy folyamatoktól származó nagy mennyiségű adat vizsgálható át, továbbá mintázatokat, trendeket és összefüggéseket kereshet, illetve információkat vonhat ki a streamből. Az alkalmazás az adattartalom alapján műveleteket hajthat végre. Kiadhat például riasztást, indíthat automatizált munkafolyamatokat, információt adhat át olyan jelentéskészítő eszköznek, mint a Power BI, vagy tárolhatja az adatokat későbbi vizsgálat céljára. 

Példák:

* Pénzügyi szolgáltatók által kínált személyre szabott, valós idejű tőzsdeelemzések és riasztások.
* Tranzakciós adatok vizsgálatán alapuló valós idejű csalásészlelés. 
* Adat- és személyazonosság-védelmi szolgáltatások.
* Fizikai objektumokba ágyazott érzékelők és indítószerkezetek (IoT) által generált adatok elemzése.
* Webes kattintássorozat-elemzés.
* Ügyfélkapcsolat-kezelő (CRM-) alkalmazások, amelyek például riasztást küldenek, ha az ügyfélélmény romlik egy időkereten belül.

## <a name="how-does-stream-analytics-work"></a>Hogyan működik a Stream Analytics?

Az alábbi ábra a Stream Analytics-folyamatot ismerteti, bemutatva az adatok feldolgozását, elemzését, valamint a bemutatás vagy a műveletek végrehajtása céljából történő továbbküldését. 

![Stream Analytics folyamatábra](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

A Stream Analytics egy adatfolyam-forrásból indul ki. Az adatok egy Azure-eseményközpontot vagy IoT Hubot használó eszközön át olvasódnak be az Azure-ba. Az adatok származhatnak olyan adattárból is, mint az Azure Blob Storage. 

A stream vizsgálatához létrehoz egy Stream Analytics-*feladatot*, amely meghatározza az adatok forrását. A feladat megad egy *transzformációt*&mdash; az adatok, minták és kapcsolatok keresési módjához. Ebben a feladatban a Stream Analytics támogat egy SQL-szerű lekérdezési nyelvet, amely lehetővé teszi a streamelési adatok adott időtartamon belüli szűrését, rendezését, összesítését és egyesítését.

A feladat megad végül egy kimenetet is, ahová a transzformált adatokat továbbítja. Ezáltal eldönthető, hogy mi a teendő az elemzett információra adott válaszként. Megteheti például, hogy az elemzés eredményeként:

* Parancsot küld egy eszköz beállításainak módosítására. 
* Adatot küld egy feldolgozási sorba, amelyet a találatok alapján választott műveletet végrehajtó folyamat figyel. 
* Adatot küld egy Power BI irányítópultnak jelentés készítéséhez.
* Adatot küld egy adattárba, amilyen a Data Lake Store, SQL Server adatbázis, Azure Blob vagy Table Storage.

Figyelhet egy feladatot a futása közben, és átállíthatja a másodpercenként feldolgozott események számát. Beállíthat feladatokat diagnosztikai naplók előállítására hibaelhárításhoz.

## <a name="key-capabilities-and-benefits"></a>Főbb képességek és előnyök

A Stream Analytics-et könnyen használhatónak, rugalmasnak, bármely feladathoz méretezhetőnek és gazdaságosnak tervezték.

### <a name="connectivity-to-many-inputs-and-outputs"></a>Kapcsolat sok bemenettel és kimenettel

A Stream Analytics a közvetlen [Azure Event Hubs-](https://azure.microsoft.com/services/event-hubs/) és [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)-kapcsolaton keresztül fogadja a streameket, az [Azure Blob Storage szolgáltatás](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts) pedig előzményadatokat biztosít a számára. Ha az adatok eseményközpontokból származnak, akkor a Stream Analytics más adatforrásokkal és feldolgozó motorokkal is kombinálható.

A feladat bemenete referenciaadatokat (statikus vagy lassan változó adatokat) is tartalmazhat. Az adatfolyamot ezekhez a referenciaadatokhoz társítva ugyanúgy végezhet keresési műveleteket, mint egy adatbázis-lekérdezés esetén.

A Stream Analytics-feladat kimenetét különböző célok felé továbbíthatja. Kiírhatja olyan tárhelyre, mint egy Azure Storage-blob vagy -tábla, egy Azure SQL Database, Azure Data Lake Store vagy Azure Cosmos Database. Az adatok innen továbbkerülhetnek például kötegelt elemzésre az Azure HDInsighttal. A kimenet egy másik szolgáltatásnak, például eseményközpontnak, Azure Service Bus témakörnek, vagy feldolgozási sornak is átadható, hogy egy másik eljárás dolgozza fel. A kimenet a Power BI felé is továbbítható a képi megjelenítéshez.

### <a name="ease-of-use"></a>Könnyű használat

Transzformációk definiálására egy egyszerű, deklaratív [Stream Analytics lekérdező nyelv](https://msdn.microsoft.com/library/azure/dn834998.aspx) szolgál, amellyel programozás nélkül hozhat létre kifinomult elemzéseket. A lekérdező nyelv egy adatfolyamot fogad bemenetként. Az adatok szűrhetők, rendezhetők és összegezhetők, végezhet velük számításokat, társíthatja őket (az adatfolyamon belül vagy a referenciaadatokkal) és földrajzi függvényekhez is használhatók. A lekérdezések a portálon szerkeszthetők IntelliSense használatával és szintaktikai ellenőrzés mellett. A lekérdezések tesztelhetők is az élő adatfolyamból kiemelt mintaadatokon.

### <a name="extensible-query-language"></a>Bővíthető lekérdező nyelv

A lekérdező nyelv lehetőségei függvények definiálásával és meghívásával tovább bővíthetők. Az Azure Machine Learning megoldásokat az Azure Machine Learning szolgáltatásban definiált függvényhívásokkal használhatja ki. Stream Analytics lekérdezés részeként felhasználó által definiált JavaScript-függvények (UDF-ek) is integrálhatók komplex számítások elvégzésére.

### <a name="scalability"></a>Méretezhetőség

A Stream Analytics akár másodpercenként 1 GB bejövő adat kezelésére is képes. Az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)- és [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/)-integrációnak köszönhetően a feladatok másodpercenként több millió eseményt képesek feldolgozni a csatlakoztatott eszközökből, kattintássorozatokból és naplófájlokból, hogy csak néhányat említsünk. Az eseményközpontok particionáló funkciójával a számítások logikai lépésekre bonthatók, melyek mindegyike tovább bontható lehet a jobb méretezhetőség érdekében.

### <a name="low-cost"></a>Alacsony költség

Felhőszolgáltatásként a Stream Analytics alacsony költségű működtetésre van optimalizálva. A szolgáltatás használatalapú fizetést tesz lehetővé, ami a Streaming Unit-használaton és a rendszer által feldolgozott adatmennyiségen alapul. A használat számítása a feldolgozott események mennyisége, illetve a Stream Analytics-feladatok kezelésére a fürtben felhasznált számítási teljesítmény mérőszáma alapján történik.

### <a name="reliability-quick-recovery-and-repeatability"></a>Megbízhatóság, gyors helyreállítás és ismételhetőség

A Stream Analytics felügyelt felhőbeli szolgáltatás segít megakadályozni az adatvesztést, és biztosítani az üzletmenet folytonosságát. A szolgáltatás beépített helyreállítási képességekkel bír hibák esetére. Mivel a szolgáltatás képes az állapot belső fenntartására, ismételhető eredményekkel biztosít lehetőséget az események archiválására és a feldolgozás jövőbeli újraalkalmazására, ami mindig ugyanazokkal az eredményekkel jár. Így visszamehet az időben, és megvizsgálhatja a számításokat a kiváltó okokat kereső elemzések, lehetőségelemzések stb. során.

## <a name="next-steps"></a>Következő lépések

* Első lépések: [kísérletezés IoT-eszközöktől származó bemenetekkel és lekérdezésekkel](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md).
* Létrehozhat egy [teljes körű Stream Analytics-megoldást](stream-analytics-real-time-fraud-detection.md), amely megvizsgálja a telefonok metaadatait a rosszindulatú hívások megkereséséhez.
* Ismerje meg a Stream Analytics SQL-re hasonlító lekérdező nyelvét és az olyan egyedi fogalmakat, mint az [ablakfüggvények](stream-analytics-window-functions.md).
* Megismerheti [a Stream Analytics-feladatok skálázását](stream-analytics-scale-jobs.md). 
* Megtudhatja, hogyan [integrálhatja a Stream Analytics és az Azure Machine Learning szolgáltatást](stream-analytics-machine-learning-integration-tutorial.md).
* A Stream Analyticsszel kapcsolatos kérdéseire az [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics) kaphat választ.


