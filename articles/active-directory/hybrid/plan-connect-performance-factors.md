---
title: Az Azure AD Connect teljesítményét befolyásoló tényezők
description: Ez a dokumentum ismerteti, hogy a különböző tényezők hogyan befolyásolják az Azure AD Connect kiépítési motor. Ezek a tényezők segítenek a szervezeteknek megtervezni az Azure AD Connect üzembe helyezését, hogy megbizonyosodjanak arról, hogy azok megfelelnek a szinkronizálási követelményeknek.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5518d516848ba7c006827faa41ff76bbca35d0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897056"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Az Azure AD Connect teljesítményét befolyásoló tényezők

Az Azure AD Connect szinkronizálja az Active Directoryt az Azure AD-vel. Ez a kiszolgáló a felhasználói identitások felhőbe való áthelyezésének kritikus eleme. Az Azure AD Connect teljesítményét befolyásoló elsődleges tényezők a következők:

| **Tervezési tényező**| **Meghatározás** |
|:-|-|
| Topológia| A végpontok és összetevők elosztása az Azure AD Connect kell kezelni a hálózaton. |
| Méretezés| Az Azure AD Connect által kezelendő objektumok, például a felhasználók, csoportok és a számítógép-kezelők száma. |
| Hardver| Az Azure AD Connect hardvere (fizikai vagy virtuális) és az egyes hardverösszetevők – beleértve a processzort, a memóriát, a hálózatot és a merevlemez-konfigurációt is – függő teljesítménykapacitása. |
| Konfiguráció| Hogyan dolgozza fel az Azure AD Connect a könyvtárakat és az információkat? |
| Betöltés| Az objektumváltozások gyakorisága. A terhelés ekként, naponta vagy héten is változhatnak. Az alkatrésztől függően előfordulhat, hogy a csúcsterhelést vagy az átlagos terhelést kell terveznie. |

Ez a dokumentum célja, hogy leírja az Azure AD Connect kiépítési motor teljesítményét befolyásoló tényezőket. Nagy vagy összetett szervezetek (több mint 100 000 objektumot létesítő szervezetek) a javaslatok segítségével optimalizálhatják az Azure AD Connect-implementációjukat, ha az itt ismertetett teljesítményproblémákat tapasztalják. Az Azure AD Connect egyéb összetevői, például az [Azure AD Connect állapota](how-to-connect-health-agent-install.md) és az ügynökök nem tartoznak ide.

> [!IMPORTANT]
> A Microsoft nem támogatja az Azure AD Connect módosítását vagy üzemeltetését a hivatalosan dokumentált műveleteken kívül. Ezen műveletek bármelyike az Azure AD Connect szinkronizálásának inkonzisztens vagy nem támogatott állapotát eredményezheti. Ennek eredményeképpen a Microsoft nem tud technikai támogatást nyújtani az ilyen telepítésekhez.

## <a name="azure-ad-connect-component-factors"></a>Az Azure AD Connect összetevőtényezői

Az alábbi ábrán egy erdőhöz csatlakozó motor kiépítésének magas szintű architektúrája látható, bár több erdő támogatott. Ez az architektúra bemutatja, hogy a különböző összetevők hogyan hatnak egymásra.

![AzureADConnent Belső](media/plan-connect-performance-factors/AzureADConnentInternal.png)

A létesítési motor minden Egyes Active Directory-erdőhöz és az Azure AD-hez csatlakozik. Az egyes könyvtárakból származó információk olvasásának folyamatát Importálásnak nevezzük. Az exportálás a könyvtárak nak a létesítési motorból való frissítésére vonatkozik. A Szinkronizálás kiértékeli az objektumok kiépítési motoron belüli áramlásának szabályait. Egy mélyebb merülés, akkor tekintse meg [az Azure AD Connect szinkronizálás: Az architektúra ismertetése.](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)

Az Azure AD Connect a következő átmeneti területeket, szabályokat és folyamatokat használja az Active Directory és az Azure AD szinkronizálásának engedélyezéséhez:

