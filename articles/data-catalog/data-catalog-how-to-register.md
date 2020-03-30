---
title: Adatforrások regisztrálása az Azure Data Catalogban
description: Ez a cikk bemutatja, hogyan regisztrálhat adatforrásokat az Azure Data Catalogban, beleértve a regisztráció során kinyert metaadat-mezőket is.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 0c5fdac7df41fec3a6206dbd78af74b7f1b58c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736322"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Adatforrások regisztrálása az Azure Data Catalogban
## <a name="introduction"></a>Bevezetés
Az Azure Data Catalog egy teljes körűen felügyelt felhőszolgáltatás, amely a vállalati adatforrások regisztrációs és felderítési rendszereként szolgál. Más szóval a Data Catalog segítségével a személyek felderíthetik, megérthetik és használhatják az adatforrásokat, és segít a szervezeteknek abban, hogy a meglévő adataikból több értéket szerezzenek. Az adatforrás adatkatalóguson keresztüli felderíthetővé tételének első lépése az adatforrás regisztrálása.

## <a name="register-data-sources"></a>Adatforrások regisztrálása
A regisztráció az adatforrás metaadatainak kinyerése és az adatok adatmásolata a Data Catalog szolgáltatásba. Az adatok helye nem változik, ahogy az sem, hogy mely rendszergazdák felügyelik az adatokat, és milyen szabályzatok érvényesek rájuk.

Adatforrás regisztrálásához tegye a következőket:
1. Az Azure Data Catalog portálon indítsa el a Data Catalog adatforrás-regisztrációs eszközt. 
2. Jelentkezzen be munkahelyi vagy iskolai fiókjával ugyanazzal az Azure Active Directory-hitelesítő adatokkal, amelyekkel bejelentkezik a portálra.
3. Jelölje ki a regisztrálni kívánt adatforrást.

További részletes részletekért tekintse meg az [Azure Data Catalog](data-catalog-get-started.md) oktatóanyagát.

Miután regisztrálta az adatforrást, a katalógus nyomon követi a helyét, és indexeli a metaadatait. A felhasználók kereshetnek, böngészhetnek és felderíthetik az adatforrást, majd a helyével csatlakozhatnak hozzá az általuk választott alkalmazás vagy eszköz segítségével.

## <a name="supported-data-sources"></a>Támogatott adatforrások
A jelenleg támogatott adatforrások listáját a Data Catalog DSR című témakörben [tésszet.](data-catalog-dsr.md)

## <a name="structural-metadata"></a>Szerkezeti metaadatok
Adatforrás regisztrálásakor a regisztrációs eszköz kinyeri a kijelölt objektumok szerkezetére vonatkozó információkat. Ezeket az információkat szerkezeti metaadatoknak nevezzük.

Az összes objektum esetében ez a szerkezeti metaadat tartalmazza az objektum helyét, így az adatokat felderítő felhasználók felhasználhatják ezeket az információkat az általuk választott ügyféleszközökben az objektumhoz való csatlakozáshoz. Az egyéb szerkezeti metaadatok közé tartozik az objektum neve és típusa, valamint az attribútum/oszlop neve és adattípusa.

## <a name="descriptive-metadata"></a>Leíró metaadatok
Az adatforrásból kinyert alapvető szerkezeti metaadatok mellett az adatforrás regisztrációs eszközlenyeri a leíró metaadatokat. Az SQL Server Analysis Services és az SQL Server Reporting Services esetében ez a metaadat a szolgáltatások által elérhetőleírási tulajdonságokból származik. Az SQL Server esetében az\_ms description extended tulajdonsághasználatával megadott értékek kibontása történik. Az Oracle Database esetében az adatforrás-regisztrációs eszköz kinyeri\_\_a MEGJEGYZÉSEK oszlopot az ÖSSZES TAB COMMENTS nézetből.

