---
title: Az Azure Data Factory-folyamat Alter sor adatátalakítás leképezése
description: Adatbázis-cél használatával az Azure Data Factory leképezési Flow Alter sor adatátalakítás frissítése
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: d842898ca700490ae99b46140be6609622a144df
ms.sourcegitcommit: 5f41e855d415cfa741d8f710792ea486480df5cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58133161"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Az Azure Data Factory Alter sor átalakítása

Használja az Alter sor átalakítás sorok beszúrása, törlés, frissítés és upsert házirendek megadása. Hozzáadhat egy-a-többhöz feltételek kifejezésként. Ezek a feltételek mindegyike eredményezhet egy sort (vagy sort) beszúrt, frissített, törölve vagy upsert. ALTER sor az adatbázison egyaránt DDL & DML műveleteket hozhat létre.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Az ALTER sor beállítások](media/data-flow/alter-row1.png "Alter sor beállítások")

> [!NOTE]
> Az adatfolyam az adatbázis fogadóként ALTER sor átalakítások csak végre lesznek hajtva. A sorok (insert, update, delete, upsert) rendelt műveleteket nem történik a hibakeresési munkamenetek során. Kell-adatfolyam végrehajtásához feladat hozzáadása egy folyamathoz, és az alter sor házirendek az adatbázistáblák kihirdeti folyamat hibakeresési és eseményindítók használatával.

## <a name="view-policies"></a>Szabályzatok megtekintése

A Data Flow hibakeresési mód on váltson, és ezután tekintse meg az eredményeket az alter sor házirendek az adatok előnézeti ablaktáblában láthatja. Az alter sor végrehajtása a Data Flow hibakeresési mód nem tudott DDL vagy DML-műveletek a célon. Annak érdekében, hogy rendelkezik ezen műveletek történnek, hajtsa végre az adatok az adatfolyam-belül egy folyamat végrehajtása az adatfolyam tevékenységen belül.

![Az ALTER sor házirendek](media/data-flow/alter-row3.png "Alter sor házirendek")

Ez lehetővé teszi, hogy ellenőrizze, és minden sorban a feltételek alapján állapotának megtekintéséhez. Ikon jelöli az egyes insert, update, delete és az upsert művelet, amelyet a rendszer az adatok folyamatban, jelzi, hogy mely műveletet kerül sor, amikor az adatok az adatfolyam belül egy folyamat végrehajtása.

## <a name="sink-settings"></a>Fogadó-beállítások

Egy adatbázis fogadó típusa Alter sor működéséhez rendelkeznie kell. A fogadó beállításait meg kell minden művelet számára engedélyezett.

![Az ALTER sor fogadó](media/data-flow/alter-row2.png "Alter sor fogadó")

Az alapértelmezett viselkedést, az ADF adatfolyam adatbázis fogadóként, hogy sor beszúrásához. Frissítések, upserts és törlések is engedélyezni szeretné, ha ezek a mezők az, hogy a műveletek a fogadó is ellenőrizze.

> [!NOTE]
> Ha a beszúrások, a frissítések vagy a upserts módosítja a gyűjtő a céloldali tábla sémáját, az adatokat a folyamat sikertelen lesz. Annak érdekében, hogy módosítja a célséma az adatbázisban, az a fogadó kell válassza az "Hozza létre újból táblában" lehetőséget. Ezzel dobja el és hozza létre újra az új sémadefiníciója tartalmazó tábla.

## <a name="next-steps"></a>További lépések

Az Alter sor átalakítás után érdemes [célként megadott adattárba be az adatokat fogadó](data-flow-sink.md).
