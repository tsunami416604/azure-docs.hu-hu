---
title: Az Azure Data Factory-leképezés az a folyamat áttekintése
description: Leképezési adatfolyamok az Azure Data Factory – áttekintés magyarázata
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: ac07c0207f9463107b0c354bcd690cd9cc3981f4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213487"
---
# <a name="what-are-mapping-data-flows-in-azure-data-factory"></a>Mik a leképezési adatfolyamok az Azure Data Factory?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Leképezés adatfolyamok engedélyezése adattervezők grafikus Adatátalakítási logikát fejlesztése kódírás nélkül. Az eredményül kapott adatfolyamok belül az Azure Data Factory-folyamatok horizontálisan felskálázott Azure Databricks-fürtök használatával a tevékenységek végrehajtása.

Az Azure Data Factory adatfolyam célja egy teljes körűen vizuális élményt biztosít kódírás nélkül. Az adatáramlás a saját végrehajtási fürt horizontálisan felskálázott adatfeldolgozási szerint fog futni. Az Azure Data Factory kezeli az összes, a kód fordítása, elérési út optimalizálás és az adatok folyamat feladatok végrehajtását.

Először hozzon létre adatfolyamok hibakeresési módban, így interaktív módon ellenőrizheti az Adatátalakítási logikát. Ezután adjon hozzá egy adatfolyam-tevékenységet a folyamat végrehajtása és a teszt, az adatok hibakeresési folyamatot a flow, vagy "Aktiválás most" a folyamat segítségével egy tevékenység folyamatból adatok folyamat tesztelése.

Lesz majd ütemezhet, és az adatok vezérlésfolyam-tevékenységek figyelése az Azure Data Factory-folyamatok, amelyek végrehajtása az adatfolyam-tevékenység használatával.

A hibakeresési mód váltása kapcsolót az adatfolyam tervezői felület lehetővé teszi, hogy adatátalakítások interaktív kiépítését. Hibakeresési módban adatok folyamat építése adatok előkészítő környezetet biztosít.