* **Összekötő tér (CS)** - Objektumok minden csatlakoztatott könyvtár (CD), a tényleges könyvtárak, a szakaszozott itt először, mielőtt feldolgozhatja a létesítő motor. Az Azure AD saját CS-vel rendelkezik, és minden egyes erdő, amelyhez csatlakozik, saját CS-vel rendelkezik.
* **Metaverzum (MV)** – A szinkronizálandó objektumok itt jönnek létre a szinkronizálási szabályok alapján. Az objektumoknak létezniük kell az MV-ben, mielőtt feltöltenék az objektumokat és attribútumokat a többi csatlakoztatott könyvtárba. Csak egy MV van.
* **Szinkronizálási szabályok** – Ők döntik el, hogy mely objektumok jönnek létre (vetítve) vagy kapcsolódnak (csatlakoznak) az MV-ben lévő objektumokhoz. A szinkronizálási szabályok azt is eldöntik, hogy mely attribútumértékeket másolja vagy alakítja át a könyvtárakba és a könyvtárakból.
* **Profilok futtatása** – Az objektumok és attribútumértékeik másolásának folyamatlépéseit az átmeneti területek és a kapcsolódó könyvtárak közötti szinkronizálási szabályok nak megfelelően kötegeli.

Különböző futtatási profilok léteznek a kiépítési motor teljesítményének optimalizálásához. A legtöbb szervezet az alapértelmezett ütemezéseket használja, és profilokat futtat a normál műveletekhez, de előfordulhat, hogy egyes szervezeteknek módosítaniuk kell [az ütemezést,](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) vagy más futtatási profilokat kell kiváltaniuk a nem gyakori helyzetek levárható lesteléséhez. A következő futtatási profilok érhetők el:

### <a name="initial-sync-profile"></a>Kezdeti szinkronizálási profil

A kezdeti szinkronizálási profil a csatlakoztatott könyvtárak , például az Active Directory-erdő első olvasási folyamata. Ezután elemzést végez a szinkronizálási motor adatbázisának összes bejegyzéséről. A kezdeti ciklus új objektumokat hoz létre az Azure AD-ben, és több időt vesz igénybe, ha az Active Directory-erdők nagyok. A kezdeti szinkronizálás a következő lépéseket tartalmazza:

1. Teljes importálás az összes csatlakozón
2. Teljes szinkronizálás az összes összekötőn
3. Exportálás az összes összekötőn

### <a name="delta-sync-profile"></a>Különbözeti szinkronizálási profil

A szinkronizálási folyamat optimalizálásához ez a futtatási profil csak a csatlakoztatott könyvtárakobjektumainak módosításait (létrehozása, törlése és frissítése) dolgozza fel az utolsó szinkronizálási folyamat óta. Alapértelmezés szerint a különbözeti szinkronizálási profil 30 percenként fut. A szervezeteknek arra kell törekedniük, hogy a 30 perc alatt tartsák az időt, és győződjön meg arról, hogy az Azure AD naprakész. Az Azure AD Connect állapotának figyeléséhez használja az [állapotfigyelő ügynök](how-to-connect-health-sync.md) a folyamattal kapcsolatos problémák megtekintéséhez. A különbözeti szinkronizálási profil a következő lépéseket tartalmazza:

1. Különbözeti importálás az összes összekötőn
2. Különbözeti szinkronizálás az összes összekötőn
3. Exportálás az összes összekötőn

Egy tipikus vállalati szervezet különbözeti szinkronizálási forgatókönyv:

- Az objektumok ~1%-a törlődik
- ~1%-a objektumok jönnek létre
- Az objektumok ~5%-a módosul

A módosítás mértéke attól függően változhat, hogy a szervezet milyen gyakran frissíti a felhasználókat az Active Directory címtárban. Például magasabb változási arányok fordulhatnak elő a munkaerő-felvétel szezonalitását és a munkaerő csökkentését.

### <a name="full-sync-profile"></a>Teljes szinkronizálási profil

Ha az alábbi konfigurációs módosítások valamelyikét végrehajtotta, teljes szinkronizálási ciklusra van szükség:



- Megnövelte a csatlakoztatott könyvtárakból importálandó objektumok vagy attribútumok hatókörét. Ha például hozzáad egy tartományt vagy szervezeti egységet az importálási hatókörhöz.
- Módosította a szinkronizálási szabályokat. Ha például létrehoz egy új szabályt, amely feltölti a felhasználó címét az Azure AD-ben az Active Directoryban extension_attribute3. Ez a frissítés megköveteli, hogy a létesítési motor újra megvizsgálja az összes meglévő felhasználó tikafrissítése a címeket, hogy alkalmazza a változás megy előre.

