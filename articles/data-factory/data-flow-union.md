---
title: Azure Data Factory leképezési adatfolyam új ág-átalakítás
description: Azure Data Factory leképezési adatfolyam új ág-átalakítás
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 4832cd2036f615d1e90d5e7a21c1a9832c2fa837
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387130"
---
# <a name="mapping-data-flow-union-transformation"></a>Az adatáramlási Unió átalakításának leképezése



A Union több adatfolyamot egyesít egy, a streamek SQL Union szolgáltatásával, amely az Unió átalakításának új kimenete lesz. Az egyes bemeneti adatfolyamokból származó összes séma össze lesz foglalva az adatfolyamattal, anélkül, hogy hozzá kellene adni egy csatlakoztatási kulcsot.

Az egyes konfigurált sorok melletti "+" ikonra kattintva a "+" ikont is egyesítheti a beállítások táblában, beleértve a forrásadatok és az adatfolyamok meglévő átalakításait is.

![Unió átalakítása](media/data-flow/union.png "Union")

Ebben az esetben kombinálhatja a különböző forrásokból származó eltérő metaadatokat (ebben a példában három különböző forrásfájl), és egyetlen streambe kombinálhatja őket:

![A Union Transformation áttekintése](media/data-flow/union111.png "1\. Unió")

Ennek eléréséhez vegyen fel további sorokat a Union-beállításokba úgy, hogy a hozzáadni kívánt forrást is tartalmazza. Közös keresési vagy csatlakoztatási kulcsra nincs szükség:

![A Union átalakítási beállításai](media/data-flow/unionsettings.png "Union-beállítások")

Ha a szervezete után kiválasztja az átalakítást, akkor átnevezheti a nem fejléces forrásokból származó átfedésben lévő mezőket vagy mezőket. A "vizsgálat" gombra kattintva tekintheti meg az alábbi három különböző forrásból származó, 132-es összesített metaadatokat:

![Az Unió átalakítása végleges](media/data-flow/union333.png "Union 3")

## <a name="name-and-position"></a>Név és pozíció

Ha az "Unió neve" lehetőséget választja, minden oszlop értéke bekerül a megfelelő oszlopba az egyes forrásokból, egy új összefűzött metaadat-sémával.

Ha a "Union by position" lehetőséget választja, minden egyes oszlop értéke bekerül az eredeti pozícióba az egyes forrásokból, ami egy új, összesített adatstreamet eredményez, amelyben az egyes forrásokból származó adatok ugyanahhoz az adatfolyamhoz lesznek hozzáadva:

![Union-kimenet](media/data-flow/unionoutput.png "Union-kimenet")

## <a name="next-steps"></a>Következő lépések

Fedezze fel a hasonló átalakításokat, beleértve a [csatlakozást](data-flow-join.md) és a [meglévőket](data-flow-exists.md).
