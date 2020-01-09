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
ms.openlocfilehash: 3399cc47af20df05d6315a4bd77965f799eaf5f9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426174"
---
# <a name="what-is-azure-stream-analytics"></a>Mi az az Azure Stream Analytics?

A Azure Stream Analytics egy valós idejű elemzési és összetett eseményvezérelt motor, amely nagy mennyiségű, egyszerre több forrásból származó gyors adatfolyam-adatok elemzésére és feldolgozására szolgál. A minták és kapcsolatok a számos bemeneti forrásból kinyert információk, például az eszközök, érzékelők, kattintássorozatokból, közösségi média-hírcsatornák és alkalmazások alapján azonosíthatók. Ezek a minták felhasználhatók a műveletek indításához és a munkafolyamatok elindításához, például riasztások létrehozásához, az információk jelentéskészítő eszközhöz való etetéséhez vagy az átalakított adatok későbbi használatra történő tárolásához. A Stream Analytics Azure IoT Edge futtatókörnyezetben is elérhető, és ugyanazokat a pontos nyelvet vagy szintaxist támogatja, mint a felhő. 

A következő forgatókönyvek példákat mutatnak a Azure Stream Analytics használatára:

* Valós idejű telemetria streamek elemzése a IoT-eszközökről
* Webnaplók és kattintássorozatok elemzése
* Térinformatikai elemzés flottakezeléshez és vezető nélküli járművekhez
* Távoli monitorozás és prediktív karbantartás a nagy értékű eszközökön
* A pénztári adatok valós idejű elemzése készletszabályozáshoz és anomáliadetektáláshoz

## <a name="how-does-stream-analytics-work"></a>Hogyan működik a Stream Analytics?

Az Azure Stream Analytics feladatok egy bemenetből, lekérdezésből és kimenetből állnak. Stream Analytics az Azure Event Hubs, az Azure IoT Hub vagy az Azure Blob Storage adatait tölti be. A lekérdezés, amely SQL-lekérdezési nyelven alapul, egyszerűen szűrheti, rendezheti, összesítheti és összekapcsolhatja az adatfolyam-adatokat egy adott időszakban. Ezt az SQL nyelvet a JavaScript és C# a felhasználó által definiált függvények (UDF) használatával is kiterjesztheti. Az összesítési műveletek egyszerű nyelvi szerkezeteken és/vagy konfigurációkon keresztül történő előírásával egyszerűen beállíthatja az események rendezési beállításait és időtartamát.

Minden feladathoz tartozik egy kimenet az átalakított adatokhoz, és szabályozható, hogy mi történik az elemzett információkra adott válaszként. Megteheti például a következőket:

* Adatokat küldhet a szolgáltatásoknak, például a Azure Functionsnak, Service Bus témákat vagy várólistákat a kommunikáció vagy az egyéni munkafolyamatok alsóbb rétegbeli elindításához.
* Adatküldés egy Power BI Irányítópultra a valós idejű irányítópultok számára.
* Az adatok tárolása más Azure Storage-szolgáltatásokban egy gépi tanulási modell betanítása a korábbi adatok alapján, illetve a Batch-elemzések elvégzése.

Az alábbi képen látható, hogyan történik az adatok továbbítása Stream Analytics, elemzése és elküldése más műveletekhez, például a tároláshoz vagy a bemutatóhoz:

