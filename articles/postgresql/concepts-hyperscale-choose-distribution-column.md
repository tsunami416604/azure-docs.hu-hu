---
title: Terjesztési oszlopok kiválasztása – Nagykapacitású (Citus) – Azure Database for PostgreSQL
description: Megtudhatja, hogyan választhat disztribúciós oszlopokat a közös nagy kapacitású forgatókönyvekben az Azure Database for PostgreSQL-ben.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 8ced9767d81affceef851820ee587f4f3dd24deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975669"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Terjesztési oszlopok kiválasztása az Azure Database for PostgreSQL – Hyperscale (Citus) szolgáltatásban

Az egyes táblázatok terjesztési oszlopainak kiválasztása az egyik legfontosabb modellezési döntés, amelyet meg hoz. Azure Database for PostgreSQL – Hyperscale (Citus) a sorokat a sorok terjesztési oszlopának értéke alapján tárolja.

A helyes adatválaszték csoportosítja a kapcsolódó adatokat ugyanazon a fizikai csomóponton, ami gyorssá teszi a lekérdezéseket, és támogatja az összes SQL-szolgáltatást. A helytelen választás miatt a rendszer lassan fut, és nem támogatja az összes SQL-szolgáltatást a csomópontokközött.

Ez a cikk terjesztési oszlop tippeket ad a két leggyakoribb nagy méretű (Citus) forgatókönyvekhez.

### <a name="multi-tenant-apps"></a>Több-bérlős alkalmazások

A több-bérlős architektúra hierarchikus adatbázis-modellezés iformáját használja a lekérdezések kiszolgálócsoport csomópontjai közötti elosztásához. Az adathierarchia tetején a *bérlői azonosító,* és kell tárolni egy oszlopban minden táblában.

A nagy kapacitású (Citus) megvizsgálja a lekérdezéseket, hogy melyik bérlőazonosítót vonják be, és megkeresi a megfelelő táblaszegmenst. A lekérdezést egy olyan munkavégző csomóponthoz irányítja, amely a szegmenst tartalmazza. A lekérdezés futtatása az összes releváns adatokat helyezett ugyanazon a csomóponton nevezzük közös elhelyezés.

Az alábbi ábra a több-bérlős adatmodell helymegosztását mutatja be. Két táblát tartalmaz, a fiókokat és `account_id`a kampányokat, amelyeket a . Az árnyékolt mezők szilánkokat jelölnek. A zöld szilánkok együtt vannak tárolva egy munkavégző csomóponton, a kék szilánkok pedig egy másik feldolgozócsomóponton. Figyelje meg, hogy a Fiókok és kampányok közötti illesztési lekérdezés hogyan rendelkezik\_az összes szükséges adatkal egy csomóponton, ha mindkét tábla ugyanarra a fiókazonosítóra korlátozódik.

