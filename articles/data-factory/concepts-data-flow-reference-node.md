---
title: Az Azure Data Factory-folyamat referencia Adatcsomóponton leképezése
description: Data Factory-adatfolyam fog összekapcsolásokat, kereséseket, egyesítések referencia csomópont hozzáadása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 626943143e8fa193f143e66d856d9b00e3589fb5
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732805"
---
# <a name="mapping-data-flow-reference-node"></a>Data Flow referencia csomópont leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Csomópont hivatkozhat](media/data-flow/referencenode.png "hivatkozási csomópont")

A referencia-csomópont automatikusan hozzáadódik a vászon jelölésére, hogy a csomópont csatlakoztatva van egy másik meglévő csomópont a vásznon hivatkozik-e. Úgy gondolja, hogy a hivatkozás csomópont mutató vagy egy hivatkozást egy másik folyamat átalakítását.

Példa: Ha csatlakozik, vagy Union több mint egy streamelési adatokon, az adatfolyam vászonalapú adhat hozzá egy referencia-csomópont, amely tükrözi a nevét és beállításait nem elsődleges bejövő adatfolyam.

A referencia-csomópont nem lehet áthelyezték vagy törölték. Azonban, kattintson az eredeti átalakítási beállítások módosításához a csomópontba.

A felhasználói felület szabályokat, amelyek szabályozzák, ha a referencia-csomópont adatfolyam hozzáadja a rendelkezésre álló területet, és velikost svislé mezery mezi sorok alapulnak.
