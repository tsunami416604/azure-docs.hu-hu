---
title: Oktatóanyag – Egyéni .NET deszerializálók az Azure Stream Analytics felhőalapú feladatokhoz
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egyéni .NET deszerializálót egy Azure Stream Analytics-felhőfeladathoz a Visual Studio használatával.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 1fffeec1434cb066487bf383589554edec2e6a86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75443692"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Oktatóanyag: Egyéni .NET deszerializálók az Azure Stream Analytics számára

Az Azure Stream Analytics [három adatformátumot támogat:](stream-analytics-parsing-json.md)JSON, CSV és Avro. Az egyéni .NET deszerializálókkal más formátumokból, például [protokollpufferből,](https://developers.google.com/protocol-buffers/) [kötésből](https://github.com/Microsoft/bond) és más, a felhőalapú és a peremhálózati feladatokhoz egyaránt felhasználó által definiált formátumokból olvashat adatokat.

Ez az oktatóanyag bemutatja, hogyan hozhat létre egyéni .NET deszerializálót egy Azure Stream Analytics-felhőfeladathoz a Visual Studio használatával. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy egyéni deszerializálót a protokollpufferhez.
> * Hozzon létre egy Azure Stream Analytics-feladatot a Visual Studióban.
> * Konfigurálja a Stream Analytics-feladatot az egyéni deszerializáló használatára.
> * Futtassa a Stream Analytics-feladatot helyileg az egyéni deszerializáló teszteléséhez.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Telepítse a [Visual Studio 2017-et](https://www.visualstudio.com/downloads/) vagy a [Visual Studio 2015-öt.](https://www.visualstudio.com/vs/older-downloads/) Az Enterprise (Ultimate/Premium), Professional és Community kiadások mind támogatottak. Az expressz kiadás nem támogatott.

* [Telepítse a Stream Analytics-eszközöket a Visual Studio számára,](stream-analytics-tools-for-visual-studio-install.md) vagy frissítsen a legújabb verzióra. A Visual Studio következő verziói támogatottak:
   * Visual Studio 2015
   * Visual Studio 2017

* Nyissa meg a **Cloud Explorert** a Visual Studióban, és jelentkezzen be Azure-előfizetésbe.

* Hozzon létre egy tárolót az Azure Storage-fiókban.
A létrehozott tároló a Stream Analytics-feladathoz kapcsolódó eszközök tárolására szolgál. Ha már van egy meglévő tárolókat tartalmazó tárfiókja, használhatja azokat. Ha nincs, [hozzon létre egy új tárolót](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Egyéni deszerializáló létrehozása

1. Nyissa meg a Visual Studio alkalmazást, és válassza **az Új > > fájl**lehetőséget. Keresse meg a **Stream Analytics elemet,** és válassza az **Azure Stream Analytics egyéni deszerializáló projekt (.NET) lehetőséget.** Adjon nevet a projektnek, például **Protobuf Deszeriaiátor**.

   ![Visual Studio dotnet szabványos osztálytár-projekt létrehozása](./media/custom-deserializer/create-dotnet-library-project.png)

2. A Megoldáskezelőben kattintson a jobb gombbal a **Protobuf Deszerializáló** projektre, és válassza a menü **NuGet csomagok kezelése parancsát.** Ezután telepítse a **Microsoft.Azure.StreamAnalytics** és a **Google.Protobuf** NuGet csomagokat.

3. Adja hozzá a [MessageBodyProto osztályt](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) és a [MessageBodyDeserializer osztályt](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) a projekthez.

4. Készítsd el a **Protobuf Deszerializáló** projektet.

## <a name="add-an-azure-stream-analytics-project"></a>Azure Stream Analytics-projekt hozzáadása

1. A Megoldáskezelőben kattintson a jobb gombbal a **Protobuf deszerializáló** megoldásra, és válassza **> új projekt hozzáadása parancsot.** Az **Azure Stream Analytics > Stream Analytics csoportban**válassza az Azure Stream **Analytics-alkalmazást.** Nevezze el **ProtobufCloudDeserializer** és válassza **az OK gombra**. 

2. Kattintson a jobb gombbal **hivatkozások a** **ProtobufCloudDeserializer** Azure Stream Analytics projekt alatt. A **Projektek csoportban**adja hozzá **a Protobuf Deszerializáló t.** Meg kell automatikusan kitölteni az Ön számára.

## <a name="configure-a-stream-analytics-job"></a>Stream Analytics-feladat konfigurálása

1. Kattintson duplán **a JobConfig.json fájlra.** Használja az alapértelmezett konfigurációkat, kivéve a következő beállításokat:

   |Beállítás|Ajánlott érték|
   |-------|---------------|
   |Globális tárolási beállítások erőforrás|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Globális tárolási beállítások előfizetés| Előfizetési > <|
   |Globális tárterület-tároló fiók| < tárfiók >|
   |Egyéni kódtárolási beállítások erőforrás|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Egyéni kódtárolási beállítások tárfiók|< tárfiók >|
   |Egyéni kódtárolási beállítások tárolója|< tároló>|

2. A **Bemenetek csoportban**kattintson duplán **az Input.json**elemre. Használja az alapértelmezett konfigurációkat, kivéve a következő beállításokat:

   |Beállítás|Ajánlott érték|
   |-------|---------------|
   |Forrás|Blob Storage|
   |Erőforrás|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Előfizetés|Előfizetési > <|
   |Tárfiók|< tárfiók >|
   |Tároló|< tároló>|
   |Eseményszeresítési formátum|Egyéb (Protobuf, XML, védett...)|
   |Erőforrás|Betöltés az ASA Project Reference vagy CodeBehind alkalmazásból|
   |CSharp-összeállítás neve|ProtobufDeserializer.dll|
   |Osztály neve|MessageBodyProto.MessageBodyDeserializer|
   |Eseménytömörítés típusa|None|

3. Adja hozzá a következő lekérdezést a **Script.asaql** fájlhoz.

   ```sql
   SELECT * FROM Input
   ```

4. Töltse le a [protobuf-beviteli mintafájlt.](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf) A **Bemenetek mappában** kattintson a jobb gombbal az **Input.json fájlra,** és válassza **a Helyi bevitel hozzáadása parancsot.** Ezután kattintson duplán **local_Input.json elemre,** és adja meg a következő beállításokat:

   |Beállítás|Ajánlott érték|
   |-------|---------------|
   |Input Alias (Bemeneti alias)|Input (Bemenet)|
   |Source Type (Forrás típusa)|Data Stream (Adatstream)|
   |Eseményszeresítési formátum|Egyéb (Protobuf, XML, védett...)|
   |CSharp-összeállítás neve|ProtobufDeserializer.dll|
   |Osztály neve|MessageBodyProto.MessageBodyDeserializer|
   |Helyi bemeneti fájl elérési útja|< a letöltött protobuf bemeneti fájl elérési útja>|

## <a name="execute-the-stream-analytics-job"></a>A Stream Analytics feladat végrehajtása

1. Nyissa meg **a Script.asaql parancsot,** és válassza **a Futtatás helyileg**lehetőséget.

2. Figyelje meg az eredményeket a **Stream Analytics helyi futtatási eredményei ben.**

Sikeresen megvalósított egy egyéni deszerializálót a Stream Analytics-feladathoz! Ebben az oktatóanyagban helyileg tesztelte az egyéni deszerializálót. A tényleges adatok, akkor megfelelően konfigurálja a bemeneti és kimeneti. Ezután küldje el a feladatot az Azure-ba a Visual Studio-ból, hogy futtassa a munkát a felhőben az imént megvalósított egyéni deszerializáló használatával.

## <a name="debug-your-deserializer"></a>Debug a deserializer

A .NET deszerializálót helyileg ugyanúgy debugolhatja, mint a szabványos .NET kódot. 

1. Töréspontok hozzáadása a függvényhez.

2. Nyomja le az **F5** billentyűt a hibakeresés megkezdéséhez. A program a várt módon leáll a töréspontoknál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, törölheti az erőforráscsoportot, a folyamatos átviteli feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha a feladatot a jövőben is szeretné használni, leállíthatja, és később újraindíthatja amikor ismét szükség van rá. Ha már nem használja a feladatot, akkor a következő lépésekkel az oktatóanyagban létrehozott összes erőforrást törölheti:

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok** menüpontot, majd válassza ki a létrehozott erőforrás nevét.  

2. Az erőforráscsoport lapján válassza a **Törlés** elemet, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan valósítható meg egyéni .NET deszerializáló a protokollpuffer bemeneti serializálásához. Ha többet szeretne megtudni az egyéni deszerializálók létrehozásáról, folytassa a következő cikkel:

> [!div class="nextstepaction"]
> [Hozzon létre különböző .NET deszerializálókat az Azure Stream Analytics-feladatokhoz](custom-deserializer-examples.md)
