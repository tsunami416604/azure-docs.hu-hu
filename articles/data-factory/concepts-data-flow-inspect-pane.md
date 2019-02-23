---
title: Az Azure Data Factory leképezés adatfolyam vizsgálata és adatok előzetes verzió
description: Az Azure Data Factory leképezése az adatfolyam rendelkezik egy data flow metaadatok (vizsgálat) és a hibakeresési módban (megtekintés), amely adatelőnézet megjelenítő panelen
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 47cde50e0874f0f73523925b6d1b2f8ee4abaea9
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727825"
---
# <a name="azure-data-factory-mapping-data-flow-transformation-inspect-tab"></a>Az Azure Data Factory hozzárendelési folyamat átalakítását vizsgálja meg a lap

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Vizsgálja meg a panelen](media/data-flow/agg3.png "ablaktábla vizsgálata")

A vizsgálata panelen a metaadatokat az Ön átalakítása adatfolyam betekintést biztosít. Oszlopok módosítja, hozzáadott oszlopokat, az adattípusok, oszloprendezési és oszlophivatkozások, fogja látni az oszlopok számát. "Vizsgálja meg" egy csak olvasható nézet metaadatait. Nem kell engedélyezni hibakeresési üzemmódját metadate a vizsgálata panelen láthatók.

Módosításakor átalakítások keresztül az adatok alakját, látni fogja a metaadatok keresztül a vizsgálja meg a folyamat módosítja. Ha nincs meghatározott sémája a forrás-átalakítást, majd metaadatok nem lesznek láthatók a vizsgálata panelen. A metaadatok hiányát közös séma eltéréseket forgatókönyvekben.

![Adatelőnézet](media/data-flow/datapreview.png "Adatelőnézet")

Adatelőnézet egy panel, amely egy csak olvasható nézet az adatokról nyújt átalakul folyamatban. A kimenet az Adatátalakítási és kifejezések a data-folyamat ellenőrzése tekintheti meg. A hibakeresési mód bekapcsolt adatok előnézetének megtekintéséhez rendelkeznie kell. Az adatok villámnézeti rácsa oszlopai kattint, látni fogja a későbbi panel jobb. Az előugró panelen jelennek meg a profil statisztikája a kiválasztott oszlopok egyes.

![Adatelőnézeti diagram](media/data-flow/chart.png "Adatelőnézeti diagram")

