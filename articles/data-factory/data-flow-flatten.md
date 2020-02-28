---
title: Adatforgalom leképezésének összevonása átalakítás
description: Azure-beli adatfeldolgozó-leképezési adatfolyam-összeolvasztási transzformáció
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 435d2469be8eb572caa02e381d84ae4e9ac32f4b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77674843"
---
# <a name="azure-data-factory-flatten-transformation"></a>Azure Data Factory az átalakítás összeolvasztása

A összeolvasztási transzformáció használatával egy hierarchikus struktúrán belüli tömb értékeket lehet új sorokba felvenni, ami lényegében az adatok normalizálása.

![Átalakítási eszközkészlet](media/data-flow/flatten5.png "Átalakítási eszközkészlet")

![1. átalakítás összeolvasztása](media/data-flow/flatten7.png "1\. átalakítás összeolvasztása")

## <a name="unroll-by"></a>Legördülő

Először válassza ki azt a tömb oszlopot, amelyet el szeretne távolítani, majd a kimutatást.

![Átalakítási beállítások összeolvasztása](media/data-flow/flatten1.png "Átalakítási beállítások összeolvasztása")

## <a name="unroll-root"></a>Legördülő gyökér

Alapértelmezés szerint az ADF leállítja a szerkezetet a fent kiválasztott unroll tömbben. Másik lehetőségként kiválaszthatja a hierarchia egy másik részét a kilépéshez.

## <a name="input-columns"></a>Bemeneti oszlopok

Végül válassza ki az új struktúra vetületét a bejövő mezők alapján, valamint az Ön által kiváltott normalizált oszlopot.

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
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>Következő lépések

* Sorok oszlopokra való kimutatásához használja a [pivot transzformációt](data-flow-pivot.md) .
* A kimutatás [transzformációval](data-flow-unpivot.md) oszlopokat szúrhat be a sorokba.