![Stream Analytics bevezető folyamat](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Főbb képességek és előnyök

Az Azure Stream Analytics szolgáltatást könnyen használhatónak, rugalmasnak, megbízhatónak és bármely feladathoz méretezhetőnek tervezték. Több Azure-régióban is elérhető. Az alábbi ábra a Azure Stream Analytics főbb képességeit szemlélteti:

![A Stream Analytics főbb képességei](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Egyszerű használatbavétel

Azure Stream Analytics könnyen elindítható. Csak néhány kattintással csatlakozhat több forráshoz és mosogatóhoz, így végpontok közötti folyamatot hozhat létre. Stream Analytics tud csatlakozni az [azure Event Hubshoz](/azure/event-hubs/) és az [Azure IoT hubhoz](/azure/iot-hub/) a folyamatos adatfeldolgozáshoz, valamint az [Azure Blob Storage](/azure/storage/storage-introduction) -hoz a korábbi adatmennyiségek betöltéséhez. A feladatok bemenete tartalmazhat statikus vagy lassan változó hivatkozási adatokat az Azure Blob Storage-ból vagy [SQL Database](stream-analytics-use-reference-data.md#azure-sql-database) , hogy a keresési műveletek elvégzéséhez csatlakozhat a folyamatos átvitelhez.

Stream Analytics a feladatok kimenetét számos olyan tárolási rendszerre irányíthatja, mint például az [Azure Blob Storage](/azure/storage/storage-introduction), a [Azure SQL Database](/azure/sql-database/), a [Azure Data Lake Store](/azure/data-lake-store/)és az [Azure CosmosDB](/azure/cosmos-db/introduction). Futtathatja a Batch Analytics szolgáltatást a tárolt kimeneteken az Azure HDInsight, vagy elküldheti a kimenetet egy másik szolgáltatásba, például a Event Hubst a felhasználáshoz, vagy [Power bi](https://docs.microsoft.com/power-bi/) a valós idejű vizualizációhoz.

A Stream Analytics kimenetek teljes listájáért lásd: [Azure stream Analyticsokból származó kimenetek megismerése](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Programozói termelékenység

Azure Stream Analytics egy egyszerű SQL-alapú lekérdezési nyelvet használ, amely hatékony időbeli korlátozásokkal bővült a mozgásban lévő adatelemzéshez. A feladattranszformációk a [Stream Analytics egyszerű, deklaratív lekérdező nyelvével](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) definiálhatók, amely lehetővé teszi, hogy egyszerű SQL-szerkezetekkel hozzon létre összetett historikus lekérdezéseket. Mivel Stream Analytics lekérdezés nyelve konzisztens az SQL nyelvével, az SQL megfelelő ismerete elegendő a feladatok létrehozásának megkezdéséhez. A feladatokat olyan fejlesztői eszközök használatával is létrehozhatja Stream Analytics, mint például a Azure PowerShell, a [Visual Studio Tools](stream-analytics-tools-for-visual-studio-install.md), a [stream Analytics Visual Studio Code bővítmény](quick-create-vs-code.md)vagy a Azure Resource Manager sablonok. A fejlesztői eszközök használatával offline módon fejleszthet transzformációs lekérdezéseket és a [folyamatos integrációs és folyamatos teljesítési folyamattal](stream-analytics-tools-for-visual-studio-cicd.md) küldhet be feladatokat az Azure-ba.

A Stream Analytics lekérdezési nyelv a streaming adatok elemzéséhez és feldolgozásához használható függvények széles skáláját kínálja. Ez a lekérdezési nyelv támogatja az egyszerű adatmanipulációt, az összesítési funkciókat és az összetett térinformatikai funkciókat. Szerkesztheti a lekérdezéseket a portálon, és tesztelheti azokat az élő streamből kinyert mintaadatok használatával.

A lekérdező nyelv lehetőségei függvények definiálásával és meghívásával tovább bővíthetők. A Azure Machine Learningban megadhatja az Azure Machine Learning megoldások előnyeit, valamint a JavaScript vagy C# a felhasználó által definiált függvények (UDF) vagy a felhasználó által definiált összesítések integrálását az stream Analytics lekérdezés részeként összetett számítások végrehajtásához.

## <a name="fully-managed"></a>Teljes körű felügyelet

Az Azure Stream Analytics egy teljes körűen felügyelt Azure-beli kiszolgáló nélküli (PaaS-) ajánlat. A feladatok futtatásához nem kell hardvert kiépítenie vagy fürtöket kezelnie. A Azure Stream Analytics teljes mértékben felügyeli a feladatot úgy, hogy összetett számítási fürtöket hoz létre a felhőben, és gondoskodik a feladat futtatásához szükséges teljesítmény finomhangolásáról. Az Azure Event Hubs és az Azure IoT Hub integrációja lehetővé teszi, hogy a feladatok másodpercenként több millió eseményt is feldolgozzanak a különböző forrásokból, a csatlakoztatott eszközöket, a kattintássorozatokból és a naplófájlokat is beleértve. Event Hubs particionálási funkciójának használatával a számításokat logikai lépésekre particionálhatja, amelyek mindegyike tovább particionálható a méretezhetőség növelése érdekében.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Futtatás a felhőben vagy az intelligens Edge-ben

A Azure Stream Analytics a felhőben, a nagyméretű elemzések futtatásához, vagy a IoT Edge-on való futtatásához rendkívül alacsony késésű elemzésekhez. Azure Stream Analytics ugyanazt a lekérdezési nyelvet használja a felhőben és a peremen is, ami lehetővé teszi, hogy a fejlesztők valóban hibrid architektúrákat hozzanak létre az adatfolyam-feldolgozáshoz. 

## <a name="low-total-cost-of-ownership"></a>Alacsony teljes tulajdonlási díj

Felhőszolgáltatásként a Stream Analytics költségoptimalizált. Nincsenek előzetes költségek – csak a [felhasznált folyamatos átviteli egységekért](stream-analytics-streaming-unit-consumption.md)és a feldolgozott adatmennyiségért kell fizetnie. Nincs szükség kötelezettségvállalásra vagy fürtök kiosztására, és az üzleti igényeknek megfelelően akár fel, akár le is méretezheti a feladatokat.

## <a name="mission-critical-ready"></a>Feladat – kritikusan kész

Azure Stream Analytics világszerte több régióban érhető el, és úgy tervezték, hogy kritikus fontosságú számítási feladatokat futtasson a megbízhatósági, biztonsági és megfelelőségi követelmények támogatásával.

### <a name="reliability"></a>Megbízhatóság

Azure Stream Analytics garantálja a pontosan egyszeri esemény-feldolgozást és az események legalább egyszeri kézbesítését, így az események soha nem vesznek el. A pontosan egyszeri feldolgozás garantált a kiválasztott kimenettel, az [esemény kézbesítési garanciái](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)között leírtak szerint.

A Azure Stream Analytics beépített helyreállítási képességekkel rendelkezik abban az esetben, ha egy esemény kézbesítése meghiúsul. A Stream Analytics beépített ellenőrzőpontokat is biztosít a feladatok állapotának fenntartásához, és megismételhető eredményeket biztosít.

Felügyelt szolgáltatásként Stream Analytics az események feldolgozását az 99,9%-os rendelkezésre állással biztosítja percenkénti részletességgel. További információ: [stream Analytics SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) oldal. 

### <a name="security"></a>Biztonság

A biztonság szempontjából Azure Stream Analytics titkosítja az összes bejövő és kimenő kommunikációt, és támogatja a TLS 1,2-et. A beépített ellenőrzőpontok is titkosítva vannak. A Stream Analytics nem tárolja a bejövő adatmennyiséget, mert az összes feldolgozás a memóriában történik.

### <a name="compliance"></a>Megfelelőség

Azure Stream Analytics az [Azure megfelelőségének áttekintése](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)című témakörben leírtak szerint több megfelelőségi tanúsítványt követ. 

## <a name="performance"></a>Teljesítmény

A Stream Analytics másodpercenként több millió eseményt képes feldolgozni, és az eredményeket rendkívül alacsony késéssel kézbesítheti. Lehetővé teszi a vertikális felskálázást és a méretezést a nagy valós idejű és összetett eseménykezelő alkalmazások kezeléséhez. Stream Analytics a particionálás nagyobb teljesítményt nyújt, és lehetővé teszi, hogy az összetett lekérdezések párhuzamosak legyenek, és több folyamatos átviteli csomóponton fussanak. A Azure Stream Analytics a [trilla](https://github.com/Microsoft/Trill)-ra épülő, nagy teljesítményű, memóriabeli adatfolyam-elemzési motor, amely a Microsoft Research szolgáltatással együttműködve lett kifejlesztve.

## <a name="next-steps"></a>Következő lépések

A cikk az Azure Stream Analytics szolgáltatásról nyújtott áttekintést. Ezután megismerheti a részleteket, és létrehozhatja első Stream Analytics-feladatát:

* [Stream Analytics-feladat létrehozása az Azure Portalon](stream-analytics-quick-create-portal.md).
* [Stream Analytics-feladat létrehozása az Azure PowerShell használatával](stream-analytics-quick-create-powershell.md).
* [Hozzon létre egy stream Analytics feladatot a Visual Studio használatával](stream-analytics-quick-create-vs.md).
* [Hozzon létre egy stream Analytics feladatot a Visual Studio Code használatával](quick-create-vs-code.md).
