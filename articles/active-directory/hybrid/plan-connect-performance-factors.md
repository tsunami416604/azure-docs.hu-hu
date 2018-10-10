---
title: Az Azure AD Connect teljesítményét befolyásoló tényezők
description: Ez a dokumentum ismerteti a különféle tényezők befolyásolják a motor kiépítése az Azure AD Connect. Ezek a tényezők segít a szervezetek számára, hogy azok az Azure AD Connect telepítésének megtervezése, hogy megfelelnek-e a szinkronizálási követelményeknek.
services: active-directory
author: billmath
manager: mtillman
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.openlocfilehash: 6ad8e04a3cd61061b44ca9b6a216f92d69a70f6b
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902994"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Az Azure AD Connect teljesítményét befolyásoló tényezők

Az Azure AD Connect szinkronizálja az Active Directory, az Azure ad-hez. Ez a kiszolgáló a felhasználói identitások a felhőbe való áthelyezés kritikus összetevője. Az Azure AD Connect teljesítményét befolyásoló legfontosabb tényezők a következők:

| **Tervezési tényező**| **Definíció** |
|:-|-|
| Topológia| A végpontok és -összetevők eloszlása az Azure AD Connect kell kezelni a hálózaton. |
| Méretezés| A felhasználók, csoportok és szervezeti egységek, az Azure AD Connect által kezelt, például az objektumok száma. |
| Hardver| A hardver (fizikai vagy virtuális) az Azure AD Connect és minden hardverösszetevő, többek között a CPU, memória, hálózati és merevlemez-meghajtó konfigurációja függő teljesítmény kapacitását. |
| Konfiguráció| Az Azure AD Connect folyamatok a könyvtárak és információkat. |
| Betöltés| Objektum módosítása gyakorisága. A terhelések egy óra, nap vagy hét folyamán eltérőek lehetnek. Az összetevőtől függően tervezhet a csúcsterheléssel vagy az átlagos terhelés is rendelkezik. |

A jelen dokumentum célja, hogy a teljesítménnyel kapcsolatos megfontolások az Azure AD Connect a kiépítési motor teljesítményét befolyásoló ismertetik. Az Azure AD Connect, a többi összetevő például [az Azure AD Connect health](how-to-connect-health-agent-install.md) és az ügynökök nem terjed ki itt.

> [!IMPORTANT]
> A Microsoft Azure AD Connect műveleteken kívüli módosítását vagy nem támogatja. Minden ilyen művelet azt eredményezheti, hogy az Azure AD Connect szinkronizálása inkonzisztens vagy nem támogatott állapotba kerül. A Microsoft ezért nem tud műszaki támogatást biztosítani az ilyen környezetekhez.

## <a name="azure-ad-connect-component-factors"></a>Az Azure AD Connect összetevő tényezők

Az alábbi ábrán egy magas szintű architektúra, üzembe helyezésének motor csatlakozik egyetlen erdő, bár támogatja a több erdőt. Ez az architektúra bemutatja, hogyan a különböző összetevők léphetnek kapcsolatba egymással.

![AzureADConnentInternal](media/plan-connect-performance-factors/AzureADConnentInternal.png)

A kiépítési motor minden egyes Active Directory-erdő és az Azure ad-hez csatlakozik. A folyamat minden egyes címtárból információ olvasása importálás nevezzük. Exportálás hivatkozik a könyvtárak frissítése az üzembe helyezési motortól. Szinkronizálási kiértékeli, hogy az objektumokat az üzembe helyezési motorjában átkerülnek a szabályokat. Részletesebben megismerni, olvassa el [Azure AD Connect szinkronizálása: az architektúra ismertetése](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture).

Az Azure AD Connect használja a következő átmeneti területekből, szabályok és folyamatok a szinkronizálás engedélyezése az Active Directoryból az Azure ad-hez:

