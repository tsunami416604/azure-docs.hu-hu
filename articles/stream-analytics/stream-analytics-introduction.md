---
title: Az Azure Stream Analytics áttekintése
description: Ismerje meg a Stream Analytics nevű felügyelt szolgáltatást, amely segít valós időben elemezni az eszközök internetes hálózatáról (IoT) származó streamadatokat.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: f15a4605d28beaf97e877f337051a2ec13148a41
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80235769"
---
# <a name="what-is-azure-stream-analytics"></a>Mi az az Azure Stream Analytics?

Az Azure Stream Analytics egy valós idejű elemzési és összetett eseményfeldolgozó motor, amelyet arra terveztek, hogy egyszerre nagy mennyiségű, több forrásból származó gyors streamelési adatot elemezzen és dolgozzon fel. A minták és kapcsolatok számos bemeneti forrásból , például eszközökből, érzékelőkből, kattintásfolyamokból, közösségi média hírcsatornákból és alkalmazásokból kinyert információkban azonosíthatók. Ezek a minták műveletek indítására és munkafolyamatok kezdeményezésére használhatók, például riasztások létrehozása, információk adagolása egy jelentéskészítő eszköz számára, vagy az átalakított adatok későbbi felhasználás céljából történő tárolása. Emellett a Stream Analytics elérhető az Azure IoT Edge futásidejében, és támogatja ugyanazt a pontos nyelvet vagy szintaxist, mint a felhő. 

A következő forgatókönyvek példák arra, hogy mikor használhatja az Azure Stream Analytics szolgáltatást:

* Valós idejű telemetriai adatfolyamok elemzése IoT-eszközökről
* Webnaplók és kattintássorozatok elemzése
* Térinformatikai elemzés flottakezeléshez és vezető nélküli járművekhez
* A nagy értékű eszközök távfelügyelete és prediktív karbantartása
* A pénztári adatok valós idejű elemzése készletszabályozáshoz és anomáliadetektáláshoz

## <a name="how-does-stream-analytics-work"></a>Hogyan működik a Stream Analytics?

Az Azure Stream Analytics-feladat egy bemeneti, lekérdezési és egy kimeneti. A Stream Analytics adatokat fog beaz Azure Event Hubsból, az Azure IoT Hubból vagy az Azure Blob Storageból. A lekérdezés, amely az SQL lekérdezési nyelv, segítségével egyszerűen szűrheti, rendezheti, összesítheti és egyesítheti a streamelési adatokat egy adott időszakban. Ezt az SQL nyelvet javascript- és C# felhasználó által definiált függvényekkel (UDF) is kiterjesztheti. Az eseményrendezési beállításokat és az időablakok időtartamát egyszerűen módosíthatja, amikor egyszerű nyelvi konstrukciók és/vagy konfigurációk révén előformázza az összesítési műveleteket.

Minden feladat rendelkezik egy kimenettel az átalakított adatokhoz, és szabályozhatja, hogy mi történjen az elemzett adatok alapján. Megteheti például a következőt:

* Adatok küldése olyan szolgáltatásokba, mint az Azure Functions, a Service Bus-témakörök vagy a várólisták a kommunikáció vagy az egyéni munkafolyamatok indikátoraktiválásához.
* Adatok küldése a Power BI irányítópultjára a valós idejű irányítópulthoz.
* Adatokat tároljon más Azure storage-szolgáltatásokban a gépi tanulási modell betanításához előzményadatok alapján, vagy végezze el a kötegelt elemzést.

Az alábbi képen látható, hogyan történik az adatok küldése a Stream Analytics szolgáltatásba, hogyan elemezhető késedelmével, és hogyan küldi el őket más műveletekre, például tárolásra vagy bemutatóra:

