---
title: 'Azure AD Connect szinkronizálás: Az architektúra ismertetése – Azure'
description: Ez a témakör ismerteti az Azure AD Connect szinkronizálásának architektúráját, és ismerteti a használt kifejezéseket.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fac0f9143918d3f273812e53abfb88d6a56f7a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261618"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD Connect szinkronizálás: Az architektúra ismertetése
Ez a témakör az Azure AD Connect-szinkronizálás alapvető architektúráját ismerteti. Sok szempontból hasonló elődeihez, a MIIS 2003-hoz, az ILM 2007-hez és a FIM 2010-hez. Az Azure AD Connect szinkronizálása ezeknek a technológiáknak a fejlődése. Ha ismeri ezeket a korábbi technológiákat, a téma tartalma is ismerős lesz Önnek. Ha még nem ért hozzá a szinkronizálás, akkor ez a témakör az Ön számára. Azonban nem követelmény, hogy ismerje a részleteket ebben a témakörben, hogy sikeresen az Azure AD Connect szinkronizálás (úgynevezett szinkronizálási motor ebben a témakörben).

## <a name="architecture"></a>Architektúra
A szinkronizálási motor integrált nézetet hoz létre a több csatlakoztatott adatforrásban tárolt objektumokról, és kezeli az ilyen adatforrásokban lévő identitásadatokat. Ezt az integrált nézetet a csatlakoztatott adatforrásokból beolvasott identitásadatok és az adatok feldolgozásának módját meghatározó szabályok határozzák meg.

### <a name="connected-data-sources-and-connectors"></a>Csatlakoztatott adatforrások és összekötők
A szinkronizálási motor különböző adattárakból, például az Active Directoryból vagy egy SQL Server-adatbázisból származó identitásadatokat dolgozza fel. Minden olyan adattár, amely adatbázis-szerű formátumban rendezi az adatait, és amely szabványos adatelérési módszereket biztosít, a szinkronizálási motor potenciális adatforrás-jelöltje. A szinkronizálási motor által szinkronizált adattárak neve **csatlakoztatott adatforrások** vagy **csatlakoztatott könyvtárak** (CD).

A szinkronizálási motor magában foglalja az **összekötő**nevű modulon belüli csatlakoztatott adatforrással való interakciót. A csatlakoztatott adatforrások minden típusa rendelkezik egy adott összekötővel. Az összekötő egy szükséges műveletet a csatlakoztatott adatforrás által ismert formátumba fordít.

Az összekötők API-hívásokat kezdeményeznek, hogy identitásadatokat cseréljenek (olvasási és írási) egy csatlakoztatott adatforrással. Egyéni összekötő hozzáadása bővíthető kapcsolódási keretrendszer használatával is lehetséges. A következő ábra bemutatja, hogyan csatlakozik egy összekötő egy csatlakoztatott adatforrást a szinkronizálási motorhoz.

