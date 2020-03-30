---
title: Oktatóanyag – C# felhasználó által definiált függvények írása az Azure Stream Analytics-feladatokhoz a Visual Studio-ban (előzetes verzió)
description: Ez az oktatóanyag bemutatja, hogyan írhat c# felhasználó által definiált függvényeket a Visual Studio Stream Analytics-feladatainak.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 1d71f4c5616efb05efe2733c49507b085ca2dcf6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75426299"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>Oktatóanyag: C# felhasználó által definiált függvény írása az Azure Stream Analytics-feladathoz (előzetes verzió)

A Visual Studióban létrehozott felhasználói C# függvényekkel (UDF-ekkel) kiterjesztheti az Azure Stream Analytics lekérdezéseinek nyelvét a saját függvényeire. A C#-pal meglévő kódokat használhat újra (így DLL-eket is), valamint matematikai vagy összetett logikát alkalmazhat. Az UDF-ek implementálásának három módja van: CodeBehind-fájlok egy Stream Analytics-projektben, UDF-ek egy helyi C#-projektből, valamint UDF-ek egy tárfiók meglévő csomagjából. Ez az oktatóanyag a CodeBehind metódussal implementál egy alapszintű C# függvényt. A Stream Analytics-feladatok UDF-szolgáltatása jelenleg előzetes verzióban érhető el, és nem használható éles számítási feladatokban.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Felhasználói C# függvény létrehozása CodeBehinddal.
> * Tesztelje a Stream Analytics-feladatot helyileg.
> * Tegye közzé a munkáját az Azure-ban.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzálátna, győződjön meg róla, hogy megfelel az alábbi előfeltételeknek:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Telepítse a [Stream Analytics Tools for Visual Studiót](stream-analytics-tools-for-visual-studio-install.md) és az **Azure-fejlesztési** vagy az **Adattárolási és -feldolgozási** számítási feladatokat.
* Tekintse meg a meglévő [Stream Analytics Edge fejlesztési útmutatót], ha IoT Edge-feladatot hoz (stream-analytics-tools-for-visual-studio-edge-jobs.md).

## <a name="create-a-container-in-your-azure-storage-account"></a>Tároló létrehozása az Azure Storage-fiókban

A létrehozott tároló a lefordított C# csomag tárolására szolgál. Ha létrehoz egy Edge-feladatot, ez a tárfiók is a csomag üzembe helyezéséhez az IoT Edge-eszköz. A Stream Analytics-feladathoz dedikált tárolót használjon. Ugyanazt a tárolót nem használhatja több Stream Analytics Edge-feladathoz. Ha már van egy meglévő tárolókat tartalmazó tárfiókja, használhatja azokat. Ha nincs, [hozzon létre egy új tárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Stream Analytics-projekt létrehozása a Visual Studióban

1. Indítsa el a Visual Studiót.

2. Válassza a **Fájl > Új projekt** lehetőséget.

3. A bal oldali sablonok listájában válassza a **Stream Analytics**lehetőséget, majd válassza az Azure Stream **Analytics Edge-alkalmazást** vagy az **Azure Stream Analytics-alkalmazást.**

