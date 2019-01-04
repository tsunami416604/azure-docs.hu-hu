---
title: Az Azure Stream Analytics áttekintése
description: Ismerje meg a Stream Analytics nevű felügyelt szolgáltatást, amely segít valós időben elemezni az eszközök internetes hálózatáról (IoT) származó streamadatokat.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.workload: data-services
ms.custom: seodec18
ms.date: 12/07/2018
ms.openlocfilehash: e14da7dff62d85c730034b620a6168b3d9b3dde7
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752690"
---
# <a name="what-is-azure-stream-analytics"></a>Mi az az Azure Stream Analytics?

Az Azure Stream Analytics egy eseményfeldolgozó motor, amely lehetővé teszi az eszközökről streamelt nagy mennyiségű adat vizsgálatát. A bejövő adatok származhatnak egyebek között eszközöktől, érzékelőktől, webhelyektől, közösségi hírcsatornákról vagy alkalmazásoktól. A szolgáltatás emellett támogatja az információk adatfolyamokból való kinyerését, illetve a mintázatok és kapcsolatok azonosítását. Ezek a minták majd használatával aktiválhat más műveleteket, mint például a riasztások létrehozása, információt adhat át jelentéskészítő eszközöknek, vagy tárolhatja későbbi használatra.

Az alábbiak az Azure Stream Analytics használatának lehetséges példái: 

* Az eszközök internetes hálózata (IoT) érzékelőinek egyesítése és eszközök telemetriai adatain végzett valós idejű elemzés
* Webnaplók és kattintássorozatok elemzése
* Térinformatikai elemzés flottakezeléshez és vezető nélküli járművekhez
* A nagy értékű eszközök távoli figyelése és prediktív karbantartása
* A pénztári adatok valós idejű elemzése készletszabályozáshoz és anomáliadetektáláshoz

## <a name="how-does-stream-analytics-work"></a>Hogyan működik a Stream Analytics?

Az Azure Stream Analytics egy streamelési adatforrásból indul ki, amelyet beolvas az Azure Event Hubs vagy az Azure IoT Hub szolgáltatásba, vagy pedig egy olyan adattárból, mint például az Azure Blob Storage. A streamek vizsgálatához egy Stream Analytics-feladatot kell létrehozni, amely meghatározza az adatokat streamelő bemeneti forrást. A feladat emellett megad egy transzformációs lekérdezést, amely meghatározza az adatok, minták és kapcsolatok keresésének módját. A transzformációs lekérdezés használja az SQL lekérdezési nyelv segítségével könnyedén szűrési, rendezési, és összeilleszthetők az streamelési adatok egy időszakon belül. A feladat végrehajtásakor módosíthatja az eseményrendezési beállításokat és az összesítési műveletek végrehajtásakor használt időtartományok hosszát.

A bejövő adatok elemzését követően meg kell adnia az átalakított adatok kimenetét, és azt is megadhatja, hogy a rendszer hogyan reagáljon az elemzett információkra. Például a következőkhöz hasonló műveleteket hajthat végre:

* Adatok elküldése egy figyelt várólistába lefelé irányuló eseményindító riasztások vagy egyéni munkafolyamatok aktiválásához.
* Az adatok elküldése egy Power BI-irányítópultra valós idejű vizualizáció céljából.
* Store más Azure storage-szolgáltatások, az adatokat, így betanításához egy gépi tanulási modellt az előzményadatok alapján, vagy hajtsa végre a batch-elemzés.

Az alábbi ábra a Stream Analytics-folyamatot szemlélteti. A Stream Analytics-feladat felhasználhatja az összes bemenetet és kimenetet, vagy azok egy kiválasztott készletét. Az ábra azt mutatja be, hogyan küldi el a rendszer az adatokat a Stream Analytics szolgáltatásba, és az hogyan elemzi, majd továbbítja azokat más műveletek, például tárolás vagy megjelenítés céljából:

![Stream Analytics – bevezetés folyamat](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Főbb képességek és előnyök

Az Azure Stream Analytics szolgáltatást könnyen használhatónak, rugalmasnak, megbízhatónak és bármely feladathoz méretezhetőnek tervezték. Érhető el több Azure-régiók között. Az alábbi ábra az Azure Stream Analytics főbb képességeit mutatja be:

![A Stream Analytics főbb képességei](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Könnyű első lépések

Az Azure Stream Analytics használatát könnyű megkezdeni. Mindössze néhány kattintással csatlakoztathat több forrást, fogadót, és létrehozhat egy teljes körű folyamatot. A Stream Analytics képes csatlakozni az [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) és az [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) szolgáltatáshoz streamelési adatok betöltése céljából. Emellett az [Azure Blob Storage](https://docs.microsoft.com/azure/storage/storage-introduction) szolgáltatáshoz is csatlakoztatható előzményadatok betöltéséhez. Képes kombinálni az eseményközpontokból származó adatokat más adatforrásokkal és feldolgozó motorokkal. A feladatok bemenetei között szerepelhetnek statikus vagy lassan változó referenciaadatok is, és ezek a referenciaadatok összekapcsolhatók streamelési adatokkal keresési műveletek végrehajtásához.

A Stream Analytics számos olyan tárolási rendszerbe tudja irányítani a feladatok kimenetét, mint például az [Azure Blob](https://docs.microsoft.com/azure/storage/storage-introduction), az [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/), az [Azure Data Lake Store-tárolók](https://docs.microsoft.com/azure/data-lake-store/) vagy az [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction). Rendezést követően futtathat kötegelt elemzést az Azure HDInsight vagy egy másik szolgáltatás például az event hubs tehet, vagy elküldheti a kimenetet [Power BI](https://docs.microsoft.com/power-bi/) Power BI streamelési API használatával valós idejű Vizualizáció céljából.

## <a name="programmer-productivity"></a>Programozói hatékonyság

Az Azure Stream Analytics egy egyszerű SQL-alapú lekérdező nyelvet használ, amely hatékony historikus korlátozásokkal lett bővítve a mozgásban lévő adatok elemzéséhez. A feladattranszformációk a [Stream Analytics egyszerű, deklaratív lekérdező nyelvével](https://msdn.microsoft.com/library/azure/dn834998.aspx) definiálhatók, amely lehetővé teszi, hogy egyszerű SQL-szerkezetekkel hozzon létre összetett historikus lekérdezéseket. A Stream Analytics lekérdező nyelve konzisztens az SQL nyelvvel, így az SQL nyelv ismerete elegendő a feladatok létrehozásának megkezdéséhez. Olyan fejlesztői eszközökkel is létrehozhatók feladatok, mint például az Azure PowerShell, [a Stream Analytics Visual Studio-hoz készült eszközei](stream-analytics-tools-for-visual-studio-install.md) vagy az Azure Resource Manager-sablonok. A fejlesztői eszközök használatával offline módon fejleszthet transzformációs lekérdezéseket és a [folyamatos integrációs és folyamatos teljesítési folyamattal](stream-analytics-tools-for-visual-studio-cicd.md) küldhet be feladatokat az Azure-ba. 

A Stream Analytics lekérdező nyelve számos funkciót biztosít a streamelési adatok elemzéséhez és feldolgozásához. A lekérdező nyelv az egyszerű adatkezeléstől és összesítő függvényektől az összetett térinformatikai funkciókig támogat műveleteket. A lekérdezéseket szerkesztheti a portálon, és az élő streamből kinyert mintaadatokkal tesztelheti őket.

A lekérdező nyelv lehetőségei függvények definiálásával és meghívásával tovább bővíthetők. Definiálhat függvényhívásokat az Azure Machine Learning szolgáltatásban Azure Machine Learning-megoldások használatához és JavaScript nyelvű felhasználó által definiált függvények (UDF-ek) integrálásához, vagy felhasználó által definiált összesítéseket összetett számítások Stream Analytics-lekérdezések részeként való elvégzéséhez.

## <a name="fully-managed"></a>Teljes körű felügyelet 

Az Azure Stream Analytics egy teljes körűen felügyelt Azure-beli kiszolgáló nélküli (PaaS-) ajánlat. Ez azt jelenti, hogy nem kell hardvert kiépítenie vagy fürtöket kezelnie a feladatok futtatásához. Az Azure Stream Analytics teljes körűen felügyeli a feladatot, gondoskodva az összetett számítási fürtök felhőben való beállításáról és a teljesítményhangolásról, mely a feladat futtatásához szükséges. Az Azure Event Hubs- és Azure IoT Hubs-integrációnak köszönhetően a feladatok másodpercenként több millió eseményt képesek feldolgozni a csatlakoztatott eszközökről, kattintássorozatokból és naplófájlokból, hogy csak néhányat említsünk. Az eseményközpontok particionáló funkciójával a számítások logikai lépésekre bonthatók, melyek mindegyike tovább bontható lehet a jobb méretezhetőség érdekében.

## <a name="run-in-the-cloud-on-in-the-intelligent-edge"></a>Az intelligens peremhálózat futtatását a felhőben

Az Azure Stream Analytics a felhőalapú, nagy méretű elemzési futtathatja, vagy futtassa az intelligens peremhálózaton egyaránt ultramagas közel valós idejű elemzés céljából.
Az Azure Stream Analytics lekérdezési nyelvre használja mind a felhőben, és az intelligens peremhálózaton, így a fejlesztők igazi hibrid architektúrákat adatfolyam-feldolgozás hozhat létre.

## <a name="low-total-cost-of-ownership"></a>Alacsony teljes bekerülési költség

Felhőszolgáltatásként a Stream Analytics költségoptimalizált. Nincsenek előzetes költségek, csak [a felhasznált streamelési egységekért](stream-analytics-streaming-unit-consumption.md) és a feldolgozott adatmennyiségért kell fizetnie. Nem szükséges kötelezettségvállalás vagy fürtlétesítés. A streamelési feladatok az üzleti igényeknek megfelelően fel- és leskálázhatók. 

## <a name="reliability"></a>Megbízhatóság 

Stream Analytics biztosítja a pontosan egyszeri esemény feldolgozása és, legalább egyszeri kézbesíti az eseményeket, tehát események soha nem elvesznek. Beépített helyreállítási képességekkel rendelkezik arra az esetre, ha az eseményeket nem sikerül kézbesíteni. Stream Analytics is, biztosít beépített ellenőrzőpontok használata, a feladat állapotának fenntartásához és megismételhető eredményeket biztosít.

Felügyelt szolgáltatásként a Stream Analytics Eseményfeldolgozási 99,9 %-os rendelkezésre állással biztosítja. További információkért lásd: a [Stream Analytics SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) témánál talál további információkat. 

## <a name="performance"></a>Teljesítmény

A Stream Analytics másodpercenként több millió eseményt képes feldolgozni, és alacsony késleltetéssel képes kézbesíteni az eredményeket.
Lehetővé teszi, hogy a vertikális és horizontális felskálázás nagy valós idejű és összetett eseményfeldolgozó alkalmazások kezeléséhez. Stream Analytics támogatja a teljesítményt particionálásával, amely lehetővé teszi az összetett lekérdezések párhuzamosíthatók és végrehajthatók több streamelési csomóponton.
Az Azure Stream Analytics épül [Trill](https://github.com/Microsoft/Trill), mégpedig a Microsoft Research fejlesztett egy nagy teljesítményű memórián belüli streamelési elemzési motorjára. 

## <a name="next-steps"></a>További lépések

A cikk az Azure Stream Analytics szolgáltatásról nyújtott áttekintést. Ezután megismerheti a részleteket, és létrehozhatja első Stream Analytics-feladatát:

* [Stream Analytics-feladat létrehozása az Azure Portalon](stream-analytics-quick-create-portal.md).
* [Stream Analytics-feladat létrehozása az Azure PowerShell használatával](stream-analytics-quick-create-powershell.md).
* [Stream Analytics-feladat létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md).

