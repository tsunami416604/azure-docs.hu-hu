---
title: Az Azure Data Factory-leképezés az a folyamat áttekintése
description: Leképezési adatfolyamok az Azure Data Factory – áttekintés magyarázata
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 5064f47444b4ca6d9194ed66144938e6e3d51a4e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732365"
---
# <a name="what-are-mapping-data-flows-in-azure-data-factory"></a>Mik a leképezési adatfolyamok az Azure Data Factory?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Leképezés adatfolyamok engedélyezése adattervezők grafikus Adatátalakítási logikát fejlesztése kódírás nélkül. Az eredményül kapott adatfolyamok belül az Azure Data Factory-folyamatok horizontálisan felskálázott Azure Databricks-fürtök használatával a tevékenységek végrehajtása.

Az Azure Data Factory adatfolyam célja egy teljes körűen vizuális élményt biztosít kódírás nélkül. Az adatáramlás a saját végrehajtási fürt horizontálisan felskálázott adatfeldolgozási szerint fog futni. Az Azure Data Factory kezeli az összes, a kód fordítása, elérési út optimalizálás és az adatok folyamat feladatok végrehajtását.

Először hozzon létre adatfolyamok hibakeresési módban, így interaktív módon ellenőrizheti az Adatátalakítási logikát. Ezután adjon hozzá egy adatfolyam-tevékenységet a folyamat végrehajtása és a teszt, az adatok hibakeresési folyamatot a flow, vagy "Aktiválás most" a folyamat segítségével egy tevékenység folyamatból adatok folyamat tesztelése.

Lesz majd ütemezhet, és az adatok vezérlésfolyam-tevékenységek figyelése az Azure Data Factory-folyamatok, amelyek végrehajtása az adatfolyam-tevékenység használatával.

A hibakeresési mód váltása kapcsolót az adatfolyam tervezői felület lehetővé teszi, hogy adatátalakítások interaktív kiépítését. Hibakeresési módban adatok folyamat építése adatok előkészítő környezetet biztosít.