A teljes szinkronizálási ciklus a következő műveleteket tartalmazza:

1. Teljes importálás az összes csatlakozón
2. Teljes/különbözeti szinkronizálás az összes összekötőn
3. Exportálás az összes összekötőn

> [!NOTE]
> Gondos tervezésszükséges, ha az Active Directory vagy az Azure AD számos objektumának tömeges frissítését végzi. A tömeges frissítések hatására a különbözeti szinkronizálási folyamat importálása hosszabb időt vesz igénybe, mivel sok objektum megváltozott. Hosszú importálás akkor is előfordulhat, ha a tömeges frissítés nem befolyásolja a szinkronizálási folyamatot. Például licencek hozzárendelése az Azure AD-ben sok felhasználó hoz egy hosszú importálási ciklust az Azure AD-ből, de nem eredményez attribútumváltozásokat az Active Directoryban.

### <a name="synchronization"></a>Szinkronizálás

A szinkronizálási folyamat futásideje a következő teljesítményjellemzőkkel rendelkezik:

* A szinkronizálás egyszálas, ami azt jelenti, hogy a kiépítési motor nem végez párhuzamos feldolgozást a csatlakoztatott könyvtárak, objektumok vagy attribútumok futtatási profiljainak.
* Az importálási idő lineárisan növekszik a szinkronizált objektumok számával. Ha például 10 000 objektum importálása 10 percet vesz igénybe, akkor 20 000 objektum körülbelül 20 percet vesz igénybe ugyanazon a kiszolgálón.
* Az exportálás is lineáris.
* A szinkronizálás exponenciálisan növekszik a más objektumokra hivatkozó objektumok száma alapján. A csoporttagságok és a beágyazott csoportok a teljesítmény reklálására gyakorolt fő hatást, mivel tagjaik felhasználói objektumokra vagy más csoportokra hivatkoznak. Ezeket a hivatkozásokat meg kell találni, és hivatkozni kell a tényleges objektumokra az MV-ben a szinkronizálási ciklus befejezéséhez.

### <a name="filtering"></a>Szűrés

Az importálni kívánt Active Directory-topológia mérete az első számú tényező, amely befolyásolja a teljesítményt és a teljes időt, amelyet a létesítő motor belső összetevői igénybe vesznek.

[A szűrést](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) az objektumok szinkronizáltra való csökkentésére kell használni. Ez megakadályozza a felesleges objektumok feldolgozását és exportálását az Azure AD-be. A preferenciák sorrendjében a következő szűrési technikák állnak rendelkezésre:



- **Tartományalapú szűrés** – ezzel a beállítással kiválaszthatja az Azure AD-vel szinkronizálandó adott tartományokat. Az Azure AD Connect-szinkronizálás telepítése után hozzá kell adnia és el kell távolítania a tartományokat a szinkronizálási motor konfigurációjából, amikor módosítja a helyszíni infrastruktúrát.
- **Szervezeti egység (OU) szűrése** – szervezeti egységek használatával az Active Directory-tartományok adott objektumait célozza meg az Azure AD-be való kiépítéshez. A szervezeti egység szűrése a második ajánlott szűrési mechanizmus, mivel egyszerű LDAP-hatókör-lekérdezéseket használ az objektumok kisebb részhalmazának importálásához az Active Directoryból.
- **Attribútumszűrés objektumonként** – az objektumok attribútumértékeit használva eldönti, hogy az Active Directory ban adott objektum ki van-e építve az Azure AD-ben. Az attribútumszűrés kiválóan alkalmas a szűrők finomhangolására, ha a tartomány- és szervezetiegység-szűrés nem felel meg az adott szűrési követelményeknek. Az attribútumszűrés nem csökkenti az importálási időt, de csökkentheti a szinkronizálási és exportálási időket.
- **Csoportalapú szűrés** – csoporttagság használatával dönti el, hogy az objektumokat ki kell-e építeni az Azure AD-ben. A csoportalapú szűrés csak tesztelési helyzetekben alkalmas, és nem ajánlott éles környezetben, mivel a szinkronizálási ciklus során a csoporttagság ellenőrzéséhez szükséges többletterhelés szükséges.

