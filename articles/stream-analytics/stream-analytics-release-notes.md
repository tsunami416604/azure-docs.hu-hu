---
title: A Stream Analytics kibocsátási megjegyzései |} Microsoft Docs
description: Stream Analytics kibocsátási megjegyzései
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 5e59f893-cd2c-43fb-9eca-c146ce637203
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 05/03/2017
ms.author: jeanb
ms.openlocfilehash: 645c9e7014beba0312de3784bbc04734927929a9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="stream-analytics-release-notes"></a>A Stream Analytics kibocsátási megjegyzései

## <a name="notes-for-06142017-update-of-stream-analytics-tools-for-visual-studio"></a>Megjegyzések a Visual Studio 06/14/2017 Stream Analytics eszközök frissítése
A frissítés a Visual Studio eszközök szolgál. Ebben a kiadásban a következő új funkciókat tartalmazza:

| Beosztás | Leírás |
| --- | --- |
| Java parancsprogram-szerkesztő támogatás |A natív java parancsfájl szerkesztő tapasztalattal a java-parancsfájl funkciók létrehozása után élvezheti.|
| Feladat futtatása idő hibaüzenet megjelenítése | Ha feladat végrehajtása során futásidejű hibák, megtekintheti azokat a hiba lapon a megjelenített időszak beállításával. Alapértelmezés szerint az utolsó 30 percig a hibaüzenetek jeleníti meg. |
| Helyi tesztelési bemeneti CSV és az Avro támogatása | Mellett JSON most már használhatja CSV és az Avro formátum helyi tesztelési bemeneti.|

## <a name="notes-for-05032017-update-of-stream-analytics"></a>Megjegyzések a Stream Analytics-05/03/2017 frissítése
A frissítés a hibaelhárítási dokumentációja kiadáshoz van.

A [hibaelhárítási útmutatója](stream-analytics-troubleshooting-guide.md) és egyéb docs kiadott. Tekintse át, visszajelzés Üdvözöljük.

## <a name="notes-for-04242017-update-of-stream-analytics-tools-for-visual-studio"></a>Megjegyzések a Visual Studio 04/24/2017 Stream Analytics eszközök frissítése
A frissítés a Visual Studio eszközök szolgál. Ebben a kiadásban a következő új funkciókat tartalmazza:

| Beosztás | Leírás |
| --- | --- |
| Tekintse meg a Visual Studio helyi tesztelési eredménye | A kimeneti eredmények a helyi vizsgálat megtekintéséhez nyomja le az ENTER BILLENTYŰT a konzol kimeneti ablakban, vagy zárja be. Az eredmény egy ablak a Visual Studio tábla formátumban jelenik meg. |
| Kimeneti helyi eredmények JSON formátumban | Egy helyi teszt futtatásakor a kimeneti eredmények jön létre, mint a JSON és a CSV fájlformátum. |
| Blob vagy tábla tárolási bemenetek/kimenetek adatok megtekintése | Kattintson duplán egy blob vagy tábla tároló bemeneti/kimeneti a feladatok nézetben, egyszerűen megtekintheti a Visual Studio található adatok közül. |
| A bemenetek/kimenetek hibaüzenet megjelenítése | Ha a feladat bemeneti vagy kimeneti kapcsolódó futásidejű hibák vannak, ahol is, és a részletes hibaüzenet megtekintéséhez vigye feladat diagram is látható.|


## <a name="notes-for-02012017-release-of-stream-analytics"></a>A Stream Analytics 02/01/2017 kibocsátási megjegyzései
Ez tartalmazza a következő frissítést:

