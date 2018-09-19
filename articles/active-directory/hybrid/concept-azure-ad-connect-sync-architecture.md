---
title: 'Az Azure AD Connect szinkronizálása: az architektúra ismertetése |} A Microsoft Docs'
description: Ez a témakör ismerteti az Azure AD Connect-szinkronizálás architektúrája, és a használt kifejezések magyarázatát tartalmazza.
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
ms.openlocfilehash: 958ec6b32434bd9e0228255c0edbe9312225586c
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312780"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Az Azure AD Connect szinkronizálása: az architektúra ismertetése
Ez a témakör ismerteti az alapszintű architektúra az Azure AD Connect szinkronizálási szolgáltatás. Bizonyos szempontokból hasonlít az rövidítés a MIIS 2003 ILM 2007 és a FIM 2010. Az Azure AD Connect szinkronizálása, ezek a technológiák rendszergazdánál veszi kezdetét. Ha ismeri az esetleges technológiák korábbi, ez a témakör tartalma lesz ismeretlen az Ön számára is. Ha most ismerkedik a szinkronizáláshoz, majd a témakör Önnek szól. Azonban nincs olyan követelmény tudni, hogy ez a témakör a testreszabási (néven a szinkronizálási motor ebben a témakörben) az Azure AD Connect szinkronizálásának részleteit.

## <a name="architecture"></a>Architektúra
A szinkronizálási motor hoz létre, amely több csatlakoztatott adatforrások tárolt objektumok integrált áttekintése, és ezeknek az adatforrásoknak a azonosító adatok kezeli. Ez a nézet integrált a azonosító adatok csatlakoztatott adatforrások és a szabályok, amelyek meghatározzák, hogyan lehet feldolgozni az információkat lekért határozza meg.

### <a name="connected-data-sources-and-connectors"></a>Csatlakoztatott adatforrások és összekötők
A szinkronizálási motor dolgozza fel a azonosító adatokat a különböző adattárak, például az Active Directory vagy az SQL Server-adatbázis. Minden adattár, amely rendszerezi az adatbázis-jellegű formátumú adatokat és a standard szintű adatelérési módszert biztosít, amely a szinkronizálási motor egy lehetséges adatok forrás jelölt. A szinkronizálási motor által szinkronizált az adattárak nevezzük **csatlakoztatott adatforrások** vagy **könyvtárak csatlakoztatott** (CD).

A szinkronizálási motor nevű modul csatlakoztatott adatforrások való interakció magában foglalja egy **összekötő**. Minden csatlakoztatott adatforrás rendelkezik egy adott összekötőt. Az összekötő fordítja le a szükséges művelet, amely együttműködik a csatlakoztatott adatforrás formátumba.

Összekötők API-hívásokat az exchange-azonosító adatok (olvasási és írási) egy csatlakoztatott adatforráshoz. Akkor is, adjon hozzá egy egyéni összekötőt a bővíthetőkapcsolat-keretrendszer használatával. Az alábbi ábra bemutatja, hogyan egy összekötőt a szinkronizálási motor csatlakoztatott adatforráshoz kapcsolódik.

