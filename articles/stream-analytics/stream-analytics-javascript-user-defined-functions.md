---
title: Az Azure Stream Analytics JavaScript által definiált függvényei
description: Ez a cikk a Stream Analytics JavaScript-felhasználó által definiált függvényeinek bemutatása.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 03/23/2020
ms.openlocfilehash: 58d750b47f3f6a2bcfbf23399ca249131e7876ae
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235392"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>JavaScript-felhasználó által definiált függvények az Azure Stream Analytics szolgáltatásban
 
Az Azure Stream Analytics támogatja a JavaScript nyelven írt felhasználói függvényeket. A JavaScript által biztosított **karakterlánc,** **RegExp**, **Math**, **Tömb**és **Dátum** metódusok gazdag készletével a Stream Analytics-feladatokkal végzett összetett adatátalakítások egyszerűbbé válnak.

## <a name="overview"></a>Áttekintés

A JavaScript-felhasználó által definiált függvények támogatják az állapotnélküli, csak számítási skaláris függvényeket, amelyek nem igényelnek külső kapcsolatot. Egy függvény visszaadott értéke csak skaláris (egyetlen) érték lehet. Miután hozzáadott egy felhasználói JavaScript-függvényt egy feladathoz, bárhol használhatja a függvényt a lekérdezésben, egy beépített skaláris függvényhez hasonlóan.

Az alábbiakban bemutatunk néhány forgatókönyvet, amelyekben hasznosnak találhatja a felhasználói JavaScript-függvényeket:
* Reguláriskifejezés-függvényeket (például: **Regexp_Replace()** és **Regexp_Extract()**) tartalmazó sztringek elemzése és módosítása.
* Adatok dekódolása és kódolása, például bináris adatok átalakítása hexadecimális adatokká.
* Matematikai számítások végzett a JavaScript **Matematikai** függvényekkel
* Tömbműveletek, például rendezés, illesztés, keresés és kitöltés

Íme néhány dolog, amit nem tehet meg egy JavaScript-felhasználó által definiált funkcióval a Stream Analytics szolgáltatásban:
* Külső REST-végpontok kihívása, például fordított IP-keresési lekérdezés vagy referenciaadatok lekérdezése külső forrásból
* Egyéni eseményformátum szerializálása vagy deszerializálása bemenetekben/kimenetekben.
* Egyéni összesítések létrehozása.

Bár a függvények, például **a Date.GetDate()** vagy a **Math.random()** függvények definíciója nincs letiltva, kerülje a használatukat. Ezek a függvények **nem** ugyanazt az eredményt adják vissza minden alkalommal, amikor meghívja őket, és az Azure Stream Analytics szolgáltatás nem vezet naplót a függvény-meghívások és a visszaadott eredmények. Ha egy függvény ugyanazon eseményeken eltérő eredményt ad vissza, az ismételhetőség nem garantált, ha ön vagy a Stream Analytics szolgáltatás újraindítja a feladatot.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>JavaScript-felhasználó által definiált függvény hozzáadása a feladathoz

