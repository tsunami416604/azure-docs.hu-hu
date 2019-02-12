---
title: Egyedi kulcsok használata az Azure Cosmos DB-ben
description: Egyedi kulcsok használata az Azure Cosmos-adatbázis
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.reviewer: sngun
ms.openlocfilehash: 3a7133d9c092ab8ad8a4bc585e3b0df2b8ca1234
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55999266"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Az Azure Cosmos DB egyedi kulcsokra vonatkozó korlátozások

Egyedi kulcs hozzáadása egy Azure Cosmos-tárolóhoz egy adatintegritási réteget. Egyedi hoz létre, amikor létrehoz egy Azure Cosmos-tárolóhoz. Egyedi kulcsokkal biztosíthatja, hogy egy logikai partíción belül egy vagy több érték egyedi legyen. Is tud garantálni kiszolgálónként egyedisége [partíciókulcs](partition-data.md). 

Miután egy tárolót hoz létre egyedi, a logikai partíció új vagy frissített ismétlődő elemek létrehozását miatt nem sikerült, az egyedi kulcsmegkötés által megadott. A partíciós kulcs egyedi kulcsa kombinálva garantálja, hogy egy elem a tároló a hatókörön belüli egyediségét.

Vegyük példaként egy Azure Cosmos-tárolóhoz, az egyedi kulcsmegkötés e-mail-címmel és `CompanyID` partíciókulcsként. Konfigurál egy egyedi kulcsot a felhasználó e-mail címét, amikor minden elem belül egyedi e-mail-címmel rendelkezik egy adott `CompanyID`. A két elem nem hozható létre, duplikált e-mail-címek és az egyazon partíciókulcs-értékkel. 

Konfigurációelemek létrehozása a azonos e-mail-cím, de nem az azonos Utónév, Vezetéknév és e-mail-címét, adja hozzá további elérési utak az egyedi kulcs házirend. Az e-mail cím alapján egyedi kulcs létrehozása helyett is létrehozhat egy egyedi kulcsot kombinációjával az Utónév, Vezetéknév és az e-mail cím. Ez a kulcs egyedi összetett kulccsal néven ismert. Ebben az esetben minden egyéni kombinációja a három értékek belül egy adott `CompanyID` engedélyezett. 

Például a tároló elemek, a következő értékeket, ahol minden egyes cikk figyelembe veszi a egyedi korlátozást is tartalmazhat.

|CompanyID|Utónév|Vezetéknév|E-mail-cím|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Ha egy másik elem beszúrása az előző táblázatban szereplő-kombinációkkal, hibaüzenetet kap. A hiba azt jelzi, hogy az egyedi megkötés nem teljesült. Kap, vagy "erőforrás a megadott azonosító vagy név már létezik" vagy "Erőforrás a megadott azonosító, név vagy egyedi index már létezik" visszatérési üzenetnek számít. 

## <a name="define-a-unique-key"></a>Egyedi kulcs megadása

Egyedi kulcsok csak egy Azure Cosmos-tároló létrehozásakor definiálhat. Egy egyedi kulcsot hatókörét a logikai partíció. Az előző példában ha a tárolót a ZIP-kód alapján particionálja, végül az egyes logikai partíciók ismétlődő elemeket. Egyedi kulcsok létrehozásakor, vegye figyelembe a következő tulajdonságokkal:

* Meglévő tároló használata egy másik egyedi kulcs nem frissíthető. Más szóval az egyedi tároló létrehozása után a házirendet nem lehet módosítani.

* Egyedi kulcs beállítása egy már meglévő tárolóhoz, hozzon létre egy új tárolót a egyedi korlátozást. A megfelelő adatok áttelepítési eszköz használata az adatok áthelyezése a meglévő tárolót az új tárolóhoz. SQL-tárolók, használja a [adatáttelepítési eszközét](import-data.md) adatok áthelyezéséhez. Használja a MongoDB-tárolók, [mongoimport.exe vagy mongorestore.exe](mongodb-migrate.md) adatok áthelyezéséhez.

* Egyedi rendelkezhet egy legfeljebb 16 elérési útja értéket. Az értékek lehetnek például /firstName /lastName és /address/zipCode. Minden egyedi kulcs szabályzat legfeljebb 10 egyedi kulcsokra vonatkozó korlátozások vagy kombinációk rendelkezhet. Minden egyedi index korlátozó kombinált elérési útjait nem haladhatja meg a 60 bájt. Az előző példában utónevét, vezetéknevét és e-mail-címre együtt egy korlátozás. Ennél a határértéknél 3 kívül a 16 lehetséges útvonalakat használ.

* Ha egy tároló tartozik egy egyedi kulcs házirendet, a kérelem kérelemegység (RU) költségek szeretne létrehozni, update és delete elem valamivel nagyobb.

* Ritka egyedi kulcsok nem támogatottak. Hiányoznak néhány egyedi elérési útja értéket, ha azok null az értéke, amely részt vesz az egyediségre vonatkozó még kezelni. Ezért csak egy elemet a null érték kielégíteni ezt a korlátozást is lehet.

* A kulcsnevek egyedi nagybetűk között. Például vegyünk egy tárolót a egyedi key megkötés /address/zipcode beállítása. Ha az adatok mezőnév irányítószám, Azure Cosmos DB szúr be "null", az egyedi kulcs, mert "Irányítószám" nem ugyanaz, mint "Irányítószám." Miatt a kis-és nagybetűk irányítószám az összes többi rekordot nem lehet beszúrni, mert az ismétlődő "null" megsérti az egyedi megkötés.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [logikai partíció](partition-data.md).
