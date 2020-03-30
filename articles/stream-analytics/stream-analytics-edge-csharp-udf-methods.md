---
title: .NET Standard függvények fejlesztése Az Azure Stream Analytics-feladatokhoz (előzetes verzió)
description: Ismerje meg, hogyan írhat c# felhasználó által definiált függvényeket a Stream Analytics-feladatokhoz.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: f07c02df1b8e0032c9e1b4ef9a24c345fee20a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426310"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>A .NET Standard szintű felhasználók által definiált függvények fejlesztése az Azure Stream Analytics-feladatokhoz (előzetes verzió)

Az Azure Stream Analytics sql-szerű lekérdezési nyelvet kínál az átalakítások és az eseményadatok streamjein keresztül történő számítások végrehajtásához. Számos beépített függvény létezik, de néhány összetett forgatókönyv további rugalmasságot igényel. A .NET Standard felhasználó által definiált függvények (UDF) segítségével bármely .NET szabványnyelven (C#, F#stb.) írt saját függvényeket meghívhat a Stream Analytics lekérdezési nyelvének kiterjesztéséhez. UdF-ek lehetővé teszik, hogy összetett matematikai számításokat hajtson végre, egyéni ML-modelleket importáljon ML.NET használatával, és egyéni imputálási logikát használjon a hiányzó adatokhoz. A Stream Analytics-feladatok UDF-szolgáltatása jelenleg előzetes verzióban érhető el, és nem használható éles számítási feladatokban.

A .NET felhasználó által definiált függvény a felhőalapú feladatokhoz a következő helyen érhető el:
* USA nyugati középső régiója
* Észak-Európa
* USA keleti régiója
* USA nyugati régiója
* USA 2. keleti régiója
* Nyugat-Európa

Ha szeretné használni ezt a funkciót bármely más régióban, [hozzáférést kérhet.](https://aka.ms/ccodereqregion)

## <a name="overview"></a>Áttekintés
Az Azure Stream Analytics Visual Studio-eszközei megkönnyítik az UDF-ek írását, a feladatok helyi (akár offline) tesztelését, valamint a Stream Analytics-feladat Azure-ban való közzétételét. Miután közzétette az Azure-ban, üzembe helyezheti a feladat átioT-eszközök az IoT Hub használatával.

Az UDF-ek megvalósításának három módja van:

* CodeBehind fájlok asa-projektben
* UDF egy helyi projektből
* Meglévő csomag egy Azure-tárfiókból

## <a name="package-path"></a>Csomag elérési útja

Bármely UDF-csomag formátuma `/UserCustomCode/CLR/*`rendelkezik az elérési úttal. A dinamikus csatolású kódtárak (DL-ek) és az erőforrások a mappa alatt kerülnek másolásra, amely segít elkülöníteni a felhasználói DL-eket a rendszertől és az `/UserCustomCode/CLR/*` Azure Stream Analytics DL-ektől. Ez a csomagelérési út minden függvényhez használatos, függetlenül az alkalmazásukhoz használt módszertől.

## <a name="supported-types-and-mapping"></a>Támogatott típusok és leképezések

|**UDF-típus (C#)**  |**Az Azure Stream Analytics típusa**  |
|---------|---------|
|long  |  bigint   |
|double  |  double   |
|sztring  |  nvarchar(max)   |
|Datetime  |  Datetime   |
|Struct  |  IRecord   |
|objektum  |  IRecord   |
|Tömbobjektum\<>  |  IArray tömb   |
|szótár<karakterlánc, objektum>  |  IRecord   |

## <a name="codebehind"></a>CodeBehind mögött
A **Parancsfájl.asql** CodeBehind fájlba felhasználó által definiált függvényeket írhat. A Visual Studio eszközei automatikusan lefordítják a CodeBehind fájlt egy összeállítási fájlba. A szerelvények zip-fájlként vannak csomagolva, és a feladat Azure-ba küldésekor kerülnek feltöltésre a tárfiókba. Megtudhatja, hogyan írhat C# UDF-et a CodeBehind használatával, ha követi a [C# UDF-et a Stream Analytics Edge-feladatokhoz](stream-analytics-edge-csharp-udf.md) oktatóanyaghoz. 

## <a name="local-project"></a>Helyi projekt
A felhasználó által definiált függvények írhatók egy szerelvény, amely később hivatkozott egy Azure Stream Analytics-lekérdezésben. Ez az ajánlott beállítás olyan összetett függvényekhez, amelyek a kifejezési nyelven túl mutató .NET standard nyelv teljes erejét igénylik, például eljárási logikát vagy rekurziót. Egy helyi projektből származó UDF-ek is használhatók, ha a függvénylogikát több Azure Stream Analytics-lekérdezésben is meg kell osztania. UdF-ek hozzáadása a helyi projekthez lehetővé teszi a funkciók helyi hibakeresését és tesztelését a Visual Studióból.

Helyi projektre való hivatkozás:

1. Hozzon létre egy új osztálytárat a megoldásban.
2. Írd be a kódot az osztályodba. Ne feledje, hogy az osztályokat *nyilvánosként,* az objektumokat pedig *statikus nyilvánosként*kell definiálni. 
3. Hozza létre a projektet. Az eszközök a tárolómappában lévő összes összetevőt egy zip-fájlba csomagolják, és feltöltik a zip fájlt a tárfiókba. Külső referenciák esetén a NuGet csomag helyett használjon összeállítási hivatkozást.
4. Hivatkozzon az új osztályra az Azure Stream Analytics-projektben.
5. Új függvény hozzáadása az Azure Stream Analytics-projekthez.
6. Konfigurálja a szerelvény elérési útját `JobConfig.json`a feladat konfigurációs fájljában, . Állítsa a szerelvény elérési útját **a Helyi projekthivatkozás vagy a CodeBehind elemre.**
7. Építse újra a függvényprojektet és az Azure Stream Analytics projektet is.  

### <a name="example"></a>Példa

Ebben a példában az **UDFTest** egy C# osztálykönyvtár-projekt, az **ASAUDFDemo** pedig az Azure Stream Analytics projekt, amely az **UDFTest-re**hivatkozik.

![Az Azure Stream Analytics IoT Edge projekt a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Készítse el a C# projektet, amely lehetővé teszi, hogy az Azure Stream Analytics-lekérdezésből hivatkozást adjon hozzá a C# UDF-hez.
    
   ![Azure Stream Analytics IoT Edge-projekt létrehozása a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Adja hozzá a hivatkozást a C# projekthez az ASA projektben. Kattintson a jobb gombbal a Hivatkozások csomópontra, és válassza a Hivatkozás hozzáadása parancsot.

   ![Hivatkozás hozzáadása C# projekthez a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Válassza ki a C# projekt nevét a listából. 
    
   ![Válassza ki a C# projekt nevét a hivatkozási listából](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Az **UDF-tesztnek** a **Megoldáskezelő**Hivatkozás listás részén kell **szerepelnie.**

   ![A felhasználó által definiált függvényhivatkozás megtekintése a megoldáskezelőben](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Kattintson a jobb gombbal a **Funkciók** mappára, és válassza **az Új elem lehetőséget**.

   ![Új elem hozzáadása a Függvényekhez az Azure Stream Analytics Edge-megoldásban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Adjon hozzá egy C# **függvényt SquareFunction.json** az Azure Stream Analytics-projekthez.

   ![CSharp-funkció kiválasztása a Visual Studio Stream Analytics Edge-elemei közül](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. A konfigurációs párbeszédpanel megnyitásához kattintson duplán a **Megoldáskezelő** ben lévő funkcióra.

   ![C éles funkciókonfiguráció a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. A C# függvény konfigurációjában válassza a **Betöltés az ASA Project Reference és** a kapcsolódó szerelvény, osztály és metódus neveket a legördülő listából. A Stream Analytics-lekérdezés metódusaira, típusaira és függvényeire való hivatkozáshoz az osztályokat *nyilvánosként* kell definiálni, és az objektumokat *statikus nyilvánosként*kell definiálni.

   ![Stream Analytics C éles függvény konfigurációja](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Meglévő csomagok

A .NET standard UDF-eket tetszőleges ide-ben készítheti, és az Azure Stream Analytics-lekérdezésből hívhatja meg őket. Először fordítsa le a kódot, és csomagolja be az összes DL-t. A csomag formátuma az `/UserCustomCode/CLR/*`elérési utat. Ezután `UserCustomCode.zip` töltse fel a tároló az Azure storage-fiók ban a tároló gyökerét.

Miután a rendszerösszeállítás-zip-csomagokat feltöltötte az Azure storage-fiókjába, használhatja az Azure Stream Analytics-lekérdezések funkcióit. Mindössze annyit kell tennie, hogy tartalmazza a tárolási adatokat a Stream Analytics feladat konfigurációjában. Ezzel a beállítással nem tesztelheti a funkciót helyileg, mert a Visual Studio eszközei nem töltik le a csomagot. A csomag elérési útja közvetlenül a szolgáltatáshoz van elemezve. 

A szerelvény elérési útjának konfigurálása a feladat konfigurációs fájljában, : `JobConfig.json`

Bontsa ki a **Felhasználói kód konfigurációja** szakaszt, és töltse ki a konfigurációt az alábbi javasolt értékekkel:

   |**Beállítás**|**Javasolt érték**|
   |-------|---------------|
   |Globális tárolási beállítások erőforrás|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Globális tárolási beállítások előfizetés| Előfizetési > <|
   |Globális tárterület-tároló fiók| < tárfiók >|
   |Egyéni kódtárolási beállítások erőforrás|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Egyéni kódtárolási beállítások tárfiók|< tárfiók >|
   |Egyéni kódtárolási beállítások tárolója|< tároló>|
   |Egyéni kódösszeállítás forrása|Meglévő összeállítási csomagok a felhőből|
   |Egyéni kódösszeállítás forrása|UserCustomCode.zip|

## <a name="limitations"></a>Korlátozások
Az UDF előzetes verzió jelenleg a következő korlátozásokkal rendelkezik:

* A .NET standard UDF-ek csak a Visual Studióban és az Azure-ban közzétehetők. A .NET standard UDF-ek csak olvasható verziói az Azure Portal **on Functions** területen tekinthetők meg. A .NET Standard függvények készítése nem támogatott az Azure Portalon.

* Az Azure Portal lekérdezésszerkesztője hibát jelez a .NET Standard UDF használatakor a portálon. 

* Mivel az egyéni kód megosztja a környezetet az Azure Stream Analytics-motorral, az egyéni kód nem hivatkozhat semmire, amely ütköző névtérrel/dll_name rendelkezik az Azure Stream Analytics-kóddal. Például nem hivatkozhat *Newtonsoft Jsonra.*

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: C# felhasználó által definiált függvény írása egy Azure Stream Analytics-feladathoz (előzetes verzió)](stream-analytics-edge-csharp-udf.md)
* [Oktatóanyag: Az Azure Stream Analytics felhasználói JavaScript-függvényei](stream-analytics-javascript-user-defined-functions.md)
* [Az Azure Stream Analytics-feladatok megtekintése a Visual Studióval](stream-analytics-vs-tools.md)