* **Összekötő-terület (CS)** – minden csatlakoztatott címtárhoz (CD), a tényleges könyvtárak objektumait elő van készítve itt először a kiépítési motor által feldolgozható előtt. Azure ad-ben a saját CS és minden olyan erdőben, hogy csatlakozni a saját CS rendelkezik.
* **Metaverzum (MV)** -igénylő szinkronizálható objektumok jönnek létre Itt a szinkronizálási szabályok alapján. Objektumok léteznie kell a MV előtt feltöltik objektumoknak és attribútumoknak más csatlakoztatott könyvtáraihoz. Csak egy MV van.
* **Szabályok szinkronizálása** -úgy döntenek, hogy mely objektumok (tervezett) létrejön vagy a MV objektumokat (csatlakoztatott) csatlakozik. A szinkronizálási szabályokat is döntse el, melyik attribútumértékek kell másolni vagy alakította át, illetve onnan a könyvtárak.
* **Futtatási profilokat** -Bundles objektumok és azok attribútumértékek az átmeneti területekből és a csatlakoztatott címtárak közötti szinkronizálási szabályai szerint másolása folyamat lépéseit.

Különböző futtatási profil létezik a kiépítési motor a teljesítmény optimalizálása érdekében. A legtöbb szervezet használja az alapértelmezett ütemtervét, és futtatási profilokat a szokásos működés, de előfordulhat, egyes szervezetek [ütemezésének módosítása](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) vagy más futtatási profilok méretformátumok figyelembe vétele ritka esetekben az eseményindítót. A következő futtatási profilokból érhetők el:

### <a name="initial-sync-profile"></a>Kezdeti szinkronizálás profil

A kezdeti szinkronizálás profil először a csatlakoztatott könyvtárak, például az Active Directory-erdő olvasása során a rendszer. Ezután hajtja végre az összes bejegyzést a szinkronizálási motor adatbázisban elemzését. A kezdeti ciklus új objektumokat hoz létre az Azure ad-ben, és, ha az Active Directory-erdők nagyok további időt vesz igénybe. A kezdeti szinkronizálás a következő lépésekből áll:

1. Teljes importálás az összes összekötő
2. Teljes szinkronizálás az összes összekötő
3. Exportálás az összes összekötő

### <a name="delta-sync-profile"></a>Különbözeti szinkronizálás profil

A szinkronizálási folyamat optimalizálása érdekében a futtatási profil csak feldolgozása a módosításokat (hoz létre, törlése és frissítések) objektumok a csatlakoztatott címtárakban, a legutóbbi szinkronizálási folyamat óta. Alapértelmezés szerint a különbözeti szinkronizálás profil 30 percenként fut. Szervezetek törekedni kell tartani, hogy mennyi idő 30 perc alatt ellenőrizze, hogy naprakész az Azure ad-ben. Az Azure AD Connect állapotának monitorozásához használja a [monitoring agent health](how-to-connect-health-sync.md) esetleges problémákat a folyamat megtekintéséhez. A különbözeti szinkronizálás profilt a következő lépésekből áll:

1. Különbözeti importálás az összes összekötő
2. Különbözeti szinkronizálás az összes összekötő
3. Exportálás az összes összekötő

Egy jellemző nagyvállalati szervezet különbözeti szinkronizálási forgatókönyv a következő:



- a rendszer törli az objektumok ~ 1 %
- ~ 1 % objektumok jönnek létre.
- az objektumok ~ 5 % módosítják.

A változási gyakoriság eltérőek lehetnek attól függően, hogy milyen gyakran a szervezet frissíti a felhasználók az Active Directoryban. A nagyobb sebesség módosítása például a humánerőforrás-, és csökkenti a munkát végző munkaerőnek szezonalitásához előfordulhatnak.

### <a name="full-sync-profile"></a>Teljes szinkronizálás profil

Egy teljes szinkronizálási ciklust szükség, ha módosította a következő konfigurációs módosításokat:



- Nagyobb az objektumok vagy lehet importálni a csatlakoztatott könyvtárakat az attribútumok körét. Például ha hozzá tartomány vagy szervezeti egység importálás szolgáltatásainkat.
- A szinkronizálási szabályok által végrehajtott módosítások. Például, ha szabályt hoz létre egy új adatokkal való feltöltéséhez a felhasználó címe extension_attribute3 az Active Directoryban az Azure AD-ben. A frissítéshez, hogy a kiépítési motor újra megvizsgálja-e frissíteni a címben, a jövőben a módosítás alkalmazása az összes meglévő felhasználót.

