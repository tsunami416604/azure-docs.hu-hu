---
title: Az Azure Stream Analytics előzetes verziójú funkciók
description: Ez a cikk, amely jelenleg előzetes verzióban az Azure Stream Analytics szolgáltatásokat sorolja fel.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 3e99263f6bf472c256e1747b8567249bbd62a445
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969623"
---
# <a name="azure-stream-analytics-preview-features"></a>Az Azure Stream Analytics előzetes verziójú funkciók

Ez a cikk összefoglalja a jelenleg az Azure Stream Analytics előzetes verzióban elérhető összes funkciót. Előzetes verziójú funkciók használata az éles környezetben nem ajánlott.

## <a name="public-previews"></a>Nyilvános előzetes verziók

A következő funkciók vannak a nyilvános előzetes verzióban érhető el. Kihasználhatja ezeket a szolgáltatásokat még ma, de ne használja éles környezetben.

### <a name="online-scaling"></a>Online skálázás

Online skálázás esetén nem kell leállítania a feladatot, ha módosítania kell a SU-foglalást. A futó feladatok SU kapacitása a Leállítás nélkül növelhető vagy csökkenthető. Ez a régóta futó, üzleti szempontból kritikus fontosságú folyamatok felhasználói ígéretére épül, Stream Analytics jelenleg kínál. További információ: [Azure stream Analytics folyamatos átviteli egység konfigurálása](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>C#Egyéni deszerializálók
A fejlesztők kihasználhatják a Azure Stream Analytics hatékonyságát, hogy Protopuf, XML-ben vagy bármilyen egyéni formátumban dolgozzák fel az adatfeldolgozást. A C#alkalmazásban [Egyéni, de szerializálók](custom-deserializer-examples.md) is létrehozhatók, amelyek a Azure stream Analytics által fogadott események deszerializálása céljából használhatók.

### <a name="extensibility-with-c-custom-code"></a>Bővíthetőség C# egyéni kóddal

A felhőben vagy IoT Edge Stream Analytics modulokat létrehozó fejlesztők írhatnak vagy felhasználhatnak egyéni C# függvényeket, és közvetlenül a lekérdezésben hívhatják őket [felhasználó által definiált függvények](stream-analytics-edge-csharp-udf-methods.md)használatával.


### <a name="debug-query-steps-in-visual-studio"></a>Lekérdezési lépések hibakeresése a Visual Studióban

Az adatdiagramon könnyen megtekintheti a köztes sort, ha helyi tesztelést végez a Visual Studio Azure Stream Analytics eszközein. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Helyi tesztelés élő adatértékekkel a Visual Studio Code-ban

A feladatokat az Azure-ba való elküldés előtt tesztelheti a helyi gépen élő adatokon. Az egyes tesztelési iterációk átlagosan kevesebb mint két-három másodpercet vesznek igénybe, ami nagyon hatékony fejlesztési folyamatot eredményez.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code a Azure Stream Analyticshoz

Azure Stream Analytics feladatokat a Visual Studio Code-ban lehet létrehozni. Tekintse meg a [vs Code – első lépéseket ismertető oktatóanyagot](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="integration-with-azure-machine-learning"></a>Az Azure Machine Learning-integráció

Stream Analytics-feladatok a Machine Learning (gépi tanulás) functions skálázhatja. Ha többet szeretne megtudni arról, hogyan használható az Stream Analytics-feladatban az ML függvények használata, látogasson el [a stream Analytics-feladatok méretezése Azure Machine learning függvényekkel](stream-analytics-scale-with-machine-learning-functions.md)című témakörre. Tekintse meg a valós forgatókönyveket, amelyekkel [Azure stream Analytics és Azure Machine learning használatával is elvégezheti a hangulat elemzését](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Élő adatok tesztelése a Visual Studióban

Az Azure Stream Analytics Visual Studio-eszközök javíthatja a helyi tesztelési funkciója, amely lehetővé teszi, hogy tesztelje, az élő esemény Streamek felhőbeli forrásokból, például az Event Hub vagy az IoT hub-lekérdezéseket. Ismerje meg, hogyan [tesztelheti helyileg az élő információkat a Visual studióhoz készült Azure stream Analytics Tools használatával](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>.NET felhasználó által definiált függvények az IoT Edge-ben

.NET standard felhasználó által definiált függvények .NET Standard kódot futtathat a streamelési folyamat részeként. Egyszerű C# osztály létrehozása, vagy a teljes projekt és a szalagtárak importálása. Teljes szerzői műveletek, és felület hibakeresése a Visual Studio használata támogatott. További információért látogasson el [a .NET szabványú, felhasználó által definiált függvények fejlesztése Azure stream Analytics Edge-feladatokhoz](stream-analytics-edge-csharp-udf-methods.md)című témakörben.

## <a name="other-previews"></a>Egyéb előzetes verziók

A következő funkciók az előzetes verzióban is elérhetők a kérelemben.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Valós idejű nagy teljesítményű pontozás a Azure Machine Learning által felügyelt egyéni ML-modellekkel

Azure Stream Analytics támogatja a nagy teljesítményű, valós idejű pontozást azáltal, hogy kihasználja a Azure Machine Learning által kezelt egyéni, előre betanított Machine Learning-modelleket, és az Azure Kubernetes szolgáltatásban (ak) vagy Azure Container Instances (ACI) futtatja a munkafolyamatot Ez nem igényli a kód írását. [Regisztrálás](https://aka.ms/asapreview1) az előzetes verzióra

### <a name="support-for-azure-stack"></a>Azure Stack támogatása
Ez a funkció engedélyezve van a Azure IoT Edge futtatókörnyezetben, a kihasználja az egyéni Azure Stack funkciókat, például a helyi bemenetek natív támogatását és a Azure Stack futó kimeneteket (például Event Hubs, IoT Hub, Blob Storage). Ez az új integráció lehetővé teszi olyan hibrid architektúrák elkészítését, amelyek a létrehozásuk helyétől, a késés csökkentése és az elemzések maximalizálása mellett is elemezhetik adataikat.
Ez a funkció lehetővé teszi olyan hibrid architektúrák létrehozását, amelyek a létrehozásuk helyétől, a késés csökkentése és az elemzések maximalizálása mellett is elemezhetik az adatait. Regisztrálnia [kell erre az előzetes](https://aka.ms/asapreview1) verzióra.
