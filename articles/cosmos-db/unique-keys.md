---
title: Egyedi kulcsok használata a Azure Cosmos DBban
description: Ismerje meg, hogyan használhatók az egyedi kulcsok az Azure Cosmos-adatbázisban
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 4a929566d464f8548c4bffeb9f89099e77722e67
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756787"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Egyedi kulcsokra vonatkozó korlátozások a Azure Cosmos DB

Az egyedi kulcsok adatintegritási réteget vesznek fel egy Azure Cosmos-tárolóba. Egy Azure Cosmos-tároló létrehozásakor egyedi kulcs-szabályzatot kell létrehoznia. Egyedi kulcsokkal gondoskodhat arról, hogy a logikai partíción belül egy vagy több érték egyedi legyen. Emellett a [partíciós kulcs](partition-data.md)egyediségét is garantálhatja. 

Miután létrehozta az egyedi kulcs-házirenddel rendelkező tárolót, a rendszer az egyedi kulcs megkötése által megadott módon megakadályozza a meglévő elemek új vagy frissítésének létrehozását egy logikai partíción belül. Az egyedi kulccsal összevont partíciós kulcs garantálja a tároló hatókörén belüli elem egyediségét.

Vegyünk például egy Azure Cosmos-tárolót e-mail-címmel, amely az egyedi kulcs megkötése, és `CompanyID` a partíciós kulcs. Ha a felhasználó e-mail-címét egyedi kulccsal konfigurálja, minden elem egyedi e-mail-címmel rendelkezik az adott `CompanyID`on belül. Két elem nem hozható létre duplikált e-mail-címmel, és ugyanazzal a partíciós kulcs értékkel. 

Ha ugyanazzal az e-mail-címmel szeretne elemeket létrehozni, de nem ugyanaz az utónév, a vezetéknév és az e-mail-cím, adjon hozzá további elérési utakat az egyedi kulcs házirendjéhez. Ahelyett, hogy csak az e-mail-cím alapján hozzon létre egyedi kulcsot, létrehozhat egy egyedi kulcsot is az utónév, a vezetéknév és az e-mail cím kombinációjával. Ezt a kulcsot összetett egyedi kulcsnak nevezzük. Ebben az esetben az adott `CompanyID` három értékének egyedi kombinációja engedélyezett. 

A tároló tartalmazhat például olyan elemeket, amelyek a következő értékekkel rendelkeznek, ahol mindegyik elem kiértékeli az egyedi kulcs megkötését.

|CompanyID|Utónév|Vezetéknév|E-mail-cím|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Ha egy másik, az előző táblázatban felsorolt kombinációval rendelkező tételt próbál beszúrni, hibaüzenet jelenik meg. A hiba azt jelzi, hogy az egyedi kulcs megkötése nem teljesült. `Resource with specified ID or name already exists` vagy `Resource with specified ID, name, or unique index already exists` kap visszatérési üzenetként. 

## <a name="define-a-unique-key"></a>Egyedi kulcs definiálása

Egyedi kulcsokat csak akkor adhat meg, ha létrehoz egy Azure Cosmos-tárolót. Az egyedi kulcs hatóköre egy logikai partíció. Az előző példában, ha a tárolót a ZIP-kód alapján particionálja, az egyes logikai partíciókban duplikált elemek jelennek meg. Egyedi kulcsok létrehozásakor vegye figyelembe a következő tulajdonságokat:

* Egy meglévő tároló nem frissíthető más egyedi kulcs használatára. Más szóval, miután egy tároló egyedi kulcs-házirenddel lett létrehozva, a szabályzat nem módosítható.

* Egy meglévő tároló egyedi kulcsának beállításához hozzon létre egy új tárolót az egyedi kulcs korlátozásával. A megfelelő áttelepítési eszköz használatával helyezze át az adatait a meglévő tárolóból az új tárolóba. SQL-tárolók esetén az [adatáttelepítési eszköz](import-data.md) használatával helyezze át az adatátvitelt. MongoDB-tárolók esetén az [mongoimport. exe vagy a mongorestore. exe](mongodb-migrate.md) használatával helyezze át az adatáthelyezést.

* Az egyedi kulcsokra vonatkozó házirend legfeljebb 16 elérésiút-értéket tartalmazhat. Az értékek lehetnek például `/firstName`, `/lastName`és `/address/zipCode`. Az egyes egyedi kulcsokra vonatkozó szabályzatok legfeljebb 10 egyedi kulcs megkötést vagy kombinációt tartalmazhatnak. Az egyes egyedi indexek korlátozásának kombinált elérési útjai nem haladhatják meg a 60 bájtot. Az előző példában az utónév, a vezetéknév és az e-mail-cím együtt egy megkötés. Ez a megkötés 3 lehetőséget használ a 16 lehetséges útvonalon.

* Ha egy tároló egyedi kulcsokra vonatkozó szabályzattal rendelkezik, az elemek létrehozásához, frissítéséhez és törléséhez szükséges használati [egység (ru)](request-units.md) díja valamivel nagyobb.

* A ritka egyedi kulcsok nem támogatottak. Ha néhány egyedi elérésiút-érték hiányzik, a rendszer null értékként kezeli őket, amelyek az egyediségi megkötésben részt vesznek. Emiatt csak egyetlen elem lehet null értékű, hogy kielégítse ezt a korlátozást.

* Az egyedi kulcsnév megkülönbözteti a kis-és nagybetűket. Vegyünk például egy olyan tárolót, amelyben az egyedi kulcs megkötése `/address/zipcode`értékre van beállítva. Ha az adatai `ZipCode`nevű mezővel rendelkeznek, Azure Cosmos DB a "NULL" értéket szúrja be egyedi kulcsként, mert a `zipcode` nem ugyanaz, mint `ZipCode`. Ennek az esetnek az eltérése miatt az irányítószámmal nem szúrható be minden más rekord, mert az ismétlődő "NULL" megsérti az egyedi kulcs megkötését.

## <a name="next-steps"></a>Következő lépések

* További információ a [logikai partíciókhoz](partition-data.md)
* Ismerje [meg, hogyan határozhat meg egyedi kulcsokat](how-to-define-unique-keys.md) tároló létrehozásakor
