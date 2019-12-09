---
title: JSON a leképezési adatfolyamban
description: Azure Data Factory a leképezési adatfolyam beépített képességekkel rendelkezik a JSON-dokumentumok hierarchiákkal való kezeléséhez
author: kromerm
ms.author: makromer
ms.review: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/30/2019
ms.openlocfilehash: 153c7a1003c68526c960644bebcc4800e92edc3c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928329"
---
# <a name="mapping-data-flow-json-handling"></a>Az adatfolyam JSON-kezelésének leképezése

## <a name="creating-json-structures-in-derived-column"></a>JSON-struktúrák létrehozása származtatott oszlopban

A származtatott oszlop Expression Builder használatával hozzáadhat egy összetett oszlopot az adatfolyamathoz. A származtatott oszlop transzformációjában adjon hozzá egy új oszlopot, és nyissa meg a Kifejezésszerkesztőt a kék mezőre kattintva. Az oszlopok összetett létrehozásához manuálisan megadhatja a JSON-struktúrát, vagy a UX használatával interaktív módon adhat hozzá aloszlopokat.

### <a name="using-the-expression-builder-ux"></a>A Expression Builder UX használata

A kimeneti séma oldali ablaktáblán vigye az egérmutatót egy oszlop fölé, és kattintson a plusz ikonra. Válassza az **aloszlop hozzáadása** lehetőséget az oszlop összetett típusának elvégzéséhez.

![Aloszlop hozzáadása](media/data-flow/addsubcolumn.png "Aloszlop hozzáadása")

Ugyanilyen módon adhat hozzá további oszlopokat és aloszlopokat. Minden nem összetett mezőhöz egy kifejezés adható hozzá a jobb oldali kifejezés-szerkesztőben.

![Összetett oszlop](media/data-flow/complexcolumn.png "Összetett oszlop")

### <a name="entering-the-json-structure-manually"></a>A JSON-struktúra manuális megadása

JSON-struktúra manuális hozzáadásához vegyen fel egy új oszlopot, és adja meg a kifejezést a szerkesztőben. A kifejezés a következő általános formátumot követi:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Ha ez a kifejezés egy "complexColumn" nevű oszlophoz lett megadva, akkor a rendszer a fogadóba írja a következő JSON-t:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Minta manuális parancsfájl a teljes hierarchikus definícióhoz
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>Forrás formátum beállításai

A JSON-adatkészletek adatáramlási forrásaként való használata lehetővé teszi öt további beállítás megadását. Ezek a beállítások a **forrás beállításai** lap **JSON-beállítások** című részében találhatók.  

![JSON-beállítások](media/data-flow/json-settings.png "JSON-beállítások")

### <a name="default"></a>Alapértelmezett

Alapértelmezés szerint a JSON-adatolvasás a következő formátumban történik.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Egyetlen dokumentum

Ha **egyetlen dokumentum** van kiválasztva, az adatforgalom leképezése minden fájlból beolvas egy JSON-dokumentumot. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>Nem jegyzett oszlopnevek

Ha nem **jegyzett oszlopnevek** vannak kiválasztva, az adatfolyamatok leképezése beolvassa azokat a JSON-oszlopokat, amelyeket nem idézőjelek öveznek. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Megjegyzésekkel rendelkezik

Válassza a **Megjegyzések lehetőséget,** ha a JSON-adatként C++ C vagy stílusú Megjegyzés van.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Egyszer idézett

Válassza az **egyszeres** idézőjel lehetőséget, ha a JSON-mezők és-értékek idézőjelek helyett szimpla idézőjeleket használnak.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Megmenekült fordított perjel

Jelölje ki az **egyszeres idézőjelet** , ha fordított perjeleket használ a JSON-Adatkarakterek elmenekülni.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Magasabb rendű függvények

A magasabb rendű függvény egy függvény, amely egy vagy több függvényt argumentumként vesz igénybe. Az alábbi lista a tömböket engedélyező adatfolyamatok leképezése által támogatott magasabb rendű függvények listáját tartalmazza.

### <a name="filter"></a>szűrő
Kiszűri a tömb azon elemeit, amelyek nem felelnek meg a megadott predikátumnak. A szűrő a predikátum függvény egy elemére mutató hivatkozást vár #itemként.

#### <a name="examples"></a>Példák
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

### <a name="map"></a>térkép
A tömb minden elemét egy új elemre képezi le a megadott kifejezés használatával. A Térkép a kifejezés függvény egy elemére mutató hivatkozást vár #itemként.

#### <a name="examples"></a>Példák
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

### <a name="reduce"></a>csökkentheti
Egy tömb elemeinek felhalmozódása. A csökkentés egy gyűjtőre és egy elemre mutató hivatkozást vár az első kifejezésben #acc és #itemként, és az eredményül kapott értéket a második kifejezés függvényében használt #resultnak számítja ki.

#### <a name="examples"></a>Példák
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

### <a name="sort"></a>rendezés
Rendezi a tömböt a megadott predikátum függvény használatával. A rendezés két egymást követő elemre mutató hivatkozást vár a kifejezés függvényben #item1 és #item2.

#### <a name="examples"></a>Példák
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

### <a name="contains"></a>tartalmazza
Igaz értéket ad vissza, ha a megadott tömb bármely eleme igaz értékre értékeli a megadott predikátumban. A tartalmaz egy hivatkozást a predikátum függvény egyik elemére #itemként.

#### <a name="examples"></a>Példák
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Következő lépések

* [A származtatott oszlop transzformációjának használata a hierarchikus struktúrák kiépítéséhez](data-flow-derived-column.md)
