---
title: "Az Azure Stream Analytics JavaScript felhasználó által definiált függvények |} Microsoft Docs"
description: "Hajtsa végre a felhasználó által definiált függvények JavaScript speciális lekérdezési mechanika"
keywords: "JavaScript, felhasználó által definiált feladatokat az UDF-ben"
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: e8c1c784a598416b478d1430258201053185fdee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Az Azure Stream Analytics JavaScript felhasználó által definiált függvények
Az Azure Stream Analytics a felhasználó által definiált függvények JavaScript nyelven írt támogatja. A gazdag készlete **karakterlánc**, **RegExp szolgáltatást**, **matematikai**, **tömb**, és **dátum** módszerek a JavaScript a Stream Analytics-feladatok úgy válnak egyre könnyebben létrehozása összetett adatátalakítást biztosít.

## <a name="javascript-user-defined-functions"></a>Felhasználó által definiált függvények JavaScript
Felhasználó által definiált függvények JavaScript támogatja állapot nélküli, csak számítási skaláris függvények, amelyek nem igényelnek külső kapcsolatot. Egy függvény visszatérési értéke csak a skaláris (önálló) érték lehet. Miután hozzáadta a JavaScript felhasználó által definiált függvény egy feladatot, a funkció használata bárhol a lekérdezésben, például a beépített skaláris függvényt.

Az alábbiakban néhány forgatókönyv, ahol hasznosak lehetnek felhasználó által definiált függvények JavaScript:
* Elemzés és kezelésére, amelyek reguláris kifejezés funkciók, például karakterláncok **Regexp_Replace()** és **Regexp_Extract()**
* Dekódolás és adatok, például bináris hexadecimális átalakítás kódolás
* JavaScript mathematic számításokat végez **matematikai** funkciók
* Például a rendezési, a csatlakozást, a Keresés és a fill tömb műveletek végrehajtása

A következőkben, amely a Stream Analytics egy JavaScript felhasználó által definiált függvény nem hajtható végre:
* Kimenő külső REST-végpontok, például végrehajtása hívás fordított IP keresési vagy referencia történő adatlekérést külső forrásból
* Hajtsa végre az egyéni esemény szerializálás vagy a bemenetek/kimenetek deszerializálási
* Egyéni összesítések létrehozása

Bár a Funkciók, például **Date.GetDate()** vagy **Math.random()** nem blokkolja a funkciók-definícióban az használatuk kerülendő. Ezek a funkciók **nem** ugyanazt az eredményt adja vissza, minden egyes keresheti őket, és az Azure Stream Analytics szolgáltatás nem őrzi meg a napló, a függvény meghívásához, és adott vissza eredményt. Ha a függvény különböző eredményt adja vissza, az azonos esemény, ismételhetőség nem garantált a feladat újraindításakor, akkor vagy a Stream Analytics szolgáltatás.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>A JavaScript felhasználó által definiált függvény hozzáadása az Azure-portálon
Hozzon létre egy egyszerű JavaScript felhasználó által definiált függvényt a meglévő Stream Analytics-feladat, hajtsa végre ezeket a lépéseket:

1.  Az Azure-portálon keresse meg a Stream Analytics-feladat.
2.  A **feladat TOPOLÓGIA**, válassza ki a függvényt. Megjelenik a funkciók listája üres.
3.  Hozzon létre egy új felhasználó által definiált függvényt, jelölje be **Hozzáadás**.
4.  Az a **új függvény** panelen a **függvénytípus**, jelölje be **JavaScript**. Egy alapértelmezett függvény sablon megjelenik a szerkesztőt.
5.  Az a **UDF alias**, adja meg **hex2Int**, és az alábbiak szerint módosítsa a függvény végrehajtása:

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Kattintson a **Mentés** gombra. A függvény függvények listája jelenik meg.
7.  Jelölje be az új **hex2Int** működik, és ellenőrizze a függvény definícióját. Minden függvényeknek a **UDF** előtagot function aliasra. Kell *előtag* függvény hívható a Stream Analytics-lekérdezés. Ebben az esetben hívható **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>A JavaScript felhasználói függvényt hívni a lekérdezésben

1. A lekérdezés-szerkesztő a **feladat TOPOLÓGIA**, jelölje be **lekérdezés**.
2.  Szerkessze a lekérdezést, és majd hívja meg a felhasználó által definiált függvényt, ehhez hasonló:

    ```
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  A minta adatfájl feltöltése, kattintson a jobb gombbal a projekt bemeneti.
4.  Válassza ki a lekérdezés teszteléséhez **tesztelése**.


## <a name="supported-javascript-objects"></a>Támogatott JavaScript-objektumok
Az Azure Stream Analytics JavaScript felhasználó által definiált függvények standard szintű, beépített JavaScript-objektumok támogatja. Ezek az objektumok listáját lásd: [globális objektumok](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>A Stream Analytics és a JavaScript adattípus átalakítása

A típusokat, hogy a Stream Analytics lekérdezési nyelv és a JavaScript-támogatás különbségek vannak. Ez a táblázat felsorolja az átalakítás leképezéseket a kettő között:

Stream Analytics | JavaScript
--- | ---
bigint | Szám (JavaScript csak jelenthet egész számok legfeljebb pontosan 2 ^ 53)
Dátum és idő | Dátum (JavaScript csak támogatja ezredmásodperc)
Dupla | Szám
típus: nvarchar(max) | Karakterlánc
rekord | Objektum
Tömb | Tömb
NULL ÉRTÉKŰ | NULL értékű


Az alábbiakban a JavaScript-Stream Analytics-átalakításhoz:


JavaScript | Stream Analytics
--- | ---
Szám | Bigint (Ha a szám kerek és a hosszú között. A MinValue és a hosszú. MaxValue; Ellenkező esetben dupla)
Dátum | Dátum és idő
Karakterlánc | típus: nvarchar(max)
Objektum | rekord
Tömb | Tömb
NULL, nem definiált | NULL ÉRTÉKŰ
Bármely más típusból (például egy függvény vagy hiba) | Nem támogatott (futásidejű hibát eredményez)

## <a name="troubleshooting"></a>Hibaelhárítás
A JavaScript futásidejű hibák végzetes minősülnek, és a műveletnapló keresztül illesztett. Beolvassa a naplót, az Azure-portálon keresse meg a feladatot, és válassza ki **tevékenységnapló**.


## <a name="other-javascript-user-defined-function-patterns"></a>Más JavaScript felhasználó által definiált függvény minták

### <a name="write-nested-json-to-output"></a>Beágyazott JSON kimeneti írása
Ha a követési feldolgozási lépést, amely a Stream Analytics-feladatot, kimeneti használja bemeneti adatként, és a JSON formátumban van szükség, írhat a kimeneti JSON karakterláncnak. A következő példa hívások a **JSON.stringify()** függvény a bemeneti minden név/érték párok csomag, majd írja őket a kimeneti egyetlen karakterlánc értéket.

**JavaScript felhasználó által definiált függvény definíciója:**

```
function main(x) {
return JSON.stringify(x);
}
```

**Mintalekérdezés:**
```
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>Segítségkérés
Segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Következő lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Az Azure Stream Analytics lekérdezési nyelvi referencia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
