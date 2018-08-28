---
title: Az Azure Data Catalogban az adategységek felügyelete
description: A cikk emeli ki, hogyan szabályozható a láthatóságot és az Azure Data Catalogban regisztrált adategységek tulajdonjogát.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 623f5ed4-8da7-48f5-943a-448d0b7cba69
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: e102b1f436b4f6d39f57445b02638ffd11f27786
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053757"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Az Azure Data Catalogban az adategységek felügyelete
## <a name="introduction"></a>Bevezetés
Az Azure Data Catalog adatforrás-felderítés, célja, hogy egyszerűen felderítése és értelmezni azon adatforrásokat kell elemzéseket végezhet, és döntéseket. Felderítési képességek győződjön meg arról, a lehető leghatékonyabb felhasználásához és más felhasználók is található, és az elérhető adatforrások széles ismertetése. Ezeket az elemeket a szem előtt, a Data Catalog alapértelmezett viselkedését az összes regisztrált adatforrások számára látható és a katalógus összes felhasználó által felderíthető van.

A Data Catalog nem biztosít hozzáférést magukhoz az adatokhoz. Adatok elérését az adatforrás tulajdonosa szabályozza. A Data Catalog adatforrások felfedezése és a megtekintéséhez, amely kapcsolódik a a katalógusban regisztrált adatforrások metaadatait.

Előfordulhat, azonban, ahol az adatforrásokat csak láthatóvá tenni bizonyos felhasználók, illetve adott csoportok tagjai. Ilyen esetekben a felhasználók átveheti a katalógusban regisztrált adategységek tulajdonjogát, és majd a láthatóságot a saját eszközök.

> [!NOTE]
> Ebben a cikkben leírt funkciója csak az az Azure Data Catalog Standard kiadásában elérhető. Az ingyenes kiadás nem biztosít tulajdonosi és a csoportkezelést-adategység láthatóságának lehetőségei.
>
>

## <a name="manage-ownership-of-data-assets"></a>Adategységek tulajdonjogát kezelése
Alapértelmezés szerint a Data catalogban regisztrált adategységeket tulajdonos nélküli. A katalógus hozzáféréssel rendelkező felhasználók is felderítését, és ezek bővítését. A felhasználók tulajdonos nélküli adategységek birtokbavétele és majd korlátozza a saját adategységek láthatóságát.

A Data Catalog egy adategységhez a tulajdonosa, ha csak a tulajdonos által engedélyezett a felhasználók az eszköz felderítése, és megtekintheti a metaadatait, és csak a tulajdonosok a katalógusból is törli az objektumot.

> [!NOTE]
> A Data Catalog tulajdonjog csak a katalógusban tárolt metaadatok van hatással. Tulajdonjog nem rendelkezik az alapul szolgáló adatforrás az engedélyek.
>
>

### <a name="take-ownership"></a>Saját tulajdonba vétel
Felhasználók saját tulajdonba adategységek kiválasztásával a **saját tulajdonba vétel** lehetőséget a Data Catalog-portálon. Nincsenek speciális engedélyek szükségesek egy tulajdonos nélküli adategység tulajdonba. Minden felhasználó egy tulajdonos nélküli adategység tulajdonba.

### <a name="add-owners-and-co-owners"></a>Tulajdonosok és a társtulajdonosok hozzáadása
Ha egy adategységet már van tulajdonosa, más felhasználók nem egyszerűen saját tulajdonba vétele. Ezek hozzá kell adnia, társtulajdonosok egy meglévő tulajdonosa. Bármely tulajdonosa adhat hozzá további felhasználókat vagy biztonsági csoportok, a társtulajdonosok.

> [!NOTE]
> Egy ajánlott legalább két személyek tulajdonosként bármely saját tulajdonú adatok eszköz.
>
>

### <a name="remove-owners"></a>Tulajdonos eltávolítása
Bármely eszköz tulajdonosa adhat hozzá a társtulajdonosok, mint bármely eszköz tulajdonosa távolíthatja el bármilyen társtulajdonos.

Egy eszköz tulajdonosa, aki eltávolítja az őt vagy saját magát tulajdonosként több nem tudja felügyelni az eszközt. Ha az eszköz tulajdonosa eltávolítja számára, vagy saját magát tulajdonosként, és nincs más társtulajdonosok van, az eszköz visszaáll egy tulajdonos nélküli állapotot.

## <a name="control-visibility"></a>Vezérlőelem látható-e
Adatok-adatforrások tulajdonosai szabályozhatják a saját adategységek láthatóságát. Látható-e az alapértelmezett, ahol a Data Catalog minden felhasználó felderítése és megtekintheti az adategységhez, korlátozhatja az eszköz tulajdonosa válthat a láthatósági a **mindenki** való **tulajdonosok és ezek a felhasználók** a az objektum tulajdonságait. Tulajdonosok ezután hozzáadhatja a konkrét felhasználókat és biztonsági csoportokat.

> [!NOTE]
> Amikor csak lehetséges, eszköz tulajdonjoga és a láthatósági engedélyek hozzá kell rendelni biztonsági csoportok és nem egyéni felhasználók számára.
>
>

## <a name="catalog-administrators"></a>Katalógus-rendszergazdák
A Data Catalog rendszergazdái implicit módon társtulajdonosok a katalógusban az összes erőforrás is. Adatforrások tulajdonosai nem távolítható el látható-e a rendszergazdák és rendszergazdák kezelhetik a tulajdonosi és a katalógus összes adategységek láthatóságát.

## <a name="summary"></a>Összegzés
A Data Catalog közösségi modellt a metaadatokat és az adatokat az adatvagyon felderítését lehetővé teszi, hogy az összes katalógus felhasználói közreműködés és felderítéséhez. A Data Catalog Standard kiadásának célja a tulajdonosi és felügyeleti korlátozza a láthatóságát és a meghatározott eszközök használatát.
