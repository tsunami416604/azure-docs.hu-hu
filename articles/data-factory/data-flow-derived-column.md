---
title: Származtatott oszlop átalakítása a leképezési adatfolyamban – Azure Data Factory | Microsoft Docs
description: Megtudhatja, hogyan alakíthatja át az adatmennyiséget a Azure Data Factory a leképezési adatfolyam származtatott oszlopainak átalakításával.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aacd6f1799f1813e168bd04e78f18cf60ad5243f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026845"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Származtatott oszlop átalakítása a leképezési adatfolyamban

A származtatott oszlop átalakításával új oszlopokat hozhatja ki az adatfolyamban, vagy módosíthatja a meglévő mezőket.

## <a name="derived-column-settings"></a>Származtatott oszlop beállításai

Meglévő oszlop felülbírálásához válassza ki azt az oszlop legördülő listából. Ellenkező esetben használja az oszlop kijelölése mezőt a szövegmezőbe, és írja be az új oszlop nevét. A származtatott oszlop kifejezésének létrehozásához kattintson a "kifejezés beírása" mezőre az adatfolyam- [kifejezés-szerkesztő](concepts-data-flow-expression-builder.md)megnyitásához.

![Származtatott oszlop beállításai](media/data-flow/dc1.png "származtatott oszlop beállításai")

További származtatott oszlopok hozzáadásához vigye a kurzort egy meglévő származtatott oszlop fölé, és kattintson a "+" elemre. Ezután válassza az "oszlop hozzáadása" vagy az "oszlop hozzáadása" mintát. Az oszlop mintái hasznosak lehetnek, ha az oszlopnevek a forrásokból származó változók. További információ: [Column Patterns](concepts-data-flow-column-pattern.md).

![Új származtatott oszlop kiválasztása](media/data-flow/columnpattern.png "új származtatott oszlop kiválasztása")

## <a name="next-steps"></a>További lépések

- További információ a [leképezési adatfolyam kifejezésének nyelvéről](data-flow-expression-functions.md).
