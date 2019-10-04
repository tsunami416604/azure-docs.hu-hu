---
title: Azure Data Factory leképezési adatfolyam módosítása sor átalakítása
description: Az adatbázis céljának frissítése a Azure Data Factory leképezési adatáramlás módosítása sor-átalakítás használatával
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: e2cd69d5977b8ad1d9be2a71a006579fe3abfd23
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971258"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure Data Factory Alter sor átalakítása

Az Alter sort Transformation paranccsal szúrhatja be a sorokba az INSERT, DELETE, Update és upsert szabályzatokat. Egy-a-többhöz feltételeket adhat hozzá kifejezésként. Ezeket a feltételeket prioritási sorrendben kell megadni, mivel minden egyes sor az első megfeleltetési kifejezésnek megfelelő szabályzattal lesz megjelölve. Ezek a feltételek egy sor (vagy sor) beszúrását, frissítését, törlését vagy upserted eredményezik. Az Alter Row a DDL-& a DML-műveleteket is előkészítheti az adatbázison.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Módosítási sor beállításai](media/data-flow/alter-row1.png "Módosítási sor beállításai")

> [!NOTE]
> Az Alter sorok átalakítása csak az adatfolyamatban található adatbázis-tárolókban fog működni. A sorokhoz rendelt műveletek (INSERT, Update, DELETE, upsert) nem fordulnak elő hibakeresési munkamenetekben. Hozzá kell adnia egy végrehajtási adatfolyam-feladatot egy folyamathoz, és a folyamat hibakeresése vagy eseményindítók használatával kell megadnia az Alter Row Policy-szabályzatokat az adatbázis tábláiban.

## <a name="indicate-a-default-row-policy"></a>Alapértelmezett sor házirendet jelöl

Hozzon létre egy Alter sort átalakítást, és határozzon meg egy feltételt `true()`tartalmazó sort. Minden olyan sor meg lesz jelölve, amely nem felel meg a korábban definiált kifejezéseknek. Alapértelmezés szerint minden olyan sor meg lesz jelölve, amely nem felel meg a `Insert`feltételes kifejezéseknek.

![Sor módosítása egy házirendet](media/data-flow/alter-row4.png "Sor módosítása egy házirendet")

> [!NOTE]
> Az összes sor egyetlen házirenddel való megjelöléséhez létrehozhat egy feltételt az adott szabályzathoz, és a `true()`feltételt is megadhatja.

## <a name="view-policies"></a>Szabályzatok megtekintése

Kapcsolja be az adatfolyam-hibakeresési módot az Alter Row-szabályzatok eredményeinek megtekintéséhez az adatelőnézet ablaktáblán. A módosítási sor adatáramlási hibakeresési módban való végrehajtása nem hoz létre DDL-vagy DML-műveleteket a célhelyen. A műveletek végrehajtásához hajtsa végre az adatfolyamatot egy folyamaton belül egy végrehajtási adatfolyam-tevékenységen belül.

![Módosítási sor házirendjei](media/data-flow/alter-row3.png "Módosítási sor házirendjei")

Ez lehetővé teszi az egyes sorok állapotának ellenőrzését és megtekintését a feltételek alapján. Az egyes INSERT, Update, DELETE és upsert műveletekhez ikon jelöli az adatfolyamatban, ami azt jelzi, hogy melyik műveletet kell végrehajtani, amikor egy folyamaton belül hajtja végre az adatfolyamot.

## <a name="sink-settings"></a>Fogadó beállításai

A módosítási sor működéséhez adatbázis-fogadó típusúnak kell lennie. A fogadó beállításainál minden olyan műveletet meg kell adni, amely megfelel az Alter Row feltételeinek.

![Módosítási sor] fogadója (media/data-flow/alter-row2.png "Módosítási sor") fogadója

Az ADF-adatforgalomban az adatbázis-elsüllyedés alapértelmezett viselkedése Sorok beszúrására szolgál. Ha engedélyezni szeretné a frissítéseket, a upsert és a törlést is, a műveletek engedélyezéséhez a fogadóban is ellenőriznie kell ezeket a mezőket.

> [!NOTE]
> Ha a lapkák, a frissítések vagy a upsert módosítja a cél tábla sémáját a fogadóban, az adatfolyam sikertelen lesz. Ha módosítani szeretné a célként megadott sémát az adatbázisban, válassza a fogadóban a "tábla újbóli létrehozása" lehetőséget. Ezzel eldobásra kerül, és újból létrehozza a táblát az új séma-definícióval.

## <a name="next-steps"></a>További lépések

A módosítási sor átalakítását követően érdemes lehet [az adatait egy célhely adattárba menteni](data-flow-sink.md).
