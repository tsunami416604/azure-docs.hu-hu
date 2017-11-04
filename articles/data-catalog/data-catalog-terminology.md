---
title: "Az Azure Data Catalog terminológiája |} Microsoft Docs"
description: "Ez a cikk bemutatja azokat a fogalmakat és szakkifejezéseket tartalmazza az Azure Data Catalog dokumentációja használt."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6fec74d9-4a3c-4b4b-88ba-cad5ad143331
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/01/2017
ms.author: maroche
ms.openlocfilehash: 8cb24357bb24c48dceda714a040427fcc0c0ba4d
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="azure-data-catalog-terminology"></a>Az Azure Data Catalog-terminológia
## <a name="catalog"></a>Alkalmazáskatalógus
Az Azure Data Catalog tárháza felhőalapú metaadatok, amely során az adatforrások és az eszközök regisztrálhatók. A katalógus egy központi tárolási helye az adatforrások kinyert metaadatokat, és a felhasználók által hozzáadott leíró metaadatok funkcionál.

## <a name="data-source"></a>Adatforrás
Egy adatforrás, a rendszer vagy a tároló, amely kezeli az adategységeket. Ilyen például az SQL Server-adatbázisok, Oracle-adatbázisok, SQL Server Analysis Services adatbázisok (táblázatos vagy többdimenziós) és az SQL Server Reporting Services-kiszolgálók.

## <a name="data-asset"></a>Adategységet
Adatok eszközök olyan adatforrásokat, a katalógus regisztrálhatók objektumok. Ilyen például az SQL Server-táblák és nézetek, Oracle táblák és nézetek, SQL Server Analysis Services intézkedések, dimenziók és KPI-k, és az SQL Server Reporting Services-jelentések.

## <a name="data-asset-location"></a>Adatok eszköz helye
A katalógus helyét egy adatforrás vagy az adategységhez, amely használható a forrás egy ügyfélalkalmazással kapcsolódni tárolja. A formátum és a hely adatait az adatforrás típusa függően változhat. Például egy SQL Server tábla is lehet a név által meghatározott négy rész – kiszolgálónév, az adatbázisnév, sémanév, objektumnév – során egy SQL Server Reporting Services jelentés azonosíthatja azokat az URL-CÍMÉT.

## <a name="structural-metadata"></a>Szerkezeti metaadatok
Szerkezeti metaadatok a metaadatokat egy adatforrásban, amely leírja egy adategységet szerkezetét. Ez magában foglalja a eszközök hellyel, a objektum neve és típusa, és további típusra vonatkozó mutatók. Például a táblák és nézetek szerkezeti metaadatokat tartalmaz, nevét és az objektum oszlopok adattípusát.

