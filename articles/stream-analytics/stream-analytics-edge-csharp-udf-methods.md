---
title: .NET Standard függvények fejlesztése Azure Stream Analytics Edge-feladatokhoz
description: Megtudhatja, hogyan fejleszthet .NET felhasználó által definiált függvényeket Azure Stream Analytics feladatokhoz a CodeBehind, egy meglévő csomag vagy egy helyi projekt használatával.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: cdfe4d2ccc39385b4020f60905b12c4683b1758c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935026"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-edge-jobs-preview"></a>.NET-szabványnak megfelelő, felhasználó által definiált függvények fejlesztése Azure Stream Analytics Edge-feladatokhoz (előzetes verzió)

A Azure Stream Analytics SQL-szerű lekérdezési nyelvet kínál az adatstreamek közötti átalakítások és számítások végrehajtásához. Számos beépített függvény létezik, de bizonyos összetett forgatókönyvek további rugalmasságot igényelnek. A .NET Standard felhasználó által definiált függvények (UDF) használatával meghívhatja a saját függvényeit bármely .NET Standard nyelven (C#, F#stb.), hogy kiterjessze a stream Analytics lekérdezési nyelvet. A UDF lehetővé teszi összetett matematikai számítások végrehajtását, egyéni ML-modellek importálását a ML.NET használatával, és a hiányzó adatokhoz egyéni imputálási logikát használhat. A Stream Analytics-feladatokhoz használható UDF funkció jelenleg előzetes verzióban érhető el, így használata nem javasolt éles környezetekben.

## <a name="overview"></a>Áttekintés
A Visual Studio Tools for Azure Stream Analytics megkönnyíti a UDF írását, a feladatok helyi tesztelését (még kapcsolat nélküli üzemmódban), és a Stream Analytics feladat közzétételét az Azure-ban. Miután közzétette az Azure-ban, üzembe helyezheti a feladatot, hogy IoT az eszközöket IoT Hub használatával.

A UDF háromféle módon valósítható meg:

* CodeBehind-fájlok egy ASA-projektben
* UDF helyi projektből
* Egy meglévő csomag egy Azure Storage-fiókból

## <a name="package-path"></a>Csomag elérési útja

Az UDF-csomagok formátuma `/UserCustomCode/CLR/*`elérési úttal rendelkezik. A rendszer a dinamikus csatolású kódtárakat (DLL-eket) és az erőforrásokat a `/UserCustomCode/CLR/*` mappában másolja, ami segít a felhasználói DLL-fájlok elkülönítésében a rendszer-és Azure Stream Analytics dll-eken. Ez a csomag elérési útja minden függvényhez használatos, függetlenül az azok alkalmazására használt módszertől.

## <a name="supported-types-and-mapping"></a>Támogatott típusok és leképezés

|**UDF-típusC#()**  |**Azure Stream Analytics típusa**  |
|---------|---------|
|hosszú  |  bigint   |
|double  |  double   |
|sztring  |  nvarchar (max.)   |
|DateTime  |  DateTime   |
|struct  |  IRecord   |
|objektum  |  IRecord   |
|Tömb\<objektum >  |  IArray   |
|szótár < sztring, objektum >  |  IRecord   |

## <a name="codebehind"></a>CodeBehind
Felhasználó által definiált függvényeket írhat a **script. asql** Codebehind. A Visual Studio Tools automatikusan lefordítja a CodeBehind-fájlt egy Assembly-fájlba. A szerelvények zip-fájlként vannak csomagolva, és feltöltve lesznek a Storage-fiókjába, amikor elküldi a feladatot az Azure-ba. [A C# ](stream-analytics-edge-csharp-udf.md) következő témakörből megtudhatja C# , hogyan írhat UDF-t az Codebehind használatával: stream Analytics Edge Jobs-oktatóanyaghoz tartozó UDF. 

## <a name="local-project"></a>Helyi projekt
A felhasználó által definiált függvények olyan szerelvényben írhatók be, amely később egy Azure Stream Analytics lekérdezésben hivatkozik rá. Ez az ajánlott lehetőség olyan összetett függvények esetén, amelyek a .NET szabvány nyelvének teljes hatékonyságát igénylik a kifejezés nyelvén, például az eljárási logikán vagy a rekurzión kívül. Helyi projektből származó UDF is használható, ha több Azure Stream Analytics lekérdezésben kell megosztania a függvény logikáját. A UDF a helyi projekthez való hozzáadása lehetővé teszi a függvények hibakeresését és tesztelését helyileg a Visual studióból.

Helyi projektre való hivatkozás:

1. Hozzon létre egy új osztály-függvénytárat a megoldásban.
2. Írja be a kódot az osztályba. Ne feledje, hogy az osztályokat *nyilvánosként* kell definiálni, és az objektumokat *statikus nyilvánosként*kell definiálni. 
3. Hozza létre a projektet. Az eszközök a bin mappában lévő összes összetevőt egy zip-fájlba csomagolják, és feltöltik a zip-fájlt a Storage-fiókba. Külső hivatkozások esetében a NuGet-csomag helyett használjon szerelvény-hivatkozást.
4. Hivatkozzon az új osztályra a Azure Stream Analytics projektben.
5. Új függvény hozzáadása a Azure Stream Analytics projektben.
6. Konfigurálja a szerelvény elérési útját a feladatok konfigurációs fájljába, `JobConfig.json`. Állítsa be a szerelvény elérési útját a **helyi projekt referenciájának vagy a Codebehind**.
7. Hozza létre újra a Function projektet és a Azure Stream Analytics projektet.  

### <a name="example"></a>Példa

Ebben a példában a **UDFTest** egy C# **ASAEdgeUDFDemo** -projekt, amely az Azure stream Analytics Edge-projekt, amely a **UDFTest**-ra hivatkozik.

![Azure Stream Analytics IoT Edge-projekt a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Hozza létre C# a projektet, amely lehetővé teszi, hogy az Azure stream Analytics lekérdezésből C# adjon hozzá egy hivatkozást az UDF-hez.
    
   ![Azure Stream Analytics IoT Edge-projekt létrehozása a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Adja hozzá a hivatkozást a C# projekthez az ASA Edge-projektben. Kattintson a jobb gombbal a hivatkozások csomópontra, majd válassza a hivatkozás hozzáadása parancsot.

   ![Hivatkozás hozzáadása egy C# projekthez a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Válassza ki C# a projekt nevét a listából. 
    
   ![Válassza ki C# a projekt nevét a hivatkozási listából](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Meg kell jelennie a **Megoldáskezelőban**található **referenciák** alatt felsorolt **UDFTest** .

   ![A felhasználó által definiált függvény hivatkozásának megtekintése a megoldás Explorerben](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Kattintson a jobb gombbal a **functions** mappára, és válassza az **új elem**lehetőséget.

   ![Új elem hozzáadása a functions szolgáltatáshoz Azure Stream Analytics Edge-megoldásban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Adjon hozzá C# egy **SquareFunction. JSON** függvényt a Azure stream Analytics projekthez.

   ![A Visual Studióban Stream Analytics Edge-elemek CSharp függvényének kiválasztása](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Kattintson duplán a függvényre **megoldáskezelő** a konfigurációs párbeszédpanel megnyitásához.

   ![C Sharp Function konfiguráció a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. A C# függvény konfigurációjában válassza a **BEtöltés az ASA-projekthez** és a kapcsolódó szerelvény, osztály és metódus nevét a legördülő listából. Az Stream Analytics Edge-lekérdezés módszereire, típusaira és funkcióihoz való hivatkozáshoz az osztályokat *nyilvánosként* kell definiálni, és az objektumokat *statikus nyilvánosként*kell definiálni.

   ![Stream Analytics C Sharp függvény konfigurálása](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Meglévő csomagok

Bármilyen IDE-ban létrehozhat .NET Standard szintű UDF, és meghívhatja őket a Azure Stream Analytics-lekérdezésből. Először fordítsa le a kódot, és csomagolja ki a DLL-eket. A csomag formátuma `/UserCustomCode/CLR/*`. Ezután töltse fel `UserCustomCode.zip`t az Azure Storage-fiókjában található tároló gyökerébe.

Miután feltöltötte az összeállítási zip-csomagokat az Azure Storage-fiókjába, használhatja Azure Stream Analytics lekérdezések funkcióit. Mindössze annyit kell tennie, hogy tartalmazza a tárolási adatokat az Stream Analytics Edge-feladatok konfigurációjában. Ezzel a beállítással helyileg nem tesztelheti a függvényt, mert a Visual Studio-eszközök nem töltik le a csomagot. A csomag elérési útját közvetlenül a szolgáltatásra kell elemezni. 

A szerelvény elérési útjának konfigurálásához a feladatok konfigurációs fájljában `JobConfig.json`:

Bontsa ki a **Felhasználói kód konfigurációja** szakaszt, és töltse ki a konfigurációt az alábbi javasolt értékekkel:

 |**Beállítás**  |**Ajánlott érték**  |
 |---------|---------|
 |Szerelvény forrása  | Meglévő szerelvény-csomagok a felhőből    |
 |Erőforrás  |  Válassza ki az adatokat az aktuális fiókból   |
 |Előfizetés  |  Válassza ki az előfizetését.   |
 |Tárfiók  |  Válassza ki a tárfiókját.   |
 |Tároló  |  Válassza ki a tárfiókjában létrehozott tárolót.   |

![Azure Stream Analytics Edge-projekt konfigurálása a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-job-config.png)

## <a name="limitations"></a>Korlátozások
Az UDF előzetes verziója jelenleg a következő korlátozásokkal rendelkezik:

* A .NET Standard nyelveket csak IoT Edge-eszközökön futó Azure Stream Analyticshoz lehet használni. A felhőalapú feladatokhoz JavaScript felhasználó által definiált függvényeket írhat. További tudnivalókért tekintse meg a [Azure stream Analytics JavaScript UDF](stream-analytics-javascript-user-defined-functions.md) oktatóanyagot.

* A .NET Standard UDF csak a Visual Studióban hozhatók létre és tehetők közzé az Azure-ban. A .NET Standard UDF csak olvasható verzióit tekintheti meg a Azure Portal **függvények** területén. A .NET Standard függvények készítése nem támogatott a Azure Portalban.

* A Azure Portal Query Editor hibaüzenetet jelenít meg, amikor a .NET Standard UDF-t használja a portálon. 

* Mivel az egyéni kódok Azure Stream Analytics motorral vannak megosztva, az egyéni kód nem hivatkozhat olyanra, amely ütköző névtérrel/dll_name rendelkezik Azure Stream Analytics kóddal. Nem hivatkozhat például a *Newtonsoft JSON*-ra.

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: C# felhasználó által definiált függvény írása Azure stream Analytics Edge-feladathoz (előzetes verzió)](stream-analytics-edge-csharp-udf.md)
* [Oktatóanyag: Azure Stream Analytics JavaScript felhasználó által definiált függvények](stream-analytics-javascript-user-defined-functions.md)
* [A Visual Studio használata Azure Stream Analytics feladatok megtekintéséhez](stream-analytics-vs-tools.md)
