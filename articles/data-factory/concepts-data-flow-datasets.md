---
title: Az Azure Data Factory-folyamat adatkészletek adatok leképezése
description: Az Azure Data Factory leképezési adatfolyam sepecific adatkészlet tesztkiszolgálókon rendelkezik
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: ccf4273489d739bb9b0d802b79944efefcd02ff4
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331224"
---
# <a name="mapping-data-flow-datasets"></a>Data Flow adatkészletek leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Adatkészlet lehet egy Data Factory-szerkezet, amely a folyamat használata az adatok felépítésének meghatározásához. Data Flow, a sor- és szolgáltatói szükséges az adatok sokkal finom részletesebben definíció folyamat control flow-ban az adatkészletek által igényelt, mint.

A Flow adatforrás és a fogadó átalakítások adatkészletek használatával az alapszintű adatok séma határozza meg. Ha a séma az adatok nem rendelkeznek, a forrás és fogadó séma eltéréseket állíthatja a. Az adathalmaz definiált sémával kell a kapcsolódó adattípusok, adatformátumok a célnyelven, helye és a kapcsolódó Linked Service csatlakozási adataival.

Jelenleg a data flow-ban található négy adatkészletek:

* Azure SQL DB
* Azure SQL DW
* Parquet eszközökben
* Karakterrel elválasztott szöveg

Data flow adatkészletek a forrás külön *típusa* , a társított szolgáltatás kapcsolattípus. Általában a Data Factory áttekintése válassza ki a kapcsolat típusát (Blob, ADLS stb.), és megadhatja az adatkészletben lévő fájl típusa. Belül adatfolyam a másik társított szolgáltatás kapcsolattípusok társítható forrástípusok válasszon ki.

![Átalakítási beállítások a forrás](media/data-flow/dataset1.png "források")

Amikor egy új adatkészletet hoz létre, van egy "Data Flow kompatibilis" jobb felső sarokban lévő távolságát a panel feliratú jelölőnégyzetet. A gombra kattintva a rendszer kiszűri csak azokat az adatkészleteket, amelyek együtt adatáramlás. 

Sémák importálása

A séma az adatfolyam adatkészletek importálásakor egy séma importálása gomb jelenik meg. A gombra kattintva megjelennek a két lehetőség közül választhat: A forrásból importálja, vagy importáljon egy helyi fájlból. A legtöbb esetben közvetlenül a forrásból fogja importálja a sémát. Ha rendelkezik egy meglévő sémafájl, amely a helyi fájl mutathat, és a Data Factory határozza meg a séma alapján, hogy sémafájl.

