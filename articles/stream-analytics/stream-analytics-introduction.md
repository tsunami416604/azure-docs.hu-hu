---
title: Az Azure Stream Analytics áttekintése
description: Ismerje meg a Stream Analytics nevű felügyelt szolgáltatást, amely segít valós időben elemezni az eszközök internetes hálózatáról (IoT) származó streamadatokat.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 05/16/2019
ms.openlocfilehash: ded2011111262eb45818ea149949989eef885f24
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65755788"
---
# <a name="what-is-azure-stream-analytics"></a>Mi az az Azure Stream Analytics?

Az Azure Stream Analytics olyan eseményfeldolgozó motor, amely a célja, hogy a streamelési adatok nagy tömegű. Minták és kapcsolatok ellenőrizhető, a számos bemeneti forrásból, beleértve az eszközök, érzékelők, webhelyek, közösségi hírcsatornákról és alkalmazások kinyert információkat. Ezek a minták segítségével aktiválhat más műveleteket, például riasztásokat hoznak létre, adatokat át jelentéskészítő eszközöknek számból vagy tárolására alakította át az adatokat későbbi használatra.

A következő esetekben példák az Azure Stream Analytics használatakor:

* Eszközök internetes hálózata (IoT) érzékelő fusion és eszköz telemetriai adatok valós idejű elemzése
* Webnaplók és kattintássorozatok elemzése
* Térinformatikai elemzés flottakezeléshez és vezető nélküli járművekhez
* A nagy értékű eszközök távoli figyelése és prediktív karbantartása
* A pénztári adatok valós idejű elemzése készletszabályozáshoz és anomáliadetektáláshoz

## <a name="how-does-stream-analytics-work"></a>Hogyan működik a Stream Analytics?

Azure Stream Analytics-feladat bemenete egy transzformációs lekérdezést és kimenet áll. Amikor a szoftverek vagy eszközök események Azure Event Hubs, az Azure IoT Hub vagy az Azure Blob Storage működésnek vannak, megadhatja egy vagy több ezeket a szolgáltatásokat, a bemeneti forrás a feladat. A transzformációs lekérdezés, amely SQL lekérdező nyelve alapul, segítségével könnyedén szűrése, rendezése, összesítése és összekapcsolhatók a streamelési adatok egy időszakon belül. Módosíthatja az eseményrendezési beállításokat és időtartományok hosszát amikor preforming összesítési műveleteket.

Minden egyes feladat van az átalakított adatok kimenetét, és szabályozhatja, hogy mi történik az elemzett információra adott válaszként. Megteheti például a következőt:

* Adatok elküldése egy figyelt várólistába lefelé irányuló eseményindító riasztások vagy egyéni munkafolyamatok aktiválásához.
* Adatok elküldése egy Power BI-irányítópultra valós idejű Vizualizáció céljából.
* Data Store más Azure-beli társzolgáltatásokban betanításához egy gépi tanulási modellt az előzményadatok alapján, vagy hajtson végre a batch-elemzés a.

Az alábbi képen látható adatok Stream Analytics felé küldött, elemzése, és a rendszer a más műveletek, például tárolás vagy bemutató:

![Stream Analytics – bevezetés folyamat](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Főbb képességek és előnyök

Az Azure Stream Analytics szolgáltatást könnyen használhatónak, rugalmasnak, megbízhatónak és bármely feladathoz méretezhetőnek tervezték. Érhető el több Azure-régiók között. Az alábbi képen az Azure Stream Analytics főbb képességeit mutatja be:

![A Stream Analytics főbb képességei](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Könnyű első lépések

Indítsa el az Azure Stream Analytics ördöngösség. Mindössze néhány kattintással való csatlakozáshoz több forrásként és fogadóként, létrehoz egy teljes körű folyamatot. Stream Analytics képes csatlakozni [Azure Event Hubs](/azure/event-hubs/) és [Azure IoT Hub](/azure/iot-hub/) az adatbetöltés, adatfolyamként, valamint [Azure Blob storage](/azure/storage/storage-introduction) pedig előzményadatokat biztosít a. Feladatbemenet is lehetnek az Azure Blob storage-ból a statikus vagy lassan változó referenciaadatok vagy [SQL Database](stream-analytics-use-reference-data.md#azure-sql-database-preview) , hogy csatlakozhat a streamelési adatok keresési műveletek végrehajtásához.

Stream Analytics is irányíthatja a feladat kimenete számos olyan tárolási rendszerbe például [Azure Blob storage](/azure/storage/storage-introduction), [Azure SQL Database](/azure/sql-database/), [Azure Data Lake Store](/azure/data-lake-store/), és [Azure A cosmos DB](/azure/cosmos-db/introduction). Az Azure HDInsight tárolt kimeneti futtathat batch-elemzés, vagy elküldheti a kimenetet egy másik szolgáltatás, például az Event Hubsból felhasználásra vagy [Power BI](https://docs.microsoft.com/power-bi/) valós idejű Vizualizáció céljából.

A Stream Analytics kimenetek teljes listájáért lásd: [megismerheti az Azure Stream Analytics kimenetei](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Programozói termelékenység

Az Azure Stream Analytics hatékony historikus korlátozásokkal lett bővítve egy egyszerű SQL-alapú lekérdezési nyelv használatával mozgásban lévő adatok elemzéséhez. A feladattranszformációk a [Stream Analytics egyszerű, deklaratív lekérdező nyelvével](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) definiálhatók, amely lehetővé teszi, hogy egyszerű SQL-szerkezetekkel hozzon létre összetett historikus lekérdezéseket. Mivel a Stream Analytics lekérdezési nyelve konzisztens az SQL nyelvvel, SQL-ismeretek elegendő feladatok létrehozásának megkezdéséhez. Fejlesztői eszközök, például az Azure PowerShell használatával is létrehozhat feladatok [Stream Analytics Visual Studio-eszközök](stream-analytics-tools-for-visual-studio-install.md), a [Stream Analytics Visual Studio Code-bővítmény](quick-create-vs-code.md), vagy az Azure Resource Manager-sablonok . A fejlesztői eszközök használatával offline módon fejleszthet transzformációs lekérdezéseket és a [folyamatos integrációs és folyamatos teljesítési folyamattal](stream-analytics-tools-for-visual-studio-cicd.md) küldhet be feladatokat az Azure-ba.

A Stream Analytics lekérdezési nyelvet biztosít elemzéséhez és streamelési adatok feldolgozásához függvények széles választékának. A lekérdező nyelv támogatja az összetett térinformatikai funkciókat, egyszerű adatkezeléstől és összesítő függvényektől. Szerkesztheti a lekérdezések a portálon, és tesztelje le azokat az élő stream kinyert mintaadatokkal.

A lekérdező nyelv lehetőségei függvények definiálásával és meghívásával tovább bővíthetők. Adja meg a függvényhívások kihasználásához az Azure Machine Learning megoldásokat az Azure Machine Learning szolgáltatásban, és integrálhatja a JavaScript vagy C# felhasználó által definiált függvények (UDF) vagy a felhasználó által definiált összesítések részeként összetett számítások végrehajtásához egy Stream Analytics-lekérdezés.

## <a name="fully-managed"></a>Teljes körű felügyelet

Az Azure Stream Analytics egy teljes körűen felügyelt Azure-beli kiszolgáló nélküli (PaaS-) ajánlat. Nem kell hardvert kiépítenie, vagy a feladatok futtatását fürt kezeléséhez. Az Azure Stream Analytics teljes körűen felügyeli a feladatot összetett számítási fürtök felhőben beállításával, és a teljesítmény-finomhangolási a feladat futtatásához szükséges figyelembe vételével. Integráció az Azure Event Hubs és az Azure IoT Hub lehetővé teszi több millió eseményt második számos forrásból közé tartozik a csatlakoztatott eszközökből, kattintássorozatokból és naplófájlokból, hogy a feladat. Az Event Hubs particionálási szolgáltatásával particionáló számítások logikai lépésekre bonthatók, melyek mindegyike tovább bontható bővítése érdekében.

## <a name="run-in-the-cloud-on-in-the-intelligent-edge"></a>Az intelligens peremhálózat futtatását a felhőben

Az Azure Stream Analytics a felhőalapú, nagy méretű elemzési futtathatja, vagy futtassa az ultramagas közel valós idejű elemzési IoT Edge-ben. Az Azure Stream Analytics használja ugyanazt a lekérdezési nyelvet a felhő és a peremhálózaton is fejleszthetők a valódi hibrid architektúráknak adatfolyam-feldolgozás engedélyezése.

## <a name="low-total-cost-of-ownership"></a>Alacsony tulajdonosi költségek

Felhőszolgáltatásként a Stream Analytics költségoptimalizált. Nincsenek előzetes költségek nincsenek érintett – csak a kell fizetnie a [streamelési egységekért](stream-analytics-streaming-unit-consumption.md), és a feldolgozott adatok mennyisége. Nem kötelezettségvállalás vagy fürtlétesítés szükséges, és méretezhetők a feladat felfelé vagy lefelé a üzleti igények alapján.

## <a name="mission-critical-ready"></a>Alapvető fontosságú kész

Az Azure Stream Analytics világszerte több régióban érhető el, és célja, hogy üzleti szempontból alapvető fontosságú számítási feladatok futtatása támogatásával, megbízhatóság, biztonsági és megfelelőségi követelményeknek.

### <a name="reliability"></a>Megbízhatóság

Az Azure Stream Analytics biztosítja pontosan – Miután eseményfeldolgozás és, legalább egyszeri kézbesíti az eseményeket, így események soha nem elvesznek. Pontosan-egyszeri feldolgozását garantálja a megadott kimeneti leírtak szerint [esemény kézbesítési garanciával könnyítik](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Az Azure Stream Analytics beépített helyreállítási képességekkel rendelkezik, arra az esetre, ha az eseményeket a kézbesítése sikertelen lesz. Stream Analytics is biztosít beépített ellenőrzőpontokat a feladat állapotának fenntartásához és megismételhető eredményeket biztosít.

Felügyelt szolgáltatásként a Stream Analytics eseményfeldolgozás egy 99,9 %-os rendelkezésre állást a percenkénti szintjén a garantálja. További információkért lásd: a [Stream Analytics SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) lapot. 

### <a name="security"></a>Biztonsági

Biztonság az Azure Stream Analytics titkosítja a bejövő és kimenő kommunikáció minden esetben, valamint támogatja a TLS 1.2. Beépített ellenőrzőpontokat is titkosítva van. Stream Analytics nem tárolja a bejövő adatokat, mivel minden feldolgozás a memóriában történik.

### <a name="compliance"></a>Megfelelőség

Az Azure Stream Analytics a következő több megfelelőségi tanúsítványok leírtak szerint a [áttekintése az Azure megfelelőségi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Teljesítmény

A Stream Analytics másodpercenként több millió eseményt képes feldolgozni, és alacsony késleltetéssel képes kézbesíteni az eredményeket. Lehetővé teszi, hogy a vertikális és horizontális felskálázás nagy valós idejű és összetett eseményfeldolgozó alkalmazások kezeléséhez. Stream Analytics támogatja a teljesítményt particionálásával, amely lehetővé teszi az összetett lekérdezések párhuzamosíthatók és végrehajthatók több streamelési csomóponton. Az Azure Stream Analytics épül [Trill](https://github.com/Microsoft/Trill), mégpedig a Microsoft Research fejlesztett egy nagy teljesítményű memórián belüli streamelési elemzési motorjára.

## <a name="next-steps"></a>További lépések

A cikk az Azure Stream Analytics szolgáltatásról nyújtott áttekintést. Ezután megismerheti a részleteket, és létrehozhatja első Stream Analytics-feladatát:

* [Stream Analytics-feladat létrehozása az Azure Portalon](stream-analytics-quick-create-portal.md).
* [Stream Analytics-feladat létrehozása az Azure PowerShell használatával](stream-analytics-quick-create-powershell.md).
* [Stream Analytics-feladat létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md).
* [Stream Analytics-feladat létrehozása a Visual Studio Code használatával](quick-create-vs-code.md).