Egy teljes szinkronizálási ciklust a következő műveleteket tartalmazza:

1. Teljes importálás az összes összekötő
2. Teljes és különbözeti szinkronizálás az összes összekötő
3. Exportálás az összes összekötő

> [!NOTE]
> Gondos tervezést akkor szükséges, amikor sok objektumot az Active Directory vagy az Azure AD a tömeges frissítéseit. Tömeges frissítések miatt a különbözeti szinkronizálási folyamat hosszabb időt vesz igénybe, mivel sok objektum változásai importálása során. Hosszú import akkor fordulhat elő, akkor is, ha a kötegelt frissítés nem befolyásolja a szinkronizálási folyamat során. Például sok felhasználó licencek hozzárendelése az Azure ad-ben egy hosszú importálás ciklus miatt az Azure ad-ből, de nem eredményez bármely attribútumainak módosítása az Active Directoryban.

### <a name="synchronization"></a>Szinkronizálás

A szinkronizálási folyamat modul a következő teljesítmény jellemzőkkel rendelkezik:

* Szinkronizálási egyetlen összefűzött, ami azt jelenti, a kiépítési motor nem csatlakoztatott könyvtárak, objektumok vagy attribútumok futtatási profil párhuzamos feldolgozási.
* Importálás idő szinkronizálódik objektumok száma lineárisan vele együtt. Például ha 10 000 objektumok importálása 10 percet is igénybe, majd 20 000 objektumra érvénybe körülbelül 20 percig ugyanazon a kiszolgálón.
* Exportálás az is lineáris.
* A szinkronizálás más objektumok hivatkozó objektumok száma alapján exponenciálisan növekszik. Csoport tagságát, beágyazott csoportokat a fő teljesítményre gyakorolt hatás, lehet, mert a tagok tekintse meg a felhasználói objektumok vagy más csoportokhoz. Ezeket a hivatkozásokat kell található, és a befejezéséhez a szinkronizálási ciklus MV a tényleges objektumra hivatkozik.

### <a name="filtering"></a>Szűrés

Az importálni kívánt Active Directory-topológia mérete a szám egy tényező befolyásolta a teljesítményt és a teljes időtartama a kiépítési motor belső összetevőinek vesz igénybe.

[Szűrés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) próbálják meg csökkenteni a szinkronizált objektumok kell használni. Megakadályozza, hogy a feldolgozott és a exportálva az Azure AD a szükségtelen objektumokat. A kívánt sorrendben szűrés a következő eljárások érhetők el:



- **Tartományalapú szűrés** – Ez a beállítás segítségével válassza ki a szinkronizálni az Azure AD-tartományok. Kell hozzá és távolíthat el tartományokat abból a szinkronizálási motor konfiguráció, ha módosítja a helyszíni infrastruktúrát az Azure AD Connect-szinkronizálás telepítése után.
- **Szervezeti egység (OU) szűrés** -szervezeti egységek használja, amelyekre meghatározott objektumokat az Active Directory-tartományok az Azure ad-ben való üzembe helyezést. Szervezeti egységek szűrése a második, ajánlott a szűrési mechanizmus, mert az objektumok kisebb részhalmazát importálására az Active Directory egyszerű LDAP attribútumhatókör-lekérdezéseket használ.
- **Objektumonkénti attribútumszűrés** -objektumok az attribútumértékek segítségével döntse el, hogy adott objektumot az Active Directory az Azure ad-ben van kiépítve. Attribútumszűrés kiválóan alkalmazható az finomhangoló a szűrőket, ha a tartomány és szervezeti egységek szűrése nem felel meg a megadott szűrési követelményeinek. Attribútumszűrés nem importálás idő csökkentése érdekében, de csökkenthető a szinkronizálás és időpontok exportálása.
- **Csoportalapú szűrés** -csoporttagság használ, döntse el, hogy objektumokat kell létrehozni az Azure ad-ben. Csoportalapú szűrés csak az olyan helyzetekben tesztelési és éles környezetben, a felesleges többletterhelést szükséges csoporttagság ellenőrzése közben a szinkronizálási ciklus miatt nem ajánlott.

