---
title: Az Azure Stream Analytics előzetes verziójú funkciók
description: Ez a cikk, amely jelenleg előzetes verzióban az Azure Stream Analytics szolgáltatásokat sorolja fel.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: f3838bf6b9f7daa24c0cdb5b0c5a08d41d164530
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65561137"
---
# <a name="azure-stream-analytics-preview-features"></a>Az Azure Stream Analytics előzetes verziójú funkciók

Ez a cikk összefoglalja a jelenleg az Azure Stream Analytics előzetes verzióban elérhető összes funkciót. Előzetes verziójú funkciók használata az éles környezetben nem ajánlott.

## <a name="public-previews"></a>Nyilvános előzetes verziók

A következő funkciók vannak a nyilvános előzetes verzióban érhető el. Kihasználhatja ezeket a szolgáltatásokat még ma, de ne használja éles környezetben.

### <a name="visual-studio-code-for-azure-stream-analytics-released-may-2019"></a>A Visual Studio Code az Azure Stream Analytics (kiadott 2019. május)

A Visual Studio Code az Azure Stream Analytics-feladatok hozhatóak létre. Tekintse meg a [VS Code kezdeti lépéseket ismertető oktatóanyag](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="anomaly-detection"></a>Anomáliadetektálás

Az Azure Stream Analytics bevezeti az új machine learning-modellek támogatása *kiugrás* és *süllyedések* észlelési mellett kétirányú, lassú pozitív, és a lassú negatív trendek észlelését. További információért látogasson el [anomáliadetektálás az Azure Stream Analyticsben](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="sql-database-reference-data"></a>Az SQL Database referenciaadatok

Az Azure Stream Analytics támogatja az Azure SQL Database bemenete referenciaadatokat forrásaként. Használhatja az SQL Database referenciaadatok a Stream Analytics-feladatot az Azure Portalon, és a Visual Studióban a Stream Analytics tools for. További információkért látogasson el, [referenciaadatok használata az Azure Stream Analytics-feladat egy SQL Database-ből](sql-reference-data.md).

### <a name="integration-with-azure-machine-learning"></a>Az Azure Machine Learning-integráció

Stream Analytics-feladatok a Machine Learning (gépi tanulás) functions skálázhatja. Többet szeretne megtudni, hogyan használhatja a Stream Analytics-feladat ML-függvényeket, a Microsoft [méretezése az Azure Machine Learning-függvényekkel a Stream Analytics-feladat](stream-analytics-scale-with-machine-learning-functions.md). Tekintse meg a való életből vett forgatókönyv [hangulatelemzés végrehajtása az Azure Stream Analytics és Azure Machine Learning segítségével](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>A JavaScript, felhasználó által meghatározott összesítés

Az Azure Stream Analytics támogatja a felhasználó által definiált összesítések (UDA), javascriptben írt, amelyek lehetővé teszik az állapotalapú összetett üzleti logikát. Ismerje meg, hogyan használhatja az Uda a [Azure Stream Analytics JavaScript felhasználó által definiált összesítések](stream-analytics-javascript-user-defined-aggregates.md) dokumentációját. 

### <a name="live-data-testing-in-visual-studio"></a>Élő adatok tesztelése a Visual Studióban

Az Azure Stream Analytics Visual Studio-eszközök javíthatja a helyi tesztelési funkciója, amely lehetővé teszi, hogy tesztelje, az élő esemény Streamek felhőbeli forrásokból, például az Event Hub vagy az IoT hub-lekérdezéseket. Ismerje meg, hogyan [tesztelése helyben az Azure Stream Analytics tools for Visual Studio használatával élő adatok](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>.NET felhasználó által definiált függvények az IoT Edge-ben

.NET standard felhasználó által definiált függvények .NET Standard kódot futtathat a streamelési folyamat részeként. Egyszerű C# osztály létrehozása, vagy a teljes projekt és a szalagtárak importálása. Teljes szerzői műveletek, és felület hibakeresése a Visual Studio használata támogatott. További információért látogasson el [Azure Stream Analytics Edge-feladatok fejlesztése .NET Standard felhasználó által definiált függvények](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Egyéb előzetes verziók

A következő funkciók is elérhetők előzetes verzióban érhető el.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud-announced-may-2019"></a>C#az Azure Stream Analytics az IoT Edge és a Felhő (Announced 2019. Május) egyéni deszerializáló

A fejlesztők valósíthat meg az egyéni deserializers C# Azure Stream Analytics a fogadott események deszerializálása. Amely képes lehet deszerializálni formátumok közé Parquet, Protopuf, XML vagy bármilyen bináris formátumot. Regisztráljon az előzetes verzió [Itt](https://aka.ms/asapreview1).

### <a name="parquet-output-announced-may-2019"></a>Parquet-kimenet (bejelentett 2019. május)
Parquet egy olyan Oszlopalapú formátum hatékony big data típusú adatfeldolgozást engedélyezése. Úgy ad ki adatokat a data lake-ben Parquet formátumban, ETL streamelési power nagy méretű Azure Stream Analytics előnyeit, és futtathat kötegelt feldolgozás, gépi tanulási algoritmusok betanításához vagy előzményadatait interaktív lekérdezések futtatására. Regisztráljon az előzetes verzió [Itt](https://aka.ms/asapreview1).

### <a name="one-click-integration-with-event-hubs-announced-may-2019"></a>Egykattintásos (Announced 2019. május) az Event Hubs-integráció 
Ez az integráció akkor lesz jelenítheti meg a bejövő adatokat, és indítsa el az Event Hubs-portálról egy Stream Analytics-lekérdezés egyetlen kattintással írni. Ha készen áll a lekérdezést, lesz néhány kattintással productize, és indítsa el a valós idejű információkhoz juthat. Ez jelentősen csökkenti az idő és a valós idejű elemzési megoldások fejlesztésének költségeit. Regisztráljon az előzetes verzió [Itt](https://aka.ms/asapreview1).

### <a name="support-for-azure-stack-announced-may-2019"></a>Az Azure Stack (bejelentett 2019. május) támogatása
A szolgáltatás nincs engedélyezve az Azure IoT Edge-futtatókörnyezet, az egyéni Azure Stack-szolgáltatások, például a natív módon támogatja a helyi bemenetek használja, és kiírja a futó Azure Stack (például az Event Hubs, az IoT Hub, a Blob Storage). Az új integrációja lehetővé teszi, hogy az adatok közel generálásának helyéről, a késés csökkentése és a lehető legnagyobb insights is elemzéséhez, hibrid architektúrákat hozhat létre.
Regisztráljon az előzetes verzió [Itt](https://aka.ms/asapreview1).

