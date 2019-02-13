---
title: Az Azure Data Factory-folyamat referencia Adatcsomóponton leképezése
description: Data Factory-adatfolyam fog összekapcsolásokat, kereséseket, egyesítések referencia csomópont hozzáadása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 641375c2b848957ffc0f5ad092a28460d91b6690
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213428"
---
# <a name="azure-data-factory-mapping-data-flow-reference-node"></a>Az Azure Data Factory-folyamat referencia Adatcsomóponton leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Csomópont hivatkozhat](media/data-flow/referencenode.png "hivatkozási csomópont")

A referencia-csomópont automatikusan hozzáadódik a vászon jelölésére, hogy a csomópont csatlakoztatva van egy másik meglévő csomópont a vásznon hivatkozik-e. Úgy gondolja, hogy a hivatkozás csomópont mutató vagy egy hivatkozást egy másik folyamat átalakítását.

Példa: Ha csatlakozik, vagy Union több mint egy streamelési adatokon, az adatfolyam vászonalapú adhat hozzá egy referencia-csomópont, amely tükrözi a nevét és beállításait nem elsődleges bejövő adatfolyam.

A referencia-csomópont nem lehet áthelyezték vagy törölték. Azonban, kattintson az eredeti átalakítási beállítások módosításához a csomópontba.

A felhasználói felület szabályokat, amelyek szabályozzák, ha a referencia-csomópont adatfolyam hozzáadja a rendelkezésre álló területet, és velikost svislé mezery mezi sorok alapulnak.