Több állandó [Terheléskapcsoló objektumok](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) az Active Directory CS okozhat szinkronizálás hosszabb időt, mert a kiépítési motor kell kiértékeli az egyes Terheléskapcsoló objektumok lehetséges a kapcsolatra vonatkozóan a a szinkronizálási ciklus. A probléma megoldásához, akkor az alábbi javaslatokat:



- Helyezze el a Terheléskapcsoló objektumok importálása a tartomány vagy szervezeti egységek szűrése az hatókörén kívül.
- Projekt/join az objektumokat a MV és a készlet a [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) attribútum értéke legyen True, ezeket az objektumokat az Azure Active Directory tanúsítványszolgáltatások kiépítése elkerülése érdekében.

> [!NOTE]
> Felhasználók is Zavarba vagy alkalmazás engedélyekkel kapcsolatos probléma akkor fordulhat elő, ha túl sok objektumot szűr a rendszer. Például a hibrid Exchange online megvalósításában, helyszíni postaládákhoz rendelkező felhasználók jelenik meg, mint a postaládák rendelkező felhasználók a globális címlista több felhasználó az Exchange online. Egyéb esetben a felhasználó érdemes hozzáférést biztosítani a cloud App egy másik felhasználónak, amely nem része hatókörébe tartozó objektumok szűrt készlete.

### <a name="attribute-flows"></a>Attribútumfolyamok

Attribútumfolyamok azt a folyamatot a Másolás vagy átalakítása egy csatlakoztatott címtárban objektumokat egy másik csatlakoztatott címtárhoz attribútum értékét. Azok a szinkronizálási szabályok részeként van definiálva. Például amikor a felhasználó telefonszáma megváltozott az Active Directoryban, az Azure ad-ben a telefonszámát frissülni fog. Szervezetek is [módosítása az attribútumfolyamok](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration) Suite különböző követelményeket. Másolja a meglévő attribútumfolyamok őket módosítása előtt ajánlott.

Egyszerű átirányítások, például egy attribútum értékét egy másik attribútum áramló jelentős hatása nincs. Átirányítási például az irodai telefonszámát, az Active Directory mobiltelefonszám áramlik az Azure ad-ben.

Átalakítása attribútum-érték, a teljesítmény hatással lehet a szinkronizálási folyamatot. Módosítása, újraformázást, összefűzi vagy attribútumának kivonva átalakítása attribútum-érték magában foglalja.

Szervezetek megakadályozhatja, hogy bizonyos áramlását az Azure AD-attribútumok, de azt nem befolyásolják a kiépítési motor teljesítményét.

> [!NOTE]
> Ne törölje a nem kívánt attribútumfolyamok a szinkronizálási szabályokban. Javasolt inkább tiltsa le azokat, mivel a törölt szabályok létrejönnek az Azure AD Connect frissítése során.

## <a name="azure-ad-connect-dependency-factors"></a>Az Azure AD Connect függőségi tényezők

A teljesítmény az Azure AD Connect szolgáltatás függ, importálja és exportálja a csatlakoztatott könyvtárak teljesítményét. Például a mérete, importálni kell az Active Directory vagy a hálózati késés, az Azure AD szolgáltatásba. A kiépítési motor használja az SQL-adatbázis is hatással van a szinkronizálási ciklus általános teljesítményét.

### <a name="active-directory-factors"></a>Az Active Directory tényezők

Ahogy korábban említettük, az importálandó objektumok száma jelentős mértékben befolyásolja teljesítmény. A [hardver- és az Azure AD Connect előfeltételei](how-to-connect-install-prerequisites.md) szerkezeti az üzemelő példány mérete alapján meghatározott típusú hardveres szinten. Az Azure AD Connect csak támogatja adott topológiák a leírt módon [az Azure AD Connect-topológiák](plan-connect-topologies.md). Nincsenek, nem támogatott topológiák javaslatok és teljesítményoptimalizálás.

Győződjön meg arról, hogy az Azure AD Connect-kiszolgáló megfelel a hardverkövetelményeknek az Active Directory mérete alapján szeretne importálni. Az Azure AD Connect-kiszolgáló és az Active Directory-tartományvezérlők közötti hibás vagy lassú hálózati kapcsolat lelassíthatják az importálás.

