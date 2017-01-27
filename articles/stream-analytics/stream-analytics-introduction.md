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
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2b4a10c77ae02ac0e9eeecf6d7d6ade6e4c33115
ms.openlocfilehash: 493a915fb4e3de4cb70324b22beeaae6720a3492


---
# <a name="what-is-stream-analytics"></a>Mi a Stream Analytics?
Az Azure Stream Analytics egy teljes körűen felügyelt, költséghatékony, valós idejű eseményfeldolgozó, amellyel részletesebben elemezheti az adatait. A Stream Analytics segítségével egyszerűen állíthat be valós idejű, az eszközökről, érzékelőkről, webhelyekről, közösségi oldalakról, alkalmazásokból, infrastruktúra-rendszerekből és egyéb helyekről érkező adatstreameket elemző számításokat.

Az Azure Portalon mindössze néhány kattintással létrehozhat egy Stream Analytics-feladatot, amelynél megadja a streamadatok bemeneti forrását, a feladat eredményeinek kimeneti fogadóját és egy adatátalakítást, amely egy SQL-szerű nyelven van kifejezve. Az Azure Portalon megfigyelheti a feladatát, és módosíthatja a méretét vagy a sebességét – ez terjedhet a másodpercenként néhány kilobájtostól egészen a másodpercenként egy gigabájtos méretű vagy még több feldolgozott eseményig.

A Microsoft Research évek óta fejleszt nagymértékben finomhangolt folyamatos adatátviteli motorokat az időfüggő számításokhoz, valamint nyelvintegrációkat az ilyen programok egyszerűen elsajátítható értelmezéséhez – a Stream Analytics ennek a munkának az eredménye.

## <a name="what-can-i-use-stream-analytics-for"></a>Mire használható a Stream Analytics?
Napjainkban az adatok óriási mennyiségben, nagy sebességgel áramlanak. A streamadatok valós idejű feldolgozására képes és rájuk azonnal reagálni tudó szervezetek jelentősen javíthatják a hatékonyságukat és kitűnhetnek a versenytársaik közül. A valós idejű streamelemzésekre minden iparágban találhatunk példát: ilyenek a pénzügyi szolgáltatók által kínált személyre szabott, valós idejű tőzsdeelemzések és riasztások, a valós idejű csalásészlelések, az adat- és személyazonosság-védelmi szolgáltatások, a fizikai objektumokba ágyazott érzékelők és indítószerkezetek (IoT) által generált adatok megbízható összegyűjtése és elemzése, a webes kattintássorozati elemzés, valamint azok az ügyfélkapcsolat-kezelő (CRM-) alkalmazások, amelyek riasztást küldenek, ha az ügyfélélmény romlik egy időkereten belül. A vállalkozások a leginkább rugalmas, megbízható és költséghatékony megoldást keresik a valós idejű eseményfolyamok adatelemzésére, hogy sikeresek lehessenek a modern üzleti világ erős versenyhelyzetében.

