---
title: A helyszíni Azure AD SSPR jelszó visszaírási-integráció
description: Első felhőalapú jelszavak visszaírja a helyszíni AD infratstructure
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 43d2ba496be90e9e87185e6365dd998adccfa09d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804531"
---
# <a name="what-is-password-writeback"></a>Mi a jelszóvisszaíró?

Kellene felhőalapú jelszó-visszaállításra segédprogram remek, de a legtöbb vállalat továbbra is rendelkezik egy helyszíni címtár, ahol a felhasználók létezik. Hogyan történik a Microsoft támogatási megtartja a hagyományos helyszíni Active Directory (AD) szinkronban a jelszó módosítására a felhőben? A jelszóvisszaíró funkciója engedélyezve van a [az Azure AD Connect](../hybrid/whatis-hybrid-identity.md) , amely lehetővé teszi, hogy a jelszó módosítására a meglévő helyszíni címtárhoz valós időben visszaírása a felhőben.

A jelszóvisszaíró támogatják az olyan környezetekben, amelyek:

* [Active Directory összevonási szolgáltatások](../hybrid/how-to-connect-fed-management.md)
* [Jelszókivonat szinkronizálása](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Átmenő hitelesítés](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> A jelszóvisszaíró régebbi során és az Azure AD Connect verziója 1.0.8641.0 használó ügyfeleink számára megszűnnek működni a [Azure Access Control service (ACS) 2018. November 7 kivonják](../develop/active-directory-acs-migration.md). Az Azure AD Connect verziója 1.0.8641.0 és a régebbi engedélyezi többé a jelszóvisszaíró adott időpontban, mert az a funkciók ACS függenek.
>
> A szolgáltatás, az Azure AD Connect egy korábbi verziójáról egy újabb verzióra frissítés elkerülése érdekében tekintse meg a cikket [az Azure AD Connect: frissítés egy előző verzióról a legújabbra](../hybrid/how-to-upgrade-previous-version.md)
>

A jelszóvisszaíró biztosítja:

* **A helyszíni Active Directory-jelszó házirendek kényszerítését**: amikor a felhasználó alaphelyzetbe állítja a jelszavát, a rendszer ellenőrzi, győződjön meg arról, megfelel-e a helyszíni Active Directory-szabályzat, a címtárhoz véglegesítése előtt. A felülvizsgálat magában foglalja az előzményeket, bonyolultsága, kor, jelszószűrők és egyéb jelszó korlátozásokat a helyi Active Directory megadott ellenőrzése.
* **Nulla-késleltetés visszajelzés**: a jelszóvisszaírás egy szinkronizált művelet. A felhasználók azonnali értesítést kapjon a jelszó nem felel meg a házirend vagy nem alaphelyzetbe állítása vagy bármilyen okból megváltozott.
* **Támogatja a jelszó is módosul, a hozzáférési panelen és az Office 365**: amikor összevont jelszókivonatok szinkronizálása és felhasználók származnak, hogy módosítsák jelszavukat lejárt vagy nem járt le, ezeket a jelszavakat a rendszer visszaírja a helyi Active Directory-környezetbe.
* **A jelszóvisszaíró támogatja, ha egy rendszergazda visszaállítja azokat az Azure Portalról**: minden alkalommal, amikor egy rendszergazda visszaállítja-e egy felhasználó jelszavát az a [az Azure portal](https://portal.azure.com), ha a felhasználó vagy a jelszókivonatok szinkronizálása, a jelszó, visszaírja a helyszíni. Ez a funkció jelenleg nem támogatott az Office felügyeleti portálon.
* **Nincs szükség semmilyen bejövő tűzfalszabályokat**: a jelszóvisszaírás egy Azure Service Bus relay használja, mint az alapul szolgáló kommunikációs csatornát. Minden kommunikáció a 443-as porton keresztül akkor kimenő forgalomról beszélünk.

> [!Note]
> A jelszóvisszaíró található védett csoportok a helyszíni Active Directoryban lévő felhasználói fiókok nem használható. Védett csoportokkal kapcsolatos további információkért lásd: [védett fiókok és csoportok az Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="licensing-requirements-for-password-writeback"></a>Jelszóvisszaíró licencelési követelményeket

**Az önkiszolgáló jelszó alaphelyzetbe állítása/módosítás /-Zárolásfeloldás helyszíni visszaírással a rendszer egy Azure ad premium-funkció**. Licenceléssel kapcsolatos további információkért lásd: a [Azure Active Directory díjszabását ismertető a hely](https://azure.microsoft.com/pricing/details/active-directory/).

A jelszóvisszaíró használandó kell rendelkeznie a következő licenccel, a bérlő egyik:

* Prémium szintű Azure AD P1
* Prémium szintű Azure AD P2
* Enterprise Mobility + Security E3 vagy A3
* Enterprise Mobility + Security E5 vagy a5 csomag
* A Microsoft 365 E3 vagy A3
* A Microsoft 365 E5 vagy a5 csomag
* A Microsoft 365 F1 csomag

> [!WARNING]
> Önálló Office 365 licencelési csomagok *nem támogatják a jelszóvisszaírást* , és rendelkezik a fenti csomagok keretében, az a funkciók működéséhez szükséges.
>

## <a name="how-password-writeback-works"></a>Jelszóvisszaírás működéséről

Amikor összevont vagy a jelszó kivonatát szinkronizálja a felhasználói kísérletek visszaállítására, vagy módosítania kell a jelszavát, a felhőben, a következő műveletek történnek:

1. Ellenőrzi, milyen típusú jelszót a felhasználó tartozik történik. Ha a jelszót a helyszínen kezel:
   * Egy-ellenőrzést hajtanak végre, ha a jelszóvisszaíró szolgáltatás működik és van-e. Ha igen, a felhasználó folytassa.
   * A jelszóvisszaíró szolgáltatás nem működik, ha a felhasználó értesítést kap, hogy a jelszó jelenleg nem állítható alaphelyzetbe.
2. Ezután a felhasználó a megfelelő hitelesítési kapu továbbítja, és eléri a **jelszó alaphelyzetbe állítása** lapot.
3. A felhasználó kiválaszt egy új jelszót, és megerősíti azt.
4. Amikor a felhasználó kiválasztja **küldés**, a titkosítatlan szöveges jelszó a jelszóvisszaíró telepítés során létrehozott szimmetrikus kulcs titkosítva van.
5. A titkosított jelszót tartalmazza a bérlő-specifikus a service bus relay (beállított Önnek a jelszóvisszaíró telepítés közben) egy HTTPS-csatornán keresztül küldött hasznos. A relay védi, amely csak a helyszíni telepítés tudja, hogy véletlenszerűen létrehozott jelszót.
6. Az üzenet elérte a service bus, a jelszó-visszaállítási végpont automatikusan felébred, és láthatja, hogy a visszaállítási kérelem függőben van.
7. A szolgáltatás majd keres a felhasználó a felhő forráshorgony attribútumának használatával. A keresés sikeres: a

   * A user objektum léteznie kell az Active Directory összekötőterében.
   * A user objektum a megfelelő (MV) metaverzum-objektum kell társítani.
   * A user objektum a megfelelő Azure Active Directory-összekötő objektumot kell társítani.
   * Az Active Directory-összekötő objektum történő hivatkozás a MV rendelkeznie kell a szinkronizálási szabály `Microsoft.InfromADUserAccountEnabled.xxx` hivatkozásra. <br> <br>
   A hívást a felhőben érhető el, ha a szinkronizálási motor használja a **cloudAnchor** keresse ki az Azure Active Directory összekötőtér objektuma attribútum. Majd követi a hivatkozást az MV objektumra, és ezután követi a hivatkozást az Active Directory-objektumra. Több Active Directory-objektumok (Többerdős) ugyanaz a felhasználó számára is lehet, mert a szinkronizálási motor támaszkodik a `Microsoft.InfromADUserAccountEnabled.xxx` hivatkozást választja ki a megfelelőt.

   > [!Note]
   > Eredményeként a logikai jelszó a jelszóvisszaíró működjön az Azure AD Connect képesnek kell lennie az elsődleges tartományvezérlő (PDC) emulátor folytatott kommunikációhoz. Ezt manuálisan engedélyezni kell, ha az Azure AD Connect csatlakozhat a PDC-emulátor. Kattintson a jobb gombbal a **tulajdonságok** az Active Directory szinkronizálási összekötőjének, majd válassza ki **könyvtárpartíciók konfigurálásának**. Itt keresse meg a **tartományvezérlő kapcsolati beállításainak** szakaszt, és válassza a jelölőnégyzetét **csak az előnyben részesített tartományvezérlők használatának**. Akkor is, ha az előnyben részesített tartományvezérlő nem egy PDC-emulátor, az Azure AD Connect megpróbál kapcsolódni az elsődleges tartományvezérlő jelszóvisszaíró.

8. Miután a felhasználók fiókot talál, a jelszót a megfelelő Active Directory-erdő közvetlenül a kísérlet történik.
9. Ha a jelszó set művelet sikeres, a felhasználó van, hogy rendelkezik a jelszó megváltozott.
   > [!NOTE]
   > A felhasználó Jelszókivonat a Jelszókivonat-szinkronizálás használatával szinkronizálva az Azure AD, ha esetén megvan az esélye, hogy a helyszíni Jelszóházirend, mint a felhőbeli jelszóházirend gyengébb. Ebben az esetben a helyi házirend van érvényben. Ez a házirend biztosítja, hogy a helyi házirend van érvényben a felhőben, függetlenül attól egyszeri bejelentkezést biztosít a Jelszókivonat-szinkronizálás vagy az összevonási használatakor.

10. A jelszó beállítása a művelet sikertelen lesz, ha a hiba kéri a felhasználót, próbálkozzon újra. A művelet sikertelen lehet, mert:
   * A szolgáltatás le lett.
   * A jelszó kiválasztották nem felelt meg a szervezet szabályzatainak.
   * Nem található a felhasználó a helyi Active Directoryban.

    A hibaüzenetek útmutatást nyújtanak a felhasználók, így az is megpróbálják feloldani rendszergazdai beavatkozás nélkül.

## <a name="password-writeback-security"></a>A jelszó-visszaírás biztonsága

A jelszóvisszaíró szolgáltatás rendkívül biztonságos. Az adatok védelme érdekében egy négy többrétegű biztonsági modellt engedélyezve van, a következőket ismerteti:

* **Bérlő-specifikus service-bus Relay használatával**
   * A szolgáltatás üzembe helyezésekor a bérlő-specifikus service bus Relay használatával állítható be egy véletlenszerűen létrehozott erős jelszót, amely a Microsoft soha nem férhet hozzá által védett.
* **Zárolt, titkosítási szempontból erős jelszó-titkosítási kulcs**
   * A service bus relay létrehozása után egy erős szimmetrikus kulcs jön létre, amely titkosítja a jelszót, mivel származik, a hálózaton keresztül szolgál. Ezt a kulcsot csak él a vállalat titkoskód-tárolót a felhőben, amely fokozottan zárolja, és naplózni, mint bármely más jelszót a címtárban.
* **Iparági standard Transport Layer Security (TLS)**
   1. Ha a jelszó alaphelyzetbe állítása, vagy módosítsa a művelethez szükséges, a felhőben, a titkosítatlan szöveges jelszó a nyilvános kulccsal titkosított.
   2. A titkosított jelszót kerül egy HTTPS-üzenetet, amely szerint a service bus Relay használatával történő Microsoft SSL-tanúsítványok használatával egy titkosított csatornán keresztül továbbítja.
   3. Az üzenet érkezik a service bus, miután a helyi ügynök felébred, és a service bus által a korábban létrehozott erős jelszóval hitelesíti magát.
   4. A helyi ügynök szerzi be a titkosított üzenetekre, és visszafejti a titkos kulcs használatával.
   5. A helyi ügynök próbál állítsa be a jelszót az AD DS SetPassword API-n keresztül. Ez a lépés nem milyen tevékenységeket engedélyez a az Active Directory a helyszíni jelszóházirend (például az összetettséget, kor, előzményei és szűrők) érvényesítése is a felhőben.
* **Üzenet-érvényességi szabályzatok**
   * Az üzenetet a service bus helyezkedik el, mert a helyszíni szolgáltatással nem működik, ha ez túllépi az időkorlátot, és néhány perc múlva törlődik. Az időkorlát és az üzenet eltávolítása növeli a biztonságot is.

### <a name="password-writeback-encryption-details"></a>Jelszó-visszaírás titkosítási adatok

Miután egy felhasználó beküld egy jelszó-visszaállítás, a visszaállítási kérelem végighalad számos titkosítási lépést, a helyszíni környezetben megérkezése előtt. Ezek a titkosítás lépései győződjön meg arról, maximális szolgáltatás megbízhatóságát. Ezek az alábbiak szerint:

* **1. lépés: Jelszó-titkosítási 2048-bites RSA-kulcsa**: visszaírását a helyszíni jelszó elküldése után a beküldött jelszó magát egy 2048 bites RSA-kulcsa titkosítva van-e.
* **2. lépés: Csomag szintű titkosítást AES-GCM-**: AES-GCM használatával titkosítja a teljes csomag: a jelszó és a szükséges metaadatokat. A titkosítás megakadályozza, hogy bárki közvetlen hozzáférést a mögöttes ServiceBus-csatornára megtekintése vagy illetéktelenül módosítsák a tartalmát.
* **3. lépés: Az összes kommunikáció a TLS/SSL keresztül történik.**: a ServiceBus folytatott minden kommunikáció zajlik az SSL/TLS-csatorna. A titkosítás a tartalom jogosulatlan harmadik felektől származó védi.
* **Minden hat hónapban automatikus kulcs visszaállítási**: az összes kulcs vihető hathavonta, illetve minden alkalommal jelszóvisszaíró le van tiltva, majd újra engedélyezik az Azure AD Connectben maximális szolgáltatás biztonsági és biztonságának.

### <a name="password-writeback-bandwidth-usage"></a>Jelszó-visszaírás sávszélesség-használat

A jelszóvisszaíró szolgáltatás alacsony sávszélességű, amely csak kéréseket küld vissza a helyi ügynök a következő esetekben:

* Két üzenetek elküldésére, amikor a szolgáltatás engedélyezve van-e az Azure AD Connect használatával.
* Mindaddig, amíg a szolgáltatás fut egy üzenetet küld öt percenként egyszer, a szolgáltatás szívverést.
* Két üzeneteket küld az egyes beküldött egy új jelszó idő:
   * Az első üzenet tulajdonképpen egy kérelem a művelet végrehajtásához.
   * A második üzenet tartalmazza a művelet eredményét, és a következő körülmények között zajlik:
      * Minden alkalommal, amikor egy új jelszót a felhasználó önkiszolgáló jelszó-visszaállítás során elküldésekor.
      * Minden alkalommal, amikor egy új jelszó küldése felhasználói jelszó-változtatási művelet során.
      * Minden alkalommal, amikor egy új jelszó küldése során egy rendszergazda által kezdeményezett felhasználói jelszó alaphelyzetbe állítása (csak az az Azure felügyeleti portál).

#### <a name="message-size-and-bandwidth-considerations"></a>Üzenet mérete és a sávszélesség kapcsolatos szempontok

Az egyes az előzőekben leírt üzenet mérete általában 1 KB alatt. Szélsőséges terhelés alatt is maga a jelszóvisszaíró szolgáltatás néhány kilobit / másodperc sávszélesség is használja. Mivel minden üzenetet küld a valós idejű, csak akkor, ha a jelszó-frissítési művelet által igényelt, és az üzenet mérete a kicsi, mert a sávszélesség-használat a jelszóvisszaíró funkció, túl kicsi a mérhető hatással van.

## <a name="supported-writeback-operations"></a>Támogatott visszaírási műveletek

Jelszavak a rendszer visszaírja a következő helyzetekben:

* **Támogatott végfelhasználói műveletek**
   * Minden olyan végfelhasználói önkiszolgáló önkéntes Módosítsa jelszavát művelet
   * Minden olyan végfelhasználói önkiszolgáló kényszerített módosítása jelszó művelet, például a jelszó lejárati ideje
   * Minden olyan végfelhasználói önkiszolgáló jelszó-visszaállítási származó a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com)
* **Támogatott rendszergazdai műveletek**
   * Minden rendszergazda önkiszolgáló önkéntes jelszó művelet módosítása
   * Minden rendszergazda önkiszolgáló kényszerített módosítása jelszó művelet, például a jelszó lejárati ideje
   * Minden rendszergazda önkiszolgáló jelszó-visszaállítási származó a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com)
   * Minden végfelhasználói rendszergazda által kezdeményezett jelszó-visszaállítás a [Azure Portalon](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>A visszaírás nem támogatott műveletek

A jelszóban *nem* visszaírja a következő helyzetek egyikében:

* **Nem támogatott a végfelhasználói műveletek**
   * A végfelhasználók saját jelszó alaphelyzetbe állítása a PowerShell 1-es verzió, a 2-es vagy az Azure AD Graph API használatával
* **A rendszergazda nem támogatott műveletek**
   * Minden végfelhasználói rendszergazda által kezdeményezett jelszó-visszaállítás a [Office felügyeleti portálján](https://portal.office.com)
   * Minden végfelhasználói rendszergazda által kezdeményezett jelszó-visszaállítás a PowerShell 1-es verzió, 2-es vagy az Azure AD Graph API

## <a name="next-steps"></a>További lépések

Az oktatóprogram a jelszóvisszaírás engedélyezése: [a jelszóvisszaírás engedélyezése](tutorial-enable-writeback.md)
