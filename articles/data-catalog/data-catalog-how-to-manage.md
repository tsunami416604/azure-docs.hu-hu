---
title: "Az Azure Data Catalog adategységek felügyelete |} Microsoft Docs"
description: "A cikk látható és az Azure Data Catalog regisztrált adategységeket tulajdonjogát vezérlése mutatja be."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 623f5ed4-8da7-48f5-943a-448d0b7cba69
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 5a4b2b5734bf8bfbbc45a65b02362d1fa37b1a87
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Az Azure Data Catalog adategységek felügyelete
## <a name="introduction"></a>Bevezetés
Az Azure Data Catalog adatforrás felderítés, célja, hogy könnyen felderítése és értelmezni azon adatforrásokat elemzést és döntéseket kell. E felderítés képességeivel a legnagyobb mértékben Ha más felhasználók is felkutatni és értelmezni a elérhető adatforrások legszélesebb skáláját. Ezeknek az elemeknek szem előtt, az alapértelmezett Data Catalog a rendszer nem minden regisztrált adatforrások számára látható és senki katalógus által felderíthető.

A Data Catalog nem hozzáférést biztosít az adatokat mozgatná. Az adatforrás tulajdonosi adatelérési vezérli. A Data Catalog adatforrások felderítésére, és a metaadatok a forrásokat a katalógusban regisztrált kapcsolódó megtekintése.

Előfordulhat, azonban ha adatforrások csak akkor látható, az adott felhasználóknak, vagy adott csoportra. Ilyen esetekben a felhasználók saját tulajdonba vétele belül a katalógusban regisztrált adategységeket és majd a vezérlő látható-e a saját eszközök.

> [!NOTE]
> A jelen cikkben ismertetett funkció csak a Standard Edition Azure Data Catalog érhető el. Az ingyenes kiadás nem biztosít tulajdonjoga és korlátozásáról adategységet láthatósági képességét.
>
>

## <a name="manage-ownership-of-data-assets"></a>Adategységek tulajdonjogát kezelése
Alapértelmezés szerint a Data Catalog regisztrált adategységeket található, a tulajdonos. A katalógus hozzáféréssel rendelkező felhasználók felderítésére, és ezeknek az eszközöknek a jegyzet. A felhasználók tulajdonos nélküli adategységek saját tulajdonba és látható-e a saját eszközök majd korlátozza.

A Data Catalog egy adategységet tulajdonosa, ha csak olyan felhasználók, akik jogosultak a tulajdonosai által az eszköz felderítése, és megtekintheti a metaadatait, és csak a tulajdonosok listája az eszköz törlése a katalógusból.

> [!NOTE]
> A Data Catalog tulajdonjoga csak a katalógusban tárolt metaadatok hatással van. Tulajdonjog nem rendelkezik engedéllyel a az alapul szolgáló adatforrásban.
>
>

### <a name="take-ownership"></a>Saját tulajdonba vétel
Felhasználók tulajdonba az adategységek kiválasztásával a **tulajdonba** lehetőséget a Data Catalog-portál. Tulajdonos nélküli adatok eszköz saját tulajdonba nem különleges engedélyekre van szükség. Bármely felhasználó tulajdonba tulajdonos nélküli adatok eszköz.

### <a name="add-owners-and-co-owners"></a>Adja hozzá a tulajdonosok és a társtulajdonosok
Ha már van tulajdonosa egy adategységet, más felhasználók nem egyszerűen saját tulajdonba. Kell őket hozzáadni, társtulajdonosok meglévő tulajdonos. A tulajdonos szerint társtulajdonosok is felvehet további felhasználók és biztonsági csoportokat.

> [!NOTE]
> Egy ajánlott legalább két egyéni felhasználók számára, mint bármely tulajdonában lévő adatok eszköz tulajdonosait.
>
>

### <a name="remove-owners"></a>Távolítsa el a tulajdonosok
Ugyanúgy, mint bármely az eszköz tulajdonosa társtulajdonosok hozzáadására, bármely eszköz tulajdonosa távolíthatja el a társtulajdonos.

Egy eszköz tulajdonost, aki számára, vagy saját magát eltávolítja a tulajdonos már nem felügyelheti az eszközt. Ha az eszköz tulajdonosa számára, vagy saját magát eltávolítja a tulajdonos, és nincs más társtulajdonosok vannak, az eszköz tulajdonos nélküli állapotba áll vissza.

## <a name="control-visibility"></a>Láthatóságának
Adategységet tulajdonosok szabályozhatja a saját adategységek láthatóságát. Korlátozható a láthatóságuk az alapértelmezett, ahol az összes Data Catalog felhasználó felderítése és megtekintése a adategységet, hogy az eszköz tulajdonosa az a láthatósági visszaváltható **mindenki** való **tulajdonosok és ezek felhasználók** a az objektum tulajdonságait. Tulajdonosok majd adhat hozzá a megadott felhasználók és biztonsági csoportok.

> [!NOTE]
> Amikor csak lehetséges – eszköz tulajdonjoga és a láthatóság érdekében engedélyek hozzá kell rendelni biztonsági csoportokra, és nem az egyes felhasználók számára.
>
>

## <a name="catalog-administrators"></a>Katalógus-rendszergazdák
Katalógus-rendszergazdák implicit módon társtulajdonosok az összes eszköz a katalógusban. Eszköz tulajdonosait nem távolítható el látható a rendszergazdák és rendszergazdák kezelhetik a tulajdonosi és a katalógusban az összes adategységek láthatóságát.

## <a name="summary"></a>Összegzés
A Data Catalog közösségi modell metaadatokat és eszköz felderítése lehetővé teszi, hogy minden katalógus felhasználói közreműködés és Fedezze fel. A Data Catalog Standard Edition tulajdonjoga és készült felügyeleti korlátozni a láthatóság és a meghatározott eszközök használatát.
