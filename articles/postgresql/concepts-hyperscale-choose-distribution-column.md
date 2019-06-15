---
title: Terjesztési oszlopok kiválasztása az Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió)
description: Jó lehetőségek a szokványos nagy kapacitású terjesztési oszlopok
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e9fba14b8979f739fd29bc277e32fb544221d08a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65078985"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Terjesztési oszlopok kiválasztása az Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió)

Minden tábla elosztási oszlop kiválasztása **a legfontosabb egyik** modellezési döntéseket hozhat. Nagy kapacitású sorok a sorok elosztási oszlop értéke alapján szegmenseket tárolja.

A legmegfelelőbb választás csoportokhoz kapcsolódó együtt ugyanazon fizikai csomóponton, így az adatok lekérdezése minden SQL-funkció gyors és hozzáadásának támogatása. Egy helytelen válasz teszi a rendszer lassabban futnak, és minden SQL-szolgáltatás nem támogatja a csomópontok között.

Ez a szakasz biztosító terjesztési oszlop tippek a két leggyakoribb nagy kapacitású forgatókönyvek.

### <a name="multi-tenant-apps"></a>Több-Bérlős alkalmazások

A több-bérlős architektúra egy hierarchikus adatbázis-lekérdezések szét a csomópontok a kiszolgálócsoport a modellezési formáját használja.  Az adatokat hierarchia tetején más néven a *bérlőazonosító*, és a egy oszlopban minden táblában kell tárolni.

Nagy kapacitású lekérdezi, hogy melyik Bérlőazonosító jár, és megkeresi a tábla megfelelő szegmenshez megvizsgálja. A lekérdezés, amely tartalmazza a szegmens egyetlen munkavégző csomópont irányítja. A lekérdezés futtatása ugyanazon a csomóponton elhelyezett minden vonatkozó adatokat tartalmazó a közös elhelyezés neve.

A következő ábra szemlélteti a közös elhelyezést a több-bérlős data model szolgáltatásban. Fiókok és a kampányok két táblát tartalmaz, minden egyes által terjesztett `account_id`. A szürke mezők szegmensek, amelynek színét mindegyike jelöli, mely munkavégző csomópont tartalmazza az azt jelentik. A munkavégző csomópont és a egy másik kék zöld szegmensek együtt tárolják. Figyelje meg, hogyan kampányokat és -fiókok közötti illesztési lekérdezés lenne a szükséges adatok együtt az egyik csomóponton ugyanazt a fiókot a mindkét táblázatot korlátozása során\_azonosítója.

