---
title: A Azure Stream Analytics előzetes verziójának funkciói
description: Ez a cikk a jelenleg előzetes verzióban elérhető Azure Stream Analytics-szolgáltatásokat sorolja fel.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 8/07/2020
ms.openlocfilehash: e11d5b14bdf6b134fefea79a1f709ec73499bd20
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815708"
---
# <a name="azure-stream-analytics-preview-features"></a>A Azure Stream Analytics előzetes verziójának funkciói

Ez a cikk az összes olyan funkciót összegzi, amely jelenleg előzetes verzióban érhető el a Azure Stream Analytics. Az előzetes verziójú funkciók éles környezetben való használata nem ajánlott.

## <a name="public-previews"></a>Nyilvános előzetes verziók

A következő funkciók nyilvános előzetes verzióban érhetők el. Ezeket a funkciókat még ma is kihasználhatja, de nem használhatja azokat az éles környezetben.

### <a name="authenticate-to-sql-database-output-with-managed-identities"></a>Hitelesítés SQL Database a kimenet felügyelt identitásokkal

Azure Stream Analytics támogatja a [felügyelt identitások hitelesítését](../active-directory/managed-identities-azure-resources/overview.md) Azure SQL Database kimeneti mosogatók esetében. A felügyelt identitások megszüntetik a felhasználó-alapú hitelesítési módszerek korlátozásait, például a jelszó módosítása miatti újrahitelesítés szükségességét. 

### <a name="output-to-azure-synapse-analytics"></a>Kimenet az Azure szinapszis Analytics szolgáltatásba

Azure Stream Analytics feladatok kimenete az [Azure szinapszis Analytics](https://azure.microsoft.com/services/synapse-analytics) egy SQL-készlet táblájában végezhető el, amely akár 200 MB/s sebességig is feldolgozható. Ez támogatja a legigényesebb valós idejű elemzési és a gyors elérésű adatfeldolgozási igényeket olyan számítási feladatokhoz, mint a jelentéskészítés és az irányítópult.  

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Valós idejű nagy teljesítményű pontozás a Azure Machine Learning által felügyelt egyéni ML-modellekkel

Azure Stream Analytics támogatja a nagy teljesítményű, valós idejű pontozást azáltal, hogy kihasználja a Azure Machine Learning által felügyelt egyéni, előre betanított Machine Learning-modelleket, és az Azure Kubernetes szolgáltatásban (ak) vagy Azure Container Instances (ACI) futtatja azokat a munkafolyamatokat, amelyek nem igénylik a kód írását. [Regisztrálás](https://aka.ms/asapreview1) az előzetes verzióra

### <a name="c-custom-de-serializers"></a>C# egyéni de-szerializálók
A fejlesztők kihasználhatják a Azure Stream Analytics hatékonyságát, hogy Protopuf, XML-ben vagy bármilyen egyéni formátumban dolgozzák fel az adatfeldolgozást. A C#-ban implementálhatja az [Egyéni deszerializáló](custom-deserializer-examples.md) eszközöket, amelyek felhasználhatók a Azure stream Analytics által fogadott események deszerializálása céljából.

### <a name="extensibility-with-c-custom-code"></a>Bővíthetőség C# egyéni kóddal

A felhőben vagy IoT Edge Stream Analytics modulokat létrehozó fejlesztők írhatnak vagy használhatnak egyéni C#-függvényeket, és közvetlenül a lekérdezésben meghívhatják azokat a [felhasználó által definiált függvények](stream-analytics-edge-csharp-udf-methods.md)használatával.

### <a name="debug-query-steps-in-visual-studio"></a>Lekérdezési lépések hibakeresése a Visual Studióban

Az adatdiagramon könnyen megtekintheti a köztes sort, ha helyi tesztelést végez a Visual Studio Azure Stream Analytics eszközein. 


### <a name="live-data-testing-in-visual-studio"></a>Élő adattesztelés a Visual Studióban

A Visual Studio Tools for Azure Stream Analytics fokozza a helyi tesztelési funkciót, amely lehetővé teszi, hogy tesztelje a Felhőbeli forrásokból, például az Event hub-ból vagy az IoT hub-ból származó élő esemény-adatfolyamokat. Ismerje meg, hogyan [tesztelheti helyileg az élő információkat a Visual studióhoz készült Azure stream Analytics Tools használatával](stream-analytics-live-data-local-testing.md).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code a Azure Stream Analyticshoz

Azure Stream Analytics feladatokat a Visual Studio Code-ban lehet létrehozni. Tekintse meg a [vs Code – első lépéseket ismertető oktatóanyagot](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Helyi tesztelés élő adatértékekkel a Visual Studio Code-ban

A feladatokat az Azure-ba való elküldés előtt tesztelheti a helyi gépen élő adatokon. Az egyes tesztelési iterációk átlagosan kevesebb mint két-három másodpercet vesznek igénybe, ami nagyon hatékony fejlesztési folyamatot eredményez.

## <a name="other-previews"></a>Egyéb előzetes verziók

A következő funkciók az előzetes verzióban is elérhetők a kérelemben.

### <a name="support-for-azure-stack"></a>Azure Stack támogatása
Ez a funkció engedélyezve van a Azure IoT Edge futtatókörnyezetben, a kihasználja az egyéni Azure Stack funkciókat, például a helyi bemenetek natív támogatását és a Azure Stack futó kimeneteket (például Event Hubs, IoT Hub, Blob Storage). Ez az új integráció lehetővé teszi olyan hibrid architektúrák elkészítését, amelyek a létrehozásuk helyétől, a késés csökkentése és az elemzések maximalizálása mellett is elemezhetik adataikat.
Ez a funkció lehetővé teszi olyan hibrid architektúrák létrehozását, amelyek a létrehozásuk helyétől, a késés csökkentése és az elemzések maximalizálása mellett is elemezhetik az adatait. Regisztrálnia [kell erre az előzetes](https://aka.ms/asapreview1) verzióra.
