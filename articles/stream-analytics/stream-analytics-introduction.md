---
title: "A Stream Analytics bemutatása | Microsoft Docs"
description: "Ismerje meg a Stream Analytics nevű felügyelt szolgáltatást, amely segít valós időben elemezni az eszközök internetes hálózatáról (IoT) származó streamadatokat."
keywords: "szolgáltatásként kínált elemzés, felügyelt szolgáltatások, streamfeldolgozás, streamelemzés, mi a stream analytics"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/16/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 421bdfb3132bc8c9f193bcca8d55c9cf9eba1c3b
ms.contentlocale: hu-hu
ms.lasthandoff: 07/04/2017


---

<a id="what-is-stream-analytics" class="xliff"></a>

# Mi a Stream Analytics?

Az Azure Stream Analytics teljes körűen felügyelt eseményfeldolgozó motor, amellyel valós idejű elemző számítások állíthatók be adatfolyamokra. Az adatok származhatnak egyebek között eszközöktől, érzékelőktől, webhelyektől, közösségi hírcsatornákról, alkalmazásoktól vagy infrastruktúra-rendszerektől. 

<a id="what-can-i-use-stream-analytics-for" class="xliff"></a>

## Mire használható a Stream Analytics?

A Stream Analytics használatával az eszközöktől vagy eljárásoktól származó nagy tömegű adat vizsgálható át, hogy mintázatokat, trendeket és összefüggéseket kereshessen az adatfolyamból kivont információk között. Az alkalmazás az adattartalom alapján műveleteket hajthat végre. Kiadhat például riasztást, indíthat automatizált munkafolyamatokat, információt adhat át olyan jelentéskészítő eszköznek, mint a Power BI, vagy tárolhatja az adatokat későbbi vizsgálat céljára. 

Példák a Stream Analytics alkalmazási területeire:

* Pénzügyi szolgáltatók által kínált személyre szabott, valós idejű tőzsdeelemzések és riasztások.
* Tranzakciós adatok vizsgálatán alapuló valós idejű csalásészlelés. 
* Adat- és személyazonosság-védelmi szolgáltatások.
* Fizikai objektumokba ágyazott érzékelők és indítószerkezetek (IoT) által generált adatok elemzése.
* Webes kattintássorozat-elemzés.
* Ügyfélkapcsolat-kezelő (CRM-) alkalmazások, amelyek például riasztást küldenek, ha az ügyfélélmény romlik egy időkereten belül.

<a id="how-does-stream-analytics-work" class="xliff"></a>

## Hogyan működik a Stream Analytics?

A Stream Analytics alábbi folyamatábrája bemutatja az adatok beolvasásának, elemzésének, majd bemutató vagy művelet céljára történő átadásának menetét. 

