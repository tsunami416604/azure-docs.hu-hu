---
title: Leképezési adatfolyam-egyesítő transzformáció
description: Azure Data Factory leképezési adatfolyam Új ág átalakítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 35f6b74f3b6cce5a0af812d4613f9da2f9fbd552
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606263"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Az Azure Data Factory leképezési adatfolyam-egyesítő átalakítása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az Unió több adatfolyamot fog egyesíteni, és e streamek SQL Unióját az uniós átalakulás új kimeneteként egyesíti. Az egyes bemeneti adatfolyamok összes sémája az adatfolyamon belül lesz kombinálva, anélkül, hogy illesztési kulccsal kellene rendelkeznie.

A beállítási táblázatban lévő adatfolyamok n számát az egyes konfigurált sorok melletti "+" ikonra kattintva kombinálhatja, beleértve a forrásadatokat és az adatfolyamok meglévő átalakításaiból származó adatfolyamokat is.

![Az Unió átalakítása](media/data-flow/union.png "Union")

Ebben az esetben több forrásból (ebben a példában három különböző forrásfájlból) származó eltérő metaadatokat egyesíthet, és egyetlen adatfolyamba egyesítheti őket:

![Az unió átalakulásának áttekintése](media/data-flow/union111.png "Unió 1")

Ennek érdekében adjon hozzá további sorokat az Union Settings-ba az összes hozzáadni kívánt forrás sal. Nincs szükség közös keresésre vagy illesztési kulcsra:

![Egyesítési beállítások](media/data-flow/unionsettings.png "Uniós beállítások")

Ha az Unió után a Select transformation-t állítja be, átnevezheti az egymást átfedő mezőket vagy mezőket, amelyeket nem fejléc nélküli forrásokból neveztek el. Kattintson az "Ellenőrzés" gombra, ha meg szeretné tekinteni a metaadatok kombinálását a példában szereplő 132 oszlopmal, három különböző forrásból:

![Az unió átalakulásának végleges](media/data-flow/union333.png "Unió 3")

## <a name="name-and-position"></a>Név és beosztás

Ha a "név szerinti egyesítés" lehetőséget választja, minden oszlopértéke az egyes források megfelelő oszlopába kerül, egy új, összefűzött metaadat-sémával.

Ha az "egység pozíció szerint" lehetőséget választja, minden oszlopérték az egyes megfelelő forrásokból az eredeti pozícióba esik, ami egy új kombinált adatfolyamot eredményez, ahol az egyes forrásokból származó adatok ugyanabba az adatfolyamhoz kerülnek:

![Uniós kibocsátás](media/data-flow/unionoutput.png "Union kimenet")

## <a name="next-steps"></a>További lépések

Fedezze fel a hasonló átalakításokat, beleértve [a Csatlakozás](data-flow-join.md) és [a Létezés t.](data-flow-exists.md)
