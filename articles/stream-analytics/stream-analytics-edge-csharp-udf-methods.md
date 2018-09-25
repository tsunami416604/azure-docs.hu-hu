---
title: Fejlesztés a .NET Standard, felhasználó által definiált függvények az Azure Stream Analytics Edge-feladatok (előzetes verzió)
description: Ismerje meg, hogyan írhat c# felhasználó által definiált függvények a Stream Analytics Edge-feladatok.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9aa61e95eb808c38646fa9b8cefd4004f5477ee6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974663"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-edge-jobs-preview"></a>Fejlesztés a .NET Standard, felhasználó által definiált függvények az Azure Stream Analytics Edge-feladatok (előzetes verzió)

Az Azure Stream Analytics egy SQL-szerű lekérdezési nyelvet, átalakítás és számítások végrehajtásához esemény adatfolyamon keresztül biztosít. Számos beépített függvényt, de néhány összetett forgatókönyvek igényelnek további rugalmasságot biztosít. A .NET Standard felhasználó által definiált függvények (UDF) bármilyen nyelven írt .NET standard (C#, F #, stb.) a Stream Analytics lekérdezési nyelv kiterjeszteni a saját függvények hívhatók meg. UDF-EK segítségével összetett matematikai számítások végrehajtása, importálja az egyéni gépi Tanulási modelleket ML.NET használatával, és egyéni imputálási logika használata a hiányzó adatok. A Stream Analytics Edge-feladatok UDF funkció jelenleg előzetes verzióban, és nem használható éles számítási feladatokhoz.

## <a name="overview"></a>Áttekintés
Visual Studio-eszközök az Azure Stream Analytics könnyebben írhat UDF-EK, tesztelje helyben a feladatok (akár kapcsolat nélküli), és tegye közzé a Stream Analytics-feladatot az Azure-bA. Miután közzétette az Azure-ba, a feladat telepítheti az IoT-eszközök IoT Hub használatával.

Háromféleképpen UDF-EK végrehajtásához:

* Az ASA-projekt háttérkódban fájlok
* A helyi projektből UDF
* Az Azure storage-fiók egy meglévő csomaghoz

## <a name="package-path"></a>Alkalmazáscsomag elérési útja

Minden olyan UDF-csomag formátuma rendelkezik az elérési út `/UserCustomCode/CLR/*`. A dinamikus csatolású Kódtárai (dll) és az erőforrások kerülnek a `/UserCustomCode/CLR/*` mappát, amely segít elkülöníteni a rendszer felhasználói DLL-eket és az Azure Stream Analytics DLL-ek.

## <a name="supported-types-and-mapping"></a>Támogatott típusok és -leképezés

|**Az UDF-típus (C#)**  |**Írja be a az Azure Stream Analytics**  |
|---------|---------|
|Logikai  |  bigint   |
|int32  |  bigint   |
|Int64  |  bigint   |
|lebegőpontos  |  double   |
|double  |  double   |
|sztring  |  típus: nvarchar(max)   |
|Dátum és idő  |  Dátum és idő   |
|typ struktura  |  IRecord   |
|objektum  |  IRecord   |
|Tömb  |  IArray   |
|Dictionary < karakterlánc, objektum >  |  IRecord   |

## <a name="codebehind"></a>Háttérkódban
Felhasználó által definiált függvények írhat a **Script.asql** háttérkódban. Visual Studio-eszközök automatikusan fog a háttérkódban fájl fordítható szerelvényfájlt. A szerelvények zip-fájlként csomagolása és feltölteni a tárfiókba az Azure-bA a feladat elküldésekor. Megismerheti háttérkódban használja az alábbi C# UDF írni a [UDF C# a Stream Analytics Edge-feladatok](stream-analytics-edge-csharp-udf.md) oktatóanyag. 

## <a name="local-project"></a>Helyi projekt
Felhasználó által definiált függvények csak írható később az Azure Stream Analytics-lekérdezések a hivatkozott szerelvények. Ez a lehetőség ajánlott az összetett átadhatók függvényt visszaadó kifejezés nyelve, például eljárási logikai vagy rekurzió túli .NET Standard nyelvének minden előnyét. A helyi projektből UDF-EK előfordulhat, hogy is használható, amikor szüksége van, a függvény logika megosztása több Azure Stream Analytics-lekérdezés. Adja hozzá a helyi projekthez UDF-EK révén a hibakeresését és tesztelését a függvények helyi a Visual Studióból.

Egy helyi projekt kíván hivatkozni:

1. Hozzon létre egy új osztálytár megoldását.
2. A kód írása az osztályban található. Ne feledje, hogy az osztályok megadott értékének *nyilvános* és objektumok megadott értékének *statikus nyilvános*. 
3. A projekt buildjének elkészítéséhez.
4. Hivatkozás az új osztályt az Azure Stream Analytics-projektben.
5. Adjon hozzá egy új funkció az Azure Stream Analytics-projektben.
6. Szerelvény elérési útjának konfigurálása a feladat konfigurációs fájlban `EdgeJobConfig.json`.
7. A Functions-projektet, mind az Azure Stream Analytics-projekt újraépítése.  

### <a name="example"></a>Példa

Ebben a példában **UDFTest** van egy C# hordozhatóosztálytár-projektjének és **ASAEdgeUDFDemo** az Azure Stream Analytics Edge-projekt, amelyre hivatkozhat a **UDFTest**.

![Az Azure Stream Analytics az IoT Edge-projektre a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. C#-projektben, amely lehetővé teszi, hogy vegyen fel egy hivatkozást a C# UDF-ben az Azure Stream Analytics-lekérdezés létrehozása.
    
   ![Hozza létre egy Azure Stream Analytics az IoT Edge-projektet a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Az ASA Edge-projektben adja hozzá a C#-projektben mutató hivatkozást. Kattintson a jobb gombbal a hivatkozás-csomópontot, és válassza ki a hivatkozás hozzáadása.

   ![Fel kell vennie egy hivatkozást egy C#-projektet a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. A listáról válassza ki a C# projekt nevét. 
    
   ![Válassza ki a C# projekt neve a referencia-listából](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Megtekintheti a **UDFTest** alatt felsorolt **hivatkozások** a **Megoldáskezelőben**.

   ![A megadott függvény hivatkozási megtekintése a felhasználó a az Azure Stream Analytics peremhálózati megoldáskezelőben](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Kattintson a jobb gombbal a **funkciók** mappát, majd **új elem**.

   ![Az Azure Stream Analytics peremhálózati megoldást a Visual Studióban a funkciók egy új elem hozzáadása](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Adja hozzá a C#-függvény **SquareFunction.json** Azure Stream Analytics-projektjéhez.

   ![A Visual Studióban a Stream Analytics peremhálózati elemek listájából válassza ki a C Sharp függvényt](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Kattintson duplán a függvény a **Megoldáskezelőben** a konfiguráció párbeszédpanel megnyitásához.

   ![C sharp függvény konfigurálása a Visual Studióban](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. Válassza ki a C# függvény konfigurációjában, **ASA projekt referencia terhelés** és a kapcsolódó metódus, szerelvény és osztály nevét a legördülő listából. Tekintse meg a módszereket, típusok és a Stream Analytics peremhálózati lekérdezésben szereplő funkciók, az osztályok definiálni kell *nyilvános* és az objektumok megadott értékének *statikus nyilvános*.

   ![Stream Analytics C sharp függvény konfigurálása](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Meglévő csomagok

Hozhat létre a .NET Standard UDF-EK bármilyen tetszőleges IDE-ben, és meghívni őket az Azure Stream Analytics-lekérdezés. Először fordítsa le a kód és a csomag összes a dll-fájl. A csomag formátuma rendelkezik az elérési út `/UserCustomCode/CLR/*`. Ezután töltse fel `UserCustomCode.zip` a legfelső szintű a tároló az Azure storage-fiókban.

Szerelvény tömörített csomagok fel lett töltve az Azure storage-fiókot, ha az funkciók is használhatja az Azure Stream Analytics-lekérdezések. Ehhez szüksége a Stream Analytics Edge-feladat konfigurálása a a storage-adatokat tartalmazó. A függvény helyi ezt a beállítást nem lehet tesztelni, mert Visual Studio-eszközök nem tölti le a csomag. Az alkalmazáscsomag elérési útja közvetlenül a szolgáltatást a rendszer elemzi. 

A feladat konfigurációs fájlban, a "EdgeJobConfig.json" szerelvény elérési útjának megadása:

Bontsa ki a **felhasználó által megadott kódot konfigurációs** szakaszt, és töltse ki a konfigurációt az alábbi javasolt értékeket:

 |**Beállítás**  |**Ajánlott érték**  |
 |---------|---------|
 |Szerelvény forrás  |  A projekt helyi hivatkozás vagy háttérkódban   |
 |Erőforrás  |  Válassza ki az adatok az aktuális fiók   |
 |Előfizetés  |  Válassza ki az előfizetését.   |
 |Tárfiók  |  Válassza ki a tárfiókját.   |
 |Tároló  |  Válassza ki a storage-fiókban létrehozott tárolóba.   |

    ![Azure Stream Analytics Edge job configuration in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-job-config.png)

## <a name="limitations"></a>Korlátozások
Az UDF-előzetes verzió jelenleg a következő korlátozások vonatkoznak:

* .NET standard nyelvek csak akkor használható, az Azure Stream Analytics az IoT Edge-ben. Felhőalapú feladat, a felhasználó által definiált JavaScript függvények írhat. További tudnivalókért látogasson el a [Azure Stream Analytics JavaScript UDF](stream-analytics-javascript-user-defined-functions.md) oktatóanyag.

* .NET standard UDF-EK csak a Visual Studióban létrehozott, és Azure-ban közzétett. .NET Standard UDF-EK csak olvasható verzióját menüpontban tekinthetők **funkciók** az Azure Portalon. A .NET Standard függvények létrehozása az Azure Portalon nem támogatott.

* Az Azure portal lekérdezésszerkesztője jelez hibát, .NET Standard UDF használatakor a portálon. 

* Az egyéni kód környezet osztanak meg az Azure Stream Analytics-motor, mert egyéni kódot nem hivatkozhat, amelyeket egy ütköző névtér/DLL_név Azure Stream Analytics kóddal rendelkezik. Ha például nem hivatkozhat *Newtonsoft Json*.

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Írhat egy C# nyelvű felhasználó által definiált függvény egy Azure Stream Analytics Edge-feladat (előzetes verzió)](stream-analytics-edge-csharp-udf.md)
* [Oktatóanyag: Azure Stream Analytics JavaScript-a felhasználó által definiált függvények](stream-analytics-javascript-user-defined-functions.md)
* [A Visual Studio használata az Azure Stream Analytics-feladatok megtekintése](stream-analytics-vs-tools.md)