![több-bérlős közös elhelyezés](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Ez a kialakítás a saját séma a alkalmazni, mi számít egy bérlőt az alkalmazás azonosításához. Közös része a vállalati, a fiók, a szervezet vagy az ügyfél. Az oszlop neve lesz hasonló `company_id` vagy `customer_id`. Vizsgálja meg a lekérdezéseket, és tegye fel magának: lenne, működni akkor részt vevő összes tábla korlátozhatja a sorok ugyanazt bérlőhöz azonosítójú további WHERE záradék?
A több-bérlős modellben lekérdezések hatóköre egy bérlőt, például értékesítési vagy a szoftverleltár-lekérdezéseket szeretne felvenni a hatókörbe egy adott tárolóban.

#### <a name="best-practices"></a>Ajánlott eljárások

-   **Partíció elosztott táblákról egy közös bérlő által\_azonosító oszlop.** Például a bérlők amelyeknél cég, a bérlő SaaS-alkalmazás\_azonosító valószínűleg vállalati\_azonosítója.
-   **Több-bérlős kis táblák referencia táblák típusra konvertál.** Ha több bérlő osztozik a kis táblák információk, kioszthatja referencia táblázatként.
-   **Szűrés kérdezi le az összes bérlő korlátozása\_azonosítója.** Minden egyes lekérdezés egyszerre több bérlő információkat igényeljen.

Olvassa el a [több-bérlős oktatóanyag](./tutorial-design-database-hyperscale-multi-tenant.md) példa az ilyen típusú alkalmazás létrehozását.

### <a name="real-time-apps"></a>Valós idejű alkalmazások

A több-bérlős architektúra hierarchikus és a lekérdezések útvonalának bérlőnkénti adatok közös elhelyezés használja. Valós idejű architektúrák ezzel szemben az adott terjesztésipont tulajdonságainak az adatok nagymértékben párhuzamos feldolgozási függenek.

"Entitás azonosító" kifejezés a valós idejű modellben terjesztési oszlopok, használjuk. Tipikus entitások olyan felhasználók, a gazdagépek vagy eszközök.

Valós idejű lekérdezéseket általában bekérése a numerikus összesítések dátum- és kategória szerint csoportosítva. Nagy kapacitású ezeket a lekérdezéseket küld minden egyes szegmens a részleges eredményeket, és több végső válasz a koordinátor-csomóponton. Lekérdezések futtatása leggyorsabb amikor sok csomóponton hozzájárul a lehető, és nem egyetlen csomópont kell ne aránytalanul nagy mennyiségű munka során.

#### <a name="best-practices"></a>Ajánlott eljárások

-   **Válasszon egy oszlopot a nagy számosságú elosztási oszlopot.** Összehasonlításképpen egy \"állapot\" egy rendelés táblán értékekkel "új" mező "fizetős", és "tartalmazza a szükséges" elosztási oszlop gyenge közül választhat. Azt feltételezi, hogy csak néhány értékeket, amely korlátozza, amely képes tárolni az adatokat a szegmensek száma és a csomópontok, amely képes feldolgozni. A nagy számosságú oszlopot, többek között, fontos továbbá válassza ki azokat, amelyeket gyakran használnak a group by záradékban vagy illesztési kulcsok.
-   **Válassza ki egy oszlopot az egyenletes eloszlás.** Ha egy tábla bizonyos gyakori értékek torzítja oszlop terjeszt, majd a táblában lévő adatokat fog általában öszesítés az egyes szegmensek. A szegmenseket tárolja az azokban rendelkezés csomópontok más csomópontoknál több munkát végző lesz végül.
-   **A közös oszlopokat a tény- és dimenziótáblákból terjesztése.**
    A ténytábla rendelkezhet csak egy terjesztési kulcs. Csatlakozzon egy másik kulcsot táblákhoz nem lesz a ténytáblára a közös elhelyezésű. Válassza ki egy dimenziót meg elhelyezése, milyen gyakran van csatlakoztatva, és a csatlakozó sor mérete alapján.
-   **Néhány dimenziótáblák parancssorán referencia táblák.** Ha egy táblát nem lehet a ténytáblára a közös elhelyezésű, javítja a lekérdezési teljesítményt osztja meg a dimenziótáblában a referenciatábla formájában a csomópontok minden példányát.

Olvassa el a [valós idejű irányítópult oktatóanyag](./tutorial-design-database-hyperscale-realtime.md) példa az ilyen típusú alkalmazás létrehozását.

### <a name="timeseries-data"></a>Adatok időrendben

Egy idősorozat-számítási alkalmazások lekérdezik a közelmúltbeli információkat archiválni a régi adatok.

A leggyakrabban előforduló hibát időrendben információk nagy kapacitású modellező terjesztési oszlopként történő küldés időbélyegzője legyen maga használ. Idő alapján kivonatoló terjesztést terjeszteni alkalommal látszólag véletlenszerű idő címtartományok együtt tartása szegmensekben helyett a különböző szegmensek be. Lekérdezések használata esetén általánosan idő hivatkozhat címtartományok idő (például a legfrissebb adatok), ezért az ilyen kivonatoló terjesztést vezet a hálózati terhelés.

#### <a name="best-practices"></a>Ajánlott eljárások

-   **Nem választott az elosztási oszlop egy időbélyegző.** Válasszon egy másik oszlopot. Egy több-bérlős alkalmazásban használja a bérlő Azonosítóját vagy a valós idejű app az entitás azonosítója.
-   **Használja a PostgreSQL tábla particionálásával alkalommal helyette.** Tábla particionálásával használatával egy nagy méretű tábla idő rendezett adatok felosztása több örökölt tábla az egyes különböző időtartományok tartalmazó.  Az örökölt táblák szegmensek terjesztése egy nagy kapacitású Postgres particionált táblára hoz létre.

Olvassa el a [időrendben oktatóanyag](https://aka.ms/hyperscale-tutorial-timeseries) példa az ilyen típusú alkalmazás létrehozását.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [közös elhelyezés](concepts-hyperscale-colocation.md) között elosztott adatok segítséget nyújt a lekérdezések gyorsabban futnak
