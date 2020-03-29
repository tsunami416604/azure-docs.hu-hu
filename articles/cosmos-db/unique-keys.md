---
title: Egyedi kulcsok használata az Azure Cosmos DB-ben
description: Ismerje meg, hogyan definiálhat és használhat egyedi kulcsokat egy Azure Cosmos-adatbázishoz. Ez a cikk azt is ismerteti, hogy az egyedi kulcsok hogyan adnak hozzá egy adatintegritási réteget.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: f234579c6fb2b6f1bc0cd518b87ea69fae30093a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74869833"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Egyedi kulcskorlátozások az Azure Cosmos DB-ben

Az egyedi kulcsok egy adatintegritási réteget adnak hozzá egy Azure Cosmos-tárolóhoz. Hozzon létre egy egyedi kulcsszabályzat, amikor létrehoz egy Azure Cosmos-tárolót. Az egyedi kulcsok segítségével győződjön meg arról, hogy egy logikai partíción belül egy vagy több érték egyedi. Azt is garantálja egyediségét [partíciókulcsonként](partition-data.md).

Miután létrehozott egy tárolót egy egyedi kulcsházirenddel, egy új elem vagy egy meglévő elem frissítésének létrehozása megakadályozza a logikai partíción belüli duplikátumot, az egyedi kulcsmegkötés által megadott módon. A partíciókulcs és az egyedi kulcs garantálja egy elem egyediségét a tároló hatókörén belül.

Például fontolja meg egy Azure Cosmos-tároló e-mail-címmel, mint az egyedi kulcsmegkötés, és `CompanyID` a partíciókulcs. Ha a felhasználó e-mail címét egyedi kulccsal konfigurálja, minden `CompanyID`elem nek egyedi e-mail címe van egy adott . Két elem nem hozható létre ismétlődő e-mail címekkel és ugyanazzal a partíciós kulcsértékkel. 

Ha azonos e-mail címmel, de nem azonos keresztnévvel, vezetéknévvel és e-mail-címmel rendelkező elemeket szeretne létrehozni, adjon hozzá további elérési utakat az egyedi kulcsházirendhez. Ahelyett, hogy csak az e-mail-cím alapján hozna létre egyedi kulcsot, létrehozhat egy egyedi kulcsot a keresztnév, a vezetéknév és az e-mail cím kombinációjával. Ezt a kulcsot összetett egyedi kulcsnak nevezzük. Ebben az esetben egy adott `CompanyID` értéken belül a három érték minden egyedi kombinációja megengedett. 

A tároló például a következő értékekkel rendelkező elemeket tartalmazhat, ahol minden elem tiszteletben tartja az egyedi kulcsmegkötést.

|CompanyID|Utónév|Vezetéknév|E-mail-cím|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam között|   |Duperre|gaby@fabraikam.com|
|Fabrkam között|   |   |gaby@fabraikam.com|

Ha megpróbál beszúrni egy másik elemet az előző táblázatban felsorolt kombinációkkal, hibaüzenet jelenik meg. A hiba azt jelzi, hogy az egyedi kulcsmegkötés nem teljesült. Vagy visszatérő `Resource with specified ID or name already exists` `Resource with specified ID, name, or unique index already exists` üzenetként jelenik meg. 

## <a name="define-a-unique-key"></a>Egyedi kulcs definiálása

Csak akkor határozhat meg egyedi kulcsokat, ha létrehoz egy Azure Cosmos-tárolót. Az egyedi kulcs hatóköre logikai partícióra terjed ki. Az előző példában, ha particionálja a tárolót az irányítószám alapján, akkor a végén duplikált elemek minden logikai partíción. Egyedi kulcsok létrehozásakor vegye figyelembe a következő tulajdonságokat:

* Egy meglévő tároló nem frissíthető egy másik egyedi kulcs használatához. Más szóval, miután egy tároló jön létre egy egyedi kulcsházirend, a szabályzat nem módosítható.

* Egy meglévő tároló egyedi kulcsának beállításához hozzon létre egy új tárolót az egyedi kulcsmegkötéssel. A megfelelő adatáttelepítési eszközzel helyezze át az adatokat a meglévő tárolóból az új tárolóba. SQL-tárolók esetén az [Adatáttelepítés eszközzel](import-data.md) helyezze át az adatokat. MongoDB-tárolók esetén használja a [mongoimport.exe vagy a mongorestore.exe](mongodb-migrate.md) segédprogramot az adatok áthelyezéséhez.

* Egy egyedi kulcsházirend legfeljebb 16 elérési útértékből rendelkezhet. Az értékek lehetnek `/firstName`például a , `/lastName`és `/address/zipCode`a. Minden egyedi kulcsházirend legfeljebb 10 egyedi kulcsmegkötéssel vagy kombinációval rendelkezhet. Az egyes egyedi indexmegkötések egyesített elérési útjai nem haladhatják meg a 60 bájtot. Az előző példában a keresztnév, a vezetéknév és az e-mail cím együttesen egy megkötés. Ez a megkötés a 16 lehetséges elérési útból 3-at használ.

* Ha egy tároló egyedi kulcsházirenddel rendelkezik, [a kérelemegység (RU)](request-units.md) egy elem létrehozásáért, frissítéséből és törléséért valamivel magasabb díjat számít fel.

* A ritka egyedi kulcsok nem támogatottak. Ha néhány egyedi elérési út értéke hiányzik, a rendszer null értékként kezeli őket, amelyek részt vesznek az egyediségi megkötésben. Ezért csak egy null értékű elem lehet a megkötés teljesítéséhez.

* Az egyedi kulcsnevek ben a kis- és nagybetűket nem lehet figyelembe. Vegyünk például egy tárolót, amelynek `/address/zipcode`egyedi kulcsmegkötése a. Ha az adatok egy `ZipCode`mező nevű , Az Azure Cosmos DB `zipcode` beszúrása "null", mint az egyedi kulcs, mert nem ugyanaz, mint `ZipCode`. A kis- és nagybetűk megkülönböztetése miatt a ZipCode-ot tartalmazó összes többi rekord nem szúrható be, mert a "null" duplikált elem megsérti az egyedi kulcsmegkötést.

## <a name="next-steps"></a>További lépések

* További információ a [logikai partíciókról](partition-data.md)
* Fedezze [fel, hogyan definiálható egyedi kulcsok](how-to-define-unique-keys.md) tároló létrehozásakor