Az adatforrásból kinyert leíró metaadatokon kívül a felhasználók az adatforrás-regisztrációs eszközzel is megadhatják a leíró metaadatokat. A felhasználók címkéket adhatnak hozzá, és azonosíthatják a regisztrált objektumok szakértőit. Ezeket a leíró metaadatokat a rendszer a data katalógus szolgáltatásba másolja a szerkezeti metaadatokkal együtt.

## <a name="include-previews"></a>Előnézetekkel együtt
Alapértelmezés szerint csak a metaadatokat nyeri ki az adatforrásokból, és másolja a Data Catalog szolgáltatásba, de az adatforrás okainak megértése gyakran egyszerűbb, ha megtekinthetjük a benne lévő adatok mintáját.

A Data Catalog adatforrás-regisztrációs eszközzel minden táblában és a regisztrált nézetben megadhat egy pillanatkép-előnézetet az adatokról. Ha úgy dönt, hogy a regisztráció során előnézeteket is felszeretne tüntetni, a regisztrációs eszköz legfeljebb 20 rekordot tartalmaz minden táblából és nézetből. Ezt a pillanatképet ezután a rendszer a szerkezeti és leíró metaadatokkal együtt másolja a katalógusba.

> [!NOTE]
> A nagy számú oszlopot tartalmazó széles táblák előnézetében 20-nál kevesebb bejegyzés szerepelhet.
>
>

## <a name="include-data-profiles"></a>Adatprofilok belefoglalása
Az előnézetek hozzáadásával is értékes környezetet biztosíthatnak azoknak a felhasználóknak, akik adatforrásokat keresnek az adatkatalógusban, beleértve az adatprofilokat is, így könnyebben megérthetik a felderített adatforrásokat.

A Data Catalog adatforrás-regisztrációs eszközzel minden táblához és regisztrált nézethez adatprofilt adhat meg. Ha úgy dönt, hogy a regisztráció során adatprofilt ad meg, a regisztrációs eszköz összesített statisztikákat tartalmaz az egyes táblázatokban és nézetekben szereplő adatokról, beleértve a következőket:

* Az objektumban lévő sorok száma és mérete.
* Az adatok és az objektumséma legutóbbi frissítésének dátuma.
* Az oszlopok null rekordjainak és eltérő értékeinek száma.
* Az oszlopok minimális, maximális, átlagos és szórási értékei.

Ezeket a statisztikákat ezután a rendszer a szerkezeti és leíró metaadatokkal együtt másolja a katalógusba.

> [!NOTE]
> A szöveg- és dátumoszlopok nem tartalmaznak átlag- vagy szórásstatisztikákat az adatprofiljukban.
>
>

## <a name="update-registrations"></a>Regisztrációk frissítése
Az adatforrás regisztrálása felderíthetővé teszi a Data Catalog-ban, ha a regisztráció során kinyert metaadatokat és választható előnézetet használja. Ha az adatforrást frissíteni kell a katalógusban (például ha egy objektum sémája megváltozott, az eredetileg kizárt táblákat is bele kell foglalni, vagy frissíteni szeretné az előnézetekben szereplő adatokat), az adatforrás-regisztrációs eszköz újra futtatható.

Egy már regisztrált adatforrás újbóli regisztrálása "upsert" egyesítési műveletet hajt végre: a meglévő objektumok frissülnek, és új objektumok jönnek létre. A felhasználók által a Data Catalog portálon keresztül biztosított metaadatok megmaradnak.

## <a name="summary"></a>Összefoglalás
Mivel szerkezeti és leíró metaadatokat másol egy adatforrásból a katalógusszolgáltatásba, az adatforrás regisztrálása a Data Catalog szolgáltatásban megkönnyíti az adatok felderítését és megértését. Miután regisztrálta az adatforrást, jegyzetelhet, kezelhet és felderítheti azt a Data Catalog portálhasználatával.

## <a name="next-steps"></a>További lépések
Az adatforrások regisztrálásáról az Azure Data Catalog oktatóanyaga az [Első lépések az Azure Data Catalog-oktatóanyagban](data-catalog-get-started.md) található.