![Ossz1](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Adatok mindkét irányban áramolhasson, de ez nem a flow a mindkét irányban egyszerre. Más szóval egy összekötő beállítható úgy, hogy lehetővé teszi az adatok áramlását a csatlakoztatott adatforrás a szinkronizálási motor vagy a szinkronizálási motor a csatlakoztatott adatforráshoz, de ezek a műveletek közül csak akkor fordulhat elő egyszerre egy objektumra és attribútum. Lehet, hogy az irány eltérő a különböző objektumokat és a különböző attribútumokat.

-Összekötő konfigurálása a szinkronizálni kívánt típusú objektumokat adjon meg. Adja meg a különböző határozza meg, amelyek szerepelnek a szinkronizálási folyamat objektumok körét. A következő lépésre, jelölje be az attribútumokat szinkronizálni, egy attribútum belefoglalási listaként néven ismert. Ezek a beállítások segítségével változásokkal bármikor módosítható az üzleti szabályokat. Az Azure AD Connect telepítővarázsló használatakor ezeket a beállításokat is konfigurálja Önnek.

Objektumok exportálása egy csatlakoztatott adatforráshoz, az attribútumlista belefoglalási tartalmaznia kell legalább a csatlakoztatott adatforrás a megfelelő Objektumtípus létrehozásához szükséges minimális attribútumokat. Ha például a **sAMAccountName** attribútumnak szerepelnie kell az attribútum listában az Active Directoryba való exportálás egy felhasználói objektumot, mert az Active Directoryban az összes felhasználói objektumoknak rendelkezniük kell egy **sAMAccountName** meghatározott attribútum. A telepítővarázsló ismét hajtja végre ezt a konfigurációt.

Ha a csatlakoztatott adatforrás szerkezeti összetevők, például a partíciók és tárolók objektumok rendszerezéséhez használja a területek a csatlakoztatott adatforrás egy adott megoldás által használt korlátozhatja.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>A szinkronizálási motor névtér belső szerkezetét
A teljes szinkronizálási motor névtér áll a két névtér, amely a azonosító adatok tárolására. A két névtér a következők:

* Az összekötő-térben (CS)
* A metaverzum (MV)

A **összekötőterében** van egy átmeneti területre, amely tartalmazza a kijelölt objektumokat a csatlakoztatott adatforrás és a attribútum belefoglalási listán megadott attribútumok ábrázolásai. A szinkronizálási motor használja az összekötőtérben, határozza meg, mi változott a csatlakoztatott adatforráshoz, és a bejövő változások előkészítéséhez. A szinkronizálási motor az összekötőtérben is használja a csatlakoztatott adatforrás exportálási kimenő módosítások előkészítéséhez. A szinkronizálási motor tart fenn, az egyes összekötők különböző összekötőtérben, egy átmeneti területre.

Egy átmeneti területre használatával a szinkronizálási motor marad, függetlenül a csatlakoztatott adatforrásokhoz, és nincs hatással a rendelkezésre állás és a kisegítő lehetőségek. Azonosító adatok bármikor, ezért az előkészítési terület adatok felhasználásával tud feldolgozni. A szinkronizálási motor kérheti csak a legutóbbi kommunikációs munkamenet megszakadt vagy a változások csak leküldéses óta az identitásadatokat, amely még nem kapta meg a csatlakoztatott adatforráshoz, ami csökkenti a hálózat belül a csatlakoztatott adatforrás végzett módosításokat a szinkronizálási motor és a csatlakoztatott adatforrás közötti adatforgalmat.

Emellett a szinkronizálási motor, hogy az összekötőtérben előkészíti az összes objektumok állapot adatait tárolja. Amikor új adat érkezik, a szinkronizálási motor mindig kiértékeli-e az adatok már be lett szinkronizálva.

A **metaverzum** a tárolási területet, amely tartalmazza az összesített identitásadatokat több csatlakoztatott adatforrásokból származó, így az összes kombinált objektum egyetlen globális, integrált nézetben van. Metaverzum-objektum, amely a rendszer beolvassa a csatlakoztatott adatforrások és a szabályok, amelyek segítségével testre szabhatja a szinkronizálási folyamat a azonosító adatok alapján jönnek létre.

Az alábbi ábrán látható, az összekötő terület névtér és a metaverzum-névteret, a szinkronizálási motor belül.

![Arch2](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Szinkronizálási motor identitásobjektumok
A szinkronizálási motor található objektumok objektumképviseleteket vagy a csatlakoztatott adatforrás vagy az integrált nézet, amely a szinkronizálás a motor rendelkezik, azokat az objektumokat. Minden szinkronizálási motor objektum egy globálisan egyedi azonosítóját (GUID) kell rendelkeznie. GUID adja meg az adatok integritásának megőrzése, és objektumok közötti kapcsolatok expressz.

### <a name="connector-space-objects"></a>Összekötő terület objektumok
Amikor a szinkronizálási motor kommunikál a csatlakoztatott adatforráshoz, beolvassa az azonosító adatokat a csatlakoztatott adatforráshoz, és ezeket az adatokat használja, hozzon létre egy reprezentációja az identitás-objektumot az összekötőtérben. Nem hozható létre, vagy külön-külön törölje ezeket az objektumokat. Azonban manuálisan törölheti az adott összekötőtérben összes objektumot.

Összes objektumot az összekötőtérben két attribútumokkal rendelkezik:

* Egy globálisan egyedi azonosítóját (GUID)
* A megkülönböztető név (DN)

Ha a csatlakoztatott adatforrás objektumhoz rendeli hozzá egy egyedi attribútumot, majd objektumot az összekötőtérben is lehet horgonyattribútum. A forráshorgony attribútumának egyedileg azonosít egy objektumot, a csatlakoztatott adatforrás. A szinkronizálási motor a forráshorgony használatával keresse meg ezt az objektumot a megfelelő megfelelője a csatlakoztatott adatforrás. A szinkronizálási motor azt feltételezi, hogy az objektum a forráshorgony soha nem módosul az objektum élettartama során.

Egy ismert egyedi azonosítója az összekötők számos használja, hogy minden objektum esetén automatikusan horgonyra is, amikor importálja. Például az Active Directory-összekötőt használja a **objectGUID** horgonyra attribútuma. Csatlakoztatott adatforrások, amely nem kínál jól definiált egyedi azonosító forráshorgony generálása az összekötő konfigurációjának részeként is megadhat.

Ebben az esetben a horgony épül fel egy vagy több egyedi attribútum egy objektum írja be, sem melyik változásokról, és, amely egyedileg azonosítja az objektumot az összekötőtérben (például az alkalmazott számát vagy egy felhasználói azonosító).

Egy összekötőtér objektuma a következők egyike lehet:

* Egy előkészítési objektum
* Helyőrző

### <a name="staging-objects"></a>Átmeneti objektumok
Egy előkészítési objektum egy példányát a kijelölt objektumtípusok a csatlakoztatott adatforrásból jelöli. A GUID Azonosítót és a megkülönböztető név kívül egy átmeneti mindig objektumnak egy értéket, amely azt jelzi, hogy az objektum típusának.

Átmeneti mindig importált objektumoknak a forráshorgony attribútumának értékét. Átmeneti objektumokat a szinkronizálási motor által újonnan kiosztott és a csatlakoztatott adatforrás létrehozása folyamatban van a forráshorgony attribútumának értékét nem kell.

Átmeneti objektumokat is biztosítunk az üzleti attribútumok és a műveleti információk, a szinkronizálási motor által a szinkronizálási folyamat végrehajtásához szükséges aktuális értékeket. Működéssel kapcsolatos adatokat tartalmaz, amelyek a frissítéseket, amelyek elő van készítve az átmeneti tárolási objektum típusát jelzi. Ha egy előkészítési objektumot kapott új azonosító adatok még nem lett feldolgozva a csatlakoztatott adatforráshoz, az objektum megjelölt **függőben lévő importálás**. Ha egy előkészítési objektumot új azonosító adatok, amelyek még nem lett exportálva a csatlakoztatott adatforráshoz, tartalomként megjelölt **függő exportálás**.

Egy előkészítési objektumot egy importálási vagy exportálási objektum lehet. A szinkronizálási motor-importálási objektumot kapott a csatlakoztatott adatforrás objektum információk alapján hoz létre. Amikor a szinkronizálási motor, amely megfelel a kiválasztott az összekötő objektum típusú új objektumot létezik-e információkat kap, azt objektumot hoz létre egy importálása az összekötőtérben, az objektumot a csatlakoztatott adatforrás reprezentációját.

Az alábbi ábrán látható a csatlakoztatott adatforrás-objektumot képviselő objektum importálása.

![Arch3](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

A szinkronizálási motor exportálási objektum metaverzumban található objektumok információk alapján hoz létre. Objektumok exportálása lesznek exportálva az a csatlakoztatott adatforrás a következő kommunikációs munkamenet során. A szinkronizálási motor szempontjából objektumok exportálása nem szerepelnek a csatlakoztatott adatforrás még. Ezért exportálási objektum a forráshorgony attribútuma nem áll rendelkezésre. Az objektum a szinkronizálási motor kap, miután a csatlakoztatott adatforrás az objektum a forráshorgony attribútuma egyedi értéket hoz létre.

Az alábbi ábra bemutatja, hogyan exportálási objektumot hoz létre a metaverzumban azonosító adatok használatával.

![Arch4](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

A szinkronizálási motor megerősíti, hogy az exportálás, az objektum által a csatlakoztatott adatforrásból az objektum importintézkedésekkel. Objektumok exportálása objektumok importálása válhatnak, ha a szinkronizálási motor megkapja azokat a csatlakoztatott adatforrás a következő importálása során.

### <a name="placeholders"></a>Helyőrzői
A szinkronizálási motor névtere strukturálatlan használja az objektumok tárolására. Azonban az egyes csatlakoztatott adatforrások, például az Active Directory hierarchikus névtér használatára. Hierarchikus névtér adatait ismeretekké névtere strukturálatlan, a szinkronizálási motor használja helyőrzőket megőrzése érdekében a hierarchiában.

Minden egyes helyőrző egy objektum hierarchikus neve, amely még nincs importálva a szinkronizálási motor, de nem kell létrehozni a hierarchikus nevét (például egy szervezeti egység) összetevője. Kitöltés hozta létre a csatlakoztatott adatforrás azon objektumok, amelyek nem átmeneti objektumot az összekötőtérben hivatkozások hiányosságokat.

A szinkronizálási motor helyőrzőket is használja, amely még nem lettek importálva hivatkozott objektumok tárolásához. Például, ha a sync konfigurálva van a kezelő attribútum tartalmazza a *Abbie Spencer* objektum és a kapott értéket a rendszer olyan objektum, amely nem lett importált még, például *CN Lee Sperry, CN = Users, DC = fabrikam, DC = = com* , a kezelő adatokat tárolja az összekötőtérben helyőrzőként. Az Engedélyezéskezelő objektum később importálható, ha a rendszer felülírja a helyőrző objektum által az átmeneti képviselő objektum, amely a kezelő.

### <a name="metaverse-objects"></a>Metaverzum-objektum
A metaverzum-objektum összesített nézetet tartalmazza, amelyek a szinkronizálási motor előkészítési objektumot az összekötőtérben rendelkezik. A szinkronizálási motor metaverzum-objektum az objektumok importálása az információk alapján hoz létre. Több összekötő terület objektumok egy egyetlen metaverzum-objektum lehet kapcsolódni, de egy összekötőtér objektuma nem csatolható több metaverzum-objektum.

Metaverzum-objektum nem lehet manuálisan létrehozott vagy törölhető. A szinkronizálási motor automatikusan törli a metaverzum-objektumok, amelyek nem rendelkeznek bármely összekötőtér objektuma mutató hivatkozást az összekötőtérben.

Csatlakoztatott adatforrások belüli objektumok leképezése a metaverzumba belül a megfelelő objektumtípus, a szinkronizálási motor biztosít egy bővíthető séma típusú objektumokat és a kapcsolódó attribútumok egy előre meghatározott készletével együtt. Új objektumtípusok és a metaverzum-objektum attribútumainak hozhat létre. Lehet, hogy attribútumok egyértékű vagy többértékű, és lehet, hogy az attribútum típusát karakterláncok, hivatkozások, számok és logikai értékek.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Átmeneti és metaverzum objektumok közötti kapcsolatok
A szinkronizálási motor névtéren belül az adatok az adatfolyam szerint engedélyezve van a hivatkozás kapcsolat átmeneti és metaverzum-objektumok között. Egy átmeneti az objektum, amely kapcsolódik a metaverzum-objektum egy **illesztett objektum** (vagy **összekötő objektum**). Egy átmeneti az objektum, amely nem kapcsolódik a metaverzum-objektum egy **objektum különálló** (vagy **Terheléskapcsoló objektumok**). A feltételeket, tartományhoz csatlakoztatott és a különálló előnyben részesített nem tévesztendő össze az adatok importálása és exportálása a kapcsolt címtárban felelős összekötőket.

Helyőrzők soha nem kapcsolódnak a metaverzum-objektum

Egy csatlakoztatott objektumot magában foglalja egy átmeneti objektum és a egy egyetlen metaverzum-objektumhoz társított kapcsolatban. Csatlakoztatott objektumainak szinkronizálása egy összekötőtér objektuma és a metaverzum-objektum közötti attribútumértékek szolgálnak.

Amikor egy előkészítési objektumot egy csatlakoztatott objektumot a szinkronizálás során, attribútumok áramolhasson az átmeneti tárolási objektum és a metaverzum-objektum között. Attribútumfolyam kétirányú és importálási attribútum és az exportálási attribútum szabályok használatával van konfigurálva.

Egy egyetlen összekötőtér objektuma csak egyetlen metaverzum-objektum lehet kapcsolódni. Azonban minden egyes metaverzum-objektum lehet kapcsolódni több összekötő terület objektum azonos vagy eltérő összekötőterek, az alábbi ábrán látható módon.

![Arch5](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Az előkészítési objektum és a metaverzum-objektum közötti társított kapcsolat állandó, és csak Ön által megadott szabályok szerint távolíthatja el.

Egy kilépett objektum egy előkészítési objektumot, amely nem kapcsolódik semmilyen metaverzum-objektum. Az attribútum-objektum összekapcsolható értékei nem dolgozza fel, minden további, a metaverzumba belül. Az attribútum értékeket a megfelelő a csatlakoztatott adatforrás-objektum nem frissülnek a szinkronizálási motor.

Összekapcsolható-objektumok használatával azonosító adatok tárolása a szinkronizálási motor, és később feldolgozni azt. Tartása egy előkészítési objektumot az összekötőtérben kilépett objektumként számos előnnyel jár. A rendszer már rendelkezik előkészített ezt az objektumot a szükséges információkat, mert nem elengedhetetlen való létrehozása során a következő importálási újra ez az objektum reprezentációját a csatlakoztatott adatforrásból. Ezzel a módszerrel a szinkronizálási motor mindig rendelkezik egy teljes pillanatképet a csatlakoztatott adatforráshoz, akkor is, ha ez nem aktuális a csatlakoztatott adatforráshoz. Összekapcsolható objektumok alakítható csatlakoztatott objektumainak és fordítva, attól függően, az Ön által megadott szabályok.

Az import-objektum összekapcsolható objektumként jön létre. Exportálás objektum egy csatlakoztatott objektumnak kell lennie. A rendszer logikai Ez a szabály kikényszeríti, és töröl minden kiviteli objektum, amely nem egy csatlakoztatott objektum.

## <a name="sync-engine-identity-management-process"></a>Szinkronizálási motor identity management folyamat
Az identity management folyamat azt szabályozza, hogy hogyan frissül a azonosító adatok különböző csatlakoztatott adatforrások között. Identitáskezelés három folyamat történik:

* Importálás
* Szinkronizálás
* Exportálás

Az importálási folyamat során a szinkronizálási motor kiértékeli a bejövő azonosító adatokat a csatlakoztatott adatforrás. Észlel, amikor, vagy új előkészítési objektumot hoz létre vagy frissíti a meglévő átmeneti objektumot az összekötőtérben, a szinkronizáláshoz.

A szinkronizálási folyamat során a szinkronizálási motor a metaverzumba az összekötőtérben történt változásainak frissíti, és frissíti az összekötőtérben, hogy a metaverzumban bekövetkezett változásokat.

Az exportálási folyamat során a szinkronizálási motor terjesztett módosítások, amelyek elő van készítve az objektumok fejlesztői és, hogy a függő exportálás vannak megjelölve.

Az alábbi ábrán látható, a folyamatok egyes akkor fordul elő, identitás információáramlás, egy csatlakoztatott adatforrásból egy másikba.

![Arch6](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Importálási folyamat
Az importálási folyamat során a szinkronizálási motor kiértékeli az azonosító adatok frissítéseit. A szinkronizálási motor összehasonlítja az azonosító adatokat a csatlakoztatott adatforrás a identitás információkat egy előkészítési objektumot kapott, és határozza meg, hogy szükséges-e az átmeneti tárolási objektum frissítései. Ha az átmeneti tárolási objektum frissítése az új adatokat, az előkészítési objektum meg van jelölve, függőben lévő importálás.

Előkészítési objektumot az összekötőtérben szinkronizálás előtt, amelyet a szinkronizálási motor csak a megváltozott azonosító adatok feldolgozhatja. Ez a folyamat az alábbi előnyöket nyújtja:

* **Hatékony szinkronizálási**. A szinkronizálás során feldolgozott adatok mennyisége másodpercekre csökken.
* **Hatékony újraszinkronizálás**. Módosíthatja, miként szinkronizálási motor dolgozza fel azonosító adatokat anélkül, hogy a szinkronizálási motor az adatforráshoz történő újracsatlakozás.
* **Szinkronizálási kipróbálásában való**. Megtekintheti, hogy ellenőrizze, hogy helyesek-e az identitás-kezelési folyamat a feltételezéseket-szinkronizálás.

A szinkronizálási motor minden objektumhoz, az összekötő a megadott, először megpróbálja megkeresni a reprezentációja az objektumot az összekötőtérben, az összekötő. A szinkronizálási motor megvizsgálja az összes előkészítési objektumot az összekötőtérben, és próbál meg megtalálni egy megfelelő átmeneti objektum, amely rendelkezik egy megfelelő a forráshorgony attribútuma. Ha nincs meglévő átmeneti objektum nem a megfelelő a forráshorgony attribútuma, a szinkronizálási motor megpróbálja megkeresni a megfelelő átmeneti objektumokat megkülönböztető ugyanazzal a névvel.

Ha a szinkronizálási motor egy előkészítési objektumot, amely megfelel a megkülönböztető név alapján, de nem a forráshorgony talál, a következő különleges történik:

* Ha az objektumot az összekötőtérben található nincs kapcsolati rendelkezik, akkor a szinkronizálási motor eltávolítja ezt az objektumot az összekötőtérben, és a metaverzum-objektum, csatolt jelöli **ismételje meg a következő szinkronizálás futtatása a kiépítés**. Ezután az importálás új objektumot hoz létre.
* Ha az objektumot az összekötőtérben található horgonyra, majd a szinkronizálási motor feltételezi, hogy ez az objektum van vagy átnevezték vagy törölték a csatlakoztatott címtárban. Az összekötőtér objektuma ideiglenes, új megkülönböztető nevét, hogy azt úgy végezheti el a bejövő objektum internetzónához. A régi objektum válik **átmeneti**, Várakozás az összekötő importálja a átnevezése vagy törlése elhárításához.

A szinkronizálási motor megkeresi az objektumhoz, az összekötő a megadott egy előkészítési objektumot, ha meghatározza, hogy milyen típusú módosításokat a alkalmazni. Például a szinkronizálási motor előfordulhat, hogy nevezze át vagy törölje az objektumot a csatlakoztatott adatforrás, vagy csak, előfordulhat, hogy frissítse az objektum attribútumértékek.

A frissített adatok átmeneti objektumok importálása folyamatban vannak megjelölve. Különböző típusú függőben lévő importálás érhetők el. Az importálási folyamat eredményétől függően egy előkészítési objektumot az összekötőtérben van függőben lévő importálás típusok a következők egyikét:

* **Nincs**. Sem az átmeneti tárolási objektum attribútumainak módosítása nélkül érhető el. A szinkronizálási motor nem jelző ilyen módon függőben lévő importálás.
* **Adjon hozzá**. Az előkészítési objektum egy új importálás objektumot az összekötőtérben. A szinkronizálási motor megőrzendő tartalomként jelöli meg a típus, függőben lévő importálás metaverzumban található további feldolgozás céljából.
* **Frissítés**. A szinkronizálási motor megkeresi a megfelelő átmeneti objektumot az összekötőtérben, és a függőben lévő importálás típusként megőrzendő tartalomként jelöli meg, hogy az attribútumok frissítései dolgozhatók fel a metaverzumba. Frissítések tartalmazza az objektum átnevezését.
* **Törlés**. A szinkronizálási motor megkeresi a megfelelő átmeneti objektumot az összekötőtérben, és a függőben lévő importálás típusként megőrzendő tartalomként jelöli meg, hogy a csatlakoztatott objektum törölhetők.
* **Törlés és hozzáadása**. A szinkronizálási motor megkeresi a megfelelő átmeneti objektumokat az összekötőtérben, de az objektum típusa nem egyezik. Ebben az esetben a törlés – hozzáadása módosítás elő van készítve. A törlés – hozzáadása a szinkronizálási motor módosítása azt jelzi, hogy az objektum teljes újraszinkronizálását kell fordul elő, mert más-más részhalmazához szabályok vonatkoznak ezt az objektumot adja meg az objektum módosításokat.

Átmeneti objektumok függőben lévő importálás állapotát beállításával, mert ezzel tehát lehetővé teszi, hogy a rendszer csak azokat az objektumokat, amely rendelkezik a módosított adatokat feldolgozza a szinkronizálás során feldolgozott adatok mennyisége jelentős csökkentése érdekében.

### <a name="synchronization-process"></a>Szinkronizálási folyamat
Szinkronizálás két kapcsolódó folyamatok áll:

* Bejövő szinkronizálás, a metaverzumba tartalmának frissítésekor az összekötőtérben adatok felhasználásával.
* Kimenő szinkronizálási, az összekötőtérben tartalmának frissítésekor metaverzumban található adatok használatával.

Az összekötőtérben előkészített információk segítségével a bejövő szinkronizálási folyamat hoz létre a metaverzumba az adatokat a csatlakoztatott adatforrások tárolt, az integrált áttekintése. Az összes előkészítési objektumok vagy csak azokat a függőben lévő importálás adatokkal vannak összesítve, a szabály konfigurációjától függően.

A kimenő szinkronizálási folyamat frissítések objektumok exportálása, amikor metaverzum-objektum módosítása.

Bejövő szinkronizálási az integrált nézet az azonosító adatokat a csatlakoztatott adatforrásokból származó fogadott metaverzumban található hoz létre. A szinkronizálási motor azonosító adatok bármikor feldolgozhatja a legújabb azonosító adatokat, hogy a csatlakoztatott adatforrás használatával.

**Bejövő szinkronizálás**

Bejövő szinkronizálási tartalmazza a következő eljárásokat:

* **Kiépítés** (más néven **leképezése** Ha fontos különbséget tenni ezt a folyamatot a kimenő szinkronizálási létesítési). A szinkronizálási motor hoz létre egy új metaverzum-objektum alapú előkészítési objektum, és összekapcsolja őket. Ennek megfelelő ítélet objektumszintű művelet.
* **Csatlakozás**. A szinkronizálási motor egy átmeneti objektumra hivatkozik, egy meglévő metaverzum-objektum. Egy összekapcsolás objektumszintű művelet.
* **Attribútumfolyam importálása**. A szinkronizálási motor frissíti az attribútumértékek Attribútumfolyam, az a metaverzum-objektum neve. Importálási Attribútumfolyam egy attribútum-szintű művelet, amely egy előkészítési objektum és a metaverzum-objektum közötti kapcsolat szükséges.

Ennek megfelelő ítélet metaverzumban található objektumokat hoz létre egyetlen folyamat. Üzembe helyezése csak a kilépett objektumok importálása objektumok van hatással. Során kiépítését a szinkronizálási motor létrehozza a metaverzum-objektum, amely felel meg az objektumtípus az importálás objektum, és mindkét objektum, így a csatlakoztatott objektum létrehozása közötti kapcsolatot létesít.

Az illesztési folyamat is létrehoz az objektumok importálása és a metaverzum-objektum közötti kapcsolat. Csatlakozási és üzembe helyezése közötti különbség, hogy a join a folyamathoz az szükséges, hogy az importálás objektum kapcsolódik-e egy meglévő metaverzum-objektum, ahol a kiépítés folyamat hoz létre egy új metaverzum-objektum.

A szinkronizálási motor megkísérli a metaverzum-objektum importálási objektum csatlakoztassa a szinkronizálási szabály konfigurációjában megadott feltételek alapján.

A kiépítés és illesztési folyamatok során a szinkronizálási motor kilépett objektum hivatkozik, a metaverzum-objektum, így azok csatlakoztatott. E objektumszintű műveletek elvégzése után a szinkronizálási motor frissítheti a társított metaverzum-objektum attribútum értékét. Ez a folyamat importálási Attribútumfolyam nevezzük.

Importálási Attribútumfolyam összes objektumok importálása, amelyek új adatokat, és a metaverzum-objektum csatolását történik.

**Kimenő szinkronizálásba**

Kimenő szinkronizálási frissítéseket objektumok exportálása, amikor metaverzum-objektum módosítása, de nem törlődnek. Kimenő szinkronizálási célja meghatározni, hogy e módosítások metaverzum-objektumhoz a összekötőterek átmeneti objektumok frissítés szükséges. Bizonyos esetekben a módosítások megkövetelheti, hogy átmeneti összes összekötőterek objektumokat frissíteni kell. Átmeneti objektumok, amelyek módosítják az export, így azok az objektumok exportálása folyamatban vannak megjelölve. Ezek az exportálási objektumokat a rendszer később elvégzi módosítását a csatlakoztatott adatforrás az exportálási folyamat során.

Kimenő szinkronizálási három folyamat rendelkezik:

* **Kiépítés**
* **A megszüntetés**
* **Attribútumfolyam exportálása**

Kiépítés és megszüntetés mindkét objektumszintű műveletek, amelyek. Megszüntetés attól függ, hogy csak a kiépítés is kezdeményezhető, mivel kiépítése. Megszüntetés akkor aktiválódik, amikor a kiépítés eltávolítja a metaverzum-objektum és a egy exportálási objektum közötti kapcsolat.

Kiépítés mindig aktiválódik, ha a módosítások érvénybe lépnek a metaverzumban található objektumok. Módosítások metaverzum-objektum, amikor a szinkronizálási motor hajthatja végre az alábbi műveletek bármelyikét a kiépítési folyamat részeként:

* Hozzon létre a csatlakoztatott objektumainak, ahol a metaverzum-objektum egy újonnan létrehozott exportálási objektum kapcsolódik.
* Nevezze át a csatlakoztatott objektum.
* Tartományról való leválasztás metaverzum-objektum és az objektumok létrehozása kilépett objektum közötti hivatkozásokat.

Szükséges, hozzon létre egy új összekötő-objektumot a szinkronizálási motor, az átmeneti tárolási objektum, amely a metaverzum-objektum kapcsolódik-e mindig exportálási objektumot, mert az objektum még nem létezik a csatlakoztatott adatforrás.

Megszüntetés akkor aktiválódik, ha szükséges, hogy tartományról való leválasztás egy csatlakoztatott objektumot, egy kilépett objektum létrehozása a szinkronizálási motor. A megszüntetési folyamatot törli az objektumot.

Megszüntetés során exportálási objektum törlése nem fizikailag törli az objektumot. Az objektum van megjelölt **törölt**, ami azt jelenti, hogy a törlési művelet lesz ütemezve, hogy az objektum.

Attribútumfolyam exportálása is hasonló módon, hogy az importálási Attribútumfolyam következik-e bejövő szinkronizálási kimenő szinkronizálási folyamat során fordul elő. Attribútumfolyam exportálása csak a tartományhoz csatlakoztatott metaverzum és exportálási objektumok között történik.

### <a name="export-process"></a>Folyamat exportálása
Az exportálási folyamat során a szinkronizálási motor megvizsgálja az összes objektumok exportálása exportálási az összekötőtérben függőben vannak megjelölve, és ezután elküldi a frissítéseket a csatlakoztatott adatforráshoz.

A szinkronizálási motor megadhatja, hogy az exportálás sikeres, de ez nem elég, hogy az identity management folyamat befejeződésének megállapítása. A csatlakoztatott adatforrás objektumok mindig módosítható úgy, hogy másik folyamat. A szinkronizálási motor nincs állandó kapcsolatot a csatlakoztatott adatforráshoz, mert nincs elegendő ahhoz, hogy az objektum tulajdonságainak feltételezéseket végezze el a csatlakoztatott adatforráshoz, az Exportálás értesítést alapján.

Például egy folyamatot a csatlakoztatott adatforrás sikerült módosítani a objektumattribútumok vissza az eredeti értékekre (azt jelenti, a csatlakoztatott adatforrás felülírhatja az értékeket az adatok a szinkronizálási motor által leküldött és sikeresen alkalmazva után azonnal a csatlakoztatott adatforrás).

A szinkronizálási motor tárolók exportálása, és minden átmeneti tárolási objektum állapotát adatainak importálása. Attribútumának értéke a megadott befoglalási szereplő legutóbbi exportálás óta módosult, ha a importálása és exportálása állapot lehetővé teszi, hogy a szinkronizálási motor megfelelően reagálhasson a. A szinkronizálási motor a csatlakoztatott adatforrás exportált attribútumértékek erősítse meg az importálási folyamat használja. Az importált és az exportált adatokat összehasonlítása az alábbi ábrán látható módon lehetővé teszi a szinkronizálási motor határozza meg a sikeres volt-e az exportálás, vagy ha kell ismételni.

![Arch7](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Például ha a szinkronizálási motor exportálja értéke 5, a csatlakoztatott adatforráshoz, C, a attribútum tárolja C = 5 az Exportálás állapota memória. Minden további exportálási ezt az objektumot exportálhat C = 5 a csatlakoztatott adatforrás újra, mert a szinkronizálási motor azt feltételezi, hogy ez az érték nem állandó telepítve van az objektum kísérlet eredményez (azt jelenti, kivéve, ha egy másik értéket nemrég lett importálva a csatlakoztatott adatforrás). Az exportálási memória nincs bejelölve, C = 5 érkezésekor az objektum az importálási művelet során.

## <a name="next-steps"></a>További lépések
Tudjon meg többet a [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációja.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

