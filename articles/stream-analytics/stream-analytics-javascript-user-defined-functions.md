---
title: Felhasználó által definiált JavaScript-függvények Azure Stream Analytics
description: Ez a cikk a JavaScript felhasználó által definiált függvények bevezetését ismerteti Stream Analyticsban.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc, devx-track-javascript
ms.date: 06/16/2020
ms.openlocfilehash: ff4af372fa0ec1b6b24698184eb3f52449e28d46
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87430811"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Felhasználó által definiált JavaScript-függvények Azure Stream Analytics
 
Az Azure Stream Analytics támogatja a JavaScript nyelven írt felhasználói függvényeket. A JavaScript által biztosított **karakterlánc**-, **regexp**-, **matematikai**, **Array**és **Date** metódusok gazdag készletével könnyebben hozhatók létre összetett adatátalakítások stream Analytics feladatokkal.

## <a name="overview"></a>Áttekintés

A felhasználó által definiált JavaScript-függvények olyan állapot nélküli, csak számítási skaláris függvényeket támogatnak, amelyek nem igényelnek külső kapcsolatot. Egy függvény visszaadott értéke csak skaláris (egyetlen) érték lehet. Miután hozzáadott egy felhasználói JavaScript-függvényt egy feladathoz, bárhol használhatja a függvényt a lekérdezésben, egy beépített skaláris függvényhez hasonlóan.

Az alábbiakban bemutatunk néhány forgatókönyvet, amelyekben hasznosnak találhatja a felhasználói JavaScript-függvényeket:
* Reguláriskifejezés-függvényeket (például: **Regexp_Replace()** és **Regexp_Extract()**) tartalmazó sztringek elemzése és módosítása.
* Adatok dekódolása és kódolása, például bináris adatok átalakítása hexadecimális adatokká.
* Matematikai-számítások végrehajtása JavaScript **matematikai** függvényekkel
* Tömbbeli műveletek, például a rendezés, a csatlakozás, a keresés és a kitöltés

Íme néhány dolog, amit nem lehet a felhasználó által definiált JavaScript-függvénnyel Stream Analytics:
* Külső REST-végpontok meghívása, például fordított IP-címkeresés végrehajtása vagy hivatkozás adatainak külső forrásból való kihúzása
* Egyéni eseményformátum szerializálása vagy deszerializálása bemenetekben/kimenetekben.
* Egyéni összesítések létrehozása.

Bár a függvények, például a **Date. GetDate ()** vagy a **Math. Random ()** függvény nem blokkolja a függvények definícióját, ne használja őket. Ezek a függvények **nem** adják vissza ugyanazt az eredményt minden alkalommal, amikor meghívja őket, és a Azure stream Analytics szolgáltatás nem tartja meg a függvény-meghívások naplóját, és eredményül adja vissza az eredményeket. Ha egy függvény eltérő eredményt ad vissza ugyanazon eseményeken, az ismételhetőség nem garantált, ha Ön vagy a Stream Analytics szolgáltatás újraindította a feladatot.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>JavaScript felhasználó által definiált függvény hozzáadása a feladathoz

> [!NOTE]
> Ezek a lépések a felhőben való futtatásra konfigurált Stream Analytics-feladatokon működnek. Ha a Stream Analytics-feladat úgy van konfigurálva, hogy Azure IoT Edgeon fusson, Ehelyett használja a Visual studiót, és [írja be a felhasználó által definiált függvényt a C# használatával](stream-analytics-edge-csharp-udf.md).

JavaScript felhasználó által definiált függvény létrehozásához a Stream Analytics feladatban válassza a **feladatok** a **feladat topológiája**alatt lehetőséget. Ezután válassza a **JavaScript UDF** elemet a **+** legördülő menüből. 

![JavaScript UDF hozzáadása](./media/javascript/stream-analytics-jsudf-add.png)

Ezután meg kell adnia a következő tulajdonságokat, majd a **Mentés**lehetőséget kell választania.

|Tulajdonság|Leírás|
|--------|-----------|
|Függvény aliasa|Adjon meg egy nevet a függvény meghívásához a lekérdezésben.|
|Kimenet típusa|A JavaScript felhasználó által definiált függvény által visszaadott típust a Stream Analytics lekérdezéshez adja vissza a rendszer.|
|Függvény definíciója|JavaScript-függvény implementálása, amely minden alkalommal végrehajtva lesz, amikor az UDF meghívja a lekérdezést.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>JavaScript UDF tesztelése és hibakeresése 

A JavaScript UDF-logikát bármely böngészőben tesztelheti és hibakeresést végezhet. A felhasználó által definiált függvények logikájának hibakeresése és tesztelése jelenleg nem támogatott a Stream Analytics portálon. Ha a függvény a várt módon működik, a fentiekben leírtak szerint adhatja hozzá a Stream Analytics feladathoz, majd közvetlenül a lekérdezésből hívhatja meg. A lekérdezési logikát a JavaScript UDF használatával tesztelheti a [Visual studióhoz készült stream Analytics eszközökkel](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

A JavaScript futásidejű hibái végzetesnek minősülnek, és a tevékenységnaplóban tekinthetők meg. A napló lekéréséhez lépjen a feladatra az Azure Portalon, és válassza a **Tevékenységnapló** elemet.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Felhasználói JavaScript-függvény meghívása lekérdezésben

A JavaScript-függvényt a lekérdezésben egyszerűen meghívhatja az **UDF**előtaggal megadott függvény alias használatával. Íme egy példa egy JavaScript UDF-re, amely a hexadecimális értékeket egy Stream Analytics lekérdezésben meghívott egész számra konvertálja.

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
Rekord | Objektum
Tömb | Tömb
NULL | Null

A JavaScriptről a Stream Analyticsre történő átalakítások:

JavaScript | Stream Analytics
--- | ---
Szám | Bigint (ha a szám kerek és a long.MinValue és a long.MaxValue közé esik, máskülönben double)
Dátum | DateTime
Sztring | nvarchar(MAX)
Objektum | Rekord
Tömb | Tömb
Null, nem definiált | NULL
Bármely más típus (például függvény vagy hiba) | Nem támogatott (futásidejű hibát eredményez)

A JavaScript nyelv megkülönbözteti a kis-és nagybetűket, és a JavaScript-kódban található objektumok mezőinek meg kell egyeznie a bejövő adatokban lévő mezők házával. A 1,0 kompatibilitási szinttel rendelkező feladatok az SQL SELECT utasítás mezőinek kisbetűs értékre lesznek konvertálva. A 1,1-es és magasabb kompatibilitási szint alatt a SELECT utasítás mezőinek az SQL-lekérdezésben megadottal azonos burkolattal kell rendelkezniük.

## <a name="other-javascript-user-defined-function-patterns"></a>Egyéb felhasználói JavaScript-függvényminták

### <a name="write-nested-json-to-output"></a>Beágyazott JSON írása a kimenetbe

Ha van még egy további feldolgozási lépés, amely a Stream Analytics-feladat kimenetét használja a bemeneteként, és JSON-formátumot igényel, a kimenetbe írhat JSON-sztringet. A következő példa a **JSON.stringify()** függvény meghívásával becsomagolja a bemenetben lévő összes név/érték párt, majd egyetlen sztringértékként írja őket a kimenetbe.

**Felhasználói JavaScript-függvény definíciója:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Példa lekérdezésre:**
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

## <a name="next-steps"></a>Következő lépések

* [UDF Machine Learning](https://docs.microsoft.com/azure/stream-analytics/machine-learning-udf)
* [C# UDF](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf-methods)
