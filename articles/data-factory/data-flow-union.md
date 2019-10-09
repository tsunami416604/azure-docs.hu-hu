---
title: Azure Data Factory leképezési adatfolyam új ág-átalakítás
description: Azure Data Factory leképezési adatfolyam új ág-átalakítás
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 35d5b2250cb5f2f5bd5b3a0073dc2e3c655ceccb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029914"
---
# <a name="mapping-data-flow-union-transformation"></a>Az adatáramlási Unió átalakításának leképezése



A Union több adatfolyamot egyesít egy, a streamek SQL Union szolgáltatásával, amely az Unió átalakításának új kimenete lesz. Az egyes bemeneti adatfolyamokból származó összes séma össze lesz foglalva az adatfolyamattal, anélkül, hogy hozzá kellene adni egy csatlakoztatási kulcsot.

Az egyes konfigurált sorok melletti "+" ikonra kattintva a "+" ikont is egyesítheti a beállítások táblában, beleértve a forrásadatok és az adatfolyamok meglévő átalakításait is.

![Union Transformation](media/data-flow/union.png "Union")

Ebben az esetben kombinálhatja a különböző forrásokból származó eltérő metaadatokat (ebben a példában három különböző forrásfájl), és egyetlen streambe kombinálhatja őket:

![Union Transformation – áttekintés](media/data-flow/union111.png "Union 1")

Ennek eléréséhez vegyen fel további sorokat a Union-beállításokba úgy, hogy a hozzáadni kívánt forrást is tartalmazza. Közös keresési vagy csatlakoztatási kulcsra nincs szükség:

A ![Union Transformation Settings](media/data-flow/unionsettings.png "Union beállításai")

Ha a szervezete után kiválasztja az átalakítást, akkor átnevezheti a nem fejléces forrásokból származó átfedésben lévő mezőket vagy mezőket. A "vizsgálat" gombra kattintva tekintheti meg az alábbi három különböző forrásból származó, 132-es összesített metaadatokat:

![Union Transformation végleges](media/data-flow/union333.png "Union 3")

## <a name="name-and-position"></a>Név és pozíció

Ha az "Unió neve" lehetőséget választja, minden oszlop értéke bekerül a megfelelő oszlopba az egyes forrásokból, egy új összefűzött metaadat-sémával.

Ha a "Union by position" lehetőséget választja, minden egyes oszlop értéke bekerül az eredeti pozícióba az egyes forrásokból, ami egy új, összesített adatstreamet eredményez, amelyben az egyes forrásokból származó adatok ugyanahhoz az adatfolyamhoz lesznek hozzáadva:

![Union output](media/data-flow/unionoutput.png "Union kimenet")

## <a name="next-steps"></a>További lépések

Fedezze fel a hasonló átalakításokat, beleértve a [csatlakozást](data-flow-join.md) és a [meglévőket](data-flow-exists.md).
