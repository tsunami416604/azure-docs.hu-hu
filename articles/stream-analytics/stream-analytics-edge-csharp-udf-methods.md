---
title: .NET-es szabványos függvények fejlesztése Azure Stream Analytics feladatokhoz (előzetes verzió)
description: Útmutató c# felhasználó által definiált függvények írásához Stream Analytics feladatokhoz.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: 4f9d117ccc763744411bfe24163ed955532e8e56
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921851"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>.NET-es szabványos felhasználó által definiált függvények fejlesztése Azure Stream Analytics feladatokhoz (előzetes verzió)

A Azure Stream Analytics SQL-szerű lekérdezési nyelvet kínál az adatstreamek közötti átalakítások és számítások végrehajtásához. Számos beépített függvény létezik, de bizonyos összetett forgatókönyvek további rugalmasságot igényelnek. A .NET Standard felhasználó által definiált függvények (UDF) használatával meghívhatja a saját függvényeit bármely .NET Standard nyelven (C#, F # stb.), hogy kiterjessze a Stream Analytics lekérdezési nyelvet. A UDF lehetővé teszi összetett matematikai számítások végrehajtását, egyéni ML-modellek importálását a ML.NET használatával, és a hiányzó adatokhoz egyéni imputálási logikát használhat. Stream Analytics feladatok UDF-funkciója jelenleg előzetes verzióban érhető el, ezért nem használható éles környezetben.

A felhőalapú feladatok .NET-felhasználó által definiált függvénye a (z) rendszerben érhető el:
* USA nyugati középső régiója
* Észak-Európa
* USA keleti régiója
* USA nyugati régiója
* USA 2. keleti régiója
* Nyugat-Európa

Ha más régiókban szeretné használni ezt a funkciót, [hozzáférés kérhető](https://aka.ms/ccodereqregion).

## <a name="overview"></a>Áttekintés
A Visual Studio Tools for Azure Stream Analytics megkönnyíti a UDF írását, a feladatok helyi tesztelését (még kapcsolat nélküli üzemmódban), és a Stream Analytics feladat közzétételét az Azure-ban. Miután közzétette az Azure-ban, üzembe helyezheti a feladatot, hogy IoT az eszközöket IoT Hub használatával.

A UDF háromféle módon valósítható meg:

* CodeBehind-fájlok egy ASA-projektben
* UDF helyi projektből
* Egy meglévő csomag egy Azure Storage-fiókból

## <a name="package-path"></a>Csomag elérési útja

Az UDF-csomagok formátuma az elérési út `/UserCustomCode/CLR/*` . A rendszer a dinamikus csatolású kódtárakat (DLL-eket) és az erőforrásokat a `/UserCustomCode/CLR/*` mappában másolja, ami segít a felhasználói DLL-fájlok elkülönítésében a rendszer-és Azure stream Analytics dll-eken. Ez a csomag elérési útja minden függvényhez használatos, függetlenül az azok alkalmazására használt módszertől.

## <a name="supported-types-and-mapping"></a>Támogatott típusok és leképezés
A C# nyelvben használandó Azure Stream Analytics értékeket az egyik környezetből a másikba kell átadni. A átadás az UDF összes bemeneti paramétere esetében történik. Minden Azure Stream Analytics típusnak megfelelő típusúnak kell lennie a C#-ban az alábbi táblázatban látható módon:

|**Azure Stream Analytics típusa** |**C# típus** |
|---------|---------|
|bigint | hosszú |
|lebegőpontos | double |
|nvarchar (max.) | sztring |
|dátum/idő | DateTime |
|Rekord | Szótár\<string, object> |
|Tömb | Objektum [] |

Ugyanez igaz, ha az adatokat a C# rendszerből Azure Stream Analyticsre kell átadni, ami az UDF kimeneti értékén történik. Az alábbi táblázat a támogatott típusokat mutatja:

|**C# típus**  |**Azure Stream Analytics típusa**  |
|---------|---------|
|hosszú  |  bigint   |
|double  |  lebegőpontos   |
|sztring  |  nvarchar (max.)   |
|DateTime  |  dateTime   |
|struct  |  Rekord   |
|objektum  |  Rekord   |
|Objektum []  |  Tömb   |
|Szótár\<string, object>  |  Rekord   |

## <a name="codebehind"></a>CodeBehind
Felhasználó által definiált függvényeket írhat a **script. asql** Codebehind. A Visual Studio Tools automatikusan lefordítja a CodeBehind-fájlt egy Assembly-fájlba. A szerelvények zip-fájlként vannak csomagolva, és feltöltve lesznek a Storage-fiókjába, amikor elküldi a feladatot az Azure-ba. Megtudhatja, hogyan írhat egy C# UDF-t az CodeBehind használatával a [C# UDF for stream Analytics Edge feladatok](stream-analytics-edge-csharp-udf.md) oktatóanyagának követésével. 

## <a name="local-project"></a>Helyi projekt
A felhasználó által definiált függvények olyan szerelvényben írhatók be, amely később egy Azure Stream Analytics lekérdezésben hivatkozik rá. Ez az ajánlott lehetőség olyan összetett függvények esetén, amelyek a .NET szabvány nyelvének teljes hatékonyságát igénylik a kifejezés nyelvén, például az eljárási logikán vagy a rekurzión kívül. Helyi projektből származó UDF is használható, ha több Azure Stream Analytics lekérdezésben kell megosztania a függvény logikáját. A UDF a helyi projekthez való hozzáadása lehetővé teszi a függvények hibakeresését és tesztelését helyileg a Visual studióból.

Helyi projektre való hivatkozás:

1. Hozzon létre egy új osztály-függvénytárat a megoldásban.
2. Írja be a kódot az osztályba. Ne feledje, hogy az osztályokat *nyilvánosként* kell definiálni, és az objektumokat *statikus nyilvánosként*kell definiálni. 
3. Hozza létre a projektet. Az eszközök a bin mappában lévő összes összetevőt egy zip-fájlba csomagolják, és feltöltik a zip-fájlt a Storage-fiókba. Külső hivatkozások esetében a NuGet-csomag helyett használjon szerelvény-hivatkozást.
4. Hivatkozzon az új osztályra a Azure Stream Analytics projektben.
5. Új függvény hozzáadása a Azure Stream Analytics projektben.
6. Konfigurálja a szerelvény elérési útját a feladatok konfigurációs fájljában `JobConfig.json` . Állítsa be a szerelvény elérési útját a **helyi projekt referenciájának vagy a Codebehind**.
7. Hozza létre újra a Function projektet és a Azure Stream Analytics projektet.  

### <a name="example"></a>Példa

Ebben a példában a **UDFTest** egy C#-beli függvénytár-projekt, és a **ASAUDFDemo** a Azure stream Analytics projekt, amely a **UDFTest**-re hivatkozik.

![Azure Stream Analytics IoT Edge-projekt a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Hozzon létre egy C#-projektet, amely lehetővé teszi a C# UDF-re mutató hivatkozás hozzáadását a Azure Stream Analytics lekérdezésből.
    
   ![Azure Stream Analytics IoT Edge-projekt létrehozása a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Adja hozzá a C#-projektre mutató hivatkozást az ASA projektben. Kattintson a jobb gombbal a hivatkozások csomópontra, majd válassza a hivatkozás hozzáadása parancsot.

   ![Hivatkozás hozzáadása C#-projekthez a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Válassza ki a C#-projekt nevét a listából. 
    
   ![Válassza ki a C#-projekt nevét a hivatkozási listából](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Meg kell jelennie a **Megoldáskezelőban**található **referenciák** alatt felsorolt **UDFTest** .

   ![A felhasználó által definiált függvény hivatkozásának megtekintése a megoldás Explorerben](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Kattintson a jobb gombbal a **functions** mappára, és válassza az **új elem**lehetőséget.

   ![Új elem hozzáadása a functions szolgáltatáshoz Azure Stream Analytics Edge-megoldásban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Adjon hozzá egy C#-függvényt **SquareFunction.jsa** Azure stream Analytics projekthez.

   ![A Visual Studióban Stream Analytics Edge-elemek CSharp függvényének kiválasztása](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Kattintson duplán a függvényre **megoldáskezelő** a konfigurációs párbeszédpanel megnyitásához.

   ![C Sharp Function konfiguráció a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. A C# függvény konfigurációjában válassza a **Betöltés az ASA-projekthez** és a kapcsolódó szerelvény, osztály és metódus nevét a legördülő listából. A Stream Analytics lekérdezés módszereire, típusaira és funkcióira vonatkozóan az osztályokat *nyilvánosként* kell definiálni, és az objektumokat *statikus nyilvánosként*kell definiálni.

   ![Stream Analytics C Sharp függvény konfigurálása](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Meglévő csomagok

Bármilyen IDE-ban létrehozhat .NET Standard szintű UDF, és meghívhatja őket a Azure Stream Analytics-lekérdezésből. Először fordítsa le a kódot, és csomagolja ki a DLL-eket. A csomag formátuma az elérési út `/UserCustomCode/CLR/*` . Ezután töltse fel `UserCustomCode.zip` Az Azure Storage-fiókjában lévő tároló gyökerébe.

Miután feltöltötte az összeállítási zip-csomagokat az Azure Storage-fiókjába, használhatja Azure Stream Analytics lekérdezések funkcióit. Mindössze annyit kell tennie, hogy tartalmazza a tárolási adatokat a Stream Analytics feladatok konfigurációjában. Ezzel a beállítással helyileg nem tesztelheti a függvényt, mert a Visual Studio-eszközök nem töltik le a csomagot. A csomag elérési útját közvetlenül a szolgáltatásra kell elemezni. 

A szerelvény elérési útjának konfigurálásához a feladatok konfigurációs fájljában `JobConfig.json` :

Bontsa ki a **Felhasználói kód konfigurációja** szakaszt, és töltse ki a konfigurációt az alábbi javasolt értékekkel:

   |**Beállítás**|**Javasolt érték**|
   |-------|---------------|
   |Globális tárolási beállítások erőforrás|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Globális tárolási beállítások előfizetése| < az előfizetést >|
   |Globális tárolási beállítások Storage-fiókja| < a Storage-fiókját >|
   |Egyéni kód tárolási beállításainak erőforrása|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Egyéni kód tárolási beállításainak Storage-fiókja|< a Storage-fiókját >|
   |Egyéni kód tárolási beállításainak tárolója|< a Storage-tárolót >|
   |Egyéni kód szerelvényének forrása|Meglévő szerelvény-csomagok a felhőből|
   |Egyéni kód szerelvényének forrása|UserCustomCode.zip|

## <a name="user-logging"></a>Felhasználói naplózás
A naplózási mechanizmus lehetővé teszi, hogy egyéni adatokat rögzítsen a feladatok futtatása közben. A naplózási adatok segítségével valós időben végezhet hibakeresést vagy felmérni az egyéni kód helyességét.

A `StreamingContext` osztály lehetővé teszi a diagnosztikai adatok közzétételét a `StreamingDiagnostics.WriteError` függvény használatával. Az alábbi kód a Azure Stream Analytics által elérhetővé tett felületet mutatja.

```csharp
public abstract class StreamingContext
{
    public abstract StreamingDiagnostics Diagnostics { get; }
}

public abstract class StreamingDiagnostics
{
    public abstract void WriteError(string briefMessage, string detailedMessage);
}
```

`StreamingContext`a rendszer az UDF metódus bemeneti paramétereként adja át, és az UDF-n belül használható az egyéni napló adatainak közzétételéhez. Az alábbi példában `MyUdfMethod` egy adatbevitelt **data** határoz meg, amelyet a lekérdezés biztosít, valamint egy **környezeti** bemenetet, amelyet a `StreamingContext` futásidejű motor biztosít. 

```csharp
public static long MyUdfMethod(long data, StreamingContext context)
{
    // write log
    context.Diagnostics.WriteError("User Log", "This is a log message");
    
    return data;
}
```

Az `StreamingContext` értéket nem kell átadni az SQL-lekérdezésnek. A Azure Stream Analytics automatikusan helyi objektumot biztosít, ha van bemeneti paraméter. A `MyUdfMethod` nem változik, ahogy az a következő lekérdezésben is látható:

```sql
SELECT udf.MyUdfMethod(input.value) as udfValue FROM input
```

A naplózási üzeneteket a [diagnosztikai naplókon](data-errors.md)keresztül érheti el.

## <a name="limitations"></a>Korlátozások
Az UDF előzetes verziója jelenleg a következő korlátozásokkal rendelkezik:

* A .NET Standard UDF csak a Visual Studióban hozhatók létre és tehetők közzé az Azure-ban. A .NET Standard UDF csak olvasható verzióit tekintheti meg a Azure Portal **függvények** területén. A .NET Standard függvények készítése nem támogatott a Azure Portalban.

* A Azure Portal Query Editor hibaüzenetet jelenít meg, amikor a .NET Standard UDF-t használja a portálon. 

* Mivel az egyéni kódok Azure Stream Analytics motorral vannak megosztva, az egyéni kód nem hivatkozhat olyanra, amely ütköző névtérrel/dll_name Azure Stream Analytics kóddal rendelkezik. Nem hivatkozhat például a *Newtonsoft JSON*-ra.

* A projektben található támogató fájlokat a rendszer átmásolja a felhasználói egyéni kód zip-fájlba, amelyet a rendszer a feladatok felhőbe való közzétételekor használ. Az almappákban található összes fájl közvetlenül a felhasználó egyéni kód mappájának gyökerére lesz másolva a kibontott kibontáskor. A zip "lapított", ha ki van tömörítve.

* A felhasználó egyéni kódja nem támogatja az üres mappákat. Ne adjon üres mappákat a projektben található támogató fájlokhoz.

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: egy Azure Stream Analytics feladathoz tartozó C# felhasználó által definiált függvény írása (előzetes verzió)](stream-analytics-edge-csharp-udf.md)
* [Oktatóanyag: Az Azure Stream Analytics felhasználói JavaScript-függvényei](stream-analytics-javascript-user-defined-functions.md)
* [A Visual Studio használata Azure Stream Analytics feladatok megtekintéséhez](stream-analytics-vs-tools.md)
