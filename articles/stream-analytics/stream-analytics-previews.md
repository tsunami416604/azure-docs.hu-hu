---
title: Az Azure Stream Analytics előzetes verziófunkciói
description: Ez a cikk az Azure Stream Analytics-funkciókat sorolja fel, amelyek jelenleg előzetes verzióban vannak.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 4c265665be26dcc6868ea9a303b0c12c52dfe05b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878306"
---
# <a name="azure-stream-analytics-preview-features"></a>Az Azure Stream Analytics előzetes verziófunkciói

Ez a cikk összegzi az Azure Stream Analytics előzetes verzióban elérhető összes funkcióját. Az előzetes verziójú funkciók éles környezetben használata nem ajánlott.

## <a name="public-previews"></a>Nyilvános előzetes verziók

Az alábbi funkciók nyilvános előzetes verzióban találhatók. Ezeket a funkciókat ma kihasználhatja, de nem használhatja őket éles környezetben.

### <a name="online-scaling"></a>Online méretezés

Az online skálázás, nem kell leállítani a munkát, ha módosítania kell az SU-allokáció. A futó feladat SU-kapacitását anélkül növelheti vagy csökkentheti, hogy le kellene állítania azt. Ez a Stream Analytics által ma elérhető, hosszú ideig futó, kritikus fontosságú folyamatok ügyfélígéretére épül. További információ: [Az Azure Stream Analytics streamelési egységek konfigurálása.](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus)

### <a name="c-custom-de-serializers"></a>C# egyéni de-szerializáló
A fejlesztők kihasználhatják az Azure Stream Analytics erejét az adatok Protobuf, XML vagy bármilyen egyéni formátumban való feldolgozásához. [Egyéni de-szerializálók](custom-deserializer-examples.md) a C#, amely ezután az Azure Stream Analytics által kapott események szerializálásának lebomlása.

### <a name="extensibility-with-c-custom-code"></a>Bővíthetőség C# egyéni kóddal

A Stream Analytics-modulokat a felhőben vagy az IoT Edge-en létrehozni fejlesztői egyéni C# függvényeket írhatnak vagy használhatnak fel újra, és a [felhasználó által definiált függvényeken](stream-analytics-edge-csharp-udf-methods.md)keresztül közvetlenül meghívhatják őket a lekérdezésben.


### <a name="debug-query-steps-in-visual-studio"></a>Lekérdezéshiba-lekérdezés lépései a Visual Studióban

Az Azure Stream Analytics-eszközök a Visual Studio-hoz való helyi tesztelés során könnyedén megtekintheti az adatdiagramon beállított köztes sor előnézetét. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Helyi tesztelés élő adatokkal a Visual Studio-kódban

A lekérdezések a helyi gépen lévő élő adatokkal tesztelheti, mielőtt elküldenék a feladatot az Azure-ba. Minden egyes tesztelési iteráció átlagosan kevesebb, mint 2-3 másodpercet vesz igénybe, ami nagyon hatékony fejlesztési folyamatot eredményez.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio-kód az Azure Stream Analytics szolgáltatáshoz

Az Azure Stream Analytics-feladatok a Visual Studio-kódban is létrehozhatóak. Tekintse meg [a VS-kód első lépéseit bemutató](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Valós idejű, nagy teljesítményű pontozás az Azure Machine Learning által kezelt egyéni gépi tanulási modellekkel

Az Azure Stream Analytics támogatja a nagy teljesítményű, valós idejű pontozást az Azure Machine Learning által felügyelt, előre betanított és az Azure Kubernetes-szolgáltatásban (AKS) vagy az Azure Container Instances (ACI) üzemeltetett, olyan munkafolyamat használatával, amely nem igényel kódot. [Feliratkozás](https://aka.ms/asapreview1) az előzetes verzióra


### <a name="live-data-testing-in-visual-studio"></a>Élő adatok tesztelése a Visual Studióban

Az Azure Stream Analytics visual Studio-eszközei javítják a helyi tesztelési funkciót, amely lehetővé teszi, hogy lekérdezéseket teszteljen a felhőbeli forrásokból, például az Event Hubvagy az IoT hub élő eseménystreamjei ellen. Ismerje meg, hogyan [tesztelheti az élő adatokat helyileg a Visual Studio Azure Stream Analytics eszközeivel.](stream-analytics-live-data-local-testing.md)


### <a name="net-user-defined-functions-on-iot-edge"></a>A .NET felhasználó által definiált függvények az IoT Edge-en

A .NET általános felhasználó által definiált függvényekkel a .NET standard kódot a streamelési folyamat részeként futtathatja. Egyszerű C# osztályokat hozhat létre, vagy teljes projektet és könyvtárakat importálhat. A Visual Studio támogatja a teljes szerzői és hibakeresési élményt. További információ: [A .NET Standard szintű felhasználók által definiált függvények fejlesztése az Azure Stream Analytics Edge-feladatokhoz](stream-analytics-edge-csharp-udf-methods.md)című webhelyen.

## <a name="other-previews"></a>Egyéb előzetesek

A következő funkciók is elérhetőelőzetes kérésre.

### <a name="support-for-azure-stack"></a>Az Azure Stack támogatása
Ez a funkció engedélyezve van az Azure IoT Edge futtatóföldön, kihasználja az egyéni Azure Stack-funkciók, például a natív támogatás a helyi bemenetek és kimenetek azure stack (például Event Hubs, IoT Hub, Blob Storage). Ez az új integráció lehetővé teszi, hogy hibrid architektúrákat hozzon létre, amelyek a létrehozásukhoz közeli adatokat elemezhetik, csökkentik a késést és maximalizálják az elemzéseket.
Ez a funkció lehetővé teszi, hogy hibrid architektúrákat hozzon létre, amelyek a létrehozásukhoz közeli adatokat elemezhetik, csökkentik a késést és maximalizálják az elemzéseket. Fel kell [iratkoznia](https://aka.ms/asapreview1) erre az előnézetre.