## <a name="descriptive-metadata"></a>Leíró metaadatok
Leíró metaadatok célját vagy egy adategységet célját leíró metaadatok. Általában hozzáadott leíró metaadatok katalógus felhasználók az Azure Data Catalog-portál használatával, de azt is történő kinyerésének az adatforrás regisztráció során. Például az Azure Data Catalog-regisztráló eszköz lesz kinyerése leírása a Description tulajdonságot az SQL Server Analysis Services és az SQL Server Reporting Services és a a [bővített tulajdonság ms_description](https://technet.microsoft.com/library/ms190243.aspx) az SQL Server-adatbázisok, ha ezek a tulajdonságok vannak-e töltve értékekkel.

## <a name="request-access"></a>Hozzáférés kérése
Egy adategységet leíró metaadatok közé tartozhatnak igénylésével adatok eszköz vagy az adatforrás elérésére. Ezeket az információkat az eszköz hely számára jelenik meg, és tartalmazhatják a következő lehetőségek közül:

* A felhasználó vagy csoport felelős az adatforráshoz való hozzáférés biztosítása e-mail címet.
* Felhasználók követnie kell az adatforrás eléréséhez dokumentált folyamat URL-CÍMÉT.
* Egy identitás- és hozzáférés-kezelési eszköz (például a Microsoft Identity Manager) az adatforrás eléréséhez használható URL-CÍMÉT.
* Egy szabad szöveges bejegyzést, amely leírja, hogyan felhasználók is hozzáférhetnek az adatforráshoz.

## <a name="preview"></a>Előzetes verzió
Az Azure Data Catalog előnézete legfeljebb 20 azt jelzi, hogy az adatforrás kinyert a regisztráció során, és az adatok eszköz metaadatokkal-katalógusban tárolt pillanatképe nem. Az előzetes segíthet felhasználók, akik egy adategységet felderítése jobb megértése érdekében a funkciójára és céljára. Ez azt jelenti megtekintheti a mintaadatok lehet a több mint jelent meg, csak az oszlopneveket és adattípusokat.
Az előzetes verziójú funkciók csak a táblák és nézetek támogatják, és kell kell explicit módon a felhasználó által megadott regisztrálás során.

## <a name="data-profile"></a>Adatok profil
Az Azure Data Catalog adatok profil egy regisztrált eszköz kibontani az adatforrás a regisztráció során, és az adatok eszköz metaadatokkal katalógusban tárolt tábla- és oszlop metaadatainak pillanatképet. Az adatok profil segítségével felhasználók, akik egy adategységet felderítése jobb megértése érdekében a funkciójára és céljára. Az előzetes verziójú funkciók hasonló, adatok profilok kell kell explicit módon a felhasználó által választott regisztráció során.

> [!NOTE]
> Adatok profil kibontása nagy táblák és nézetek az erőforrás-igényes művelet lehet, és jelentősen növelheti az adatforrást regisztrálni szükséges időt.
>
>

## <a name="user-perspective"></a>Felhasználói perspektíva
Az Azure Data Catalog minden olyan felhasználó, egy regisztrált eszköz biztosíthat leíró metaadatok. Minden felhasználó rendelkezik-e az adatokat, majd a használatával a különböző perspektívát. Például adja meg a rendszergazda egy server felelős előfordulhat, hogy a szolgáltatásiszint-szerződéssel (SLA) vagy a biztonsági mentési Időablakok; adatait egy adatok steward által biztosított a vállalati dokumentációjára mutató hivatkozásokat dolgozza fel az adatok támogatja; és valamelyik elemzőnek előfordulhat, hogy írjon be egy leírást a feltételeit, amelyek más elemzők igényeinek jobban megfelelő, és ami ezen felhasználók, akik megtalálhatóvá és értelmezhetővé az adatokat a legértékesebb lehet.

A szempontok mindegyikének eredendően értékes, és az Azure Data Catalog minden felhasználó információval szolgálhat a megfelelő, kifejező őket, amíg minden felhasználó használhatja ezt az információt megtudhatja, hogy az adatok és a célja.

## <a name="expert"></a>szakértői
Szakértőnek olyan felhasználó, aki rendelkezik azonosított, egy adategységet egy tájékozott "szakértői" szempont. Minden olyan felhasználó, eszköz szakértő szerint is felvehet maguk vagy egy másik felhasználó. Szakértőnek alatt felsorolva nem továbbítja az Azure Data Catalog; további jogosultságokra lehetővé teszi a felhasználók így könnyebben megtalálhatja ezeket perspektívát, amely a legnagyobb valószínűség lehet hasznos, ha egy eszköz leíró metaadatok áttekintése.

## <a name="owner"></a>Tulajdonos
Tulajdonos az az Azure Data Catalog egy adategységet további jogosultságokkal rendelkező felhasználóként. Felhasználók saját tulajdonukba vehetik regisztrált adategységeket, és tulajdonosok társtulajdonosok más felhasználók is hozzáadni. További információ: [adategységek felügyelete](data-catalog-how-to-manage.md)  

> [!NOTE]
> Tulajdonjog és felügyeleti csak a Standard Edition Azure Data Catalog a érhetők el.
>
>

## <a name="registration"></a>Regisztráció
Regisztráció az adatok eszköz metaadatok beolvasása az adatforrást, és másolja az Azure Data Catalog szolgáltatást. Regisztrált adategységeket majd feliratozva és felderített.

## <a name="see-also"></a>Lásd még:
* [Mi az az Azure Data Catalog?](data-catalog-what-is-data-catalog.md) – Ez a cikk áttekintést nyújt az Azure Data Catalog szolgáltatás, milyen értékeket nyújt, és a forgatókönyveket támogatja.
* [Ismerkedés az Azure Data Catalog](data-catalog-get-started.md) – Ez a cikk ismerteti egy végpont oktatóanyag, amely bemutatja, hogyan használható az Azure Data Catalog az adatforrás-felderítés.  
