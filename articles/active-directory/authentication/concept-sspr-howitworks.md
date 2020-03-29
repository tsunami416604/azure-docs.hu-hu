---
title: Önkiszolgáló jelszó-visszaállítás – mélymerülés – Azure Active Directory
description: Hogyan működik az önkiszolgáló jelszó-visszaállítás?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b19c80378aa40a7f791a3eb61130b013217ddee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848578"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Hogyan működik: Az Azure AD önkiszolgáló jelszó-visszaállítás

Hogyan működik az önkiszolgáló jelszó-visszaállítás (SSPR) Mit jelent ez a lehetőség a felületen? Folytatódik olvasó, hogy többet megtudjon az Azure Active Directory (Azure AD) SSPR.

## <a name="how-does-the-password-reset-portal-work"></a>Hogyan működik a jelszó-visszaállítási portál?

Amikor egy felhasználó a jelszó-visszaállítási portálra lép, a munkafolyamat elindul, hogy meghatározza:

   * Hogyan kell honosítani az oldalt?
   * Érvényes a felhasználói fiók?
   * Melyik szervezethez tartozik a felhasználó?
   * Hol kezelik a felhasználó jelszavát?
   * A felhasználó rendelkezik engedéllyel a funkció használatára?

Olvassa el az alábbi lépéseket, hogy megismerjék a jelszó-visszaállítási lap mögötti logikát:

1. A felhasználó a **Nem fér hozzá a fiókhoz hivatkozást,** vagy közvetlenül a . [https://aka.ms/sspr](https://passwordreset.microsoftonline.com)
   * A böngésző területi beállításai alapján a felhasználói felület a megfelelő nyelven jelenik meg. A jelszó-visszaállítási felület az Office 365 által támogatott nyelveken honosítódik.
   * Ha a jelszó-visszaállítási portált egy másik honosított nyelven szeretné megtekinteni, a jelszó-visszaállítási URL-cím [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us)ének végéhez fűzi a jelszó-visszaállítási URL-címéhez a spanyol ra való honosítást követő példával.
2. A felhasználó beírja a felhasználói azonosítót, és átadja a captcha.
3. Az Azure AD a következő ellenőrzések elvégzésével ellenőrzi, hogy a felhasználó használhatja-e ezt a funkciót:
   * Ellenőrzi, hogy a felhasználó rendelkezik-e ezzel a funkcióval, és rendelkezik-e Hozzárendelt Azure AD-licenccel.
     * Ha a felhasználó nem rendelkezik ezzel a funkcióval, vagy licenc van hozzárendelve, a rendszer felkéri a felhasználót, hogy lépjen kapcsolatba a rendszergazdával a jelszó alaphelyzetbe állítása érdekében.
   * Ellenőrzi, hogy a felhasználó rendelkezik-e a megfelelő hitelesítési módszerekkel a fiókjában a rendszergazdai házirendnek megfelelően.
     * Ha a házirend csak egy módszert igényel, akkor biztosítja, hogy a felhasználó a rendszergazdai házirend által engedélyezett hitelesítési módszerek legalább egyikéhez megfelelő adatokat definiáljon.
       * Ha a hitelesítési módszerek nincsenek konfigurálva, akkor a felhasználónak azt tanácsoljuk, hogy lépjen kapcsolatba a rendszergazdával a jelszó alaphelyzetbe állításához.
     * Ha a házirend hez két módszer szükséges, akkor biztosítja, hogy a felhasználó a rendszergazdai házirend által engedélyezett hitelesítési módszerek közül legalább kettőhöz megfelelő adatokkal rendelkezzen.
       * Ha a hitelesítési módszerek nincsenek konfigurálva, akkor a felhasználónak azt tanácsoljuk, hogy lépjen kapcsolatba a rendszergazdával a jelszó alaphelyzetbe állításához.
     * Ha egy Azure-rendszergazdai szerepkör van rendelve a felhasználóhoz, majd az erős kétkapus jelszóházirend kényszerítve. Erről a házirendről további információt a [Rendszergazda házirend-különbségek visszaállítása](concept-sspr-policy.md#administrator-reset-policy-differences)című szakaszban talál.
   * Ellenőrzi, hogy a felhasználó jelszava a helyszínen van-e kezelve (összevont, átmenő hitelesítés vagy jelszókivonat szinkronizálva).
     * Ha a visszaírás telepítve van, és a felhasználó jelszavát a helyszínen kezelik, akkor a felhasználó folytathatja a hitelesítést, és alaphelyzetbe állíthatja a jelszavát.
     * Ha a visszaírás nincs telepítve, és a felhasználó jelszavát a helyszínen kezelik, akkor a felhasználónak kapcsolatba kell lépnie a rendszergazdával a jelszó alaphelyzetbe állításához.
4. Ha megállapítást nyer, hogy a felhasználó sikeresen vissza tudja állítani a jelszavát, akkor a rendszer végigvezeti a felhasználót az alaphelyzetbe állítási folyamaton.

## <a name="authentication-methods"></a>Hitelesítési módszerek

Ha az SSPR engedélyezve van, a hitelesítési módszerekhez legalább az alábbi lehetőségek közül kell választania. Néha hallja ezeket a lehetőségeket a továbbiakban "kapuk." Javasoljuk, hogy **válasszon két vagy több hitelesítési módszert,** hogy a felhasználók nagyobb rugalmasságot kaphassanak abban az esetben, ha nem tudnak hozzáférni egyhez, amikor szükségük van rá. Az alább felsorolt módszerekkel kapcsolatos további részletek a [Mik a hitelesítési módszerek?](concept-authentication-methods.md).

* Mobilalkalmazás-értesítés
* Mobilalkalmazás-kód
* E-mail
* Mobiltelefon
* Munkahelyi telefon
* Biztonsági kérdések

A felhasználók csak akkor állíthatják alaphelyzetbe a jelszavukat, ha a rendszergazda által engedélyezett hitelesítési módszerekben adatok vannak.

> [!IMPORTANT]
> 2019 márciusátantól a telefonhívási beállítások nem lesznek elérhetők az MFA- és SSPR-felhasználók számára az ingyenes/próbaverziós Azure AD-bérlőkben. Az SMS-üzeneteket ez a változás nem érinti. A telefonhívás továbbra is elérhető lesz a fizetős Azure AD-bérlők felhasználói számára. Ez a módosítás csak az ingyenes/próba verziós Azure AD-bérlők.

> [!WARNING]
> Az Azure-rendszergazdai szerepkörökhöz rendelt fiókoknak a [Rendszergazda alaphelyzetbe állítási házirend-különbségek](concept-sspr-policy.md#administrator-reset-policy-differences)című szakaszban meghatározott metódusokat kell használniuk.

![A hitelesítési módszerek kiválasztása az Azure Portalon][Authentication]

### <a name="number-of-authentication-methods-required"></a>A szükséges hitelesítési módszerek száma

Ez a beállítás határozza meg a rendelkezésre álló hitelesítési módszerek vagy kapuk minimális számát, amelyen a felhasználónak át kell mennie a jelszó alaphelyzetbe állításához vagy feloldásához. Beállítható egy vagy kettőre.

A felhasználók több hitelesítési módszert is megadhatnak, ha a rendszergazda engedélyezi a hitelesítési módszert.

Ha a felhasználó nem rendelkezik a minimálisan szükséges módszerekkel, megjelenik egy hibaoldal, amely arra utasítja őket, hogy kérje a rendszergazda jelszavának alaphelyzetbe állítását.

#### <a name="mobile-app-and-sspr"></a>Mobilalkalmazás és SSPR

Ha mobilalkalmazást, például a Microsoft Authenticator alkalmazást használja a jelszó-visszaállítás isztrási módszerként, tisztában kell lennie a következő kikötésekkel:

* Ha a rendszergazdák nak egy módszert kell használniuk a jelszó alaphelyzetbe állításához, az ellenőrző kód az egyetlen elérhető lehetőség.
* Ha a rendszergazdák nak két módszert kell használniuk a jelszó alaphelyzetbe állításához, a felhasználók bármely más engedélyezett módszer mellett **vagy** értesítési **vagy** ellenőrző kódot is használhatnak.

| Az alaphelyzetbe állításhoz szükséges módszerek száma | Eggyel | Kettővel |
| :---: | :---: | :---: |
| Elérhető mobilalkalmazás-funkciók | Kód | Kód vagy értesítés |

A felhasználóknak nincs lehetőségük regisztrálni a mobilalkalmazásukat, amikor önkiszolgáló jelszó-visszaállításra regisztrálnak a rendszerből. [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) A felhasználók regisztrálhatják [https://aka.ms/mfasetup](https://aka.ms/mfasetup)mobilalkalmazásukat a ban vagy [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)az új biztonsági adatok regisztrációs előnézetében a rendszerben.

> [!WARNING]
> Ahhoz, hogy a felhasználók hozzáférhessenek az új élményhez, engedélyeznie kell az [önkiszolgáló jelszó-visszaállítás és az Azure multi-factor authentication (Nyilvános előzetes verzió) konvergens regisztrációját.](concept-registration-mfa-sspr-converged.md) [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)

> [!IMPORTANT]
> A hitelesítő alkalmazás nem választható ki egyetlen hitelesítési módszerként az egykapus házirend konfigurálásakor. Hasonlóképpen a hitelesítő alkalmazás és csak egy további módszer nem választható ki a 2-kapus házirend konfigurálásakor.
> Ezután a hitelesítő alkalmazást metódusként tartalmazó SSPR-házirendek konfigurálásakor legalább egy további módszert kell kiválasztani az egykapus házirend konfigurálásakor, és legalább két további módszert kell kiválasztani a kétkapus házirend konfigurálásakor.
> Ennek oka az, hogy a jelenlegi SSPR regisztrációs élmény nem tartalmazza a hitelesítő alkalmazás regisztrálásának lehetőségét. A hitelesítő alkalmazás regisztrálásának lehetősége megtalálható az [önkiszolgáló jelszó-visszaállítás és az Azure multi-factor authentication (nyilvános előzetes verzió) új konvergens](concept-registration-mfa-sspr-converged.md)regisztrációjával.
> Ha engedélyezi a csak a hitelesítő alkalmazást (1-gate-szabályzatokhoz) vagy a hitelesítő alkalmazást, és csak egy további módszert (a 2 kapus házirendek esetében), a felhasználók letiltása az SSPR-re való regisztrációtól vezethet, amíg be nem vannak állítva az új regisztrációs élményt nyújt.

### <a name="change-authentication-methods"></a>Hitelesítési módszerek módosítása

Ha olyan házirenddel kezdi, amely csak egy szükséges hitelesítési módszert tartalmaz a regisztrált alaphelyzetbe állításhoz vagy feloldáshoz, és ezt két módszerre módosítja, mi történik?

| A regisztrált módszerek száma | A szükséges módszerek száma | Eredmény |
| :---: | :---: | :---: |
| 1 vagy több | 1 | **Képes** alaphelyzetbe állítani vagy feloldani |
| 1 | 2 | **Nem lehet** alaphelyzetbe állítani vagy feloldani |
| 2 vagy több | 2 | **Képes** alaphelyzetbe állítani vagy feloldani |

Ha módosítja a felhasználó által használható hitelesítési módszerek típusait, előfordulhat, hogy véletlenül leállítja a felhasználókat az SSPR használatában, ha nem rendelkeznek a rendelkezésre álló minimális adatmennyiséggel.

Példa:
1. Az eredeti házirend két hitelesítési módszerrel van konfigurálva. Csak az irodai telefonszámot és a biztonsági kérdéseket használja. 
2. A rendszergazda úgy módosítja a házirendet, hogy már ne használja a biztonsági kérdéseket, de lehetővé teszi a mobiltelefon és egy másodlagos e-mail használatát.
3. A feltöltött mobiltelefon- vagy másodlagos e-mail mezőkkel nem rendelkező felhasználók nem állíthatnak vissza jelszóval.

## <a name="registration"></a>Regisztráció

### <a name="require-users-to-register-when-they-sign-in"></a>A felhasználók regisztrációjának megkövetelése bejelentkezéskor

Ennek a beállításnak engedélyezése megköveteli, hogy a felhasználó töltse ki a jelszó-visszaállítási regisztrációt, ha bejelentkezik az Azure AD-vel bármely alkalmazásba. Ez a munkafolyamat a következő alkalmazásokat tartalmazza:

* Office 365
* Azure portál
* Hozzáférési panel
* Összevont alkalmazások
* Egyéni alkalmazások az Azure AD használatával

Ha a regisztráció megkövetelése le van tiltva, a felhasználók manuálisan regisztrálhatnak. A Hozzáférési [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) panel **Profil** lapján felkeresik vagy kiválaszthatják a **Jelszó-visszaállítás regisztrálása** hivatkozást.

> [!NOTE]
> A felhasználók elvethetik a jelszó-visszaállítási regisztrációs portált a **Mégse** gombra kattintva vagy az ablak bezárásával. A rendszer azonban minden bejelentkezéskor regisztrálnia kell, amíg be nem fejezi a regisztrációt.
>
> Ez a megszakítás nem szakítja meg a felhasználó kapcsolatát, ha már be van jelentkezve.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>A felhasználók hitelesítési adatainak újbóli megerősítésére irányuló kérés előtti napok számának beállítása

Ez a beállítás határozza meg a hitelesítési adatok beállítása és újbóli megerősítése közötti időtartamot, és csak akkor érhető el, ha engedélyezi a **Felhasználók regisztrálásának megkövetelése bejelentkezéskor** beállítást.

Az érvényes értékek 0 és 730 nap között vannak, a "0" érték pedig azt jelenti, hogy a felhasználóknak soha nem kell újra megerősíteniük a hitelesítési adataikat.

## <a name="notifications"></a>Értesítések

### <a name="notify-users-on-password-resets"></a>Értesítse a felhasználókat új jelszó kérésekor?

Ha ez a beállítás **Igen**, akkor a jelszó alaphelyzetbe állítása koront kap egy e-mailben, amelyben értesíti őket a jelszavuk módosításáról. Az e-mail az SSPR-portálon keresztül kerül elküldésre az Elsődleges és másodlagos e-mail címekre, amelyek az Azure AD-ben fájlban vannak. Senki más nem kap értesítést az alaphelyzetbe állítási eseményről.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Az összes rendszergazda értesítése, ha más rendszergazdák visszaállítják a jelszavukat

Ha ez a beállítás **Igen**, majd *minden rendszergazda* kap egy e-mailt az elsődleges e-mail címére az Azure AD fájlban. Az e-mail értesíti őket arról, hogy egy másik rendszergazda megváltoztatta a jelszavát az SSPR használatával.

Példa: Négy rendszergazda van egy környezetben. Az "A" rendszergazda visszaállítja a jelszavát az SSPR használatával. A B, C és D rendszergazdák e-mailt kapnak, amely figyelmezteti őket a jelszó-visszaállításról.

## <a name="on-premises-integration"></a>Helyszíni integráció

Ha telepíti, konfigurálja és engedélyezi az Azure AD Connectet, a következő további lehetőségek et kínál a helyszíni integrációkhoz. Ha ezek a beállítások szürkén jelennek meg, akkor a visszaírás nincs megfelelően konfigurálva. További információt a [Jelszó-visszaírás konfigurálása](howto-sspr-writeback.md)című témakörben talál.

![A jelszó-visszaírás érvényesítése engedélyezve van és működik][Writeback]

Ez a lap a helyszíni visszaíró ügyfél gyors állapotát biztosítja, az aktuális konfiguráció alapján az alábbi üzenetek egyike jelenik meg:

* A helyszíni visszaíró ügyfél működik.
* Az Azure AD online állapotban van, és csatlakozik a helyszíni visszaíró ügyfélhez. Azonban úgy néz ki, hogy az Azure AD Connect telepített verziója elavult. Fontolja meg [az Azure AD Connect frissítését,](../hybrid/how-to-upgrade-previous-version.md) és győződjön meg arról, hogy rendelkezik a legújabb kapcsolódási funkciókkal és fontos hibajavításokkal.
* Sajnos nem tudjuk ellenőrizni a helyszíni visszaíró ügyfél állapotát, mert az Azure AD Connect telepített verziója elavult. [Frissítse az Azure AD Connectet](../hybrid/how-to-upgrade-previous-version.md) a kapcsolat állapotának ellenőrzéséhez.
* Sajnos úgy tűnik, hogy jelenleg nem tudunk csatlakozni a helyszíni visszaíró ügyfélhez. [Az Azure AD Connect hibaelhárítása](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) a kapcsolat visszaállításához.
* Sajnos nem tudunk csatlakozni a helyszíni visszaíró klienshez, mert a jelszó-visszaírás nincs megfelelően konfigurálva. [A jelszó-visszaírás konfigurálása](howto-sspr-writeback.md) a kapcsolat visszaállításához.
* Sajnos úgy tűnik, hogy jelenleg nem tudunk csatlakozni a helyszíni visszaíró ügyfélhez. Ez lehet az oka, hogy átmeneti kérdések a mi oldalon. Ha a probléma továbbra is fennáll, [az Azure AD Connect hibaelhárításával](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) állítsa vissza a kapcsolatot.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Jelszavak visszaírása a helyszíni címtárba

Ez a vezérlő határozza meg, hogy engedélyezve van-e a jelszóvisszaírás ehhez a könyvtárhoz. Ha a visszaírás be van kapcsolva, az a helyszíni visszaíró szolgáltatás állapotát jelzi. Ez a vezérlő akkor hasznos, ha ideiglenesen le szeretné tiltani a jelszó-visszaírást az Azure AD Connect újrakonfigurálása nélkül.

* Ha a kapcsoló **igen**értékre van állítva, akkor a visszaírás engedélyezve van, és az összevont, átmenő hitelesítés vagy jelszókivonatos felhasználók visszaállíthatják jelszavukat.
* Ha a kapcsoló **nem**, majd a visszaírás le van tiltva, és az összevont, átmenő hitelesítés vagy jelszókivonatos felhasználók nem tudják visszaállítani a jelszavukat.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>A fiókok zárolásának engedélyezése a fiókok zárolásának feloldásához a jelszó alaphelyzetbe állítása nélkül

Ez a vezérlő azt határozza meg, hogy a jelszó-visszaállítási portált feltűnő felhasználók számára biztosítani kell-e a helyszíni Active Directory-fiókok zárolásának feloldását anélkül, hogy alaphelyzetbe kellene állítania a jelszavukat. Alapértelmezés szerint az Azure AD feloldja a fiókok, amikor jelszó-visszaállítás t hajt végre. Ezzel a beállítással elválaszthatja ezt a két műveletet.

* Ha az **Igen**beállítás van megadva, akkor a felhasználók nak lehetőségük van a jelszó visszaállítására és a fiók feloldására, illetve a fiók zárolásának feloldására anélkül, hogy a jelszót vissza kellene állítaniuk.
* Ha a beállítás **nem,** akkor a felhasználók csak kombinált jelszó-visszaállítási és fiókfeloldási műveletet hajthatnak végre.

### <a name="on-premises-active-directory-password-filters"></a>Helyszíni Active Directory jelszószűrők

Az Azure AD önkiszolgáló jelszó-visszaállítás a rendszergazda által kezdeményezett jelszó-alaphelyzetbe állítással egyenértékű az Active Directoryban. Ha külső jelszószűrőt használ az egyéni jelszószabályok kényszerítéséhez, és megköveteli, hogy ez a jelszószűrő be legyen jelölve az Azure AD önkiszolgáló jelszó-visszaállítás során, győződjön meg arról, hogy a harmadik féltől származó jelszószűrő-megoldás úgy van beállítva, hogy a rendszergazdai jelszó-visszaállítási forgatókönyv. [A Windows Server Active Directory Azure AD jelszavas védelme](concept-password-ban-bad-on-premises.md) alapértelmezés szerint támogatott.

## <a name="password-reset-for-b2b-users"></a>Jelszó-visszaállítás B2B-felhasználók számára

A jelszó-visszaállítás és -módosítás teljes mértékben támogatott minden vállalkozáson ként (B2B) konfigurációban. A B2B felhasználói jelszó alaphelyzetbe állítását a következő három eset támogatja:

* **Meglévő Azure AD-bérlővel rendelkező partnerszervezet felhasználói:** Ha a partneri kapcsolatban álló szervezet rendelkezik egy meglévő Azure AD-bérlővel, *tiszteletben tartjuk az adott bérlőn engedélyezett jelszó-visszaállítási szabályzatokat.* A jelszó-visszaállítás működéséhez a partnerszervezetnek csak meg kell győződnie arról, hogy az Azure AD SSPR engedélyezve van. Az Office 365-ügyfelek számára nem számítunk fel további díjat, és az Első lépések [a jelszókezelési](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) útmutatóban leírt lépések végrehajtásával engedélyezhető.
* Azok a felhasználók, akik önkiszolgáló **regisztrációval regisztrálnak:** Ha az önkiszolgáló regisztrációs funkciót használó szervezet az [önkiszolgáló regisztrációs](../users-groups-roles/directory-self-service-signup.md) funkciót használta a bérlőbe való beutazáshoz, akkor lehetővé tesszük számára, hogy alaphelyzetbe állítsa a jelszót a regisztrált e-maillel.
* **B2B-felhasználók:** Az új [Azure AD B2B-funkciók](../active-directory-b2b-what-is-azure-ad-b2b.md) kal létrehozott új B2B-felhasználók is visszaállíthatják jelszavukat a meghívási folyamat során regisztrált e-maillel.

A forgatókönyv teszteléséhez https://passwordreset.microsoftonline.com nyissa meg az egyik partnerfelhasználóval. Ha van egy másik e-mail vagy hitelesítési e-mail definiálva, jelszó-visszaállítás működik a várt módon.

> [!NOTE]
> Az Azure AD-bérlőhöz vendéghozzáféréssel rendelkező Microsoft-fiókok, például a Hotmail.com, Outlook.com vagy más személyes e-mail címek, nem használhatják az Azure AD SSPR-t. A ["Ha nem tud bejelentkezni a Microsoft-fiók"](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikkben található információk alapján vissza kell állítaniuk a jelszavukat.

## <a name="next-steps"></a>További lépések

Az alábbi cikkekben az Azure AD jelszóátállításáról olvashat további információkat:

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó alaphelyzetbe állítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció az önkiszolgáló jelszó alaphelyzetbe állításához](../user-help/active-directory-passwords-reset-register.md)
* [Van engedélyezési kérdése?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, valami eltört. Hogyan háríthatók el az SSPR hibái?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Az elérhető Azure AD-hitelesítési módszerek és a szükséges mennyiség"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "Helyszíni integrációs jelszó-visszaírási konfiguráció és hibaelhárítási információk"
