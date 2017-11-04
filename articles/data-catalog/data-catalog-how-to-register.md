---
title: "Adatforrások regisztrálása az Azure Data Catalog |} Microsoft Docs"
description: "Ez a cikk adatforrások regisztrálása az Azure Data Catalog, beleértve a regisztráció során kibontott metaadatmezőket mutatja be."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: maroche
ms.openlocfilehash: 42d7fddd592b9cea8fbfa38bf39ca5def3542d66
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Az Azure Data Catalog adatforrások regisztrálása
## <a name="introduction"></a>Bevezetés
Az Azure Data Catalog egy teljes körűen felügyelt felhőszolgáltatás, amely a regisztráció és a vállalati adatforrások felderítését a rendszer funkcionál. Más szóval a Data Catalog segít személyek felderítése, megismeréséhez és használatához adatforrások, és segít a szervezeteknek több érték lekérése a meglévő adatokat. Az első lépés, hogy a Data Catalog által felfedezhetők adatforrás-hoz, hogy az adatforrás regisztrálása.

## <a name="register-data-sources"></a>Adatforrások regisztrálása
Regisztráció az a folyamat a metaadatok beolvasása az adatforrás adatainak és másolása, hogy a Data Catalog szolgáltatásba. Az adatok helye nem változik, ahogy az sem, hogy mely rendszergazdák felügyelik az adatokat, és milyen szabályzatok érvényesek rájuk.

Egy adatforrás regisztrálása, tegye a következőket:
1. Az Azure Data Catalog-portálon indítsa el a Data Catalog adatforrás-regisztráló eszköz. 
2. Jelentkezzen be a munkahelyi vagy iskolai fiókjával jelentkezzen be a portálra használó azonos Azure Active Directory hitelesítő adatokkal.
3. Jelölje ki a regisztrálni kívánt adatforrást.

További részletes információkért tekintse meg a [Ismerkedés az Azure Data Catalog](data-catalog-get-started.md) oktatóanyag.

Az adatforrás regisztrálása után a katalógust nyomon követi az helyére, és indexeli a metaadatait. Felhasználók keresésében, böngészésében, és Fedezze fel az adatforrást, és az helyére segítségével az alkalmazás vagy az általuk választott eszköz használatával kapcsolódni hozzá.

## <a name="supported-data-sources"></a>Támogatott adatforrások
A jelenleg támogatott adatforrások listájáért lásd: [Data Catalog DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Szerkezeti metaadatok
Egy adatforrás regisztrálásakor a frissítésregisztráló eszköz kibontja az objektumokhoz szerkezete kapcsolatos információkat. Ez az információ szerkezeti metaadatok nevezzük.

Minden objektum a szerkezeti metaadatok magában foglalja az objektum tárfiókbeli helyének, hogy a felhasználók számára az adatok felderítése használhatja ezt az információt az ügyféleszközökben az általuk választott objektum való kapcsolódáshoz. Egyéb szerkezeti metaadatokat objektum neve és típusa, és attribútum-vagy nevét és az adatok írja be.

## <a name="descriptive-metadata"></a>Leíró metaadatok
A fő szerkezeti metaadatokat, amelyek az adatforrás ki kell olvasni, mellett az adatforrás-regisztráló eszköz leíró metaadatok bontja ki. Az SQL Server Analysis Services és az SQL Server Reporting Services a metaadatok jelennek meg, ha ezek a szolgáltatások leírása tulajdonságai lesz végrehajtva. Az SQL Server használatával a ms megadott értékek\_bővített tulajdonság leírás ki kell olvasni. Az Oracle-adatbázishoz, az adatforrás regisztráló eszköz kibontja a Megjegyzés oszlopban az összes\_lapon\_megjegyzések megtekintése.

Ki kell olvasni az adatforrásból leíró metaadatok, mellett felhasználók adhat meg a leíró metaadatok az adatforrás-regisztráló eszköz használatával. Címkék a felhasználók hozzáadhatnak, és azonosítani tudják a regisztrálandó objektumok szakértői. A leíró metaadatok a Data Catalog szolgáltatás együtt a szerkezeti metaadatokat másolja.

## <a name="include-previews"></a>Tartalmazza az előzetes verziójú funkciók
Alapértelmezés szerint csak a metaadatok adatforrások kinyert, és másolja a Data Catalog szolgáltatást, de az adatforrás gyakran könnyebbé egy minta a benne található adatok megtekintésekor ismertetése.

Az adatkatalógus-adatforrás regisztráló eszköz használatával az adatok pillanatkép előnézete foglalandó minden tábla és nézet, amely regisztrálva van-e. Ha azt választja előzetes regisztráció során, akkor a frissítésregisztráló eszköz minden tábla és nézet legfeljebb 20 rekordokat tartalmazza. A pillanatkép majd át lesznek másolva a katalógust a szerkezeti és leíró metaadatok együtt.

> [!NOTE]
> Széles táblákon, amelyekhez nagy számú oszlopot a 20-nál kevesebb bejegyzés szerepel az előzetes tartozhat.
>
>

## <a name="include-data-profiles"></a>Adatok kísérhetők
Többek között a következőket az előzetes verziójú funkciók értékes környezetet biztosíthat a felhasználók számára a Data Catalog tárolt adatforrások keresésére, mint például az adatok profil teheti megérteni a felderített adatforrások.

Az adatkatalógus-adatforrás regisztráló eszköz használatával minden tábla és nézet, amellyel regisztrálva van-e az adatok profilt is megadhat. Ha tartalmazza a adatok profilt a regisztráció során, a frissítésregisztráló eszköz tartalmazza az adatokat összesített statisztikája minden tábla és nézet, beleértve:

* Sorok és az objektum az adatok méretét száma.
* A legújabb frissítés az adatok és a séma a dátuma.
* Null rekordok és a különböző értékeket az oszlopok száma.
* Oszlopok minimum, maximum, átlagos és szórás értékeit.

A statisztikai információk majd kerülnek a katalógust a szerkezeti és leíró metaadatok együtt.

> [!NOTE]
> Szöveg és a dátum oszlop nem tartalmaz átlagos vagy a szórás statisztika a adatok profilban.
>
>

## <a name="update-registrations"></a>Regisztráció frissítése
Egy adatforrás regisztrálása segítségével felderíthetők a Data Catalog a metaadatok és regisztráció során kibontott választható preview használatakor. Az adatforrás a katalógusban (például ha egy objektum a séma megváltozott, eredetileg kizárt táblák kell figyelembe venni, vagy frissíti az adatokat, amely megtalálható a előzetes) frissíteni kell, ha az adatforrás-regisztráló eszköz futtatható újra.

Egy már regisztrált adatforrás újraregisztrálása egyesítési "upsert" műveletet hajt végre: meglévő objektumok frissülnek, és új objektumok létrehozásakor. Minden metaadatot, felhasználókon a Data Catalog-portál által biztosított megmaradnak.

## <a name="summary"></a>Összefoglalás
Az alkalmazáskatalógus webszolgáltatás adatforrás szerkezeti és leíró metaadatok másolja át, mert az adatforrás regisztrálása a Data Catalog, könnyebben megtalálhatóvá és értelmezhetővé. Az adatforrás regisztráció után megjegyzésekkel, kezeléséhez, és felderíti a Data Catalog-portál használatával.

## <a name="next-steps"></a>Következő lépések
Adatforrások nyilvántartására vonatkozó további információkért lásd: a [Ismerkedés az Azure Data Catalog](data-catalog-get-started.md) oktatóanyag.
