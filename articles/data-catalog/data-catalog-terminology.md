---
title: Azure Data Catalog-terminológia
description: Ez a cikk mutatja be a fogalmakat és használja az Azure Data Catalog dokumentációja feltételeit.
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 9b945057ed0c94fbab7a9114ad1198a0f157fa4d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270965"
---
# <a name="azure-data-catalog-terminology"></a>Azure Data Catalog-terminológia

Ez a cikk mutatja be a fogalmakat és használja az Azure Data Catalog dokumentációja feltételeit.

## <a name="catalog"></a>Katalógus

Az Azure Data Catalog egy felhőalapú metaadat-adattár, amely során források és az eszközöket regisztrálni lehet. A katalógus egy központi tárolóhely adatforrások kinyert szerkezeti metaadatokat és a felhasználók által hozzáadott leíró metaadatok funkcionál.

## <a name="data-source"></a>Adatforrás

Egy adatforrás, a rendszer vagy a tároló, amely kezeli az adategységeket. Ilyenek például az SQL Server-adatbázisok, Oracle-adatbázisok, SQL Server Analysis Services-adatbázisok (táblázatos vagy többdimenziós) és az SQL Server Reporting Services-kiszolgálóval.

## <a name="data-asset"></a>Adategység

Adategységei számára a belüli a katalógusban regisztrált adatforrásokat. Ilyenek például az SQL Server-táblák és nézetek, Oracle-táblák és nézetek, SQL Server Analysis Services mértékek, dimenziók és KPI-k, és az SQL Server Reporting Services-jelentések.

## <a name="data-asset-location"></a>Az adatok eszköz helye

A katalógus helyét egy adatforrás vagy az adategységhez, amely használható a forrás egy ügyfélalkalmazással kapcsolódni tárolja. A formátum és a részletek a hely változnak az az adatforrás típusa alapján. Például egy SQL Server-táblát úgy azonosítható a négyrészes név – a kiszolgáló neve, a adatbázis neve, a séma neve, a objektum neve – bár egy SQL Server Reporting Services jelentés azonosíthatja azokat az URL-CÍMÉT.

## <a name="structural-metadata"></a>Szerkezeti metaadatok

Szerkezeti metaadatokat egy adategységhez szerkezetét leíró adatforrás kinyert metaadatok. Ez magában foglalja az eszközök helyét, az objektum neve és típusa és további típusspecifikus jellemzőit. Például a táblák és nézetek szerkezeti metaadatokat tartalmaz, nevét és az objektum oszlopok adattípusát.

## <a name="descriptive-metadata"></a>A leíró metaadatok

