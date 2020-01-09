---
title: A Azure Stream Analytics előzetes verziójának funkciói
description: Ez a cikk a jelenleg előzetes verzióban elérhető Azure Stream Analytics-szolgáltatásokat sorolja fel.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 59bb866d7a339608555f0bb802e1716eba5d3255
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431570"
---
# <a name="azure-stream-analytics-preview-features"></a>A Azure Stream Analytics előzetes verziójának funkciói

Ez a cikk az összes olyan funkciót összegzi, amely jelenleg előzetes verzióban érhető el a Azure Stream Analytics. Az előzetes verziójú funkciók éles környezetben való használata nem ajánlott.

## <a name="public-previews"></a>Nyilvános előzetes verziók

A következő funkciók nyilvános előzetes verzióban érhetők el. Ezeket a funkciókat még ma is kihasználhatja, de nem használhatja azokat az éles környezetben.

### <a name="online-scaling"></a>Online skálázás

Online skálázás esetén nem kell leállítania a feladatot, ha módosítania kell a SU-foglalást. A futó feladatok SU kapacitása a Leállítás nélkül növelhető vagy csökkenthető. Ez a régóta futó, üzleti szempontból kritikus fontosságú folyamatok felhasználói ígéretére épül, Stream Analytics jelenleg kínál. További információ: [Azure stream Analytics folyamatos átviteli egység konfigurálása](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>C#Egyéni deszerializálók
A fejlesztők kihasználhatják a Azure Stream Analytics hatékonyságát, hogy Protopuf, XML-ben vagy bármilyen egyéni formátumban dolgozzák fel az adatfeldolgozást. A C#alkalmazásban [Egyéni, de szerializálók](custom-deserializer-examples.md) is létrehozhatók, amelyek a Azure stream Analytics által fogadott események deszerializálása céljából használhatók.

### <a name="extensibility-with-c-custom-code"></a>Bővíthetőség C# egyéni kóddal

A felhőben vagy IoT Edge Stream Analytics modulokat létrehozó fejlesztők írhatnak vagy felhasználhatnak egyéni C# függvényeket, és közvetlenül a lekérdezésben hívhatják őket [felhasználó által definiált függvények](stream-analytics-edge-csharp-udf-methods.md)használatával.

### <a name="managed-identity-authentication-with-power-bi"></a>Felügyelt identitások hitelesítése Power BI

A Azure Stream Analytics teljes körű támogatást nyújt a felügyelt identitás-alapú hitelesítéshez, és a Power BI dinamikus irányítópult-kialakítási élményt biztosít.

### <a name="anomaly-detection"></a>Anomáliadetektálás

A gépi tanulási modellek a két irányú, lassú és lassú negatív trendek észlelése mellett a *Spike* és a *dips* észlelését is támogatják. Azure stream Analytics További információ [: anomáliák észlelése Azure stream Analyticsban](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="debug-query-steps-in-visual-studio"></a>Lekérdezési lépések hibakeresése a Visual Studióban

Az adatdiagramon könnyen megtekintheti a köztes sort, ha helyi tesztelést végez a Visual Studio Azure Stream Analytics eszközein. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Helyi tesztelés élő adatértékekkel a Visual Studio Code-ban

A feladatokat az Azure-ba való elküldés előtt tesztelheti a helyi gépen élő adatokon. Az egyes tesztelési iterációk átlagosan kevesebb mint két-három másodpercet vesznek igénybe, ami nagyon hatékony fejlesztési folyamatot eredményez.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code a Azure Stream Analyticshoz

Azure Stream Analytics feladatokat a Visual Studio Code-ban lehet létrehozni. Tekintse meg a [vs Code – első lépéseket ismertető oktatóanyagot](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="anomaly-detection"></a>Anomáliadetektálás

Azure Stream Analytics új gépi tanulási modelleket vezet be a *Spike* és a *dips* észlelés támogatásával, a kétirányú, lassú pozitív és lassú negatív trendek észlelése mellett. További információ [: anomáliák észlelése Azure stream Analyticsban](stream-analytics-machine-learning-anomaly-detection.md).


### <a name="integration-with-azure-machine-learning"></a>Integráció a Azure Machine Learning

Stream Analytics feladatokat Machine Learning (ML) függvények segítségével méretezheti. Ha többet szeretne megtudni arról, hogyan használható az Stream Analytics-feladatban az ML függvények használata, látogasson el [a stream Analytics-feladatok méretezése Azure Machine learning függvényekkel](stream-analytics-scale-with-machine-learning-functions.md)című témakörre. Tekintse meg a valós forgatókönyveket, amelyekkel [Azure stream Analytics és Azure Machine learning használatával is elvégezheti a hangulat elemzését](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Élő adattesztelés a Visual Studióban

A Visual Studio Tools for Azure Stream Analytics fokozza a helyi tesztelési funkciót, amely lehetővé teszi, hogy tesztelje a Felhőbeli forrásokból, például az Event hub-ból vagy az IoT hub-ból származó élő esemény-adatfolyamokat. Ismerje meg, hogyan [tesztelheti helyileg az élő információkat a Visual studióhoz készült Azure stream Analytics Tools használatával](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>.NET-felhasználó által definiált függvények IoT Edge

A .NET Standard felhasználó által definiált függvények használatával a folyamatos átviteli folyamat részeként futtathatja a .NET Standard szintű kódot. Létrehozhat egyszerű C# osztályokat, illetve importálhatja a teljes projektet és a kódtárakat is. A Visual Studióban a teljes authoring és hibakeresési élmény támogatott. További információért látogasson el [a .NET szabványú, felhasználó által definiált függvények fejlesztése Azure stream Analytics Edge-feladatokhoz](stream-analytics-edge-csharp-udf-methods.md)című témakörben.

## <a name="other-previews"></a>Egyéb előzetes verziók

A következő funkciók az előzetes verzióban is elérhetők a kérelemben.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Valós idejű nagy teljesítményű pontozás a Azure Machine Learning által felügyelt egyéni ML-modellekkel

Azure Stream Analytics támogatja a nagy teljesítményű, valós idejű pontozást azáltal, hogy kihasználja a Azure Machine Learning által kezelt egyéni, előre betanított Machine Learning-modelleket, és az Azure Kubernetes szolgáltatásban (ak) vagy Azure Container Instances (ACI) futtatja a munkafolyamatot Ez nem igényli a kód írását. [Regisztrálás](https://aka.ms/asapreview1) az előzetes verzióra

### <a name="support-for-azure-stack"></a>Azure Stack támogatása
Ez a funkció engedélyezve van a Azure IoT Edge futtatókörnyezetben, a kihasználja az egyéni Azure Stack funkciókat, például a helyi bemenetek natív támogatását és a Azure Stack futó kimeneteket (például Event Hubs, IoT Hub, Blob Storage). Ez az új integráció lehetővé teszi olyan hibrid architektúrák elkészítését, amelyek a létrehozásuk helyétől, a késés csökkentése és az elemzések maximalizálása mellett is elemezhetik adataikat.
Ez a funkció lehetővé teszi olyan hibrid architektúrák létrehozását, amelyek a létrehozásuk helyétől, a késés csökkentése és az elemzések maximalizálása mellett is elemezhetik az adatait. Regisztrálnia [kell erre az előzetes](https://aka.ms/asapreview1) verzióra.