## <a name="key-capabilities-and-benefits"></a>Főbb képességek és előnyök
* **Könnyű használat:** A Stream Analytics egy egyszerű, deklaratív lekérdezési modellel támogatja az átalakítások leírását. A könnyű használatra optimalizált Stream Analytics egy T-SQL-változatot használ, és kiküszöböli, hogy az ügyfeleknek a streamfeldolgozó rendszerek bonyolult technikai részleteivel kelljen bajlódniuk. A böngésző lekérdezésszerkesztőjében használt [Stream Analytics lekérdezési nyelv](https://msdn.microsoft.com/library/azure/dn834998.aspx) intelligens automatikus kiegészítésekkel segít gyorsan és könnyedén alkalmazni az idősorozatos lekérdezéseket, ideértve az időalapú illesztéseket, az ablakos összesítéseket, az időalapú szűrőket és más olyan gyakori műveleteket, mint az illesztések, az összesítések, a leképezések és a szűrők. Emellett a lekérdezések mintaadatfájlokon, a böngészőben elvégezhető tesztelése gyors, fokozatos fejlődést tesz lehetővé.  
* **Méretezhetőség:** A Stream Analytics a nagy sebességű eseményátviteleket is képes kezelni, egészen 1 GB/másodperc sebességig. Az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)- és [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/)-integrációnak köszönhetően a megoldás másodpercenként több millió eseményt képes feldolgozni a csatlakoztatott eszközökből, kattintássorozatokból és naplófájlokból, hogy csak néhányat említsünk. A Stream Analytics ehhez az Event Hubs particionálási képességeit használja, amelyek partíciónként akár 1 MB/s sebességet biztosíthatnak. A felhasználók a lekérdezésdefiníción belül több logikai lépésre bonthatják a számítást, és ezen lépések mindegyikét még tovább bonthatják a méretezhetőség javítása érdekében.  
* **Megbízhatóság, ismételhetőség és gyors helyreállítás:** A Stream Analytics felügyelt felhőbeli szolgáltatás a beépített helyreállítási képességek segítségével megakadályozza az adatvesztést, és biztosítja az üzletmenet folytonosságát hibák esetén. Mivel a szolgáltatás képes az állapot belső fenntartására, ismételhető eredményekkel biztosít lehetőséget az események archiválására és a feldolgozás jövőbeli újraalkalmazására, ami mindig ugyanazokkal az eredményekkel jár. Az ügyfelek így visszamehetnek az időben, és megvizsgálhatják a számításokat a kiváltó okokat kereső elemzések, lehetőségelemzések stb. során.  
* **Alacsony költség:** Mivel a Stream Analytics felhőszolgáltatás, arra optimalizálták, hogy a felhasználók nagyon alacsony költség mellett indíthassanak el és tarthassanak fenn valós idejű elemzési megoldásokat. A szolgáltatás használatalapú fizetést tesz lehetővé, ami a Streaming Unit-használaton és a rendszer által feldolgozott adatmennyiségen alapul. A használat számítása a feldolgozott események mennyisége, illetve a megfelelő Stream Analytics-feladatok kezelésére a fürtben kiépített számítási teljesítmény mennyisége alapján történik.  
* **Referenciaadatok:** A Stream Analytics lehetővé teszi a referenciaadatok megadását és használatát a felhasználók számára. Ezek lehetnek előzményadatok vagy egyszerűen nem streamadatok, amelyek ritkábban változnak az idő folyamán. A rendszer a referenciaadatok használatának leegyszerűsítésével más bejövő eseménystreamekhez hasonlóan kezeli őket, így ezek más valós időben feldolgozott eseménystreamekhez csatlakoztatva átalakításokat hajthattak végre.  
* **Felhasználó által definiált függvények:** A Stream Analytics integrálva lett az Azure Machine Learning szolgáltatással, így függvényhívásokat lehet meghatározni a Machine Learning szolgáltatásban egy Stream Analytics-lekérdezés részeként. Ez kibővíti a Stream Analytics képességeit, és képes használni az Azure Machine Learning meglévő megoldásait. Az ezzel kapcsolatos további információkért tekintse át a [Machine Learning integrációs oktatóanyagát](stream-analytics-machine-learning-integration-tutorial.md).
* **Csatlakozás:** A Stream Analytics a közvetlen Azure Event Hubs- és Azure IoT Hub-kapcsolatokon keresztül fogadja a streameket, az Azure Blob szolgáltatás pedig előzményadatokat biztosít a számára. A Stream Analytics eredményei beírhatók az Azure Storage Blobsba vagy a Tablesbe, az Azure SQL Database-be, az Azure Data Lake Storesba, a DocumentDB-be, az Event Hubsba, az Azure Service Bus-témakörökbe vagy -üzenetsorokba és a Power BI-ba, ahol megjeleníthetők, tovább feldolgozhatók munkafolyamatokkal, felhasználhatók kötegelt elemzésekben az [Azure HDInsighton](https://azure.microsoft.com/services/hdinsight/) keresztül, vagy ismét feldolgozhatók események sorozataként. Az Event Hubs lehetővé teszi több Stream Analytics más adatforrásokkal és feldolgozórendszerekkel való összeállítását a számítások streamelő jellegének elvesztése nélkül.  

## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Következő lépések
Bemutattuk Önnek a Stream Analytics felügyelt szolgáltatást, amely streamelő elemzéseket biztosít az eszközök internetes hálózatáról (IoT) származó adatokon. További információk a szolgáltatásról:

* [Get started using Azure Stream Analytics](stream-analytics-get-started.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Jan17_HO4-->


