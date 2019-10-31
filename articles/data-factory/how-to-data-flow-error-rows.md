---
title: Hibás sorok kezelése a Azure Data Factory leképezési adatforgalmával
description: Megtudhatja, hogyan kezelheti az SQL-csonkolt hibákat Azure Data Factory a leképezési adatfolyamatok használatával.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: makromer
ms.openlocfilehash: 3fe3403ad06d82ba5ccd33d2718bf0e5eff64490
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166537"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>SQL-csonkolt hibák sorainak kezelése Data Factory leképezési adatforgalomban

A leképezési adatfolyamatok használatakor Data Factory gyakori forgatókönyv, hogy az átalakított adatait egy Azure SQL Database-adatbázisba írja. Ebben a forgatókönyvben egy gyakori hiba feltételt kell megakadályozni, hogy az oszlop csonkítható legyen. Az alábbi lépéseket követve megadhatja azokat az oszlopokat, amelyek nem illeszkednek a célként megadott karakterlánc-oszlopba, így az adatfolyamok továbbra is folytatják ezeket a forgatókönyveket.

## <a name="scenario"></a>Alkalmazási helyzet

1. A cél Azure SQL Database-táblázat egy "Name" nevű ```nvarchar(5)``` oszlopot tartalmaz.

2. Az adatfolyamon belül a fogadótól a "Name" oszlophoz tartozó film-címeket szeretnénk leképezni.

    ![1\. filmes adatfolyam](media/data-flow/error4.png)
    
3. A probléma az, hogy a film címe nem fér el egy fogadó oszlopon belül, amely csak 5 karaktert tartalmazhat. Ha végrehajtja ezt az adatfolyamatot, a következőhöz hasonló hibaüzenet jelenik meg: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

## <a name="how-to-design-around-this-condition"></a>Az állapot megtervezése

1. Ebben az esetben a "Name" oszlop maximális hossza öt karakter. Tegyük fel, hogy hozzáadunk egy feltételes felosztású átalakítást, amely lehetővé teszi számunkra, hogy az öt karakternél hosszabb sorokba írja a sorokat, miközben a többi olyan sort is lehetővé teszi, amely elfér az adott helyen az adatbázisba való íráshoz.

    ![feltételes felosztás](media/data-flow/error1.png)

2. Ez a feltételes felosztású átalakítás a "title" maximális hosszát határozza meg öt értékre. Minden olyan sor, amely kisebb vagy egyenlő, mint öt, a ```GoodRows``` streambe kerül. Az öt-nál nagyobb sorok a ```BadRows``` streambe kerülnek.

3. Most be kell jelentkeznie a sikertelen sorokat. A naplózáshoz vegyen fel egy fogadó átalakítást a ```BadRows``` streambe. Itt "automatikusan leképezjük" az összes mezőt, így a teljes tranzakciós rekord naplózása is megtörtént. Ez egy, a CSV-fájlnak a Blob Storageban lévő egyetlen fájlba való kimenete. Meghívjuk a "badrows. csv" naplófájlt.

    ![Helytelen sorok](media/data-flow/error3.png)
    
4. Az elkészült adatfolyam az alábbi ábrán látható. Most már eloszthatjuk a hibák sorait, hogy elkerülje az SQL-csonkítás hibáit, és a bejegyzéseket egy naplófájlba helyezi. Eközben a sikeres sorok továbbra is írhatnak a célként megadott adatbázisba.

    ![teljes adatforgalom](media/data-flow/error2.png)

## <a name="next-steps"></a>Következő lépések

* Hozza létre a többi adatáramlási logikát az adatforgalom- [átalakítások](concepts-data-flow-overview.md)leképezése használatával.
