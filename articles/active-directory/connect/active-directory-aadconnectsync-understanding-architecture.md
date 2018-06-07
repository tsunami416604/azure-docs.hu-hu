---
title: 'Azure AD Connect szinkronizálása: a architektúra ismertetése |} Microsoft Docs'
description: Ez a témakör ismerteti az Azure AD Connect szinkronizálási szolgáltatás architektúrája és a használt kifejezések magyarázatát.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 079dfe772e6c189c0e81ea7af16d11a1c40f3ebe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593486"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD Connect szinkronizálása: a architektúra ismertetése
Ez a témakör ismerteti az Azure AD Connect szinkronizálási szolgáltatás alapvető architektúráját. Számos tekintetben hasonló a korábbi verzióknál MIIS 2003 ILM 2007 és a FIM 2010. Azure AD Connect szinkronizálása, ezek a technológiák alakulását. Ha ismeri az összes ilyen korábbi technológiát, ez a témakör tartalma lesz ismerős lehet is. Ha most ismerkedik a szinkronizálást, akkor ez a témakör értéke meg. Van azonban nem követelmény az ebben a témakörben a testreszabási az Azure AD Connect sync (néven a szinkronizálási motor ebben a témakörben) sikeres részleteit.

## <a name="architecture"></a>Architektúra
A szinkronizálási motor egy integrált nézetben több csatlakoztatott adatforrások tárolt objektumok létrehozása, és azonosító adatokat az adatforrások kezelése. Ez a nézet integrált csatlakoztatott adatforrások és a szabályok, amelyek meghatározzák, hogyan kell feldolgozni az információkat lekért identitásinformációi határozza meg.

### <a name="connected-data-sources-and-connectors"></a>Csatlakoztatott adatforrások és összekötők
A szinkronizálási motor dolgozza fel a azonosító adatokat a különböző adattárolók, például az Active Directory vagy egy SQL Server-adatbázist. Minden adat tárház, amely rendszerezi az adatbázis-szerű formátumú adatokat, illetve szabványos adat-hozzáférési metódusokat biztosít, amely a szinkronizálási motor egy potenciális adatok forrás jelölt. A szinkronizálási motor által szinkronizált adattárolók nevezzük **csatlakoztatott adatforrások** vagy **csatlakoztatott könyvtárak** (CD).

A szinkronizálási motor hívása modulon belül csatlakoztatott adatforráshoz való együttműködéshez magában foglalja a **összekötő**. Minden csatlakoztatott adatforrás rendelkezik egy adott összekötőt. Az összekötő eszköz, amely együttműködik a csatlakoztatott adatforrás formátumra alakítja a szükséges műveletet.

Összekötők hívások API-t az exchange-azonosító adatokat (olvasási és írási) olyan csatlakoztatott adatforráshoz. Akkor is a bővíthető kapcsolat keretrendszer egyéni összekötő hozzáadásához. A következő ábra azt mutatja, hogy egy összekötő hogyan csatlakozzon a csatlakoztatott adatforrás a szinkronizálási motor.