| Beosztás | Leírás |
| --- | --- |
| Introducing JavaScript felhasználói függvény (UDF) |[Java-felhasználó által definiált függvényeket](stream-analytics-javascript-user-defined-functions.md) is elérhető, a lekérdezések létrehozásáról további rugalmasságot biztosít. |
| Tools for Visual Studio és a Stream Analytics bemutatása |[A Visual Studio eszközök](stream-analytics-tools-for-visual-studio.md) hibakeresési és nagyobb segédprogram érhető el. |
| Diagnosztikai naplózás bemutatása |[Diagnosztikai naplózás](stream-analytics-job-diagnostic-logs.md) készen áll a további hibaelhárítási lehetőségekkel. |
| A földrajzi funkciók bemutatása |[A földrajzi funkciók](http://msdn.microsoft.com/library/mt778980(Azure.100).aspx) általában érhető el. |

## <a name="notes-for-04152016-release-of-stream-analytics"></a>A Stream Analytics 04/15/2016 kibocsátási megjegyzései
Ez tartalmazza a következő frissítést:

| Beosztás | Leírás |
| --- | --- |
| A Power BI-kimenettel általánosan rendelkezésre álló |[A Power BI-kimenettel](stream-analytics-power-bi-dashboard.md) általában érhető el. A Power BI a 90 napos engedélyezési lejárati el lett távolítva. Forgatókönyvek, ahol engedélyezési kell megújítani a további információkért lásd: a [újra a portálon](stream-analytics-power-bi-dashboard.md#renew-authorization) szakaszában a Power BI-irányítópultot létrehozni. |

## <a name="notes-for-03032016-release-of-stream-analytics"></a>A Stream Analytics 2016. 03/03 kiadási megjegyzései
Ez tartalmazza a következő frissítést:

| Beosztás | Leírás |
| --- | --- |
| Új Stream Analytics lekérdezési nyelv elemek |Most már SAQL [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "GetType MSDN oldalon"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST MSDN oldalon") és [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN oldalon"). |

## <a name="notes-for-12102015-release-of-stream-analytics"></a>A Stream Analytics 12/10/2015 kiadási megjegyzései
Ez tartalmazza a következő frissítést:

| Beosztás | Leírás |
| --- | --- |
| REST API-verzió frissítése |A REST API-verzió 2015-10-01 környezetébe. Az MSDN Webhelyén, a részletek megtalálhatók [Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx) és [Stream Analytics a Machine Learning-integráció](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md). |
| Az Azure Machine Learning-integráció |Ebben a kiadásban támogatja az Azure Machine Learning felhasználó által definiált függvények származnak. Tekintse meg a [oktatóanyag](stream-analytics-machine-learning-integration-tutorial.md) további információt, valamint a [általános blog közlemény](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx). |

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Megjegyzések a Stream Analytics 11/12/2015 kiadása
Ez tartalmazza a következő frissítést:

| Beosztás | Leírás |
| --- | --- |
| Válassza ki az új módon működnek |Válassza ki a Stream Analytics kibővített engedélyezése *, a beágyazott rekord tulajdonságelérő. További információkért lásd: [ http://msdn.microsoft.com/library/mt622759.aspx ] (http://msdn.microsoft.com/library/mt622759.aspx "összetett adattípusok"). |

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Megjegyzések a Stream Analytics 2015/10/22-es kiadása
Ez tartalmazza a következő frissítéseket:

| Beosztás | Leírás |
| --- | --- |
| További lekérdezési nyelv funkciók |A Stream Analytics lekérdezési nyelv bővített által többek között a következő szolgáltatásokat: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [felső határ](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [EMELET](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [bejelentkezési](https://msdn.microsoft.com/library/azure/mt605290.aspx), [SZÖGLETES](https://msdn.microsoft.com/library/azure/mt605288.aspx), és [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx). |
| Összesített korlátozások eltávolítása |Ebben a kiadásban a korlátozás 15 aggregátumok eltávolítja a lekérdezésben. Már létezik lekérdezésenként összesítések száma nincs korlátozva. |
| A hozzáadott csoport által System.Timestamp szolgáltatás |A [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) funkció mostantól lehetővé teszi vagy window_type vagy [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx). |
| Az Átfedésmentes és a Hopping windows hozzáadott ELTOLÁSA |Alapértelmezés szerint [Átfedésmentes](https://msdn.microsoft.com/library/azure/dn835055.aspx) és [Hopping](https://msdn.microsoft.com/library/azure/dn835041.aspx) windows igazodnak leállása ellen (1/1/0001 12:00:00-kor UTC). Az új (nem kötelező) paraméter "offsetsize" lehetővé teszi, hogy egyéni eltolását (vagy igazítás) megadásával. |

## <a name="notes-for-09292015-release-of-stream-analytics"></a>A Stream Analytics 09/29/2015 kibocsátási megjegyzései
Ez tartalmazza a következő frissítéseket:

| Beosztás | Leírás |
| --- | --- |
| Az Azure IoT Suite nyilvános előzetes verzió |A Stream Analytics a nyilvános előzetes verziójának az Azure IoT Suite része. |
| Azure portál-integráció |Az Azure portálon jelenléte mellett a Stream Analytics mostantól integrálva van a a [Azure-portálon](https://azure.microsoft.com/overview/preview-portal/). Vegye figyelembe, hogy a Stream Analytics funkciót a betekintő portálon jelenleg található funkciók egy részét érhető el az Azure-portálon, a böngészőben a lekérdezéstesztelés, támogatása nélkül a Power BI kimeneti konfiguráció, és tallózással vagy új bemeneti és kimeneti létrehozása erőforrások az előfizetések rendelkezik hozzáféréssel. |
| Cosmos DB kimeneti támogatása |Stream Analytics-feladatok most kimenő [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). |
| IoT Hub bemeneti támogatása |Stream Analytics-feladatok most is betöltik az adatokat az IoT-központok. |
| TIMESTAMP BY heterogén eseményekhez. |Ha egyetlen adatfolyammá több eseménytípus időbélyegeket rendelkező különböző mezőket tartalmaz, most már használhatja [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) kifejezésekkel a adja meg a minden esetben másik Timestamp típusú mezőket. |

## <a name="notes-for-09102015-release-of-stream-analytics"></a>A Stream Analytics 09/10/2015 kibocsátási megjegyzései
Ez tartalmazza a következő frissítéseket:

| Beosztás | Leírás |
| --- | --- |
| Power bi csoportok támogatása |Ahhoz, hogy más Power BI-felhasználókkal adatmegosztásra, Stream Analytics-feladatok most írhat [Power bi csoportok](stream-analytics-define-outputs.md#power-bi) belül a Power BI-fiókjába. |

## <a name="notes-for-08202015-release-of-stream-analytics"></a>A Stream Analytics 2015-08/20 kibocsátási megjegyzései
Ez tartalmazza a következő frissítéseket:

| Beosztás | Leírás |
| --- | --- |
| A hozzáadott utolsó függvény |A [utolsó](http://msdn.microsoft.com/library/mt421186.aspx) funkció már elérhető a Stream Analytics-feladatok, így lehetővé teszi a legutóbbi esemény egy adott időkereten belül eseményfelhasználó beolvasása. |
| Új tömb funkciók |A tömb funkciók [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) és [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) érhető el. |
| Új rekord funkciók |Jegyezze fel funkciók [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) és [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) érhető el. |

## <a name="notes-for-07302015-release-of-stream-analytics"></a>A Stream Analytics 07/30/2015 kiadási megjegyzései
Ez tartalmazza a következő frissítéseket:

| Beosztás | Leírás |
| --- | --- |
| A Power BI szervezeti azonosítójához különválik Azure azonosítója |Ez a funkció lehetővé teszi, hogy [Power BI-kimenet](stream-analytics-power-bi-dashboard.md) ASA feladatok az összes Azure-fiók típusa (Live Id vagy szervezeti azonosító). Ezenkívül egy szervezeti azonosítójához rendelkezik Azure-fiókját, és használjon egy másik, amelyek engedélyezik a Power BI-kimenet. |
| Service Bus-üzenetsorok kimeneti támogatása |[Service Bus-üzenetsorok](stream-analytics-define-outputs.md#service-bus-queues) kimenetek is elérhetők a Stream Analytics-feladatok. |
| Service Bus-üzenettémakörök kimeneti támogatása |[Service Bus-üzenettémakörök](stream-analytics-define-outputs.md#service-bus-topics) kimenetek is elérhetők a Stream Analytics-feladatok. |

## <a name="notes-for-07092015-release-of-stream-analytics"></a>A Stream Analytics 07/09/2015 kibocsátási megjegyzései
Ez tartalmazza a következő frissítéseket:

| Beosztás | Leírás |
| --- | --- |
| Egyéni Blob kimeneti particionálás |BLOB storage kimenetek most engedélyezése egy lehetőség, hogy a gyakoriságot adja meg a blobok írt kimenet és a struktúra és a kimeneti adatok elérési útja gyökérmappa-szerkezetében formátuma. |

## <a name="notes-for-05032015-release-of-stream-analytics"></a>A Stream Analytics 2015-05/03 kiadási megjegyzései
Ez tartalmazza a következő frissítéseket:

| Beosztás | Leírás |
| --- | --- |
| A soron tűrési nagyobb maximális értéke |A soron of tűrési maximális mérete már 59:59 (PP: mm) |
| JSON kimeneti formátum: Sorban, egymástól szóközzel vagy tömb |Most nincs lehetősége Blob-tároló vagy az Eseményközpont kimeneti tömbként vagy JSON-objektumok vagy egy új sorral a JSON-objektumok megjelenítése. |

## <a name="notes-for-04162015-release-of-stream-analytics"></a>A Stream Analytics 2015-04/16 kibocsátási megjegyzései
| Beosztás | Leírás |
| --- | --- |
| Késleltetés az Azure Storage-fiók konfigurációja |A Stream Analytics-feladat létrehozása régióban először esetén hozzon létre egy új tárfiókot, vagy adjon meg egy meglévő fiókot az adott régióban Stream Analytics-feladatok figyelése kéri. Miatt várakozási ideje a figyelést ugyanabban a régióban belül 30 perc kérése a második Storage-fiók helyett a Tárfiók figyelő legördülő nemrég konfigurált szerkezetével jelenít meg egy másik Stream Analytics-feladat létrehozása állítja be. Elkerülése érdekében a szükségtelen Storage-fiók létrehozása, várjon a feladat létrehozása a régióban további feladatok az adott régióban üzembe helyezése előtt először után 30 percet. |
| Feladat frissítése |Jelenleg a Stream Analytics nem támogatja a működés közbeni módosítását a definícióban vagy egy futó feladat konfigurációját. Ahhoz, hogy a bemeneti, kimeneti, lekérdezés, méretezési vagy egy futó feladat konfigurációjának módosításához először le kell állítania a feladatot. |
| A bemeneti forrás következtetni adattípusok |A CREATE TABLE utasítás nem használható, ha a bemeneti típus bemeneti formátum van származtatva, például a fürt megosztott kötetei szolgáltatás minden mező karakterlánc. Mezőket kell explicit módon kell konvertálni a függvény használatával típusa eltérő hibák elkerülése érdekében a megfelelő típusú. |
| Hiányzó mezőket null értékként vannak outputted |A kimeneti esemény null értéket eredményez hivatkozik, amely azonban nem szerepel a bemeneti forrás mező. |
| AZ utasítások meg kell előznie KIVÁLASZTÓ utasítást |A lekérdezés KIVÁLASZTÓ utasítást kell jelentkezniük az utasítás definiált segédlekérdezések kell követnie. |
| Kevés a memória probléma |Adatfolyam-továbbítási Analytics-feladatok soron események, illetve állapot nagy mennyiségű okozhat a feladat futtatásához kevés a memória, ami azt eredményezi, a feladatok fenntartása összetett lekérdezések nagy tűréssel indítsa újra. A feladat műveleti naplói a kezdési és befejezési műveletek láthatók. Ez a viselkedés elkerülése érdekében kiterjesztése a lekérdezés több partícióról között. Egy későbbi kiadásban ez a korlátozás van tárgyalt terhelése teljesítménye érintett feladatokra őket újraindítása helyett. |
| Nagy blob bemeneti hasznos időbélyeg nélkül kevés a memória problémát okozhat |Nagy fájlok blobtárolóból fel okozhat a Stream Analytics-feladatok összeomlását, ha időbélyegmezővel nem szerepel a TIMESTAMP BY keresztül. A probléma elkerülése érdekében, tartsa minden egyes blob mérete legfeljebb 10 MB-nál. |
| SQL-adatbázis esemény kötet korlátozása |SQL-adatbázis egy kimeneti célként használatakor kimeneti adatok nagyon nagy mennyiségű okozhat a Stream Analytics-feladat időtúllépésbe kerül. A probléma megoldásához, csökkenthető a kimeneti összesítések vagy szűrési operátorokhoz használatával, vagy Azure Blob-tároló vagy az Event Hubs egy kimeneti célként Válasszon helyette. |
| Power bi-adatkészletek csak egy táblát tartalmazhat |Power bi nem támogatja a megadott adatkészletben több tábla. |

## <a name="get-help"></a>Segítség kérése
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