> [!NOTE]
> Ezek a lépések a felhőben való futtatásra konfigurált Stream Analytics-feladatokon működnek. Ha a Stream Analytics-feladat úgy van beállítva, hogy az Azure IoT Edge-en fusson, használja a Visual Studio-t, és [írja meg a felhasználó által definiált függvényt a C# használatával.](stream-analytics-edge-csharp-udf.md)

Ha JavaScript-felhasználó által definiált függvényt szeretne létrehozni a Stream Analytics-feladatban, válassza **a Funkciók** lehetőséget a **Feladattopológia**csoportban. Ezután válassza a **JavaScript UDF** lehetőséget a **+Add** legördülő menüből. 

![JavaScript UDF hozzáadása](./media/javascript/stream-analytics-jsudf-add.png)

Ezután meg kell adnia a következő tulajdonságokat, és válassza a **Mentés lehetőséget.**

|Tulajdonság|Leírás|
|--------|-----------|
|Függvény aliasa|Írjon be egy nevet a függvény meghívásához a lekérdezésben.|
|Kimenet típusa|Írja be, amelyet a JavaScript felhasználó által definiált függvény visszaad a Stream Analytics-lekérdezésnek.|
|Függvény definíciója|A JavaScript-függvény implementációja, amely minden alkalommal végrehajtásra kerül, amikor az UDF meghívást kap a lekérdezésből.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>JavaScript UDF-ek tesztelése és hibaelhárítása 

A JavaScript UDF logikát bármely böngészőben tesztelheti és debugolhatja. A felhasználó által definiált függvények logikájának hibakeresésés tesztelése jelenleg nem támogatott a Stream Analytics portálon. Miután a függvény a várt módon működik, hozzáadhatja a stream analytics-feladathoz a fent említettek szerint, majd közvetlenül a lekérdezésből hívhatja meg. A lekérdezési logikát javaScript UDF-fel tesztelheti [a Visual Studio Stream Analytics eszközeivel.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)

A JavaScript futásidejű hibái végzetesnek minősülnek, és a tevékenységnaplóban tekinthetők meg. A napló lekéréséhez lépjen a feladatra az Azure Portalon, és válassza a **Tevékenységnapló** elemet.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Felhasználói JavaScript-függvény meghívása lekérdezésben

Könnyedén meghívhatja a JavaScript függvényt a lekérdezésben az **udf**előtaggal ellátott függvény alias használatával. Íme egy példa egy JavaScript UDF-re, amely hexadecimális értékeket egész számmá alakít egy Stream Analytics-lekérdezésben.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>Támogatott JavaScript-objektumok

Az Azure Stream Analytics felhasználói JavaScript-függvényei támogatják a szabványos, beépített JavaScript-objektumokat. Az objektumok listáját a [globális objektumokkal](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects) foglalkozó cikkben tekintheti meg.

### <a name="stream-analytics-and-javascript-type-conversion"></a>Típusátalakítás a Stream Analytics és a JavaScript között

A Stream Analytics lekérdezési nyelv és a JavaScript által támogatott típusok között különbségek vannak. Az alábbi táblázat a kettő közötti átalakítás megfeleléseit tartalmazza:

Stream Analytics | JavaScript
--- | ---
bigint | Szám (a JavaScript legfeljebb pontosan a 2^53-ig tudja a számokat kezelni)
DateTime | Dátum (a JavaScript csak az ezredmásodperceket támogatja)
double | Szám
nvarchar(MAX) | Sztring
Record | Objektum
Tömb | Tömb
NULL | Null

A JavaScriptről a Stream Analyticsre történő átalakítások:

JavaScript | Stream Analytics
--- | ---
Szám | Bigint (ha a szám kerek és a long.MinValue és a long.MaxValue közé esik, máskülönben double)
Dátum | DateTime
Sztring | nvarchar(MAX)
Objektum | Record
Tömb | Tömb
Null, nem definiált | NULL
Bármely más típus (például függvény vagy hiba) | Nem támogatott (futásidejű hibát eredményez)

A JavaScript nyelve a kis- és nagybetűket, és a JavaScript-kódban lévő objektummezők burkolatának meg kell egyeznie a bejövő adatok mezőinek burkolatával. Az 1.0-s kompatibilitási szinttel rendelkező feladatok az SQL SELECT utasításmezőit kisbetűssé alakítják. Az 1.1-es és újabb kompatibilitási szint esetén a SELECT utasítás mezőinek az SQL-lekérdezésben megadott akta lesz.

## <a name="other-javascript-user-defined-function-patterns"></a>Egyéb felhasználói JavaScript-függvényminták

### <a name="write-nested-json-to-output"></a>Beágyazott JSON írása a kimenetbe

Ha van még egy további feldolgozási lépés, amely a Stream Analytics-feladat kimenetét használja a bemeneteként, és JSON-formátumot igényel, a kimenetbe írhat JSON-sztringet. A következő példa a **JSON.stringify()** függvény meghívásával becsomagolja a bemenetben lévő összes név/érték párt, majd egyetlen sztringértékként írja őket a kimenetbe.

**Felhasználói JavaScript-függvény definíciója:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Mintalekérdezés:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="next-steps"></a>További lépések

* [Gépi tanulási UDF](https://docs.microsoft.com/azure/stream-analytics/machine-learning-udf)
* [C# UDF](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf-methods)