![Ossz1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

Mindkét irányban áramolhasson az adatokat, de azt nem flow mindkét irányban egyidejűleg. Más szóval összekötő beállítható úgy, hogy teszi lehetővé az adatok áramlását a szinkronizálási motor a csatlakoztatott adatforrás vagy a szinkronizálási motor a csatlakoztatott adatforráshoz, de ezek a műveletek közül csak akkor fordulhat elő, és egy attribútum egy időben. A lehet különböző, a különböző objektumok esetében, és különböző attribútumokhoz.

Konfigurálja az összekötőt, meg kell adnia a szinkronizálni kívánt objektumtípusok. Adja meg a következő típusú objektumokat, amelyek szerepelnek a szinkronizálási folyamat objektumok körét határozza meg. A következő lépésre, jelölje be az attribútumok legyenek szinkronizálva, egy attribútum listában nevezik. Ezeket a beállításokat az üzleti szabályok változásai bármikor lehet megváltoztatni. Az Azure AD Connect varázsló használatakor ezeket a beállításokat meg vannak konfigurálva.

Objektumok exportálása csatlakoztatott adatforráshoz, a attribútum listában tartalmaznia kell legalább egy csatlakoztatott adatforrás egy adott objektumtípus létrehozásához szükséges minimális attribútumok. Például a **sAMAccountName** attribútum az Active Directoryba való exportálás olyan felhasználói objektum, mert az összes felhasználói objektumok az Active Directoryban kell rendelkeznie a attribútum listában szerepelnie kell egy **sAMAccountName** attribútummal. Ebben az esetben a telepítési varázsló hajtja végre ezt a konfigurációt.

Ha a csatlakoztatott adatforrás strukturális összetevőket használ, partíciók vagy a tárolókat rendszerezéséhez objektumok, például korlátozhatja a területeken, amelyeket egy adott megoldás a csatlakoztatott adatforrás.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>A szinkronizálási motor névtér belső szerkezete
A teljes szinkronizálási motor névtér áll két névtér, amely a azonosító adatok tárolására. A két névtér a következők:

* A kapcsolódási térbe (CS)
* A metaverse (MV)

A **kapcsolódási térbe** van egy átmeneti területre, amely a kijelölt objektumok ábrázolásai csatlakoztatott adatforrás és a attribútum listában attribútumát tartalmazza. A szinkronizálási motor használja a kapcsolódási térbe, annak meghatározásához, hogy mi változott a csatlakoztatott adatforráshoz, és a bejövő változások tesztelése. A szinkronizálási motor is a kapcsolódási térbe használja az exportált a csatlakoztatott adatforráshoz kimenő változások tesztelése. A szinkronizálási motor egy különálló kapcsolódási térbe egy átmeneti területre, az egyes összekötő kezeli.

Segítségével egy átmeneti területre, a szinkronizálási motor független csatlakoztatott adatforrások marad, és nem érinti a rendelkezésre állás és a kisegítő lehetőségek. Azonosító adatok bármikor feldolgozhatja ennek eredményeképpen az átmeneti területen lévő adatok felhasználásával. A szinkronizálási motor kérheti csak a legutóbbi kommunikációs munkamenet megszakadt vagy csak a módosításoknak leküldéses óta az identitásinformációi, amely még nem kapta meg a csatlakoztatott adatforráshoz, ami csökkenti a hálózati belül a csatlakoztatott adatforrás végrehajtott módosítások a szinkronizálási motor és a csatlakoztatott adatforrás közötti forgalmat.

Emellett a szinkronizálási motor, hogy a kapcsolódási térbe szakaszában minden objektumot állapotának adatait tárolja. Amikor új adatok érkezik, szinkronizálási motor mindig kiértékeli, hogy az adatok már be lett szinkronizálva.

A **metaverse** egyik tárolóhelye több csatlakoztatott adatforrások, így az összes kombinált objektum egyetlen globális, integrált nézetben az összevont identitás vonatkozó információk. Metaverzum-objektum, amely veszi át a csatlakoztatott adatforrások és a szabályok, amelyek segítségével testre szabhatja a szinkronizálási folyamat a azonosító adatok alapján jönnek létre.

A következő ábrán az összekötő terület névtér és a metaverzum névterének a szinkronizálási motor.

![Arch2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Szinkronizálási motor identitásobjektumok
A szinkronizálási motor objektumaira objektumképviseleteket vagy a csatlakoztatott adatforrás vagy az integrált nézet, amelyek szinkronizálják a motor objektumok rendelkezik. Minden szinkronizálási motor objektum egy globálisan egyedi azonosítóját (GUID) kell rendelkeznie. GUID azonosítók biztosít adatintegritást és -objektumok között kifejezett kapcsolatok.

### <a name="connector-space-objects"></a>Összekötő terület objektumok
Ha a szinkronizálási motor kommunikál a csatlakoztatott adatforráshoz, beolvassa az a csatlakoztatott adatforráshoz a azonosító adatok, és ezt az információt használja a kapcsolódási térbe a megjelenítése, az identitás-objektum létrehozásához. Nem hozható létre, vagy külön-külön törölje ezeket az objektumokat. Azonban manuálisan törölheti a kapcsolódási térbe található összes objektumot.

A kapcsolódási térbe található összes objektumnak két attribútumokkal rendelkezik:

* A globálisan egyedi azonosítóját (GUID)
* A megkülönböztető név (DN)

Ha a csatlakoztatott adatforrás egyedi attribútum rendeli hozzá az objektumhoz, a kapcsolódási térbe objektumokat is horgonyattribútum. A horgonyattribútum egyedileg azonosít egy objektumot, a csatlakoztatott adatforrás. A szinkronizálási motor a horgony használja ezt az objektumot a megfelelő ábrázolását meghatározni a csatlakoztatott adatforráshoz. Szinkronizálási motor feltételezi, hogy a horgony objektum soha nem módosítja az objektum élettartama során.

Az összekötők számos ismert egyedi azonosító segítségével horgonya automatikusan az egyes objektumok létrehozása, amikor importálja. Például az Active Directory-összekötőt használja a **objectGUID** horgonya attribútuma. Csatlakoztatott adatforrások, amelyek nem adnak meg egyértelműen meghatározott egyedi azonosítója horgonyzási generációs is megadhat, az összekötő-konfiguráció részeként.

Abban az esetben a horgony épül egy vagy több egyedi attribútum egy objektum, sem mely változások, és típusú, amely egyedileg azonosítja a kapcsolódási térbe (például az alkalmazott számát vagy a felhasználói azonosító) objektum.

Egy összekötő terület objektum a következő egyike lehet:

* Egy átmeneti objektum
* A helyőrző

### <a name="staging-objects"></a>Átmeneti objektumok
Egy átmeneti objektumot a kijelölt Objektumtípus egyik példányát képviseli a csatlakoztatott adatforrásból. A GUID Azonosítót és a megkülönböztető név kívül egy átmeneti mindig objektumnak egy érték, amely azt jelzi, az objektum típusának.

Átmeneti mindig importált objektumoknak a horgony attribútum értékét. Átmeneti objektumok újonnan kiépített szinkronizálási motor által, a csatlakoztatott adatforrás létrehozása folyamatban nem tartalmaz a horgony attribútum értékét.

Átmeneti objektumok továbbítani az aktuális értékek üzleti, és a szinkronizálási motor által a szinkronizálási folyamat elvégzéséhez szükséges működéssel kapcsolatos adatokat is. Működési adatok között szerepelnek a jelzőkkel, amelyek elő van készítve az átmeneti objektumon frissítések típusát jelzi. Ha egy átmeneti objektumot kapott új identitásadatok a csatlakoztatott adatforrásból, amelyek még nem dolgozott, az objektum megjelölt **függőben lévő importálás**. Ha egy átmeneti objektum, amely még nem lett exportálva a csatlakoztatott adatforrás új identitásadatok, mint a megjelölt **exportálási függőben lévő**.

Az importálási vagy exportálási objektum egy átmeneti objektum lehet. A szinkronizálási motor importálási objektum a csatlakoztatott adatforrásból kapott objektuminformáció használatával hozza létre. Információ egy új objektum, amely megfelel az összekötő kijelölt objektumtípusok egyikének fogadásakor a szinkronizálási motor hozna létre az importálás objektum a kapcsolódási térbe, az objektum a csatlakoztatott adatforrás ábrázolása.

A következő ábrán egy objektum a csatlakoztatott adatforrás jelölő importálási objektum.

![Arch3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

A szinkronizálási motor exportálási objektum a metaverzumban objektuminformáció használatával hozza létre. Objektumok exportálása során a következő kommunikációs munkamenet exportálják a csatlakoztatott adatforráshoz. A szinkronizálási motor szempontjából objektumok exportálása nem szerepelnek a csatlakoztatott adatforrás még. Ezért a horgonyattribútum egy exportálási objektum nem áll rendelkezésre. Azután, hogy megkapja az objektum a szinkronizálási motor, a csatlakoztatott adatforrás létrehoz egy egyedi értéket a horgonyattribútum objektum.

A következő ábra azt mutatja, hogyan exportálási objektumot hoz létre a metaverzumban identitásinformációi segítségével.

![Arch4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

A szinkronizálási motor megerősíti, hogy az objektum az Exportálás importintézkedésekkel a csatlakoztatott adatforrásból az objektum által. Objektumok exportálása objektum importálása válnak, amikor a szinkronizálási motor megkapja azokat az csatlakoztatott adatforrásból a következő importálása során.

### <a name="placeholders"></a>Helyőrzők
A szinkronizálási motor egy egyszerű névtér objektumok tárolására használja. Azonban néhány csatlakoztatott adatforrások, például az Active Directory használata a hierarchikus névtér. A hierarchikus névtér adatait egyszerű névtérbe átalakításához szinkronizálási motor helyőrzők használja a hierarchia megőrzése érdekében.

Minden egyes helyőrző hierarchikus neve az objektum, amely a szinkronizálási motor nem lett importálva, de a hierarchikus neve összeállításához szükséges (például egy szervezeti egységhez) összetevője. Kitöltés objektumokat, amelyek nem átmeneti objektumokat a kapcsolódási térbe hivatkozik a csatlakoztatott adatforrás által létrehozott hézagok meghatározása érdekében.

A szinkronizálási motor helyőrzők is használ, amely még nem importálta hivatkozott objektumok tárolásához. Például, ha a sync be van állítva a kezelő attribútum tartalmazza a *Abbie Spencer* objektum és a kapott értéket a rendszer olyan objektum, amely nem lett még, importált, többek között *CN Kálmán Sperry, CN = Users, DC = fabrikam, DC = = com* , a kezelő adatokat tárolja a kapcsolódási térbe helyőrzőként. Az objektum később importálható, ha a rendszer felülírja a helyőrző objektum által az átmeneti, a kezelő képviselő objektum.

### <a name="metaverse-objects"></a>Metaverzum-objektum
A metaverzum-objektum összevont nézetet tartalmazza a szinkronizálási motor rendelkezik a kapcsolódási térbe átmeneti objektumára. Szinkronizálási motor metaverzum-objektumokat hoz létre. az objektum importálása szereplő információk segítségével. Több összekötő terület objektumok egy egyetlen metaverzum-objektum lehet társítani, de egy összekötő terület objektum nem csatolható több metaverzum-objektum.

Metaverzum-objektum nem lehet manuálisan létrehozott vagy törölt. A szinkronizálási motor, amely nem rendelkezik a hely összekötő objektumok csatolásának a kapcsolódási térbe metaverzum-objektumok automatikusan törli.

A csatlakoztatott adatforrás belüli objektumok hozzárendelése a megfelelő objektumtípus belül a metaverzumba, a szinkronizálási motor egy bővíthető séma objektumtípusokra és a kapcsolódó attribútumok előre meghatározott számú biztosít. Új objektumtípusok és metaverzum-objektum attribútumainak hozhat létre. Attribútumok egyértékű vagy többértékű lehet, és karakterláncokat, hivatkozások, számok és logikai értékek attribútumtípust lehet.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Átmeneti és metaverzum objektumok közötti kapcsolatok
A szinkronizálási motor névtéren belül az adatfolyam szerint engedélyezve van a hivatkozás kapcsolat átmeneti és metaverzum-objektumok között. Egy átmeneti az objektumot, amely csatolva van egy metaverzum-objektum egy **illesztett objektum** (vagy **összekötő objektum**). Egy átmeneti az objektumot, amely nem kapcsolódik a metaverzum-objektum egy **objektum leválasztják** (vagy **Terheléskapcsoló objektumok**). Adatok importálása és exportálása a csatlakoztatott könyvtárból felelős összekötőkkel nem hathat a feltételeket, tartományhoz csatlakoztatott és leválasztják előnyben részesített.

Helyőrzők soha nem kapcsolódnak a metaverzum-objektum

Illesztett objektum magában foglalja, egy átmeneti objektum és a csatolt kapcsolat egy egyetlen metaverzum-objektum. Csatlakoztatott objektumainak szinkronizálása a connector terület objektum és a metaverzum-objektum között attribútumértékek használhatók.

Amikor egy átmeneti objektum illesztett objektum szinkronizálás során, attribútumok áramolhasson az átmeneti objektum és a metaverzum-objektum között. Attribútumfolyam kétirányú és attribútum szabályok importálása és exportálása attribútum szabályok segítségével konfigurálhatók.

Egy egyetlen összekötőt terület objektum csak egyetlen metaverzum-objektum lehet társítani. Azonban minden metaverzum-objektum kapcsolható több összekötő terület objektumok azonos vagy különböző összekötőterek, a következő ábrán látható módon.

![Arch5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

Az átmeneti objektum és a metaverzum-objektum csatolt kapcsolatának állandó, és csak a megadott szabályok szerint távolíthatja el.

Egy kilépett célja egy átmeneti objektum, amely nem kapcsolódik egyetlen metaverzum-objektum. Az attribútum egy kilépett objektum értékei nem dolgozza fel, minden további, a metaverzumba belül. Az attribútum értékei a megfelelő a csatlakoztatott adatforrás-objektum nem frissíti a szinkronizálási motor.

Kilépett objektumok használatával a szinkronizálási motor azonosító adatok tárolására, és később feldolgozni azt. Egy átmeneti objektumot megőrzi a kapcsolódási térbe kilépett objektumként számos előnye van. Mivel a rendszer már rendelkezik a szükséges adatokat e objektumra vonatkozó előkészített, nincs szükség a megjelenítése, az objektum a következő importálása során újra létrehozni a csatlakoztatott adatforrásból. Ezzel a módszerrel szinkronizálási motor mindig egy teljes pillanatkép tartozik a csatlakoztatott adatforrás, akkor is, ha a csatlakoztatott adatforrás jelenleg nincs kapcsolat van. Kilépett objektumok alakíthatók illesztett objektumokat, és fordítva, attól függően, hogy a megadott szabályok.

Az importálás objektum kilépett objektumként jön létre. Az Exportálás objektum illesztett objektumnak kell lennie. A rendszer programot érvénybe lépteti az e szabály, és töröl minden exportálási objektum, amely nem egy illesztett objektum.

## <a name="sync-engine-identity-management-process"></a>Szinkronizálási motor identity management folyamat
Az identitás-kezelési folyamat szabályozza, hogyan történik a azonosító adatok között különböző csatlakoztatott adatforrások frissítése. Az Identitáskezelés három folyamat akkor fordul elő:

* Importálás
* Szinkronizálás
* Exportálás

Az importálási folyamat során a szinkronizálási motor kiértékeli a bejövő azonosító adatokat a csatlakoztatott adatforrás. Észlel, amikor az átmeneti új objektumot hoz létre vagy frissíti a meglévő átmeneti objektumokat a kapcsolódási térbe szinkronizáláshoz.

A szinkronizálási folyamat során a szinkronizálási motor a metaverzumba történt a kapcsolódási térbe módosításokkal frissíti, és frissíti a kapcsolódási térbe a metaverzumban történt a módosításokkal.

Az exportálási folyamat során a szinkronizálási motor, amely elő van készítve az objektumok fejlesztői és, exportálás függőben vannak megjelölve módosításokat leküldéses értesítések.

Az alábbi ábrán látható, hogy hol a folyamatok mindegyikének jelentkezik, identitás információáramlás egy csatlakoztatott adatforrásból egy másikra.

![Arch6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>Az importálási folyamat
Az importálási folyamat során a szinkronizálási motor kiértékeli a azonosító adatok frissítéseit. Szinkronizálási motor összehasonlítja a azonosító adatok átmeneti objektum azonosító információkat a csatlakoztatott adatforrásból kapott, és meghatározza, hogy az átmeneti objektum frissítésekre van szüksége. Ha az átmeneti objektum frissítenie kell az új adatokat, az átmeneti tárolási objektum, függőben lévő importálás van megjelölve.

Átmeneti objektumokat a kapcsolódási térbe szinkronizálás előtt, amelyet a szinkronizálási motor csak a megváltozott azonosító adatok tud feldolgozni. Ez a folyamat a következő előnyöket nyújtja:

* **Hatékony szinkronizálási**. A szinkronizálás során feldolgozandó adatok mennyiségétől másodpercekre csökken.
* **Az újraszinkronizálás hatékony**. Módosíthatja, miként szinkronizálási motor dolgozza fel a azonosító adatok nélkül újracsatlakozás a szinkronizálási motor az adatforráshoz.
* **Szinkronizálás előzetes**. Győződjön meg arról, hogy helyesek-e az Előfeltételek, identity management folyamatával kapcsolatos szinkronizálás megtekintheti.

Minden olyan objektumon, az összekötő megadott a szinkronizálási motor először megpróbálja megkeresni a megjelenítése, az objektum az az összekötő a kapcsolódási térbe. Szinkronizálási motor megvizsgálja az összes átmeneti objektumokat a kapcsolódási térbe és próbál meg megtalálni egy megfelelő tesztelési objektum, amely rendelkezik a megfelelő horgonyattribútum. Ha nincs meglévő átmeneti objektum a megfelelő horgonyattribútum, a szinkronizálási motor megpróbálja található a megfelelő átmeneti objektum megkülönböztető ugyanazzal a névvel.

Ha a szinkronizálási motor egy átmeneti objektum, amely megfelel a megkülönböztető név szerint, de nem a horgony talál, a következő különleges történik:

* Ha található, a kapcsolódási térbe objektumnak nincs kapcsolati, akkor a szinkronizálási motor eltávolítja ezt az objektumot a kapcsolódási térbe és jelöli meg a metaverzum-objektum, mert nincs csatolva **ismételje meg a következő szinkronizálás alkalmával futtatása kiépítés**. Majd azt az új objektumot hoz létre importálása.
* Ha az objektum található, a kapcsolódási térbe horgonyra, majd szinkronizálási motor feltételezi, hogy ez az objektum rendelkezik vagy átnevezték vagy törölték a csatlakoztatott könyvtárban. Az összekötő terület objektum ideiglenes, új megkülönböztető nevét, hogy azt is készítheti a bejövő objektum ki. A régi objektum lesz **átmeneti**, az összekötőt sikerült importálni a átnevezése vagy törlése elhárításához való várakozás közben.

Ha a szinkronizálási motor egy átmeneti objektum, amely megfelel az összekötő megadott objektumot talál, meghatározza, hogy milyen módosítások alkalmazásához. Például szinkronizálási motor lehet, hogy nevezze át vagy törölje a csatlakoztatott adatforrás objektumát, vagy lehet, hogy az az objektum attribútumértékek csak frissíti.

Frissített adatok átmeneti objektumok, függőben lévő importálás lesznek megjelölve. Különböző típusú importálja függőben lévő érhetők el. Az importálási folyamat eredményétől függően végezzen el a kapcsolódási térbe átmeneti objektumban van függőben lévő importálás típusok a következők egyikét:

* **Nincs**. Nem módosult a attribútumokat a átmeneti objektum érhetők el. Szinkronizálási motor nem ez a jelző ehhez a típushoz, függőben lévő importálás.
* **Adja hozzá**. Átmeneti célja a kapcsolódási térbe új importálási objektumot. Szinkronizálási motor jelzők ehhez a típushoz, függőben lévő importálás a metaverzumban további feldolgozásra.
* **Frissítés**. Szinkronizálási motor a kapcsolódási térbe egy megfelelő tesztelési objektumot talál, és ez típusú, mint a függőben lévő importálás tartalomként jelöli meg, hogy az attribútumok frissítések dolgozhatók fel a metaverzumba. Frissítések tartalmaznak objektum átnevezése.
* **Törlés**. Szinkronizálási motor a kapcsolódási térbe egy megfelelő tesztelési objektumot talál, és a függőben lévő importálás típusú tartalomként jelöli meg, hogy a csatlakoztatott objektum törlése.
* **Törlés/felvétele**. Szinkronizálási motor a kapcsolódási térbe egy megfelelő tesztelési objektumot talál, de az objektum típusa nem egyezik. Ebben az esetben a delete hozzáadási módosítása elő van készítve. A delete-vegye fel a szinkronizálási motor módosítása azt jelzi, hogy az objektum teljes újraszinkronizálási kell fordulhat elő, mert más-más részhalmazához szabályok erre az objektumra alkalmazza, ha az objektum írja be a módosításokat.

A függőben lévő importálás egy átmeneti tárolási objektum állapotát beállításával jelentősen feldolgozása a szinkronizálás során, mert ezzel engedélyezi a rendszer csak azokat az objektumokat, amely rendelkezik a módosított adatokat feldolgozni adatmennyiség csökkentése érdekében.

### <a name="synchronization-process"></a>Szinkronizálási folyamat
Szinkronizálás két kapcsolódó folyamatokat foglalja magában:

* Bejövő szinkronizálás, a metaverzumba tartalmának frissítésekor a kapcsolódási térbe lévő adatok felhasználásával.
* Kimenő szinkronizálási, a kapcsolódási térbe tartalmának frissítésekor a metaverzumban adatok használatával.

A kapcsolódási térbe az előkészített információk segítségével a vonatkozó bejövő szinkronizálási folyamat hoz létre a metaverzumba a csatlakoztatott adatforrások tárolt adatok integrált nézet. Minden átmeneti objektumot, vagy csak azokat a függőben lévő importálás információkkal összesítése, attól függően, hogy a szabályok úgy vannak konfigurálva.

A kimenő szinkronizálási folyamat frissítések objektumok exportálása metaverzum-objektumok módosításakor.

Bejövő szinkronizálás az integrált nézet a azonosító adatok, a csatlakoztatott adatforrások érkezett a metaverzumba hoz létre. Szinkronizálási motor képes a azonosító adatok bármikor útmutatása szerint legújabb identitás, amely rendelkezik a csatlakoztatott adatforrásból.

**Bejövő szinkronizálás**

Bejövő szinkronizálás a következő eljárásokat tartalmazza:

* **Kiépítés** (más néven **leképezése** esetén fontos, hogy ez a folyamat megkülönböztetni a kimenő szinkronizálási kiépítése). A szinkronizálási motor hoz létre egy új metaverzum-objektum, egy átmeneti objektum alapján, és csatolja őket. Kiépítés objektumszintű során.
* **Csatlakozás**. A szinkronizálási motor egy átmeneti objektum egy meglévő metaverzum-objektum hivatkozásokat tartalmaz. Illesztés objektumszintű során.
* **Attribútumfolyam importálása**. Szinkronizálási motor frissíti az attribútumértékek Attribútumfolyam, a a metaverzum-objektum neve. Attribútumfolyam importálása során attribútum szintű, amely egy átmeneti objektum és a metaverzum-objektum közötti kapcsolat szükséges.

Rendelkezés a rendszer csak által létrehozott objektumok a metaverzumban. Kiépítés csak kilépett objektumok importálása objektumok hatással van. Során kiépítését a szinkronizálási motor létrehozza a metaverzum-objektum, amely megfelel az objektumtípus az importálási objektum, és mindkét objektumok, így a csatlakoztatott objektum létrehozása közötti kapcsolatot hoz létre.

A csatlakozási folyamat is az objektum importálása és a metaverzum-objektum közötti kapcsolatot hoz létre. Csatlakozás és a kiépítés közötti különbség, hogy az illesztés a folyamathoz az szükséges, hogy az importálás objektum kapcsolódik-e egy meglévő metaverzum-objektum, ahol a kiépítési folyamat létrehoz egy új metaverzum-objektum.

Szinkronizálási motor megkísérli a metaverzum-objektum importálási objektum csatlakoztassa a szinkronizálási szabály konfigurációjában megadott feltételek alapján.

A kiépítés és a csatlakozás folyamatok során szinkronizálási motor kilépett objektum minősítené csatlakoztatott metaverzum-objektum hivatkozásokat tartalmaz. Ezen objektumszintű műveletek elvégzése után a szinkronizálási motor frissítheti a attribútumértékeit a társított metaverzum-objektum. A folyamat elnevezése importálási Attribútumfolyam.

Importálási Attribútumfolyam következik be, amelyek új adatokat és a metaverzum-objektum csatolását az összes importálási objektumokon.

**Kimenő szinkronizálási**

Kimenő szinkronizálási frissítések exportálhatja az objektumokat, ha módosítja a metaverzum-objektum, de nem törlődik. Kimenő szinkronizálási célja annak kiértékelésére, hogy a metaverzum-objektumok változásait a összekötőterek átmeneti objektumok frissítés szükséges. Bizonyos esetekben a változtatásokat, hogy átmeneti lehet szükség az összes összekötőterek objektumokat frissíteni. Módosított átmeneti objektumok exportálása, így azok az objektumok exportálása függőben vannak megjelölve. Ezek az exportálási objektumok vannak később leküldött a csatlakoztatott adatforrás az exportálási folyamat során.

Kimenő szinkronizálási három folyamat rendelkezik:

* **Kiépítés**
* **Megszüntetés**
* **Attribútumfolyam exportálása**

Kiépítés és megszüntetés mindkét objektumszintű műveletek, amelyek. Megszüntetés attól függ, hogy csak kiépítés is kezdeményezhető, mivel kiépítés. Megszüntetés lesz kiváltva, ha a kiépítés eltávolítja a metaverzum-objektum és az Exportálás objektum közötti kapcsolat.

Kiépítés mindig kiváltva, ha a metaverzumban található objektumok módosításai érvényesek lesznek. Metaverzum-objektum módosításai, amikor szinkronizálási motor is hajtsa végre a következő feladatokat a telepítési folyamat részeként:

* Hozzon létre csatlakoztatott objektumainak, ahol a metaverzum-objektum egy újonnan létrehozott exportálási objektum kapcsolódik.
* Nevezze át a csatlakoztatott objektum.
* Metaverzum-objektum és az átmeneti kilépett objektum létrehozása objektumok közötti kapcsolatok disjoin.

Kiépítés van szükség a szinkronizálási motor egy új összekötő objektum, ha átmeneti, amelyhez csatolva van a metaverzum-objektum célja mindig exportálási objektum, mert az objektum még nem létezik a csatlakoztatott adatforrás.

Ha a kiépítés igényel egy-egy kilépett objektum létrehozása illesztett objektum disjoin a szinkronizálási motor megszüntetés elindul. A megszüntetési folyamatot törli az objektumot.

Megszüntetés, során exportálási objektum nem fizikailag törlésekor az objektum. Az objektum van megjelölt **törölt**, ami azt jelenti, hogy a törlési művelet az objektum tartalma.

Exportálás Attribútumfolyam is hasonló módon, hogy az importálás Attribútumfolyam rendszerindításakor vonatkozó bejövő szinkronizálási kimenő szinkronizálási folyamat során fordul elő. Exportálás Attribútumfolyam csak tartományhoz csatlakoztatott metaverse és exportálási objektumok között történik.

### <a name="export-process"></a>Exportálási folyamata
Az exportálási folyamat során a szinkronizálási motor megvizsgálja összes exportálási objektumokat a kapcsolódási térbe exportálás függőben vannak megjelölve, és ezután elküldi a frissítések a csatlakoztatott adatforráshoz.

A szinkronizálási motor megállapíthatja, hogy az Exportálás sikerességének, de nem tudja megfelelően megállapítani, hogy az identity management folyamat be nem fejeződik. A csatlakoztatott adatforrás objektumokat mindig módosítható úgy, hogy más folyamatokkal. Szinkronizálási motor nincs állandó kapcsolatot a csatlakoztatott adatforráshoz, mert nincs elegendő ahhoz, hogy az objektum tulajdonságainak feltételezéseket végezze el a csatlakoztatott adatforráshoz, csak az Exportálás értesítést alapján.

Például egy folyamatot, a csatlakoztatott adatforrás változtathatja a címtárobjektum-attribútumok vissza az eredeti értékekre (Ez azt jelenti, hogy a csatlakoztatott adatforrás felülírhatja az értékeket közvetlenül az adatok szinkronizálási motor leküldött és sikeresen alkalmazva után a csatlakoztatott adatforráshoz).

A szinkronizálási motor tároló exportálja és importálja a minden átmeneti objektumot állapotának adatait. Ha az attribútum a attribútum listában megadott értéke a legutóbbi exportálás óta módosult, a importálása és exportálása állapot lehetővé teszi, hogy szinkronizálási motor megfelelően reagálni azokra. Szinkronizálási motor az importálási folyamat használja, a csatlakoztatott adatforráshoz exportált attribútumértékek megerősítéséhez. Az importált és az exportált adatokat összehasonlítása az alábbi ábrán látható módon lehetővé teszi, hogy határozza meg, hogy sikeres volt-e az Exportálás vagy is megismételhető, hogy szükséges a szinkronizálási motor.

![Arch7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

Például ha a szinkronizálási motor exportálja attribútum C, melynek értéke 5, csatlakoztatott adatforráshoz, tárolja C = 5 az Exportálás állapot memória. Minden további exportálási ezen az objektumon eredményezi, C = 5 a csatlakoztatott adatforráshoz újra exportálni, mert a szinkronizálási motor feltételezi, hogy ez az érték nem állandó telepítve van az objektum kísérlet (Ez azt jelenti, kivéve, ha egy másik értéket importált nemrég a csatlakoztatott adatforráshoz). Az Exportálás memória nincs bejelölve, C = 5 objektumon, az importálási művelet során fogadásakor.

## <a name="next-steps"></a>További lépések
További információ a [az Azure AD Connect szinkronizálási szolgáltatás](active-directory-aadconnectsync-whatis.md) konfigurációs.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