Az Active Directory CS-ben számos állandó [leválasztó objektum](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) hosszabb szinkronizálási időt okozhat, mivel a kiépítési motornak újra kell értékelnie minden egyes leválasztó objektumot a szinkronizálási ciklusban való lehetséges kapcsolat érdekében. A probléma megoldásához vegye figyelembe az alábbi ajánlások egyikét:



- Helyezze a leválasztó objektumokat a tartomány vagy szervezeti egység szűrése segítségével történő importálás hatókörén kívülre.
- Projekt/csatlakozzon az objektumokat az MV-hez, és állítsa be a [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) attribútum értéke igaz, hogy megakadályozzák ezek az objektumok kiépítése az Azure AD CS-ben.

> [!NOTE]
> A felhasználók összezavarodhatnak, vagy alkalmazásengedélyekkel kapcsolatos problémák léphetnek fel, ha túl sok objektum van szűrve. Egy hibrid Exchange online implementációban például a helyszíni postaládával rendelkező felhasználók több felhasználót látnak a globális címlistában, mint azok, akik postaládával rendelkezők az Exchange online-ban. Más esetekben előfordulhat, hogy a felhasználó hozzáférést szeretne adni egy felhőalapú alkalmazásban egy másik felhasználónak, amely nem része a szűrt objektumkészlet hatókörének.

### <a name="attribute-flows"></a>Attribútumfolyamatok

Az attribútumfolyamatok az objektumok attribútumértékeinek egyik csatlakoztatott könyvtárból a másikba történő másolására vagy átalakítására szolgáló folyamat. Ezek a szinkronizálási szabályok részeként vannak definiálva. Ha például egy felhasználó telefonszáma megváltozik az Active Directoryban, az Azure AD-ben lévő telefonszám frissül. A szervezetek [módosíthatják az attribútumfolyamatokat](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration) a suite különböző követelményekhez. Javasoljuk, hogy a meglévő attribútumfolyamatok másolása a módosítás előtt.

Az egyszerű átirányítások, például egy attribútumérték egy másik attribútumba való átfolyása nem befolyásolja a teljesítményre gyakorolt hatást. Egy átirányítás egy mobilszámot az Active Directoryban az Office-telefonszám az Azure AD-ben.

Az attribútumértékek átalakítása hatással lehet a szinkronizálási folyamatra. Az attribútumértékek átalakítása magában foglalja az attribútumok értékeinek módosítását, újraformázását, összefűzését vagy kivonását.

A szervezetek megakadályozhatják, hogy bizonyos attribútumok az Azure AD-be kerüljenek, de ez nem befolyásolja a létesítési motor teljesítményét.

> [!NOTE]
> Ne törölje a nem kívánt attribútumfolyamatokat a szinkronizálási szabályokban. Javasoljuk, hogy inkább tiltsa le őket, mert a törölt szabályok újra létre az Azure AD Connect frissítések során.

## <a name="azure-ad-connect-dependency-factors"></a>Az Azure AD Connect függőségi tényezői

Az Azure AD Connect teljesítménye az általa behozott és exportált csatlakoztatott könyvtárak teljesítményététől függ. Például az Importálandó Active Directory mérete vagy a hálózati késés az Azure AD szolgáltatásba. A kiépítési motor által használt SQL-adatbázis is hatással van a szinkronizálási ciklus általános teljesítményére.

### <a name="active-directory-factors"></a>Active Directory-tényezők

Ahogy korábban említettük, az importálandó objektumok száma jelentősen befolyásolja a teljesítményt. Az [Azure AD Connect hardvere és előfeltételei](how-to-connect-install-prerequisites.md) a központi telepítés mérete alapján ismertetik a hardverszinteket. Az Azure AD Connect csak az Azure [AD Connect topológiáiban](plan-connect-topologies.md)ismertetett speciális topológiákat támogatja. Nincsenek teljesítményoptimalizálások és nem támogatott topológiákra vonatkozó javaslatok.

Győződjön meg arról, hogy az Azure AD Connect-kiszolgáló megfelel az importálni kívánt Active Directory-méret alapján a hardverkövetelményeknek. Az Azure AD Connect kiszolgáló és az Active Directory tartományvezérlők közötti rossz vagy lassú hálózati kapcsolat lelassíthatja az importálást.