![Több-bérlős helymegosztás](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Ha ezt a tervet a saját sémájában szeretné alkalmazni, azonosítsa, hogy mi minősül bérlőnek az alkalmazásban. A gyakori példányok közé tartozik a vállalat, a fiók, a szervezet vagy az ügyfél. Az oszlop neve lesz `company_id` `customer_id`valami ilyesmi, vagy . Vizsgálja meg az egyes lekérdezések, és kérdezd meg magadtól, működne, ha már további WHERE záradékok, hogy korlátozza az összes érintett táblák sorok azonos bérlői azonosító?
A több-bérlős modell lekérdezések hatóköre egy bérlő. Például az értékesítésre vagy a készletre vonatkozó lekérdezések hatóköre egy adott üzleten belül van.

#### <a name="best-practices"></a>Ajánlott eljárások

-   **Megosztott táblák particionálása egy közös bérlői\_azonosító oszlop által.** Például egy SaaS-alkalmazásban, ahol a bérlők\_vállalatok, a bérlői\_azonosító valószínűleg a vállalati azonosító.
-   **Kis bérlőközi táblák átalakítása hivatkozási táblákká.** Ha több bérlő osztozik egy kis adattáblán, ossza el referenciatáblaként.
-   **Korlátozza az összes alkalmazáslekérdezés\_bérlői azonosító szerinti szűrését.** Minden lekérdezésnek egyszerre egy bérlőhöz kell információt kérnie.

Olvassa el a [több-bérlős oktatóanyag](./tutorial-design-database-hyperscale-multi-tenant.md) egy példát, hogyan hozhat létre az ilyen típusú alkalmazás.

### <a name="real-time-apps"></a>Valós idejű alkalmazások

A több-bérlős architektúra hierarchikus struktúrát vezet be, és az adatok együttelhelyezését használja a lekérdezések bérlőnkénti irányításához. Ezzel szemben a valós idejű architektúrák az adataik meghatározott terjesztési tulajdonságaitól függenek a rendkívül párhuzamos feldolgozás elérése érdekében.

Az "entitásazonosítót" használjuk a valós idejű modell terjesztési oszlopainak kifejezéseként. A tipikus entitások a felhasználók, a gazdagépek vagy az eszközök.

A valós idejű lekérdezések általában dátum vagy kategória szerint csoportosított numerikus aggregátumokat kérnek. A nagy kapacitású (Citus) elküldi ezeket a lekérdezéseket az egyes szegmensek részleges eredményeket, és összeállítja a végső választ a koordinátor csomóponton. A lekérdezések akkor futnak a leggyorsabban, ha a lehető legtöbb csomópont járul hozzá, és ha egyetlen csomópontnak sem kell aránytalanul sok munkát végeznie.

#### <a name="best-practices"></a>Ajánlott eljárások

-   **Válasszon egy nagy számosságú oszlopot terjesztési oszlopként.** Összehasonlításképpen: az Új, Fizetett és Szállított értékekkel rendelkező rendeléstábla Állapot mezője nem megfelelő a felosztási oszlop kiválasztása. Azt feltételezi, csak a néhány érték, amely korlátozza az adatokat tartalmazó szilánkok számát, és a csomópontok száma, amely feltudja dolgozni. A nagy számosságú oszlopok közül is jó, ha azokat az oszlopokat választja, amelyeket gyakran használnak a csoportonkénti záradékokban vagy illesztési kulcsokként.
-   **Válasszon egy egyenletes eloszlású oszlopot.** Ha egy táblázatot egy oszlopferde bizonyos közös értékekre, a táblázatban lévő adatok általában felhalmozódnak bizonyos szegmensekben. Azok a csomópontok, amelyek ezeket a szilánkokat tartják, végül több munkát végeznek, mint más csomópontok.
-   **Tény- és dimenziótáblák elosztása a közös oszlopokon.**
    A ténytábla csak egy terjesztési kulccsal rendelkezhet. A másik kulcshoz csatlakozó táblák nem lesznek a ténytáblával együtt elhelyezve. Válasszon ki egy dimenziót az összeillesztéshez az összeillesztés és az illesztési sorok mérete alapján.
-   **Néhány dimenziótábla módosítása referenciatáblákká.** Ha egy dimenziótábla nem helyezhető el a ténytáblával, javíthatja a lekérdezési teljesítményt, ha a dimenziótábla példányait egy referenciatábla formájában az összes csomópontnak elosztja.

Olvassa el a [valós idejű irányítópult-oktatóanyagot](./tutorial-design-database-hyperscale-realtime.md) egy példa az ilyen típusú alkalmazások létrehozásához.

### <a name="time-series-data"></a>Idő-sorozat adatai

Az idősorozat-munkaterhelésben az alkalmazások lekérdezik a legutóbbi információkat, miközben régi adatokat archiválnak.

A hyperscale (Citus) idősorozat-információk modellezésének leggyakoribb hibája, hogy magát az időbélyeget használja terjesztési oszlopként. Az idő alapú kivonateloszlás az időpontokat véletlenszerűen különböző szegmensekbe osztja el, ahelyett, hogy az időtartományokat szegmensekben tartaná. Lekérdezések, amelyek időáltalános hivatkozási időtartományok, például a legfrissebb adatok. Az ilyen típusú kivonat-eloszlás hálózati terheléshez vezet.

#### <a name="best-practices"></a>Ajánlott eljárások

-   **Ne válasszon időbélyeget terjesztési oszlopként.** Válasszon másik terjesztési oszlopot. Egy több-bérlős alkalmazásban használja a bérlői azonosítót, vagy egy valós idejű alkalmazásban használja az entitásazonosítót.
-   **Használja postgreSQL tábla particionálás az idő helyett.** A táblaparticionálás sal az időrendelt adatok nagy tábláját több örökölt táblára bonthatja, amelyek mindegyike különböző időtartományokat tartalmaz. Postgres-particionált tábla terjesztése a nagy kapacitású (Citus) az örökölt táblák hoz létre szilánkok.

Olvassa el az [idősorozat-oktatóanyagot](https://aka.ms/hyperscale-tutorial-timeseries) az ilyen típusú alkalmazások létrehozásának példájával.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogy az elosztott adatok közötti [közös elhelyezés](concepts-hyperscale-colocation.md) hogyan segíti a lekérdezések gyors futását.