A leíró metaadatok célját vagy egy adategységet célját leíró metaadatok. Általában a leíró metaadatok használatával az Azure Data Catalog portál katalógus felhasználói által hozzáadott, de azt is kinyerésének az adatforrás regisztrációja során. Például az Azure Data Catalog-regisztráló eszköz fogja kinyerni leírások Description tulajdonságában, illetve az SQL Server Analysis Services és az SQL Server Reporting Services a [kiterjesztett tulajdonság ms_description](https://technet.microsoft.com/library/ms190243.aspx) SQL-ben Server-adatbázisok, ha ezek a tulajdonságok vannak-e töltve értékekkel.

## <a name="request-access"></a>Hozzáférés kérése

Leíró metaadatokat egy adategységhez tájékoztatást arról, hogyan kérhet hozzáférést az adategységhez vagy adatforrás is tartalmazhat. Ez az információ egyike jelenik meg az adatok eszköz helyét, és tartalmazhat egy vagy több, a következő lehetőségek közül:

* A felhasználó vagy az adatforráshoz való hozzáférés biztosítása felelős csapat e-mail-cím.
* Az URL-címe a dokumentált folyamat, amely a felhasználók követnie kell a hozzáférést az adatforráshoz.
* Egy identitás- és hozzáférés-kezelési eszköz (például a Microsoft Identity Manager) az adatforrás eléréséhez használható URL-címe
* Egy szabad szöveges bejegyzést, amely leírja, hogyan felhasználók is hozzáférhetnek az adatforrás.

## <a name="preview"></a>Előzetes verzió

Egy minta Azure Data Catalog a pillanatkép legfeljebb 20 bejegyzést, amely a regisztráció során az adatforrásból kinyert, és a katalógusban az adatok adategység metaadatait tárolja. Az előnézet segítségével felhasználók számára egy adategységet értelmezheti annak funkciójára és céljára. Más szóval minta adatmegjelenítési lehet még értékesebb, mint a oszlopneveket és adattípusokat.
Előzetes verziók csak a táblák és nézetek támogatják, és kell lennie explicit módon a felhasználó által kiválasztott regisztrációja során.

## <a name="data-profile"></a>Adatprofil

Az Azure Data Catalog egy adatprofil a regisztráció során az adatforrásból kinyert, és tárolja az adatokat az eszközintelligencia metaadatokkal a katalógusban regisztrált adatforrásokat adategység metaadatait táblaszintű és oszlopszintű pillanatképét. Segíthet az adatok profil felhasználók számára egy adategységet értelmezheti annak funkciójára és céljára. Előzetes verziók hasonlóan adatok profilokat kell kell explicit módon a felhasználó által kiválasztott regisztrációja során.

> [!NOTE]
> Egy adatprofil kibontása nagy táblák és nézetek az erőforrás-igényes művelet lehet, és jelentősen növelheti a adatforrást regisztrálni szükséges időt.


## <a name="user-perspective"></a>Felhasználó szemszögéből

Az Azure Data Catalog minden felhasználó biztosíthatja a leíró metaadatok regisztrált adategység. Minden felhasználó rendelkezik egy különálló perspektíva az adatokkal és annak használatára. Például adja meg a kiszolgáló felelős rendszergazda előfordulhat, hogy a szolgáltatói szerződés (SLA) vagy a biztonsági mentési Időablakok; adatait egy az adatgazdai nyújthat az üzleti dokumentációs hivatkozásokat feldolgozza az adatokat támogatja; és elemző előfordulhat, hogy ismertesse a feltételeit, amelyek más elemzők számára leginkább releváns, és amely lehet a legértékesebb, ezek a felhasználók, akiknek szükség van a megismerni és értelmezni az adatokat.

A szempontok mindegyikének természetüknél fogva értékes, és az Azure Data Catalog minden felhasználó a megfelelő, kifejező, amíg minden felhasználó használhatja ezt az információt megtudhatja, hogy az adatok és a cél információkat biztosítja.

## <a name="expert"></a>Szakértő

Szakértő az egy felhasználó, aki az adategység-megalapozott "szakértői" perspektívát tartalmazóként lett azonosítva. Bármely felhasználó adhat hozzá az adott eszköz számára szakértőként maguk vagy egy másik felhasználó. Szakértőként alatt felsorolt nem tartalmazza automatikusan az Azure Data Catalog; további jogosultságokra Ez lehetővé teszi a felhasználók könnyen elérhetők az adott perspektívákat, amelyek a legnagyobb valószínűséggel lehet hasznos, ha egy eszköz a leíró metaadatok áttekintése.

## <a name="owner"></a>Tulajdonos

A tulajdonos az kezeléséhez az Azure Data Catalog adategység további jogosultságokkal rendelkező felhasználóként. Felhasználók regisztrált adategységek tulajdonjogát, és az tulajdonosok, társtulajdonosok adhat hozzá más felhasználókat. További információkért lásd: [adategységek felügyelete](data-catalog-how-to-manage.md)  

> [!NOTE]
> Tulajdonosi és felügyeleti csak az a az Azure Data Catalog Standard kiadásában érhetők el.

## <a name="registration"></a>Regisztráció

Regisztráció az adatok eszköz metaadatok beolvasása egy adatforrásból, és másolja az Azure Data Catalog szolgáltatást. Regisztrált adategységeket ezután feliratozva és felderítése.

## <a name="next-steps"></a>További lépések

[Rövid útmutató: Hozzon létre egy Azure Data Catalog](data-catalog-get-started.md) 