![Stream Analytics folyamatábra](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

A Stream Analytics egy adatfolyam-forrásból indul ki. Az adatok egy Azure-eseményközpontot vagy IoT Hubot használó eszközön át olvasódnak be az Azure-ba. Az adatok származhatnak olyan adattárból is, mint az Azure Blob Storage. 

Az adatfolyam vizsgálatához létre kell hozni egy Stream Analytics-*feladatot*, amely megadja az adatok forrását. A feladat megad egy *transzformációt*&mdash; az adatok, minták és kapcsolatok keresési módjához. Erre a célra a Stream Analytics egy SQL-hez hasonló lekérdező nyelvet támogat, amellyel szűrhetők, összegezhetők és összeilleszthetők az egy adott időszakban beérkező adatok.

A feladat megad végül egy kimenetet is, ahová a transzformált adatokat továbbítja. Ezáltal eldönthető, hogy mi a teendő az elemzett információra adott válaszként. Megteheti például, hogy az elemzés eredményeként:

* Parancsot küld egy eszköz beállításainak módosítására. 
* Adatot küld egy feldolgozási sorba, amelyet a találatok alapján választott műveletet végrehajtó folyamat figyel. 
* Adatot küld egy Power BI irányítópultnak jelentés készítéséhez.
* Adatot küld egy adattárba, amilyen a Data Lake Store, SQL Server adatbázis, Azure Blob vagy Table Storage.

Figyelhet egy feladatot a futása közben és átállíthatja a másodpercenként feldolgozott események számát. Beállíthat feladatokat diagnosztikai naplók előállítására hibaelhárításhoz.

<a id="key-capabilities-and-benefits" class="xliff"></a>

## Főbb képességek és előnyök

A Stream Analytics-et könnyen használhatónak, rugalmasnak, bármely feladathoz méretezhetőnek és gazdaságosnak tervezték.

<a id="connectivity-to-many-inputs-and-outputs" class="xliff"></a>

### Kapcsolat sok bemenettel és kimenettel

A Stream Analytics a közvetlen [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/)- és [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)-kapcsolatokon keresztül fogadja a streameket, az [Azure Blob tárhely-szolgáltatás](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts) pedig előzményadatokat biztosít a számára. Ha az adatok eseményközpontokból származnak, akkor a Stream Analytics más adatforrásokkal és feldolgozó motorokkal is kombinálható.

A feladat bemenete referenciaadatokat (statikus vagy lassan változó adatokat) is tartalmazhat. Az adatfolyamot ezekhez a referenciaadatokhoz társítva ugyanúgy végezhet keresési műveleteket, mint egy adatbázis-lekérdezés esetén.

Egy Stream Analytics-feladat kimenete sokfelé átirányítható. Kiírható olyan tárhelyre, mint egy Azure Storage blob vagy tábla, egy Azure SQL Database, Azure Data Lake Store vagy Azure Cosmos Database. Az adatok innen továbbkerülhetnek például kötegelt elemzésre az Azure HDInsighttal. A kimenet egy másik szolgáltatásnak, például eseményközpontnak, Azure Service Bus témakörnek, vagy feldolgozási sornak is átadható, hogy egy másik eljárás dolgozza fel. A kimenet a Power BI felé is továbbítható a képi megjelenítéshez.

<a id="ease-of-use" class="xliff"></a>

### Könnyű használat

Transzformációk definiálására egy egyszerű, deklaratív [Stream Analytics lekérdező nyelv](https://msdn.microsoft.com/library/azure/dn834998.aspx) szolgál, amellyel programozás nélkül hozhat létre kifinomult elemzéseket. A lekérdező nyelv egy adatfolyamot fogad bemenetként. Az adatok szűrhetők, rendezhetők és összegezhetők, végezhet velük számításokat, társíthatja őket (az adatfolyamon belül vagy a referenciaadatokkal) és földrajzi függvényekhez is használhatók. A lekérdezések a portálon szerkeszthetők IntelliSense használatával és szintaktikai ellenőrzés mellett. A lekérdezések tesztelhetők is az élő adatfolyamból kiemelt mintaadatokon.

<a id="extensible-query-language" class="xliff"></a>

### Bővíthető lekérdező nyelv

A lekérdező nyelv lehetőségei függvények definiálásával és meghívásával tovább bővíthetők. Az Azure Machine Learning megoldásokat az Azure Machine Learning szolgáltatásban definiált függvényhívásokkal használhatja ki. Stream Analytics lekérdezés részeként felhasználó által definiált JavaScript-függvények (UDF-ek) is integrálhatók komplex számítások elvégzésére.

<a id="scalability" class="xliff"></a>

### Méretezhetőség

A Stream Analytics akár másodpercenként 1 GB bejövő adat kezelésére is képes. Az [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/)- és [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)-integrációnak köszönhetően a feladat másodpercenként több millió eseményt képes feldolgozni a csatlakoztatott eszközökből, kattintássorozatokból és naplófájlokból, hogy csak néhányat említsünk. Az eseményközpontok particionáló funkciójával a számítások logikai lépésekre bonthatók, melyek mindegyike tovább bontható lehet a jobb méretezhetőség érdekében.

<a id="low-cost" class="xliff"></a>

### Alacsony költség

Felhőszolgáltatásként a Stream Analytics alacsony költségű működtetésre van optimalizálva. A szolgáltatás használatalapú fizetést tesz lehetővé, ami a Streaming Unit-használaton és a rendszer által feldolgozott adatmennyiségen alapul. A használat számítása a feldolgozott események mennyisége, illetve a Stream Analytics-feladatok kezelésére a fürtben felhasznált számítási teljesítmény mérőszáma alapján történik.

<a id="reliability-quick-recovery-and-repeatability" class="xliff"></a>

### Megbízhatóság, gyors helyreállítás és ismételhetőség

A Stream Analytics felügyelt felhőbeli szolgáltatás segít megakadályozni az adatvesztést, és biztosítani az üzletmenet folytonosságát. A szolgáltatás beépített helyreállítási képességekkel bír hibák esetére. Mivel a szolgáltatás képes az állapot belső fenntartására, ismételhető eredményekkel biztosít lehetőséget az események archiválására és a feldolgozás jövőbeli újraalkalmazására, ami mindig ugyanazokkal az eredményekkel jár. Így visszamehet az időben, és megvizsgálhatja a számításokat a kiváltó okokat kereső elemzések, lehetőségelemzések stb. során.

<a id="next-steps" class="xliff"></a>

## Következő lépések

* Első lépések: [kísérletezés IoT-eszközöktől származó bemenetekkel és lekérdezésekkel](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md).
* Készítsen [teljes funkcionalitású Streaming Analytics-megoldást](stream-analytics-real-time-fraud-detection.md), amely telefonos metaadatok vizsgálatával keres csalási célú hívásokat.
* Ismerje meg a Stream Analytics SQL-re hasonlító lekérdező nyelvét és az olyan egyedi fogalmakat, mint az [ablakfüggvények](stream-analytics-window-functions.md).
* Ismerkedjen meg [a Stream Analytics-feladatok méretezésével](stream-analytics-scale-jobs.md). 
* Ismerkedjen meg [a Stream Analytics és az Azure Machine Learning integrálásával](stream-analytics-machine-learning-integration-tutorial.md).
* Találjon választ a Stream Analytics-szel kapcsolatos kérdéseire az [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).


