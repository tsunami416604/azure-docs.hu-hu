---
title: Az Azure Data Factory-térképadatokat új ág átalakítási folyamat
description: Az Azure Data Factory-térképadatokat új ág átalakítási folyamat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: c48c1500a31ce8f22bcef7207c0ea67caca24231
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271863"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Az Azure Data Factory-térképadatokat új ág átalakítási folyamat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Beállítások ágban](media/data-flow/menu.png "menü")

Elágaztatási igénybe vehet a jelenlegi adatfolyamban az adatok folyamatban, és végezze el a replikációt a másik adatfolyamba. Új ágat használja több végrehajtásához műveletek és elleni ugyanarra az adatstreamre átalakítások állítja be.

Példa: Az adatfolyam rendelkezik, egy forrás átalakítása a kijelölt oszlopokat és adatokat típuskonverziók együtt. Ezután elhelyezheti egy származtatott oszlopot következett közvetlenül az adatforráshoz. A származtatott oszlop, már hozzon létre egy új mezőt adott ötvözi a Keresztnév és Vezetéknév, hogy egy új "teljes neve" mezőt.

Átalakítások és a egy sort egy fogadó vannak beállítva, hogy új stream kezeli, és új ág egy másolatát, ahol alakíthatja át, hogy ugyanazokat az adatokat más-más átalakítások stream létrehozásához használja. A másolt adatokat egy külön ágban átalakításával keletkező, ezt követően egy külön helyen is fogadó az adatokat.

> [!NOTE]
> "Új ág" csak akkor jelenik meg műveletként a a + átalakítási menü az aktuális helyen, ahol próbált ágban követő későbbi átalakítás esetén. azaz nem jelenik meg itt a végén a "Új ág" lehetőséget egy másik átalakítás után válassza a hozzáadásáig

![Ág](media/data-flow/branch2.png "ág 2")
