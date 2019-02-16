---
title: Az Azure Data Factory-folyamat referencia Adatcsomóponton leképezése
description: Data Factory-adatfolyam fog összekapcsolásokat, kereséseket, egyesítések referencia csomópont hozzáadása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 2e318210d96822b13f65eadeef79798b1b4595d1
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56325894"
---
# <a name="mapping-data-flow-reference-node"></a>Data Flow referencia csomópont leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Csomópont hivatkozhat](media/data-flow/referencenode.png "hivatkozási csomópont")

A referencia-csomópont automatikusan hozzáadódik a vászon jelölésére, hogy a csomópont csatlakoztatva van egy másik meglévő csomópont a vásznon hivatkozik-e. Úgy gondolja, hogy a hivatkozás csomópont mutató vagy egy hivatkozást egy másik folyamat átalakítását.

Példa: Ha csatlakozik, vagy Union több mint egy streamelési adatokon, az adatfolyam vászonalapú adhat hozzá egy referencia-csomópont, amely tükrözi a nevét és beállításait nem elsődleges bejövő adatfolyam.

A referencia-csomópont nem lehet áthelyezték vagy törölték. Azonban, kattintson az eredeti átalakítási beállítások módosításához a csomópontba.

A felhasználói felület szabályokat, amelyek szabályozzák, ha a referencia-csomópont adatfolyam hozzáadja a rendelkezésre álló területet, és velikost svislé mezery mezi sorok alapulnak.
