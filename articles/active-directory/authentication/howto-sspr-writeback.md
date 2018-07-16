---
title: A jelszóvisszaíró használata az Azure AD SSPR |} A Microsoft Docs
description: Az Azure AD és a jelszóvisszaíró jelszavait, hogy egy helyszíni címtár Azure AD Connect
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: fce92845591f20f7f2e9cff1a856e0904629255b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054794"
---
# <a name="password-writeback-overview"></a>Jelszó-visszaírás – áttekintés

A jelszóvisszaírás konfigurálhat az Azure Active Directory (Azure AD) a jelszavakat írhasson vissza a helyszíni Active Directoryban. A jelszóvisszaíró feleslegessé teszi a beállítását és kezelését egy bonyolult helyszíni önkiszolgáló jelszó-visszaállítási (SSPR) megoldás, és a jelszavaikat a helyszíni, bárhol is vannak a felhasználók számára egy kényelmes felhőalapú megoldást kínál. A jelszóvisszaíró rendszer részét képező [Azure Active Directory Connect](./../connect/active-directory-aadconnect.md) , amely engedélyezi, és használja a jelenlegi előfizetők a prémium szintű [Azure Active Directory-kiadások](../fundamentals/active-directory-whatis.md).

A jelszóvisszaíró a következő szolgáltatásokat biztosítja:

* **Nulla-késleltetés visszajelzést**: a jelszóvisszaírás egy szinkronizált művelet. A felhasználók azonnali értesítést kapjon a jelszó nem felel meg a házirend vagy nem alaphelyzetbe állítása vagy bármilyen okból megváltozott.
* **Támogatja a jelszó alaphelyzetbe állítja az Active Directory összevonási szolgáltatások (AD FS) vagy más összevonási technológiát használó felhasználók**: A jelszóvisszaírás, mindaddig, amíg az összevont felhasználói fiókok szinkronizálva vannak az Azure AD-bérlőhöz be átállíthatják a a helyszíni Active Directory-jelszavaikat felügyelete a felhőből.
* **Támogatja a jelszó alaphelyzetbe használó felhasználók** [Jelszókivonat-szinkronizálás](./../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md): jelszó-átállítási szolgáltatás észleli, hogy a szinkronizált felhasználói fiók engedélyezve van-e a Jelszókivonat-szinkronizálást, amikor alaphelyzetbe áll vissza Ez fiók a helyszíni és felhőbeli jelszó egyszerre. A jelszóvisszaírás nem függ a Jelszókivonat-szinkronizálást.
* **Támogatja a jelszó alaphelyzetbe átmenő hitelesítést használó felhasználók**: A jelszóvisszaírás, mindaddig, amíg az átmenő hitelesítés fiókok szinkronizálva vannak az Azure AD-bérlőhöz be azok tudni felügyelni a helyszíni Active Directory-jelszavaikat a felhőben.
* **Támogatja a jelszó is módosul, a hozzáférési panelen és az Office 365**: amikor összevont vagy jelszószinkronizált felhasználók módosítani a lejárt vagy nem lejárt jelszót, azt ezen jelszavakat írhasson vissza a helyi Active Directory-környezetbe való.
* **Jelszavak visszaírása támogatja, ha egy rendszergazda visszaállítja azokat az Azure Portalról**: minden alkalommal, amikor egy rendszergazda visszaállítja-e egy felhasználó jelszavát az a [az Azure portal](https://portal.azure.com), ha a felhasználó vagy a jelszó szinkronizálása, értékre állítjuk a jelszót a rendszergazda kijelölése, valamint a helyi Active Directoryban. Ez a funkció jelenleg nem támogatott az Office felügyeleti portálon.
* **A helyszíni Active Directory-jelszó házirendek érvénybe lépteti**: amikor egy felhasználói jelszó alaphelyzetbe állítása, hogy győződjön meg arról, hogy megfelel-e a helyszíni Active Directory házirendet ahhoz, hogy véglegesítheti a könyvtárhoz. A felülvizsgálat magában foglalja az előzményeket, bonyolultsága, kor, jelszószűrők és egyéb jelszó korlátozásokat a helyi Active Directory megadott ellenőrzése.
* **Nincs szükség semmilyen bejövő tűzfalszabályokat**: a jelszóvisszaírás egy Azure Service Bus relay használja, mint az alapul szolgáló kommunikációs csatornát. Nyissa meg a bejövő portra a tűzfalon a funkció működéséhez nem kell.
* **Védett csoportok a helyszíni Active Directoryban található felhasználói fiókok esetében nem támogatott**: védett csoportokkal kapcsolatos további információkért lásd: [védett fiókok és csoportok az Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Jelszóvisszaírás működéséről

Amikor egy összevont vagy a jelszó-kivonat szinkronizált felhasználói visszaállítására, vagy módosítania kell a jelszavát, a felhőben érhető el, az alábbiak történnek:

1. Hogy ellenőrizze, hogy milyen típusú jelszót a felhasználó rendelkezik. Ha azt látja, hogy a jelszó a helyszínen kezel:
   * Ellenőrizzük, hogy ha a jelszóvisszaíró szolgáltatás működik-e. Ha helyezheti üzembe, a felhasználó folytassa értesítettük.
   * Ha a jelszóvisszaíró szolgáltatás nem fel, hogy szólítania a felhasználót, hogy a jelszó jelenleg nem állítható alaphelyzetbe.
2. Ezután a felhasználó a megfelelő hitelesítési kapu továbbítja, és eléri a **jelszó alaphelyzetbe állítása** lapot.
3. A felhasználó kiválaszt egy új jelszót, és megerősíti azt.
4. Amikor a felhasználó kiválasztja **küldés**, hogy a titkosítatlan szöveges jelszó titkosítása a jelszóvisszaíró telepítési folyamat során létrehozott szimmetrikus kulcs.
5. Után titkosítja a jelszót, hogy foglalja bele egy hasznos, amelyek egy HTTPS-csatornán keresztül küldi a bérlő-specifikus a service bus relay (amely is be lesznek állítva az Ön számára a jelszóvisszaíró telepítés során). A relay védi, amely csak a helyszíni telepítés tudja, hogy véletlenszerűen létrehozott jelszót.
6. Az üzenet elérte a service bus, a jelszó-visszaállítási végpont automatikusan felébred, és láthatja, hogy a visszaállítási kérelem függőben van.
7. A szolgáltatás majd keres a felhasználó a felhő forráshorgony attribútumának használatával. A keresés sikeres: a

    * A user objektum léteznie kell az Active Directory összekötőterében.
    * A user objektum a megfelelő (MV) metaverzum-objektum kell társítani.
    * A user objektum a megfelelő Azure Active Directory-összekötő objektumot kell társítani.
    * Az Active Directory-összekötő objektum történő hivatkozás a MV rendelkeznie kell a szinkronizálási szabály `Microsoft.InfromADUserAccountEnabled.xxx` hivatkozásra. <br> <br>
    A hívást a felhőben érhető el, ha a szinkronizálási motor használja a **cloudAnchor** keresse ki az Azure Active Directory összekötőtér objektuma attribútum. Majd követi a hivatkozást az MV objektumra, és ezután követi a hivatkozást az Active Directory-objektumra. Több Active Directory-objektumok (Többerdős) ugyanaz a felhasználó számára is lehet, mert a szinkronizálási motor támaszkodik a `Microsoft.InfromADUserAccountEnabled.xxx` hivatkozást választja ki a megfelelőt.

    > [!Note]
    > Eredményeként a logikai jelszó a jelszóvisszaíró működjön az Azure AD Connect képesnek kell lennie az elsődleges tartományvezérlő (PDC) emulátor folytatott kommunikációhoz. Ezt manuálisan engedélyezni kell, ha az Azure AD Connect csatlakozhat a PDC-emulátor. Kattintson a jobb gombbal a **tulajdonságok** az Active Directory szinkronizálási összekötőjének, majd válassza ki **könyvtárpartíciók konfigurálásának**. Itt keresse meg a **tartományvezérlő kapcsolati beállításainak** szakaszt, és válassza a jelölőnégyzetét **csak az előnyben részesített tartományvezérlők használatának**. Akkor is, ha az előnyben részesített tartományvezérlő nem egy PDC-emulátor, az Azure AD Connect megpróbál kapcsolódni az elsődleges tartományvezérlő jelszóvisszaíró.

8. Miután a felhasználók fiókot talál, hogy próbálja meg alaphelyzetbe állíthatja a jelszót, közvetlenül a megfelelő Active Directory-erdőben.
9. Ha a jelszó set művelet sikeres, biztosítunk a felhasználó módosította a jelszavát.
    > [!NOTE]
    > Ha a felhasználó jelszava szinkronizálva az Azure AD jelszó-szinkronizálás használatával, esetén megvan az esélye, hogy a helyszíni Jelszóházirend, mint a felhőbeli jelszóházirend gyengébb. Ebben az esetben továbbra is foglaljuk függetlenül a helyszíni szabályzat van, ezért használja inkább a Jelszókivonat-szinkronizálás szinkronizálni, hogy a jelszó kivonatát. Ez a házirend biztosítja, hogy a helyi házirend van érvényben a felhőben, függetlenül attól, adja meg az egyszeri bejelentkezés jelszó-szinkronizálással vagy összevonási használatakor.

10. A jelszó beállítása a művelet sikertelen lesz, ha azt a felhasználó hibát adhat vissza, és értesítse, próbálkozzon újra. A művelet sikertelen lehet, mert:
    * A szolgáltatás le lett.
    * A jelszó kiválasztották nem felelt meg a szervezet szabályzatainak.
    * Nem lehet megtalálni a felhasználó helyi Active Directoryban.

    Ezekben az esetekben számos esetében egy adott üzenet rendelkezik, és amelyet a felhasználó értesítése, mit tehet a probléma megoldásához.

## <a name="configure-password-writeback"></a>Jelszóvisszaíró konfigurálása

Azt javasoljuk, hogy az automatikus frissítési funkcióját használja [az Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md) Ha jelszóvisszaíró használni szeretne.

A DirSync és az Azure AD Sync a jelszóvisszaírás engedélyezéséhez, hogy már nem támogatott. Segítséget nyújtson az átállás további információkért lásd: [frissítés a DirSync és az Azure AD Sync](../connect/active-directory-aadconnect-dirsync-deprecated.md).

A következő lépések azt feltételezik, hogy már konfigurálta az Azure AD Connect a környezetében az a [Express](./../connect/active-directory-aadconnect-get-started-express.md) vagy [egyéni](./../connect/active-directory-aadconnect-get-started-custom.md) beállításait.

1. Konfigurálni, és a jelszóvisszaírás engedélyezéséhez jelentkezzen be az Azure AD Connect-kiszolgáló, és indítsa el a **az Azure AD Connect** konfigurációs varázsló.
2. Az a **üdvözlő** lapon jelölje be **konfigurálása**.
3. Az a **további feladatok** lapon jelölje be **szinkronizálási beállítások testreszabása**, majd válassza ki **tovább**.
4. Az a **az Azure AD Connect** lapon, majd válassza ki és adja meg egy globális rendszergazdai hitelesítő adatok **tovább**.
5. Az a **címtárak csatlakoztatása** és **Domain/OU** lapok szűrése, válassza ki **tovább**.
6. Az a **választható funkciók** lapon, jelölje be a a **jelszóvisszaíró** válassza **tovább**.
   ![Az Azure AD Connect a jelszóvisszaírás engedélyezése][Writeback]
7. Az a **konfigurálásra kész** lapon jelölje be **konfigurálása** és várjon, amíg a folyamat befejezéséhez.
8. Amikor megjelenik a konfiguráció befejezéséhez, válassza ki a **kilépési**.

A jelszóvisszaírás, kapcsolódó általános hibaelhárítási feladatokat című [a jelszóvisszaíró hibaelhárítása](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) hibaelhárítási című cikkben.

## <a name="active-directory-permissions"></a>Az Active Directory-engedélyek

A fiók megadva, az Azure AD Connect eszközben a következő elemek be kell, ha az SSPR hatókörébe szeretné:

* **Új jelszó létrehozása** 
* **Jelszó módosítása** 
* **Írási engedélyekkel** a `lockoutTime`  
* **Írási engedélyekkel** a `pwdLastSet`
* **Bővített jogosultságokat** a következők:
   * A legfelső szintű objektumához *minden egyes tartományhoz* az adott erdőben
   * Az SSPR hatókörébe kell szeretne a felhasználó szervezeti egységekhez (OU)

Ha nem biztos hivatkozik melyik fiókra az itt ismertetett fiókot, nyissa meg az Azure Active Directory Connect konfigurációs felhasználói Felületet, és válassza ki a **aktuális konfiguráció megtekintése** lehetőséget. A fiókot, amelyet kell hozzáadnia részen **szinkronizált címtárak**.

Ha ezeket az engedélyeket, az egyes erdők MA-szolgáltatásfiókja kezelheti jelszavakat az erdőben lévő felhasználói fiókok nevében. 

> [!IMPORTANT]
> Ha azt elmulasztják, a hozzá ezeket az engedélyeket, majd, annak ellenére, hogy a jelszóvisszaíró megfelelően kell konfigurálni jelenik meg felhasználók hibákat tapasztalnak, amikor azok megkísérlik a helyszíni jelszavak kezeléséhez a felhőben.
>

> [!NOTE]
> Azt is igénybe vehet egy óráig vagy tovább ezen engedélyeket a címtár összes objektumába replikálja.
>

Állítsa be a megfelelő engedélyekkel a jelszóvisszaírást, fordulhat elő, hajtsa végre az alábbi lépéseket:

1. Nyissa meg az Active Directory – felhasználók és számítógépek egy olyan fiókkal, amely a megfelelő tartományi rendszergazdai engedélyekkel rendelkezik.
2. Az a **nézet** menü, győződjön meg arról, **speciális szolgáltatások** be van kapcsolva.
3. A bal oldali panelen kattintson a jobb gombbal a tartomány, és válassza ki a legfelső szintű képviselő objektumot **tulajdonságok** > **biztonsági** > **speciális**.
4. Az a **engedélyek** lapon jelölje be **Hozzáadás**.
5. Válassza ki a fiókot, amelyet engedélyek vannak alkalmazva (az az Azure AD Connect beállítása).
6. Az a **vonatkozik** legördülő listában válassza **leszármazott felhasználó** objektumokat.
7. A **engedélyek**, jelölje be az alábbiakat:
    * **Új jelszó létrehozása**
    * **Jelszó módosítása**
    * **LockoutTime írása**
    * **PwdLastSet írása**
8. Válassza ki **alkalmaz/OK** alkalmazza a módosításokat, és lépjen ki az összes megnyitott párbeszédpanelen.

## <a name="licensing-requirements-for-password-writeback"></a>Jelszóvisszaíró licencelési követelményeket

Licenceléssel kapcsolatos további információkért lásd: [jelszóvisszaírás szükséges licencek](concept-sspr-licensing.md) vagy a következő helyekre:

* [Az Azure Active Directory-hely díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [A Microsoft 365 nagyvállalati verzió](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>A jelszóvisszaíráshoz támogatott a helyszíni hitelesítési-módok

A következő felhasználói jelszó típusú jelszó visszaírási támogatása:

* **Csak felhőalapú felhasználói**: a jelszóvisszaírás nem vonatkozik ebben a helyzetben, mert nem a helyi jelszó.
* **Jelszó-szinkronizált felhasználók**: a jelszóvisszaírás használata támogatott.
* **Összevont felhasználók**: a jelszóvisszaírás használata támogatott.
* **Átmenő hitelesítés felhasználók**: a jelszóvisszaírás használata támogatott.

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>A jelszóvisszaíráshoz támogatott felhasználói és rendszergazdai műveletek

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

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>Felhasználói és rendszergazdai műveletek nem támogatottak a jelszóvisszaíráshoz

A jelszóban *nem* visszaírja a következő helyzetek egyikében:

* **Nem támogatott a végfelhasználói műveletek**
  * A végfelhasználók saját jelszó alaphelyzetbe állítása a PowerShell 1-es verzió, a 2-es vagy az Azure AD Graph API használatával
* **A rendszergazda nem támogatott műveletek**
  * Minden végfelhasználói rendszergazda által kezdeményezett jelszó-visszaállítás a [Office felügyeleti portálján](https://portal.office.com)
  * Minden végfelhasználói rendszergazda által kezdeményezett jelszó-visszaállítás a PowerShell 1-es verzió, 2-es vagy az Azure AD Graph API

Ezek a korlátozások eltávolítása folyamatban van, de nem kell, hogy egy meghatározott ütemterv még is osztjuk.

## <a name="password-writeback-security-model"></a>Jelszóvisszaírás biztonsági modelljéről

A jelszóvisszaíró szolgáltatás rendkívül biztonságos. Az adatok védelme érdekében engedélyezzük a négy többrétegű biztonsági modellt, amelyek leírása a következő:

* **Bérlő-specifikus service-bus Relay használatával**
  * A szolgáltatás beállítása, beállítjuk a bérlő-specifikus a service bus relay-véletlenszerűen generált erős jelszót, amely a Microsoft soha nem rendelkezik hozzáféréssel a védett.
* **Zárolt, titkosítási szempontból erős jelszó-titkosítási kulcs**
  * A service bus relay létrehozása után hozunk létre egy erős szimmetrikus kulcsot, amely titkosítja a jelszót, mivel származik, a hálózaton keresztül használjuk. Ezt a kulcsot csak él a vállalat titkoskód-tárolót a felhőben, amely fokozottan zárolja, és naplózni, mint bármely más jelszót a címtárban.
* **Iparági standard Transport Layer Security (TLS)**
  1. Amikor jelszó alaphelyzetbe állítása, vagy módosítsa a művelethez szükséges, a felhőben, azt igénybe vehet a titkosítatlan szöveges jelszó és titkosítás a nyilvános kulccsal.
  2. Hogy helyezi, amely egy HTTPS-üzenetet, amely szerint a service bus Relay használatával történő Microsoft SSL-tanúsítványok használatával egy titkosított csatornán keresztül továbbítja.
  3. Az üzenet érkezik a service bus, miután a helyi ügynök felébred, és a service bus által a korábban létrehozott erős jelszóval hitelesíti magát.
  4. A helyi ügynök szerzi be a titkosított üzenetekre, és visszafejti azt, hogy hozza létre a titkos kulcsot használatával.
  5. A helyi ügynök próbál állítsa be a jelszót az AD DS SetPassword API-n keresztül. Ez a lépés nem teszi számunkra, hogy az Active Directory a helyszíni jelszóházirend (például az összetettséget, kor, előzményei és szűrők) kényszerítése a felhőben.
* **Üzenet-érvényességi szabályzatok** 
  * Az üzenetet a service bus helyezkedik el, mert a helyszíni szolgáltatással nem működik, ha ez túllépi az időkorlátot, és néhány perc múlva törlődik. Az időkorlát és az üzenet eltávolítása növeli a biztonságot is.

### <a name="password-writeback-encryption-details"></a>Jelszó-visszaírás titkosítási adatok

Miután egy felhasználó beküld egy jelszó-visszaállítás, a visszaállítási kérelem végighalad számos titkosítási lépést, a helyszíni környezetben megérkezése előtt. Ezek a titkosítás lépései győződjön meg arról, maximális szolgáltatás megbízhatóságát. Ezek az alábbiak szerint:

* **1. lépés: Jelszó-titkosítási 2048-bites RSA-kulcsa**: visszaírását a helyszíni jelszó elküldése után a beküldött jelszó magát egy 2048 bites RSA-kulcsa titkosítva van-e.
* **2. lépés: Csomag szintű titkosítást AES-GCM-**: AES-GCM használatával titkosítja a teljes csomag: a jelszó és a szükséges metaadatokat. A titkosítás megakadályozza, hogy bárki közvetlen hozzáférést a mögöttes ServiceBus-csatornára megtekintése vagy illetéktelenül módosítsák a tartalmát.
* **3. lépés: Az összes kommunikáció a TLS/SSL keresztül történik.**: a ServiceBus folytatott minden kommunikáció zajlik az SSL/TLS-csatorna. A titkosítás a tartalom jogosulatlan harmadik felektől származó védi.
* **Az automatikus kulcsváltást hathavonta**: hathavonta, illetve minden alkalommal jelszóvisszaíró le van tiltva, majd újra engedélyezik az Azure AD Connectben. Azt a maximális szolgáltatás biztonsági és biztonságának összes kulcsok automatikusan lecserélhetők.

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

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Új jelszó kérése vagy jelszó módosítása](../user-help/active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](../user-help/active-directory-passwords-reset-register.md).
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Az Azure AD Connect a jelszóvisszaírás engedélyezése"
