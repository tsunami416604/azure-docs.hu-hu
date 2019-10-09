---
title: Azure Data Factory leképezési adatfolyam JSON-fogalmai
description: Data Factory a leképezési adatfolyam beépített képességekkel rendelkezik a JSON-dokumentumok hierarchiákkal való kezeléséhez
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 37db3e153e8dfcbc1120fcb1f6d2f77187edc78e
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029661"
---
# <a name="mapping-data-flow-json-handling"></a>Az adatfolyam JSON-kezelésének leképezése



## <a name="creating-json-structures-in-expression-editor"></a>JSON-struktúrák létrehozása a kifejezés-szerkesztőben
### <a name="derived-column-transformation"></a>Származtatott oszlop átalakítása
Egy összetett oszlop az adatfolyamathoz való hozzáadása egyszerűbb a származtatott oszlop kifejezés-szerkesztőjén keresztül. Új oszlop hozzáadását és a szerkesztő megnyitását követően két lehetőség közül választhat: írja be a JSON-struktúrát manuálisan, vagy használja a felhasználói felületet az aloszlopok interaktív hozzáadásához.

#### <a name="interactive-ui-json-design"></a>Interaktív felhasználói felület JSON-kialakítása
A kimeneti séma oldali ablaktáblán új aloszlopokat adhat hozzá a `+` menü használatával: ![Aloszlop hozzáadása](media/data-flow/addsubcolumn.png "aloszlop hozzáadása")

Ettől kezdve az új oszlopok és aloszlopok is hozzáadhatók ugyanúgy. Minden nem összetett mezőhöz egy kifejezés adható hozzá a jobb oldali kifejezés-szerkesztőben.

![Összetett oszlop](media/data-flow/complexcolumn.png "összetett oszlopa")

#### <a name="manual-json-design"></a>Kézi JSON-kialakítás
JSON-struktúra manuális hozzáadásához vegyen fel egy új oszlopot, és adja meg a kifejezést a szerkesztőben. A kifejezés a következő általános formátumot követi:
```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```
Ha ezt a kifejezést egy "complexColumn" nevű oszlophoz adta meg, akkor a rendszer a következő JSON-ként írja a fogadóba:
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
### <a name="default"></a>Alapértelmezett
```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Egyetlen dokumentum
* Egy lehetőség
```
[
    {
        "json": "record 1"
    },
    {
        "json": "record 2"
    },
    {
        "json": "record 3"
    }
]
```

* Második lehetőség
```
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
```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Megjegyzésekkel rendelkezik
```
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Egyszer idézett
```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Megmenekült fordított perjel
```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Magasabb rendű függvények
## <a name="filter"></a>filter
Kiszűri a tömb azon elemeit, amelyek nem felelnek meg a megadott predikátumnak. A szűrő a predikátum függvény egy elemére mutató hivatkozást vár #itemként.

### <a name="examples"></a>Példák
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

## <a name="map"></a>térkép
A tömb minden elemét egy új elemre képezi le a megadott kifejezés használatával. A Térkép a kifejezés függvény egy elemére mutató hivatkozást vár #itemként.

### <a name="examples"></a>Példák
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

## <a name="reduce"></a>csökkentheti
Egy tömb elemeinek felhalmozódása. A csökkentés egy gyűjtőre és egy elemre mutató hivatkozást vár az első kifejezésben #acc és #itemként, és az eredményül kapott értéket a második kifejezés függvényében használt #resultnak számítja ki.

### <a name="examples"></a>Példák
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

## <a name="sort"></a>Rendezés
Rendezi a tömböt a megadott predikátum függvény használatával. A rendezés két egymást követő elemre mutató hivatkozást vár a kifejezés függvényben #item1 és #item2.

### <a name="examples"></a>Példák
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

## <a name="contains"></a>contains
Igaz értéket ad vissza, ha a megadott tömb bármely eleme igaz értékre értékeli a megadott predikátumban. A tartalmaz egy hivatkozást a predikátum függvény egyik elemére #itemként.

### <a name="examples"></a>Példák
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>További lépések

* [A származtatott oszlop transzformációjának használata a hierarchikus struktúrák kiépítéséhez](data-flow-derived-column.md)
