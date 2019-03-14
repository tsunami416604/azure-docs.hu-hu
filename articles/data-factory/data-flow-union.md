---
title: Az Azure Data Factory-térképadatokat új ág átalakítási folyamat
description: Az Azure Data Factory-térképadatokat új ág átalakítási folyamat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: af2225d749283c7124f89d5a7cd735b2f6bfd121
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792616"
---
# <a name="mapping-data-flow-union-transformation"></a>Adatátalakítás folyamat union leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Union egy, az SQL Union ezeket adatfolyamok, a Union átalakítás új kimenete az össze a több adatfolyamokat. Minden egyes bemeneti streamből a séma egyesítve jön létre az adatok folyamaton, anélkül, hogy rendelkezik egy illesztési kulccsal.

A beállítások tábláját Streamek n számú mellett minden egyes konfigurált sorban, beleértve a forrásadatok és is adatfolyamot a meglévő átalakításokat az adatfolyam a "+" ikont választva kombinálhatók.

![A UNION átalakítási](media/data-flow/union.png "Union")

Ebben az esetben különálló metaadatok (az ebben a példában három különböző forrásfájlok) több forrásból egyesítheti és kombinálhatja őket, egyetlen streammel:

![Union átalakítást – áttekintés](media/data-flow/union111.png "Union 1")

Ennek érdekében hozzáadhat további sorokat az Unió között beleértve a hozzáadni kívánt összes forrás. Esetén nem kell egy közös lookup vagy illesztési kulcs:

![A UNION átalakítási beállítások](media/data-flow/unionsettings.png "Union beállításai")

Válassza ki az átalakítás után az Unió állít be, ha tudják, egymást átfedő és, amelyek nem voltak nevű headerless forrásból származó mezők átnevezése. Kattintson a "Vizsgálat" az összevonás metaadat az ebben a példában három különböző forrásokból származó 132 összesen oszlopokban tekintheti meg:

![Végső Union átalakítási](media/data-flow/union333.png "Union 3")

## <a name="name-and-position"></a>Név és a pozíció

Ha "union neve szerint" lehetőséget választja, minden oszlop értéke csökken, a megfelelő oszlopban minden forrás, egy új összefűzött metaadat-sémával.

Ha úgy dönt, hogy a "union pozíciója", minden oszlop értékét az eredeti pozíciójuktól lefelé vagy egy új egyesített adatfolyam ahol hozzáadódik az egyes forrásokból származó adatok ugyanazon streamben eredményez, minden egyes megfelelő forrásból fogja betett:

![A UNION kimeneti](media/data-flow/unionoutput.png "Union kimenet")

## <a name="next-steps"></a>További lépések

Ismerkedés a hasonló átalakítások, beleértve a [csatlakozzon](data-flow-join.md) és [Exists](data-flow-exists.md).