### <a name="azure-ad-factors"></a>Az Azure AD-tényezői

Az Azure AD szabályozással védi a felhőszolgáltatást a szolgáltatásmegtagadási (DoS) támadások ellen. Jelenleg az Azure AD-nek 5 percenként 7000 írási határa van (84 000 óránként). Például a következő műveletek szabályozhatók:



- Az Azure AD Connect exportálása az Azure AD-be.
- PowerShell-parancsfájlok vagy alkalmazások frissítése az Azure AD közvetlenül a háttérben, például a dinamikus csoporttagságok.
- A felhasználók frissítik saját identitásrekordjaikat, például az MFA vagy az SSPR (önkiszolgáló jelszó-visszaállítás) regisztrálását.
- Műveletek a grafikus felhasználói felületen belül.

Tervezze meg az üzembe helyezési és karbantartási feladatokat, hogy az Azure AD Connect szinkronizálási ciklusát ne befolyásolják a sávszélesség-szabályozási korlátok. Ha például van egy nagy felvételi hullám, ahol több ezer felhasználói identitást hoz létre, a dinamikus csoporttagságok, a licencelési hozzárendelések és az önkiszolgáló jelszó-visszaállítási regisztrációk frissítései. Jobb, ha ezeket az írásokat több órára vagy néhány napra terjesztjük.

### <a name="sql-database-factors"></a>SQL-adatbázis-tényezők

A forrás Active Directory topológiájának mérete befolyásolja az SQL-adatbázis teljesítményét. Kövesse az SQL-kiszolgáló adatbázisának [hardverkövetelményeit,](how-to-connect-install-prerequisites.md) és vegye figyelembe az alábbi javaslatokat:



- A 100 000-nél több felhasználóval rendelkező szervezetek csökkenthetik a hálózati késéseket az SQL-adatbázis és a kiépítési motor ugyanazon a kiszolgálón történő lefoglalásával.
- A szinkronizálási folyamat magas lemezbeviteli és -kimeneti (I/O-) követelményei miatt használja a Szilárdtest-meghajtókat (SSD) a kiépítési motor SQL-adatbázisához az optimális eredmény érdekében, ha ez nem lehetséges, fontolja meg raid 0 vagy RAID 1 konfigurációk használatát.
- Ne végezze el a teljes szinkronizálást megelőzően; szükségtelen lemorzsolódást és lassabb válaszidőt okoz.

## <a name="conclusion"></a>Összegzés

Az Azure AD Connect-implementáció teljesítményének optimalizálásához vegye figyelembe az alábbi javaslatokat:



- Használja az [ajánlott hardverkonfiguráció](how-to-connect-install-prerequisites.md) t az Azure AD Connect-kiszolgáló megvalósítási mérete alapján.
- Amikor az Azure AD Connectet nagyméretű üzemelő példányokban frissíti, fontolja meg [a swing-áttelepítési módszer](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration)használatát, hogy a lehető legkevesebb állásidőt és megbízhatóságot biztosítsa. 
- A legjobb írási teljesítmény érdekében az SQL-adatbázishoz ssd-t használjon.
- Az Active Directory-hatókör szűrése csak olyan objektumokat tartalmazzon, amelyeket az Azure AD-ben kell kiépíteni, tartomány, szervezeti egység vagy attribútumszűrés használatával.
- Ha módosítania kell az alapértelmezett attribútumfolyamat-szabályokat, először másolja a szabályt, majd módosítsa a másolatot, és tiltsa le az eredeti szabályt. Ne felejtse el újrafuttatni a teljes szinkronizálást.
- Tervezzen megfelelő időt a kezdeti teljes szinkronizálási futtatási profilhoz.
- Törekedjen a delta szinkronizálási ciklus befejezésére 30 perc alatt. Ha a különbözeti szinkronizálási profil 30 percen belül nem fejeződik be, módosítsa az alapértelmezett szinkronizálási gyakoriságot úgy, hogy tartalmazza a teljes különbözeti szinkronizálási ciklust.
- Az [Azure AD Connect szinkronizálási állapotának](how-to-connect-health-agent-install.md) figyelése az Azure AD-ben.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
