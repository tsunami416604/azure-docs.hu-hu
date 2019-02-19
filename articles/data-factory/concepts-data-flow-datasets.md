---
title: Az Azure Data Factory-folyamat adatkészletek adatok leképezése
description: Az Azure Data Factory leképezési adatfolyam sepecific adatkészlet tesztkiszolgálókon rendelkezik
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 36ca5e07adf79de77ac4ab4149ff8e96a1dece8d
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408748"
---
# <a name="mapping-data-flow-datasets"></a>Data Flow adatkészletek leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Adatkészlet lehet egy Data Factory-szerkezet, amely a folyamat használata az adatok felépítésének meghatározásához. Az adatfolyam sor- és szolgáltatói szükséges az adatok egy finom részletesebben adatkészlet-definícióban. A control flow folyamatokban használt Adatkészletekre nem szükséges adatok megismerése azonos mélységét.

A Flow adatforrás és a fogadó átalakítások adatkészletek segítségével határozhatók meg az alapszintű sémát. Ha a séma az adatok nem rendelkeznek, a forrás és fogadó séma eltéréseket állíthatja a. Az adathalmaz definiált sémával kell a kapcsolódó adattípusok, adatformátumok a célnyelven, helye és a kapcsolódó Linked Service csatlakozási adataival.

## <a name="dataset-types"></a>Adatkészlet-típusok

Jelenleg a data flow-ban található négy adatkészlet típusa:

* Azure SQL DB
* Azure SQL DW
* Parquet eszközökben
* Karakterrel elválasztott szöveg

Data flow adatkészletek a forrás külön *típusa* , a társított szolgáltatás kapcsolattípus. Általában a Data Factory áttekintése válassza ki a kapcsolat típusát (Blob, ADLS stb.), és megadhatja az adatkészletben lévő fájl típusa. Belül adatfolyam a másik társított szolgáltatás kapcsolattípusok társítható forrástípusok válasszon ki.

![Átalakítási beállítások a forrás](media/data-flow/dataset1.png "források")

## <a name="data-flow-compatible-datasets"></a>Data Flow kompatibilis adatkészletek

Amikor egy új adatkészletet hoz létre, van egy "Data Flow kompatibilis" jobb felső sarokban lévő távolságát a panel feliratú jelölőnégyzetet. A gombra kattintva a rendszer kiszűri csak azokat az adatkészleteket, amelyek együtt adatáramlás. 

## <a name="import-schemas"></a>Sémák importálása

A séma az adatfolyam adatkészletek importálásakor egy séma importálása gomb jelenik meg. A gombra kattintva megjelennek a két lehetőség közül választhat: A forrásból importálja, vagy importáljon egy helyi fájlból. A legtöbb esetben közvetlenül a forrásból fogja importálja a sémát. Azonban ha egy meglévő sémafájl (Parquet-fájlok vagy CSV-fejlécek), mutatva, hogy helyi fájlt és a Data Factory határozza meg a séma séma fájl alapján.