### <a name="azure-ad-factors"></a>Az Azure AD-tényezők

Az Azure AD használatával szabályozás-szolgáltatásmegtagadásos (DoS) támadásokkal szembeni védelemhez a felhőszolgáltatás. Jelenleg az Azure AD esetében a sávszélesség-szabályozási az 7000 írások száma 5 percenként (84,000 óránként). Ha például a következő műveletek szabályozható:



- Az Azure AD Connect, az Azure AD-exportálás.
- PowerShell-parancsfájlok vagy alkalmazások közvetlenül az Azure AD frissítése akkor is igaz, a háttérben, például a dinamikus csoporttagságok.
- A felhasználók a saját identitás rögzíti, például a többtényezős hitelesítés vagy az SSPR (önkiszolgáló jelszó-visszaállítás) regisztrálása frissítése.
- A grafikus felhasználói felület műveleteit.

Tervezze meg a központi telepítési és karbantartási feladatok, annak biztosításához, hogy az Azure AD Connect szinkronizálási ciklus nem befolyásolják szabályozási korlátait. Például ha egy nagy felvételi wave, ahol létrehozhatja a felhasználói identitások több ezer, dinamikus csoporttagságok licencelési hozzárendelések, a frissítések okozhat, és önkiszolgáló jelszóátállítási regisztrációk. Érdemes ezen írások több óráig vagy néhány napon keresztül terjednek.

### <a name="sql-database-factors"></a>Az SQL database tényezők

A forrás az Active Directory-topológia méretét az SQL database szolgáltatás teljesítményének befolyásolják. Kövesse a [hardverkövetelmények](how-to-connect-install-prerequisites.md) az SQL Server adatbázis-, vegye figyelembe az alábbi javaslatokat:



- Több mint 100 000 felhasználóval rendelkező szervezetek csökkentheti a hálózati késések közös elhelyezése az SQL database és az üzembe helyezési motor ugyanazon a kiszolgálón.
- Miatt a magas lemez bemeneti és kimeneti (I/O) követelményeinek a szinkronizálási folyamat használata tartós állapot-meghajtók (SSD) az SQL database, a kiépítési motor az optimális eredmény, ha nem lehetséges, fontolja meg RAID 0 vagy RAID 1 konfigurációk.
- Jegyértékesítésről; ne végezze el a teljes szinkronizálás a felesleges és a lassabb válaszidők okoz.

## <a name="conclusion"></a>Összegzés

Az Azure AD Connect megvalósítási a teljesítmény optimalizálása érdekében fontolja meg az alábbi javaslatokat:



- Használja a [hardverkonfiguráció javasolt](how-to-connect-install-prerequisites.md) a megvalósítása az Azure AD Connect-kiszolgáló mérete alapján.
- Amikor frissíti az Azure AD Connect nagy méretű telepítések, érdemes [áttelepítési módszer párhuzamos](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration), hogy ellenőrizze, hogy a minimális állásidő és a legjobb megbízhatóság. 
- Az SQL database a legjobb teljesítmény írása SSD használja.
- Csak olyan objektumok, amelyek úgy kell létrehozni az Azure AD-ben a tartomány, szervezeti egység vagy attribútumszűrés az Active Directory szűrje.
- Módosítsa az alapértelmezett szabályok attribútum van szüksége, ha először másolja a szabályt, majd módosítsa a másolás és letiltja az eredeti szabályt. Futtassa újra a teljes szinkronizálás a vágólapra.
- Tervezze meg a kezdeti teljes szinkronizálás futtatási profil elegendő időt.
- A különbözeti szinkronizálás üzemeltetői ciklus befejezésekor 30 percben. A különbözeti szinkronizálás profil 30 perc múlva indul el, ha egy teljes különbözeti szinkronizálási ciklus tartalmazza alapértelmezett szinkronizálási gyakoriságot módosítani.
- A figyelő a [az Azure AD Connect szinkronizálási állapot](how-to-connect-health-agent-install.md) az Azure ad-ben.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
