---
title: Azure Cosmos DB-ben egyedi kulcsaival
description: Az Azure Cosmos DB adatbázis egyedi kulcsok használata
author: aliuy
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: 006d0ef28d82a7648a56b3bf871c5a3afd6a55a6
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624420"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Azure Cosmos DB-ben egyedi kulcsaival

Egyedi kulcsok lehetővé teszi, hogy egy Cosmos-tárolóhoz egy adatintegritási réteget biztosít. Egyedi hoz létre, amikor egy Cosmos-tároló létrehozása. Egyedi kulccsal rendelkező logikai partíción belül egy vagy több értéket egyediségének biztosítása (kiszolgálónként egyedisége garantálhatja [partíciókulcs](partition-data.md)). Miután létrehozott egy tároló egyedi, ez megakadályozza, hogy egy logikai partíciót, azokat az egyedi megkötés belül bármely új (vagy frissített) ismétlődő elemeket hozza létre. A partíciókulcs a hatókörön belüli elem a tároló egyedi kulcs garanciák egyediségének együtt.

Vegyük példaként egy Cosmos-tároló egyedi megkötés, e-mail-címmel és `CompanyID` partíciókulcsként. Konfigurálása a felhasználó e-mail-cím egy egyedi kulcsot, biztosíthatja, hogy minden elem belül egyedi e-mail-címmel rendelkezik egy adott `CompanyID`. A két elem nem hozható létre, duplikált e-mail-címek és az egyazon partíciókulcs-értékkel.  

Ha szeretne biztosítani a felhasználók létrehozása több elem is ugyanazt az e-mail címet, de nem az azonos Utónév, az utolsó és az e-mail-címét, hozzáadhat további elérési utak a egyedi kulcs szabályzathoz. Az e-mail cím alapján egyedi kulcs létrehozása helyett is létrehozhat egy egyedi kulcsot kombinációjával az Utónév, Vezetéknév és az e-mail cím (összetett egyedi kulccsal). Ebben az esetben minden egyéni kombinációja a három értékek belül egy adott `CompanyID` engedélyezett. Például a tároló elemek, a következő értékeket, ahol minden egyes cikk weboldalak alapján a egyedi korlátozást is tartalmazhat.

|CompanyID|Utónév|Vezetéknév|E-mail-cím|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|A Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|A Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Ha egy másik elem beszúrása a fenti táblázatban felsorolt-kombinációkkal, kapni fog egy hibaüzenet, hogy az egyedi megkötés nem teljesült. Kapni fog vagy "erőforrás a megadott azonosító vagy név már létezik" vagy "Erőforrás a megadott azonosító, név vagy egyedi index már létezik" visszatérési üzenetnek számít.  

## <a name="defining-a-unique-key"></a>Egy egyedi kulcsot meghatározása

Egyedi kulcsok csak egy Cosmos-tároló létrehozásakor definiálhat. Egy egyedi kulcsot hatókörét a logikai partíció. Az előző példában ha a tároló az irányítószám alapján particionálja, megszűnik be kellene az egyes logikai partíciók elemek ismétlődik. Egyedi kulcsok létrehozásakor, vegye figyelembe a következő tulajdonságokkal:

* Meglévő tároló használata egy másik egyedi kulcs nem frissíthető. Más szóval az egyedi tároló létrehozása után a házirendet nem lehet módosítani.

* Ha azt szeretné, egyedi kulcs beállítása egy már meglévő tárolóhoz, akkor hozzon létre egy új tárolót a egyedi korlátozást, és a megfelelő adatok áttelepítési eszköz használata az adatok áthelyezése az új tároló meglévő tárolót. SQL-tárolók, használja a [adatáttelepítési eszköz](import-data.md) adatok áthelyezéséhez. Használja a MongoDB-tárolók, [mongoimport.exe vagy mongorestore.exe](mongodb-migrate.md) adatok áthelyezéséhez.

* Egyedi legfeljebb 16 elérési út értékek (például: /firstName, /lastName, / cím/irányítószám). Minden egyedi kulcs szabályzat legfeljebb 10 egyedi kulcsra vonatkozó megkötések, vagy kombinációk és az egyes egyedi index megkötés összevont elérési útjait nem lehet hosszabb 60 bájt. Az előző példában utónevét, vezetéknevét és e-mail-címre együtt egyetlen megkötés, és három a 16 lehetséges útvonalakat használ.

* Ha egy tároló tartozik egy egyedi kulcs házirendet, a kérelem egység (RU) költségek szeretne létrehozni, update és delete elem valamivel nagyobb.

* Ritka egyedi kulcsok nem támogatottak. Ha néhány egyedi elérési útja érték hiányzik, azok null az értéke, amely részt vesz az egyediségre vonatkozó feltételnek kell kezelni. Ezért csak lehet egy elem null értékű kielégíteni ezt a korlátozást.

* A kulcsnevek egyedi nagybetűk között. Például vegyünk egy tárolót a egyedi key megkötés /address/zipcode beállítása. Ha az adatok mezőnév irányítószám, Cosmos DB beszúrása "null", az egyedi kulcs, mert "Irányítószám" nem ugyanaz, mint a "Irányítószám". A kis-és nagybetűk, mert irányítószám az összes többi rekordot nem lehet beszúrni, mert az ismétlődő "null" megsérti az egyedi megkötés.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [logikai partíció](partition-data.md)