4.  Adja meg az alkalmazás **nevét**, **helyét** és a **megoldás nevét**, majd kattintson az **OK** gombra.

    ![Azure Stream Analytics Edge-projekt létrehozása a Visual Studióban](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Szerelvénycsomag elérési útjának konfigurálása

1. Nyissa meg a Visual Studio **Megoldáskezelőjét**.

2. Kattintson duplán a feladat konfigurációs fájljára (`EdgeJobConfig.json`).

3. Bontsa ki a **Felhasználói kód konfigurációja** szakaszt, és töltse ki a konfigurációt az alábbi javasolt értékekkel:

   |**Beállítás**|**Javasolt érték**|
   |-------|---------------|
   |Globális tárolási beállítások erőforrás|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Globális tárolási beállítások előfizetés| Előfizetési > <|
   |Globális tárterület-tároló fiók| < tárfiók >|
   |Egyéni kódtárolási beállítások erőforrás|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Egyéni kódtárolási beállítások tárfiók|< tárfiók >|
   |Egyéni kódtárolási beállítások tárolója|< tároló>|


## <a name="write-a-c-udf-with-codebehind"></a>C# UDF írása a CodeBehind segítségével
A CodeBehind fájl egyetlen ASA-lekérdezési parancsfájlhoz társított C# fájl. A Visual Studio eszközei automatikusan tömörítik a CodeBehind fájlt, majd a beküldés után feltöltik az Azure Storage-fiókba. Minden osztályt *nyilvánosként*, minden objektumot pedig *statikus nyilvánosként* kell definiálni.

1. A **Megoldáskezelőben** bontsa ki a **Script.asql** fájlt a **Script.asaql.cs** CodeBehind fájl megkereséséhez.

2. Cserélje le a kódot az alábbi mintára:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>Az UDF implementálása

1. A **Megoldáskezelőben** nyissa meg a **Script.asaql** fájlt.

2. Cserélje le a meglévő lekérdezést a következőre:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Helyi tesztelés

1. Töltse le a [hőmérséklet-szimulátor mintaadatfájlját.](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json)

2. A **Megoldáskezelőben** bontsa ki a **Bemenetek** csomópontot, majd kattintson a jobb gombbal az **Input.json** fájlra, és válassza a **Helyi bemenet hozzáadása** lehetőséget.

   ![Helyi bemenet hozzáadása a Stream Analytics-feladathoz a Visual Studióban](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Adja meg a helyi bemeneti fájl útvonalát a letöltött mintaadatokban, majd **mentse** a módosításokat.

    ![A Visual Studio Stream Analytics-feladatának helyi bemeneti konfigurációja](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. A szkriptszerkesztőben kattintson a **Helyi futtatás** lehetőségre. Miután a helyi futtatás elmentette a kimeneti eredményeket, nyomjon le egy billentyűt az eredmények táblázatos megjelenítéséhez. 

    ![Az Azure Stream Analytics-feladat helyi futtatása a Visual Studióval](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. A nyersfájlokat JSON és CSV formátumban is megtekinthet az **Eredmények mappa megnyitása** lehetőséggel.

    ![A helyi Azure Stream Analytics-feladat eredményeinek megtekintése Visual Studióval](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>UDF hibakeresése
A C# UDF-en helyileg, a szabványos C#-kódokhoz hasonlóan végezhet hibakeresést. 

1. Adjon töréspontokat a C# függvényhez.

    ![Töréspontok hozzáadása a Stream Analytics felhasználó által definiált függvényéhez a Visual Studióban](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Nyomja le az **F5** billentyűt a hibakeresés megkezdéséhez. A program a várt módon leáll a töréspontoknál.

    ![A Stream Analytics felhasználó által definiált függvényhiba-keresésének eredményeinek megtekintése](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>A feladat közzététele az Azure-ban
A lekérdezés helyi tesztelése után válassza a szkriptszerkesztő **Küldés az Azure-ba** lehetőségét a feladat az Azure-ban való közzétételéhez.

![A Stream Analytics Edge-feladat Azure-ban való közzététele a Visual Studióból](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Üzembe helyezés IoT Edge-eszközökön
Ha úgy döntött, hogy egy Stream Analytics Edge-feladat, ez most már üzembe helyezhető, mint egy IoT Edge-modul. Az [IoT Edge – gyors üzembe helyezés](https://docs.microsoft.com/azure/iot-edge/quickstart) lépéseit követve létrehozhat egy IoT Hubot, regisztrálhat egy IoT Edge-eszközt, majd telepítheti és elindíthatja az IoT Edge-futtatókörnyezetet a eszközön. Ezután [A feladat üzembe helyezése](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job) oktatóanyagot követve üzembe helyezheti a Stream Analytics-feladatot IoT Edge-modulként. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy egyszerű C# felhasználó által definiált függvényt a CodeBehind használatával, közzétette a feladatát az Azure-ban, és üzembe helyezte a feladatot az Azure-ba vagy az IoT Edge-eszközre. 

Ha többet szeretne megtudni a C# felhasználó által definiált függvények Stream Analytics-feladatokhoz való használatának különböző módjairól, folytassa ezt a cikket:

> [!div class="nextstepaction"]
> [C# függvények írása az Azure Stream Analytics szolgáltatáshoz](stream-analytics-edge-csharp-udf-methods.md)
