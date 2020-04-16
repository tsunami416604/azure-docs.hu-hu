---
title: Átalakítás összeolvasztása az adatfolyam leképezésében
description: Denormalizálás a hierarchikus adatokat az összeolvasztási transzformáció használatával
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413681"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Átalakítás összeolvasztása az adatfolyam leképezésében

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az összeolvasztási transzformáció segítségével tömbértékeket vehet fel hierarchikus struktúrákon belül, például a JSON-on belül, és egyes sorokba húzva azokat. Ezt a folyamatot denormalizációnak nevezzük.

## <a name="configuration"></a>Konfiguráció

Az összeolvasztási transzformáció a következő konfigurációs beállításokat tartalmazza:

![Beállítások összeolvasztása](media/data-flow/flatten1.png "Beállítások összeolvasztása")

### <a name="unroll-by"></a>Kitekerés

Jelöljön ki egy letekerni kívánt tömböt. A kimeneti adatok minden tömbben elemenként egy sort kapnak. Ha a bemeneti sorban lévő tömb letekerése null értékű vagy üres, akkor egy kimeneti sor lesz, amelynek kibontott értékei nullértékűek.

### <a name="unroll-root"></a>Gyökér kitekerése

Alapértelmezés szerint az összeolvasztási transzformáció kiteker idomítja a tömböt annak a hierarchiának a tetejére, amelyben létezik. Tetszés szerint kiválaszthat egy tömböt a roll-gyökér kioldásaként. A letekerési gyökérnek olyan összetett objektumok tömbjének kell lennie, amelyek tömb szerint vannak vagy tartalmazzák a letekerést. Ha a letekerési gyökér van kiválasztva, a kimeneti adatok elemenként legalább egy sort tartalmaznak a roll gyökérben. Ha a bemeneti sorban nincs elem a roll gyökér, akkor el kell dobni a kimeneti adatokat. A letekerési gyökér kiválasztása mindig kevesebb vagy egyenlő számú sort eredményez, mint az alapértelmezett viselkedés.

### <a name="flatten-mapping"></a>Leképezés összeolvasztása

A select átalakításhoz hasonlóan válassza ki az új struktúra vetületét a bejövő mezőkből és a denormalizált tömbből. Ha egy denormalizált tömb van leképezve, a kimeneti oszlop ugyanaz lesz az adattípus, mint a tömb. Ha a letekerés tömb szerint olyan összetett objektumok tömbje, amelyek résztömböket tartalmaznak, a subarry egy elemének leképezése tömböt fog kiadni.

A leképezési kimenet ellenőrzéséhez tekintse meg a vizsgálat lapot és az adatok előnézetét.

## <a name="examples"></a>Példák

Az alábbi JSON-objektumból megtudhatja, hogy milyen példákat talál az összeolvasztási transzformációra

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Nincs letekerési gyökér karakterlánctömbbel

| Kitekerés | Gyökér kitekerése | Vetület |
| --------- | ----------- | ---------- |
| goods.customers | None | név <br> vevő = áruk.vevő |

#### <a name="output"></a>Kimenet

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Nincs letekerési gyökér összetett tömbbel

| Kitekerés | Gyökér kitekerése | Vetület |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderelemek | None | név <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.item <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> hely = hely |

#### <a name="output"></a>Kimenet

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Ugyanaz a gyökér, mint a letekerési tömbnél

| Kitekerés | Gyökér kitekerése | Vetület |
| --------- | ----------- | ---------- |
| goods.orders | goods.orders | név <br> goods.orders.shipped.orderItems.itemName <br> goods.customers <br> location |

#### <a name="output"></a>Kimenet

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Gyökér letekerése összetett tömbbel

| Kitekerés | Gyökér kitekerése | Vetület |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItem | goods.orders |név <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.item <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> hely = hely |

#### <a name="output"></a>Kimenet

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Példa

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>További lépések

* A [Pivot átalakítássegítségével](data-flow-pivot.md) a sorokat oszlopokba forgatja.
* A [Kimutatás feloldása az](data-flow-unpivot.md) oszlopok sorokká alakítása érdekében.
