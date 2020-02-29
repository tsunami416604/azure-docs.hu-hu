---
title: Adatforgalom leképezésének összevonása átalakítás
description: Azure Data Factory leképezési adatfolyamok összeolvasztásának átalakítása
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 415a093fd8a8fbe27e1d240b061548e18f2ca6b6
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164729"
---
# <a name="azure-data-factory-flatten-transformation"></a>Azure Data Factory az átalakítás összeolvasztása

A összeolvasztási transzformáció használatával egy hierarchikus struktúrán belüli tömb értékeket lehet új sorokba felvenni, ami lényegében az adatok normalizálása.

![Átalakítási eszközkészlet](media/data-flow/flatten5.png "Átalakítási eszközkészlet")

![1. átalakítás összeolvasztása](media/data-flow/flatten7.png "1\. átalakítás összeolvasztása")

## <a name="unroll-by"></a>Legördülő

Először válassza ki azt a tömb oszlopot, amelyet el szeretne távolítani, majd a kimutatást.

![Átalakítási beállítások összeolvasztása](media/data-flow/flatten1.png "Átalakítási beállítások összeolvasztása")

## <a name="unroll-root"></a>Legördülő gyökér

Alapértelmezés szerint az ADF leállítja a szerkezetet a fent kiválasztott unroll tömbben. Másik lehetőségként kiválaszthatja a hierarchia egy másik részét a kilépéshez. A "unroll root" egy opcionális beállítás.

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