![Ív1](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Az adatok mindkét irányban folyhatnak, de nem folyhatnak egyszerre mindkét irányban. Más szóval az összekötő beállítható úgy, hogy lehetővé tegye az adatok áramlását a csatlakoztatott adatforrásból a szinkronizálási motorba vagy a szinkronizálási motorból a csatlakoztatott adatforrásba, de csak egy ilyen művelet történhet egyszerre egy objektum és attribútum esetében. Az irány eltérő lehet a különböző objektumok és a különböző attribútumok.

Összekötő konfigurálásához adja meg a szinkronizálni kívánt objektumtípusokat. Az objektumtípusok megadása határozza meg a szinkronizálási folyamatrészét képező objektumok hatókörét. A következő lépés a szinkronizálandó attribútumok kiválasztása, amely attribútumfelsorolási lista néven ismert. Ezek a beállítások bármikor módosíthatók az üzleti szabályok változásaira reagálva. Az Azure AD Connect telepítővarázsló használatakor ezek a beállítások konfigurálva vannak.

Ha objektumokat szeretne egy csatlakoztatott adatforrásba exportálni, az attribútumfelvételi listának legalább azokat a minimális attribútumokat kell tartalmaznia, amelyek egy csatlakoztatott adatforrás adott objektumtípus létrehozásához szükségesek. Például az **sAMAccountName** attribútumnak szerepelnie kell az attribútumfelvétel listájában ahhoz, hogy egy felhasználói objektumot exportáljon az Active Directoryba, mert az Active Directory összes felhasználói objektumának meg kell adni egy **sAMAccountName** attribútumot. A telepítővarázsló ismét elvégzi ezt a konfigurációt.

Ha a csatlakoztatott adatforrás szerkezeti összetevőket, például partíciókat vagy tárolókat használ az objektumok rendszerezéséhez, korlátozhatja a csatlakoztatott adatforrás adott megoldáshoz használt területeit.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>A szinkronizálási motor névterének belső szerkezete
A teljes szinkronizálási motor névtere két névtérből áll, amelyek az identitásadatokat tárolják. A két névtér a következő:

* A csatlakozó tér (CS)
* A metaverzum (MV)

Az **összekötőterület** egy átmeneti terület, amely a csatlakoztatott adatforrásból származó kijelölt objektumok és az attribútumfelvételi listában megadott attribútumok ábrázolását tartalmazza. A szinkronizálási motor az összekötő térben határozza meg, mi változott a csatlakoztatott adatforrás, és a színpadon a bejövő változásokat. A szinkronizálási motor is használja az összekötő helyet a kimenő módosításokat a csatlakoztatott adatforrásba való exportáláshoz. A szinkronizálási motor egy külön összekötő helyet tart fenn az egyes összekötők átmeneti területeként.

Átmeneti terület használatával a szinkronizálási motor független marad a csatlakoztatott adatforrásoktól, és nem befolyásolja azok rendelkezésre állását és hozzáférhetőségét. Ennek eredményeképpen az identitásadatokat bármikor feldolgozhatja az átmeneti területen lévő adatok használatával. A szinkronizálási motor csak a csatlakoztatott adatforráson belül végrehajtott módosításokat kérheti, mivel az utolsó kommunikációs munkamenet megszakadt, vagy csak azokat a módosításokat tudja kinyomni, amelyeket a csatlakoztatott adatforrás még nem kapott meg, ami csökkenti a hálózatot a szinkronizálási motor és a csatlakoztatott adatforrás közötti forgalmat.

Ezenkívül a szinkronizálási motor az összekötő térben fázisban lévő összes objektum állapotadatait tárolja. Amikor új adatok érkeznek, a szinkronizálási motor mindig kiértékeli, hogy az adatok már szinkronizálva vannak-e.

A **metaverzum** egy olyan tárolóterület, amely több csatlakoztatott adatforrás összesített identitásadatait tartalmazza, és az összes egyesített objektum egyetlen globális, integrált nézetét biztosítja. A metaverzum-objektumok a csatlakoztatott adatforrásokból beolvasott identitásadatok és a szinkronizálási folyamat testreszabását lehetővé tévő szabályok alapján jönnek létre.

A következő ábra az összekötő tér névterét és a metaverzum-névteret mutatja a szinkronizálási motoron belül.

![Ív2](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Motoridentitás-objektumok szinkronizálása
A szinkronizálási motorban lévő objektumok a csatlakoztatott adatforrás vagy a szinkronizálási motor ezen objektumok integrált nézetének ábrázolásai. Minden szinkronizálási motorobjektumnak globálisan egyedi azonosítóval (GUID) kell rendelkeznie. A GUID-ok biztosítják az adatok integritását és az objektumok közötti kapcsolatok kifejezését.

### <a name="connector-space-objects"></a>Összekötő térobjektumai
Amikor a szinkronizálási motor kommunikál egy csatlakoztatott adatforrással, beolvassa a csatlakoztatott adatforrás identitásadatait, és ezeket az adatokat az összekötő térben lévő identitásobjektum ábrázolásához használja fel. Ezeket az objektumokat nem lehet külön-külön létrehozni vagy törölni. Az összekötőtérben lévő összes objektumot azonban manuálisan is törölheti.

Az összekötő térben lévő összes objektumnak két tulajdonsága van:

* Globálisan egyedi azonosító (GUID)
* Megkülönböztető név (más néven DN)

Ha a csatlakoztatott adatforrás egyedi attribútumot rendel az objektumhoz, akkor az összekötő térben lévő objektumok nak horgonyattribútumuk is lehet. A horgonyattribútum egyedileg azonosítja az objektumot a csatlakoztatott adatforrásban. A szinkronizálási motor a horgony segítségével keresi meg az objektum megfelelő ábrázolását a csatlakoztatott adatforrásban. A szinkronizálási motor feltételezi, hogy egy objektum horgonya soha nem változik az objektum élettartama alatt.

Az összekötők közül sok egy ismert egyedi azonosítót használ, hogy importáláskor automatikusan létrehozzon egy horgonyt az egyes objektumokhoz. Az Active Directory-összekötő például egy **horgony objectGUID** attribútumát használja. Olyan csatlakoztatott adatforrások esetén, amelyek nem biztosítanak egyértelműen meghatározott egyedi azonosítót, az összekötő konfiguráció részeként megadhatja a horgony generálását.

Ebben az esetben a horgony egy objektumtípus egy vagy több egyedi attribútumából épül fel, amelyek közül egyik sem változik, és amely egyedileg azonosítja az objektumot az összekötő térben (például egy alkalmazott számát vagy egy felhasználói azonosítót).

Az összekötő térobjektum a következők egyike lehet:

* Átmeneti objektum
* Helyőrző

### <a name="staging-objects"></a>Átmeneti objektumok
Az átmeneti objektum a csatlakoztatott adatforrásból kijelölt objektumtípusok egy példányát jelöli. A GUID azonosító és a megkülönböztető név mellett az átmeneti objektum nak mindig van egy értéke, amely az objektum típusát jelzi.

Az importált átmeneti objektumok mindig rendelkeznek értékkel a horgony attribútumhoz. A szinkronizálási motor által újonnan kiépített és a csatlakoztatott adatforrásban létrehozás alatt álló átmeneti objektumok nem rendelkeznek a horgonyattribútum értékével.

Az átmeneti objektumok az üzleti attribútumok aktuális értékeit és a szinkronizálási motor által a szinkronizálási folyamat végrehajtásához szükséges működési információkat is hordozzák. A működési információk közé tartoznak azok a jelzők, amelyek jelzik az átmeneti objektumon előkészített frissítések típusát. Ha egy átmeneti objektum új identitásadatokat kapott a csatlakoztatott adatforrástól, amely még nem lett feldolgozva, az objektum **függőben lévő importálásként**lesz megjelölve. Ha egy átmeneti objektum olyan új identitásadatokkal rendelkezik, amelyeket még nem exportáltak a csatlakoztatott adatforrásba, akkor **függőben lévő exportálásként**lesz megjelölve.

Az átmeneti objektum lehet importálási objektum vagy exportobjektum. A szinkronizálási motor a csatlakoztatott adatforrásból kapott objektumadatok felhasználásával hoz létre importálási objektumot. Amikor a szinkronizálási motor információt kap egy olyan új objektum létezéséről, amely megfelel az Összekötőben kiválasztott objektumtípusok egyikének, létrehoz egy importálási objektumot az összekötő térben az objektum ábrázolásaként a csatlakoztatott adatforrásban.

A következő ábrán egy importobjektum látható, amely egy objektumot jelöl a csatlakoztatott adatforrásban.

![Ív3](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

A szinkronizálási motor a metaverzumban lévő objektumadatok használatával hoz létre egy exportálási objektumot. Az exportálási objektumok a következő kommunikációs munkamenet során kerülnek exportálásra a csatlakoztatott adatforrásba. A szinkronizálási motor szempontjából az exportálási objektumok még nem léteznek a csatlakoztatott adatforrásban. Ezért az exportobjektum horgonyattribútuma nem érhető el. Miután megkapta az objektumot a szinkronizálási motorból, a csatlakoztatott adatforrás egyedi értéket hoz létre az objektum horgonyattribútumához.

A következő ábra bemutatja, hogyan jön létre egy exportálási objektum a metaverzumidentitás-adatok használatával.

![Ív4](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

A szinkronizálási motor az objektum exportálását a csatlakoztatott adatforrásból történő újraimportálással erősíti meg. Az exportálási objektumok importálási objektumokká válnak, amikor a szinkronizálási motor a következő importálás során megkapja őket a csatlakoztatott adatforrásból.

### <a name="placeholders"></a>Helyőrzők
A szinkronizálási motor egy sima névteret használ az objektumok tárolására. Egyes csatlakoztatott adatforrások, például az Active Directory azonban hierarchikus névteret használnak. Ha hierarchikus névtérből származó adatokat egysíkú névtérré szeretne alakítani, a szinkronizálási motor helyőrzők segítségével őrzi meg a hierarchiát.

Minden helyőrző egy objektum hierarchikus nevének egy olyan összetevőjét (például szervezeti egységét) jelöli, amelyet nem importáltak a szinkronizálási motorba, de a hierarchikus név létrehozásához szükséges. Kitöltik a csatlakoztatott adatforrásban olyan objektumokra való hivatkozások által létrehozott hézagokat, amelyek nem átmeneti objektumok az összekötő térben.

A szinkronizálási motor helyőrzőket is használ a még nem importált hivatkozott objektumok tárolására. Ha például a szinkronizálás úgy van beállítva, hogy az *Abbie Spencer* objektum kezelői attribútumát tartalmazza, és a fogadott érték olyan objektum, amely még nem lett importálva, például *CN=Lee Sperry,CN=Users,DC=fabrikam,DC=com,* a kezelő adatok helyőrzőként tárolódnak az összekötő térben. Ha a kezelő objektumot később importálja, a helyőrző objektumot felülírja a vezetőt képviselő átmeneti objektum.

### <a name="metaverse-objects"></a>Metaverzum objektumok
A metaverzum-objektum azt az összesített nézetet tartalmazza, amelya szinkronizálási motorral rendelkezik az összekötő térben lévő átmeneti objektumokból. A Szinkronizálás motor metaverzum-objektumokat hoz létre az importálási objektumokban lévő információk felhasználásával. Több összekötőtér-objektum csatolható egyetlen metaverzum-objektumhoz, de egy összekötő térobjektum nem csatolható egynél több metaverzum-objektumhoz.

A metaverzum objektumok nem hozhatók létre és nem törölhetők manuálisan. A szinkronizálási motor automatikusan törli azokat a metaverzum-objektumokat, amelyek nem rendelkeznek hivatkozással az összekötő térben lévő összekötő térobjektumhoz.

Ha egy csatlakoztatott adatforráson belül lévő objektumokat a metaverzumon belül egy megfelelő objektumtípushoz szeretné hozzájuk hozzá, a szinkronizálási motor egy előre definiált objektumtípus-készlettel és a kapcsolódó attribútumokkal rendelkező bővíthető sémát biztosít. Új objektumtípusokat és attribútumokat hozhat létre a metaverzum-objektumokhoz. Az attribútumok lehetnek egyértékűek vagy többértékűek, és az attribútumtípusok lehetnek karakterláncok, hivatkozások, számok és logikai értékek.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Átmeneti objektumok és metaverzum-objektumok közötti kapcsolatok
A szinkronizálási motor névterében az adatfolyamot az átmeneti objektumok és a metaverzum-objektumok közötti kapcsolat engedélyezi. A metaverzum-objektumhoz csatolt átmeneti objektumot **illesztett objektumnak** (vagy **összekötő objektumnak) nevezzük.** A metaverzum-objektumhoz nem csatolt átmeneti objektumot **leválasztott objektumnak** (vagy **leválasztó objektumnak)** nevezzük. Az összekapcsolt és leválasztott kifejezések nem tévesztendők össze az adatok csatlakoztatott könyvtárból történő importálásáért és exportálásáért felelős összekötőkkel.

A helyőrzők soha nem kapcsolódnak metaverzum-objektumhoz

Az egyesített objektum egy átmeneti objektumból és egyetlen metaverzum-objektumhoz való kapcsolódó kapcsolatból áll. Az illesztett objektumok az összekötő térobjektum és a metaverzum-objektum attribútumértékeinek szinkronizálására szolgálnak.

Amikor egy átmeneti objektum a szinkronizálás során egyesített objektummá válik, az attribútumok az átmeneti objektum és a metaverzum-objektum között folyhatnak. Az attribútumfolyamat kétirányú, és importálási attribútumszabályok és exportálási attribútumszabályok használatával van konfigurálva.

Egyetlen összekötő térobjektum csak egy metaverzum-objektumhoz csatolható. Az egyes metaverzum-objektumok azonban több összekötő térobjektumhoz is csatolhatók ugyanabban vagy különböző összekötőhelyeken, ahogy az az alábbi ábrán is látható.

![Ív5](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Az átmeneti objektum és a metaverzum-objektum közötti csatolt kapcsolat állandó, és csak a megadott szabályokkal távolítható el.

A leválasztott objektum olyan átmeneti objektum, amely nem kapcsolódik semmilyen metaverzum-objektumhoz. A leválasztott objektum attribútumértékeit a program nem dolgozza fel tovább a metaverzumon belül. A csatlakoztatott adatforrásmegfelelő objektum attribútumértékeit a szinkronizálási motor nem frissíti.

A leválasztatlan objektumok használatával az identitásadatokat a szinkronizálási motorban tárolhatja, és később feldolgozhatja. Az átmeneti objektum leválasztott objektumként való tartása az összekötő térben számos előnnyel jár. Mivel a rendszer már előkészítette az objektumra vonatkozó szükséges információkat, nem szükséges újra létrehozni az objektum ábrázolását a csatlakoztatott adatforrásból történő következő importálás során. Így a szinkronizálási motor mindig teljes pillanatképet ad a csatlakoztatott adatforrásról, még akkor is, ha nincs aktuális kapcsolat a csatlakoztatott adatforrással. A leválasztott objektumok a megadott szabályoktól függően illesztett objektumokká alakíthatók, és fordítva.

Az importálási objektum leválasztott objektumként jön létre. Az exportálási objektumnak illesztett objektumnak kell lennie. A rendszerlogika kényszeríti ezt a szabályt, és minden olyan exportálási objektumot töröl, amely nem illesztett objektum.

## <a name="sync-engine-identity-management-process"></a>Szinkronizáló motoridentitás-kezelési folyamat
Az identitáskezelési folyamat határozza meg, hogyan frissüljenek az identitásadatok a különböző csatlakoztatott adatforrások között. Az identitáskezelés három folyamatban történik:

* Importálás
* Szinkronizálás
* Exportálás

Az importálási folyamat során a szinkronizálási motor kiértékeli a csatlakoztatott adatforrásból érkező identitásadatokat. A rendszer módosításokat észlel, vagy új átmeneti objektumokat hoz létre, vagy frissíti a meglévő átmeneti objektumokat az összekötő térben a szinkronizáláshoz.

A szinkronizálási folyamat során a szinkronizálási motor frissíti a metaverzumot, hogy tükrözze az összekötő térben bekövetkezett változásokat, és frissíti az összekötő területet, hogy tükrözze a metaverzumban bekövetkezett változásokat.

Az exportálási folyamat során a szinkronizálási motor kitolja az átmeneti objektumokon előkészített és függőben lévő exportálásként megjelölt módosításokat.

A következő ábra azt mutatja be, hogy az egyes folyamatok hol fordulnak elő, amikor az identitásadatok egyik csatlakoztatott adatforrásból a másikba áramlanak.

![Ív6](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Importálási folyamat
Az importálási folyamat során a szinkronizálási motor kiértékeli az identitásadatok frissítéseit. A szinkronizálási motor összehasonlítja a csatlakoztatott adatforrásból kapott identitásadatokat az átmeneti objektum identitásadataival, és meghatározza, hogy az átmeneti objektumnak szüksége van-e frissítésekre. Ha az átmeneti objektumot új adatokkal kell frissíteni, az átmeneti objektum függőimportként lesz megjelölve.

Az összekötő térben a szinkronizálás előtt az objektumok előkészítése, a szinkronizálási motor csak a megváltozott identitásadatokat tudja feldolgozni. Ez a folyamat a következő előnyöket biztosítja:

* **Hatékony szinkronizálás**. A szinkronizálás során feldolgozott adatok mennyisége minimálisra csökken.
* **Hatékony újraszinkronizálás**. Módosíthatja, hogy a szinkronizálási motor hogyan dolgozza fel az identitásadatokat anélkül, hogy újra csatlakoztatna a szinkronizálási motort az adatforráshoz.
* **Lehetőség a szinkronizálás előnézetének megtekintésére.** Megtekintheti a szinkronizálás előnézetét, és ellenőrizheti, hogy az identitáskezelési folyamattal kapcsolatos feltételezések helyesek-e.

Az összekötőben megadott minden objektum esetében a szinkronizálási motor először megpróbálja megtalálni az objektum ábrázolását az összekötő összekötő területén. A szinkronizálási motor megvizsgálja az összekötő térben lévő összes átmeneti objektumot, és megpróbálja megtalálni a megfelelő horgonyattribútummal rendelkező megfelelő átmeneti objektumot. Ha egyetlen meglévő átmeneti objektum sem rendelkezik megfelelő horgonyattribútummal, a szinkronizálási motor megpróbál egy megfelelő, azonos megkülönböztető nevű átmeneti objektumot keresni.

Ha a szinkronizálási motor olyan átmeneti objektumot talál, amely megkülönböztetett névvel megegyezik, de nem horgonyalapján, a következő speciális viselkedés lép fel:

* Ha az összekötő térben található objektumnak nincs horgonya, akkor a szinkronizálási motor eltávolítja ezt az objektumot az összekötő térből, és a **következő szinkronizálási futtatáskor újralétesítésként**jelöli meg azt a metaverzum-objektumot, amelyhez kapcsolódik. Ezután létrehozza az új importálási objektumot.
* Ha az összekötő térben található objektumnak van horgonya, akkor a szinkronizálási motor feltételezi, hogy az objektumot átnevezték vagy törölték a csatlakoztatott könyvtárban. Ideiglenes, új megkülönböztető nevet rendel az összekötő térobjektumhoz, hogy a bejövő objektumot megrendezhesse. A régi objektum ezután **átmenetivé**válik, és várja, hogy az összekötő importálja az átnevezést vagy a törlést a helyzet megoldásához.

Ha a szinkronizálási motor olyan átmeneti objektumot talál, amely megfelel az összekötőben megadott objektumnak, meghatározza, hogy milyen módosításokat kell alkalmazni. Előfordulhat például, hogy a szinkronizálási motor átnevezi vagy törli az objektumot a csatlakoztatott adatforrásban, vagy csak az objektum attribútumértékeit frissíti.

A frissített adatokat megtekintő átmeneti objektumok függő importálásként vannak megjelölve. Különböző típusú függőben lévő behozatalok állnak rendelkezésre. Az importálási folyamat eredményétől függően az összekötő térben lévő átmeneti objektum nak az alábbi függőben lévő importálási típusai vannak:

* **Nem, nem.** Az átmeneti objektum egyik attribútuma sem érhető el. A szinkronizálási motor nem jelöli meg ezt a típust függő importálásként.
* Adja hozzá a **bővítményt.** Az átmeneti objektum egy új importálási objektum az összekötő térben. A szinkronizálási motor ezt a típust függőimportként jelzi a metaverzumban történő további feldolgozáshoz.
* **Frissítés .** A szinkronizálási motor megtalálja a megfelelő átmeneti objektumot az összekötő térben, és ezt a típust függő importálásként jelzi, hogy az attribútumok frissítései feldolgozhatók legyenek a metaverzumban. A frissítések közé tartozik az objektum átnevezése.
* **Törölje**a gombot. A szinkronizálási motor megtalálja a megfelelő átmeneti objektumot az összekötő térben, és ezt a típust függő importálásként jelzi, hogy az egyesített objektum törölhető legyen.
* **Törlés/hozzáadás.** A szinkronizálási motor megtalálja a megfelelő átmeneti objektumot az összekötő térben, de az objektumtípusok nem egyeznek. Ebben az esetben a törlés-add módosítás van előkészítve. A delete-add módosítás azt jelzi a szinkronizálási motorszámára, hogy az objektum teljes újraszinkronizálásának meg kell történnie, mert az objektum típusa változásakor különböző szabálykészletek vonatkoznak az objektumra.

Az átmeneti objektum függőben lévő importálási állapotának beállításával jelentősen csökkenthető a szinkronizálás során feldolgozott adatok mennyisége, mivel ez lehetővé teszi, hogy a rendszer csak azokat az objektumokat dolgozza fel, amelyek frissített adatokkal rendelkeznek.

### <a name="synchronization-process"></a>Szinkronizálási folyamat
A szinkronizálás két kapcsolódó folyamatból áll:

* Bejövő szinkronizálás, amikor a metaverzum tartalma frissül az összekötő térben lévő adatok használatával.
* Kimenő szinkronizálás, amikor az összekötő tér tartalma a metaverzumban lévő adatok használatával frissül.

Az összekötő térben előkészített információk használatával a bejövő szinkronizálási folyamat a metaverzumban létrehozza a csatlakoztatott adatforrásokban tárolt adatok integrált nézetét. Vagy az összes átmeneti objektum, vagy csak a függőben lévő importálási adatokkal rendelkezőobjektumok összesítve vannak, attól függően, hogy a szabályok hogyan vannak konfigurálva.

A kimenő szinkronizálási folyamat frissíti az objektumok exportálását, amikor a metaverzum objektumok megváltoznak.

A bejövő szinkronizálás létrehozza az integrált nézetet a csatlakoztatott adatforrásokból kapott identitásadatok metaverzumában. A szinkronizálási motor bármikor feltudja dolgozni az identitásadatokat a csatlakoztatott adatforrásból származó legfrissebb identitásadatok használatával.

**Bejövő szinkronizálás**

A bejövő szinkronizálás a következő folyamatokat tartalmazza:

* **Provision** (más néven **Vetítés,** ha fontos megkülönböztetni ezt a folyamatot a kimenő szinkronizálás kiépítése). A Szinkronizálás motor új metaverzum-objektumot hoz létre egy átmeneti objektum alapján, és összekapcsolja őket. A kiépítés objektumszintű művelet.
* **Csatlakozzon**. A Szinkronizálási motor egy átmeneti objektumot egy meglévő metaverzum-objektumhoz kapcsol. Az illesztés objektumszintű művelet.
* **Attribútumfolyamat importálása**. A szinkronizálási motor frissíti a metaverzumban lévő objektum attribútumértékeit, az úgynevezett attribútumfolyamot. Az importattribútum-folyamat olyan attribútumszintű művelet, amelyhez egy átmeneti objektum és egy metaverzum-objektum közötti kapcsolat ra van szükség.

A kiépítés az egyetlen olyan folyamat, amely objektumokat hoz létre a metaverzumban. A kiépítés csak a lepárolt objektumokimportálására van hatással. A kiépítés során a szinkronizálási motor létrehoz egy metaverzum-objektumot, amely megfelel az importálási objektum objektumtípusának, és kapcsolatot hoz létre a két objektum között, így létrehozva egy egyesített objektumot.

Az illesztési folyamat kapcsolatot hoz létre az importálási objektumok és a metaverzum-objektum között is. Az illesztés és a leépítés közötti különbség az, hogy az illesztési folyamat megköveteli, hogy az importálási objektum egy meglévő metaverzum-objektumhoz kapcsolódjon, ahol a létesítési folyamat új metaverzum-objektumot hoz létre.

A szinkronizálási motor a szinkronizálási szabály konfigurációjában megadott feltételek kel próbál importobjektumot egy metaverzum-objektumhoz illeszteni.

A folyamatüzembe építés és a csatlakoztatás során a szinkronizálási motor egy leválasztott objektumot egy metaverzum-objektumhoz kapcsol, így azok összekapcsolódnak. Az objektumszintű műveletek befejezése után a szinkronizálási motor frissítheti a társított metaverzum-objektum attribútumértékeit. Ezt a folyamatot importattribútum-folyamatnak nevezzük.

Az import attribútumfolyamat minden olyan importobjektumon megtörténik, amely új adatokat tartalmaz, és metaverzum-objektumhoz van csatolva.

**Kimenő szinkronizálás**

A kimenő szinkronizálás frissíti az exportált objektumokat, ha egy metaverzum-objektum megváltozik, de nem törlődik. A kimenő szinkronizálás célja annak kiértékelése, hogy a metaverzum-objektumok módosításai szükségessé teszik-e az összekötő terekben lévő átmeneti objektumok frissítését. Bizonyos esetekben a módosítások megkövetelhetik, hogy az összes összekötő térben lévő átmeneti objektumok at frissíteni kell. A módosított átmeneti objektumok függőben lévő exportálásként vannak megjelölve, így objektumokat exportálnak. Ezeket az exportálási objektumokat később kilöki a rendszer a csatlakoztatott adatforrásba az exportálási folyamat során.

A kimenő szinkronizálás három folyamatból van:

* **Telepítés**
* **Megszüntetés**
* **Attribútumfolyamat exportálása**

A kiépítés és a megszüntetés objektumszintű műveletek. A megszüntetés a kiépítéstől függ, mert csak a kiépítés kezdeményezheti azt. A megszüntetés akkor aktiválódik, amikor a kiépítés eltávolítja a metaverzum-objektum és az exportobjektum közötti kapcsolatot.

A kiépítés mindig akkor aktiválódik, ha a metaverzumobjektumaira módosításokat alkalmaz. Metaverzum-objektumok módosításakor a szinkronizálási motor a létesítési folyamat részeként a következő feladatok bármelyikét el tudja végezni:

* Illesztett objektumok létrehozása, ahol egy metaverzum-objektum egy újonnan létrehozott exportobjektumhoz van csatolva.
* Illesztett objektum átnevezése.
* A metaverzum-objektum és az átmeneti objektumok közötti kapcsolatok leválasztása, kiválasztott objektum létrehozása.

Ha a kiépítéshez szinkronizálási motorra van szükség egy új összekötő objektum létrehozásához, az átmeneti objektum, amelyhez a metaverzum-objektum kapcsolódik, mindig exportobjektum, mert az objektum még nem létezik a csatlakoztatott adatforrásban.

Ha a kiépítés hez szinkronizálási motorra van szükség az összekapcsolt objektum lekapcsolódásához, és egy leválasztott objektumlétrehozása esetén a megszüntetés aktiválódik. A megszüntetési folyamat törli az objektumot.

A megszüntetés során az exportálási objektum törlése fizikailag nem törli az objektumot. Az objektum **törlésként**van megjelölve, ami azt jelenti, hogy a törlési művelet az objektumon van előkészítve.

Az exportálási attribútumfolyamat a kimenő szinkronizálási folyamat során is megtörténik, hasonlóan ahhoz, ahogyan az importálási attribútumfolyamat a bejövő szinkronizálás során történik. Az exportálási attribútumfolyamat csak metaverzum és az egyesített objektumok között történik.

### <a name="export-process"></a>Exportálási folyamat
Az exportálási folyamat során a szinkronizálási motor megvizsgálja az összekötő térben függőben lévő exportálásként megjelölt összes exportálási objektumot, majd frissítéseket küld a csatlakoztatott adatforrásnak.

A szinkronizálási motor meghatározhatja az exportálás sikeressíthető, de nem tudja megfelelően megállapítani, hogy az identitáskezelési folyamat befejeződött-e. A csatlakoztatott adatforrásobjektumait mindig módosíthatják más folyamatok. Mivel a szinkronizálási motor nem rendelkezik állandó kapcsolattal a csatlakoztatott adatforrással, nem elegendő feltételezéseket tenni a csatlakoztatott adatforrásban lévő objektum tulajdonságairól, csak egy sikeres exportálási értesítés alapján.

Például a csatlakoztatott adatforrás egy folyamata visszaváltoztathatja az objektum attribútumait az eredeti értékekre (azaz a csatlakoztatott adatforrás felülírhatja az értékeket közvetlenül az adatok szinkronizálási motor általi kiküldése és sikeres alkalmazása után csatlakoztatott adatforrás).

A szinkronizálási motor az egyes átmeneti objektumok exportálási és importálási állapotadatait tárolja. Ha az attribútumfelvételi listában megadott attribútumok értékei megváltoztak a legutóbbi exportálás óta, az importálási és exportálási állapot tárolása lehetővé teszi a szinkronizálási motor megfelelő reagálását. A szinkronizálási motor az importálási folyamat segítségével erősíti meg a csatlakoztatott adatforrásba exportált attribútumértékeket. Az importált és az exportált információ összehasonlítása , amint az az alábbi ábrán is látható, lehetővé teszi a szinkronizálási motor számára annak megállapítását, hogy az exportálás sikeres volt-e, vagy meg kell ismételni.

![Ív7](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Ha például a szinkronizálási motor az 5-ös értékű C attribútumot exportálja egy csatlakoztatott adatforrásba, a C=5-öt az exportálási állapotmemóriájában tárolja. Az objektum minden további exportálása azt eredményezi, hogy a C=5 programot újra megpróbálja exportálni a csatlakoztatott adatforrásba, mert a szinkronizálási motor feltételezi, hogy ez az érték nem lett tartósan alkalmazva az objektumra (azaz kivéve, ha a közelmúltban más értéket importáltak a csatlakoztatott adatforrás). Az exportálási memória törlődik, ha c=5 érkezik az objektumimportálási művelet során.

## <a name="next-steps"></a>További lépések
További információ az [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációjáról.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