![Stream Analytics bevezető folyamata](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Főbb képességek és előnyök

Az Azure Stream Analytics szolgáltatást könnyen használhatónak, rugalmasnak, megbízhatónak és bármely feladathoz méretezhetőnek tervezték. Több Azure-régióban érhető el. Az alábbi képen az Azure Stream Analytics kulcsfontosságú funkciói láthatók:

![A Stream Analytics főbb képességei](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Könnyű első lépések

Az Azure Stream Analytics könnyen indítható. Csak néhány kattintással csatlakozhat több forráshoz és fogadóhoz, így végpontok között futó folyamat jön létre. A Stream Analytics csatlakozhat [az Azure Event Hubs-hoz](/azure/event-hubs/) és [az Azure IoT Hubhoz](/azure/iot-hub/) az adatbetöltés streameléséhez, valamint az [Azure Blob storage-hoz](/azure/storage/storage-introduction) a korábbi adatok betöltéséhez. A feladatbevitel statikus vagy lassan változó referenciaadatokat is tartalmazhat az Azure Blob storage-ból vagy [az SQL Database-ből,](stream-analytics-use-reference-data.md#azure-sql-database) amelyekhez keresési műveletek végrehajtásához csatlakozhat a streamelési adatokhoz.

A Stream Analytics számos tárolási rendszerre, például az [Azure Blob storage-ra,](/azure/storage/storage-introduction)az Azure SQL [Database-re,](/azure/sql-database/)az Azure Data Lake [Store-ra](/azure/data-lake-store/)és az Azure [CosmosDB-re](/azure/cosmos-db/introduction)irányíthatja a feladatkimenetet. Az Azure HDInsight segítségével kötegelt elemzéseket futtathat a tárolt kimeneten, vagy elküldheti a kimenetet egy másik szolgáltatásnak, például az Event Hubs-nak használatra vagy [a Power BI-t](https://docs.microsoft.com/power-bi/) a valós idejű megjelenítéshez.

A Stream Analytics-kimenetek teljes listáját az [Azure Stream Analytics kimeneteinek ismertetése](stream-analytics-define-outputs.md)című témakörben található.

## <a name="programmer-productivity"></a>Programozói termelékenység

Az Azure Stream Analytics egy egyszerű SQL-alapú lekérdezési nyelvet használ, amelyet hatékony időbeli korlátozásokkal bővítettek a mozgásban lévő adatok elemzéséhez. A feladattranszformációk a [Stream Analytics egyszerű, deklaratív lekérdező nyelvével](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) definiálhatók, amely lehetővé teszi, hogy egyszerű SQL-szerkezetekkel hozzon létre összetett historikus lekérdezéseket. Mivel a Stream Analytics lekérdezési nyelve konzisztens az SQL nyelvvel, az SQL ismerete elegendő a munkahelyek létrehozásához. Olyan fejlesztői eszközökkel is létrehozhat feladatokat, mint az Azure PowerShell, a [Stream Analytics Visual Studio-eszközök,](stream-analytics-tools-for-visual-studio-install.md)a [Stream Analytics Visual Studio-kódbővítmény](quick-create-vs-code.md)vagy az Azure Resource Manager-sablonok. A fejlesztői eszközök használatával offline módon fejleszthet transzformációs lekérdezéseket és a [folyamatos integrációs és folyamatos teljesítési folyamattal](stream-analytics-tools-for-visual-studio-cicd.md) küldhet be feladatokat az Azure-ba.

A Stream Analytics lekérdezési nyelv e-alapú funkciók széles skáláját kínálja a streamelési adatok elemzéséhez és feldolgozásához. Ez a lekérdezési nyelv támogatja az egyszerű adatkezelést, az összesítési függvényeket és az összetett térinformatikai függvényeket. A lekérdezéseket szerkesztheti a portálon, és tesztelheti őket az élő adatfolyamból kinyert mintaadatok használatával.

A lekérdező nyelv lehetőségei függvények definiálásával és meghívásával tovább bővíthetők. Az Azure Machine Learningben definiálhat függvényhívásokat az Azure Machine Learning-megoldások előnyeinek kihasználásához, és integrálhatja a JavaScript vagy C# felhasználó által definiált függvényeket (UDF- vagy felhasználó által definiált összesítéseket) összetett számítások végrehajtásához egy Stream Analytics-lekérdezés részeként.

## <a name="fully-managed"></a>Teljes körű felügyelet

Az Azure Stream Analytics egy teljes körűen felügyelt Azure-beli kiszolgáló nélküli (PaaS-) ajánlat. A feladatok futtatásához nem kell hardvert létesítenie vagy fürtöket kezelnie. Az Azure Stream Analytics teljes mértékben kezeli a feladatot azáltal, hogy összetett számítási fürtöket állít be a felhőben, és gondoskodik a feladat futtatásához szükséges teljesítményhangolással. Az Azure Event Hubs és az Azure IoT Hub integrációja lehetővé teszi, hogy a feladat másodpercenként több millió, több forrásból származó eseményt vegyen fel, beleértve a csatlakoztatott eszközöket, kattintási adatfolyamokat és naplófájlokat. Az Event Hubs particionálási szolgáltatásával a számítási műveleteket logikai lépésekre particionálhatja, amelyek mindegyike további particionálásra képes a méretezhetőség növelése érdekében.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Futtatás a felhőben vagy az intelligens peremhálózaton

Az Azure Stream Analytics futhat a felhőben, a nagy léptékű elemzés, vagy az IoT Edge ultra-alacsony késésű elemzés. Az Azure Stream Analytics ugyanazt a lekérdezési nyelvet használja a felhőben és a peremhálózaton is, lehetővé téve a fejlesztők számára, hogy valóban hibrid architektúrákat építsenek az adatfolyam-feldolgozáshoz. 

## <a name="low-total-cost-of-ownership"></a>Alacsony teljes tulajdonlási költség

Felhőszolgáltatásként a Stream Analytics költségoptimalizált. Nincsenek előzetes költségek - csak a [felhasznált streamelési egységekért](stream-analytics-streaming-unit-consumption.md)és a feldolgozott adatok mennyiségéért kell fizetnie. Nincs szükség kötelezettségvállalásra vagy fürtkiépítésre, és az üzleti igényeknek megfelelően fel- vagy leskálázhatja a feladatot.

## <a name="mission-critical-ready"></a>Küldetés-kritikus kész

Az Azure Stream Analytics világszerte több régióban érhető el, és a megbízhatósági, biztonsági és megfelelőségi követelmények támogatásával létfontosságú számítási feladatok futtatására szolgál.

### <a name="reliability"></a>Megbízhatóság

Az Azure Stream Analytics garantálja a pontosan egyszeri eseményfeldolgozást és az események legalább egyszeri kézbesítését, így az események soha nem vesznek el. A pontosan egyszeri feldolgozás garantált az [Eseménykézbesítési garanciákban leírtak](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)szerint kiválasztott kimenettel.

Az Azure Stream Analytics beépített helyreállítási képességekkel rendelkezik arra az esetre, ha egy esemény kézbesítése sikertelen lenne. A Stream Analytics beépített ellenőrzőpontokat is biztosít a feladat állapotának fenntartásához, és megismételhető eredményeket biztosít.

Felügyelt szolgáltatásként a Stream Analytics garantálja az eseményfeldolgozást, 99,9%-os rendelkezésre állással, egy perces részletességgel. További információt a [Stream Analytics SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) oldalán talál. 

### <a name="security"></a>Biztonság

Ami a biztonságot illeti, az Azure Stream Analytics titkosítja az összes bejövő és kimenő kommunikációt, és támogatja a TLS 1.2-t. A beépített ellenőrzőpontok is titkosítva vannak. A Stream Analytics nem tárolja a bejövő adatokat, mivel az összes feldolgozás a memóriában történik.

### <a name="compliance"></a>Megfelelőség

Az Azure Stream Analytics az [Azure-megfelelőség áttekintésében](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)ismertetett több megfelelőségi tanúsítványt követ. 

## <a name="performance"></a>Teljesítmény

A Stream Analytics másodpercenként több millió eseményt képes feldolgozni, és rendkívül alacsony késleltetéssel képes eredményeket elérni. Lehetővé teszi a nagy valós idejű és összetett eseményfeldolgozó alkalmazások kezeléséhez és horizontális felskálázásához. A Stream Analytics particionálással támogatja a nagyobb teljesítményt, lehetővé téve az összetett lekérdezések párhuzamossá tétele és végrehajtása több streamelési csomóponton. Az Azure Stream Analytics a [Trillre](https://github.com/Microsoft/Trill)épül, amely egy nagy teljesítményű, memórián belüli streamelési elemzőmotor, amelyet a Microsoft Research-lel együttműködve fejlesztettek ki.

## <a name="next-steps"></a>További lépések

A cikk az Azure Stream Analytics szolgáltatásról nyújtott áttekintést. Ezután megismerheti a részleteket, és létrehozhatja első Stream Analytics-feladatát:

* [Stream Analytics-feladat létrehozása az Azure Portalon](stream-analytics-quick-create-portal.md).
* [Hozzon létre egy Stream Analytics-feladatot az Azure PowerShell használatával.](stream-analytics-quick-create-powershell.md)
* [Stream Analytics-feladat létrehozása a Visual Studio használatával.](stream-analytics-quick-create-vs.md)
* [Stream Analytics-feladat létrehozása a Visual Studio-kód használatával.](quick-create-vs-code.md)
