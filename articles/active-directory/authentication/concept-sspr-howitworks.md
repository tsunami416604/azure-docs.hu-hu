---
title: Az Azure Active Directory önkiszolgáló jelszó-részletes bemutatása
description: Hogyan alaphelyzetbe állítja a önkiszolgáló jelszó-munkahelyi
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0d1c2a35a6ad246eea593990c485181aa776594
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175095"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>A működési elv: Az Azure AD önkiszolgáló jelszó-visszaállítás

Nem az önkiszolgáló jelszó-visszaállítás (SSPR) munkahelyi? Mi ezt a lehetőséget jelent a felületen? Tudjon meg többet az Azure Active Directory (Azure AD) az SSPR kihívásokra.

|     |
| --- |
| Mobilalkalmazás-értesítés és módszerek a az Azure AD önkiszolgáló jelszó-, mobilalkalmazás-kód alaphelyzetbe állítása a nyilvános előzetes verziójú funkciók az Azure Active Directory. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="how-does-the-password-reset-portal-work"></a>Hogyan a jelszó-visszaállítási portál működése?

Amikor egy felhasználó megnyitja a a jelszó-visszaállítási portál, a munkafolyamat megkezdődik a meghatározásához:

   * Hogyan lehet honosított az oldal?
   * Az a felhasználói fiók érvényes?
   * Milyen a szervezetben a felhasználó tartozik?
   * Hol történik a felhasználó jelszava kezelése?
   * A felhasználó rendelkezik licenccel a funkció használatához?

Olvassa el az alábbi lépések segítségével megismerheti a jelszó mögötti logika-visszaállítási oldalra:

1. A felhasználó kiválasztja az **nem tudja elérni a fiókját** hivatkozásra, vagy közvetlenül kerül [ https://aka.ms/sspr ](https://passwordreset.microsoftonline.com).
   * A böngésző területi beállítása alapján, a tapasztalatok jelenik meg a megfelelő nyelven. A jelszó alaphelyzetbe állítása honosított ugyanazokat a nyelveket, amely támogatja az Office 365-be.
   * Megtekintéséhez a jelszó-változtatási portál eltérő honosított nyelvű hozzáfűzése "? mkt =" a teljes körű, a jelszó alaphelyzetbe állítása URL-cím a spanyol, azaz a következő példában [ https://passwordreset.microsoftonline.com/?mkt=es-us ](https://passwordreset.microsoftonline.com/?mkt=es-us).
2. A felhasználó beírja egy felhasználói Azonosítót, és átadja a captcha.
3. Az Azure AD ellenőrzi, hogy a felhasználó nem használhatja ezt a szolgáltatást felderítéséhez a következő tevékenységek végrehajtásával:
   * Ellenőrzi, hogy a felhasználó rendelkezik-e a szolgáltatás nincs engedélyezve, és rendelkezik az Azure AD-licenccel.
     * Ha a felhasználó nem rendelkezik a szolgáltatás nincs engedélyezve, vagy egy licenccel, a felhasználónak meg kell adnia a saját jelszavuk-rendszergazdától.
   * Ellenőrzi, hogy a felhasználó rendelkezik-e a megfelelő hitelesítési módszereket, a fiók rendszergazdája szabályzatának megfelelően definiálva.
     * Ha a szabályzat csak egy módszert igényel, majd biztosítja azt, hogy a felhasználó rendelkezik-e a megfelelő adatokat, legalább egy hitelesítési módszert a rendszergazda házirend engedélyezve van definiálva.
       * Ha nincsenek konfigurálva a hitelesítési módszereket, majd a felhasználó javasolt a rendszergazdától új jelszót kérnek.
     * Ha a házirend a két módszer van szüksége, majd biztosítja azt, hogy a felhasználó rendelkezik-e a megfelelő adatokat a felügyeleti házirend által engedélyezett hitelesítési módszerek közül legalább két definiálva.
       * Ha nincsenek konfigurálva a hitelesítési módszereket, majd a felhasználó javasolt a rendszergazdától új jelszót kérnek.
     * Ha egy Azure-rendszergazdai szerepkör van rendelve a felhasználó, a két-kapu erős jelszót házirend van érvényben. További információ a szabályzat a szakaszban található [rendszergazda alaphelyzetbe házirend különbségek](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Ellenőrzi, hogy a felhasználó jelszava felügyelt helyszíni (összevont, átmenő hitelesítést, vagy a jelszókivonatok szinkronizálása).
     * Ha a jelszóvisszaíró telepíti, és a felhasználó jelszava a helyszínen kezel, akkor a felhasználó számára engedélyezett a jelszavuk és hitelesítéséhez a folytatáshoz.
     * Ha a jelszóvisszaíró nincs telepítve, és a felhasználó jelszava a helyszínen kezel, akkor a felhasználónak meg kell adnia a rendszergazdától új jelszót kérnek.
4. Ha megállapította, hogy a felhasználó nem tudja sikeresen visszaállítva jelszavukat, majd a felhasználó rendszer végigvezeti a visszaállítás folyamatához.

## <a name="authentication-methods"></a>Hitelesítési módszerek

Ha az SSPR engedélyezve van, választania kell a hitelesítési módszerek az alábbiak közül legalább egyet. Néha hallgassa meg ezek a beállítások "kapuk." néven Kifejezetten ajánljuk, hogy Ön **két vagy több hitelesítési módszer kiválasztása** , hogy a felhasználók rendelkeznek a nagyobb rugalmasságot, arra az esetre, szükség esetén az egyik hozzáférés nem tudnak.

* Mobilalkalmazásbeli értesítés (előzetes verzió)
* Mobilalkalmazás-kód (előzetes verzió)
* E-mail
* Mobiltelefon
* Irodai telefon
* Biztonsági kérdések

Csak alaphelyzetbe állíthatja az felhasználók a jelszavát, ha a hitelesítési módszereket, a rendszergazda engedélyezte az adatok rendelkeznek.

> [!WARNING]
> Azure-rendszergazdai szerepkörök hozzárendelt fiókok lesz szükség az szakaszban definiált metódusok használata [rendszergazda alaphelyzetbe házirend különbségek](concept-sspr-policy.md#administrator-reset-policy-differences).

![Hitelesítés][Authentication]

### <a name="number-of-authentication-methods-required"></a>Hitelesítés szükséges módszerek száma

Ez a beállítás meghatározza, hogy a rendelkezésre álló hitelesítési módszerek vagy visszaállítására vagy zárolásának feloldásához jelszó keresztül kell haladnia a felhasználó kapuk minimális számát. Egy vagy két állítható.

Adja meg a további hitelesítési módszereket, ha a rendszergazda a hitelesítési módszer lehetővé teszi a felhasználók választhat.

Ha egy felhasználó nincs regisztrálva a minimálisan szükséges módszerek, láthatják, amelyek szólítja fel őket, hogy a rendszergazdák új jelszót kérnek kérelem hibalap.

#### <a name="mobile-app-and-sspr-preview"></a>Mobilalkalmazás és az SSPR (előzetes verzió)

A jelszó-visszaállításhoz módszerként mobilalkalmazás, például a Microsoft Authenticator alkalmazás használatakor vegye figyelembe az alábbi korlátozásokkal:

* Ha a rendszergazdák egy módszert igényel használt jelszó alaphelyzetbe állítása, ellenőrző kódot csak a lehetőség érhető el.
* Ha a rendszergazdáknak a két módszer van szükségük lehet használt jelszó alaphelyzetbe állítása, felhasználók is használhatják **vagy** értesítési **vagy** ellenőrzőkódot kívül bármely más módszerek engedélyezve van.

| Az új jelszó kéréséhez szükséges módszerek száma | Az egyik | Kettő |
| :---: | :---: | :---: |
| Elérhető Mobile Apps funkciói | Kód | Kód vagy az értesítés |

Felhasználóknak nem kell regisztrálni a mobilalkalmazásukkal a önkiszolgáló jelszó-átállítási való regisztrálásakor [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup). A mobilalkalmazás, a felhasználók regisztrálhatják [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup), vagy az új biztonsági adatok regisztrációs verzió díjának [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo).

> [!WARNING]
> Engedélyeznie kell a [regisztráció önkiszolgáló jelszó-visszaállítás és az Azure multi-factor Authentication (nyilvános előzetes verzió) összevont](concept-registration-mfa-sspr-converged.md) felhasználók fognak tudni hozzáférni az új felhasználói felületre, mielőtt [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo).

### <a name="change-authentication-methods"></a>Módosítsa a hitelesítési módszerek

Ha először egy szabályzatot, amely csak egy szükséges hitelesítési módszert a visszaállítására vagy zárolásának feloldására rendelkezik a regisztrált, és módosíthatja, hogy a két módszer, mi történik?

| Regisztrált módszerek száma | Szükséges módszerek száma | Eredmény |
| :---: | :---: | :---: |
| 1 vagy több | 1 | **Képes** visszaállítására vagy zárolásának feloldása |
| 1 | 2 | **Nem sikerült** visszaállítására vagy zárolásának feloldása |
| 2 vagy több | 2 | **Képes** visszaállítására vagy zárolásának feloldása |

Ha módosítja a hitelesítési módszereket, amelyek a felhasználó használhatja típusú, a felhasználók nem tudnak az SSPR használatára, ha nem rendelkeznek a minimálisan elérhető adatok véletlenül leállhat.

Példa:
1. Szükséges két hitelesítési módszerek az eredeti házirenddel van konfigurálva. Csak az irodai telefonszámát és a biztonsági kérdések használ. 
2. A rendszergazda módosítja a már nem az a biztonsági kérdések használni kívánt házirendet, de engedélyezi a mobiltelefon és a egy másodlagos e-mail.
3. A mobiltelefonszám vagy a másodlagos e-mail mezők feltöltése nem rendelkező felhasználók a jelszavuk nem állítható alaphelyzetbe.

## <a name="registration"></a>Regisztráció

### <a name="require-users-to-register-when-they-sign-in"></a>Szükséges a felhasználóknak regisztrálniuk a bejelentkezéskor

Ez a beállítás engedélyezése csak a jelszó kérése – regisztráció végrehajtásához, ha bejelentkeznek az Azure AD-vel alkalmazások. Ez a munkafolyamat tartalmazza a következő alkalmazásokat:

* Office 365
* Azure Portal
* Hozzáférési panel
* Összevont alkalmazások
* Egyéni alkalmazások az Azure AD-vel

Regisztráció megkövetelése le van tiltva, a felhasználók manuálisan regisztrálhatnak. Vagy keresse fel a következőkre [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup) vagy válassza ki a **regisztráció új jelszó kéréséhez** hivatkozásra a **profil** fülre a hozzáférési panelen.

> [!NOTE]
> Felhasználó elvetheti a jelszó-visszaállítási portál kiválasztásával **Mégse** vagy zárja be az ablakot. De figyelmeztetést kapnak minden egyes bejelentkezéskor mindaddig, amíg azok a regisztráció befejezéséhez regisztrálja.
>
> Ez a megszakítás nem szünet a felhasználói kapcsolat, ha már bejelentkezett a.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Adja meg, hány nap elteltével a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését

Ez a beállítás határozza meg azt a beállítást, és a hitelesítési adatok reconfirming közötti időszakot, és csak akkor, ha engedélyezi a **szükséges a felhasználóknak regisztrálniuk a bejelentkezéskor** lehetőséget.

Érvényes értékek: 0 és 730 nap, a "0", azaz a felhasználók soha nem kéri a hitelesítési adataik ismételt megerősítését.

## <a name="notifications"></a>Értesítések

### <a name="notify-users-on-password-resets"></a>Értesítse a felhasználókat új jelszó kérésekor?

Ha ez a beállítás értéke **Igen**, majd a jelszó alaphelyzetbe állításával felhasználók kapnak egy e-mailben értesíti őket, hogy módosította a jelszavát. Az e-mailt küld az SSPR-portálon keresztül elsődleges és másodlagos e-mail-címüket, amelyek a fájl az Azure ad-ben. Senki más nem alaphelyzetbe állítása esemény értesítést kap.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérjenek

Ha ez a beállítás értéke **Igen**, majd *minden rendszergazda* kap egy e-mailt a fájl elsődleges e-mail-címére az Azure ad-ben. Az e-mailben értesíti őket, hogy egy másik rendszergazda az SSPR megváltoztatta a jelszavát.

Példa: Nincsenek négy rendszergazdák környezetben. A rendszergazda SSPR segítségével alaphelyzetbe állítja a jelszavát. A rendszergazdák B, C és D őket, az új jelszó kérése e-mailt kapni.

## <a name="on-premises-integration"></a>Helyszíni integráció

Ha telepítése, konfigurálása és az Azure AD Connect engedélyezéséhez, akkor a következő beállítások a helyszíni Integrációk. Ha ezek a beállítások szürkén jelennek meg, majd a jelszóvisszaíró nincs megfelelően konfigurálva. További információkért lásd: [konfigurálása a jelszóvisszaíró](howto-sspr-writeback.md).

![A Jelszóvisszaíró][Writeback]

Ez az oldal tartalmaz egy gyors a helyszíni visszaírási ügyfél állapotát, az alábbi üzenetek egyike jelenik meg a jelenlegi konfiguráció alapján:

* A helyszíni visszaírási ügyfél működik-e.
* Az Azure AD online állapotban, és a helyszíni visszaírási ügyfélhez csatlakozik. Azonban úgy tűnik, az Azure AD Connect telepített verziója elavult. Érdemes lehet [frissítése az Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) , gondoskodjon arról, hogy a legújabb kapcsolati funkciók és a fontos hibajavításokat tartalmaz.
* Sajnos jelenleg nem lehet ellenőrizni a helyszíni visszaírási ügyfél állapotát, mert az Azure AD Connect telepített verziója elavult. [Azure AD Connect frissítése](../hybrid/how-to-upgrade-previous-version.md) tudni ellenőrizni a kapcsolat állapotát.
* Sajnos úgy tűnik, nem lehet csatlakozni a helyszíni visszaírási ügyfélhez most. [Az Azure AD Connect – hibaelhárítás](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) a kapcsolat visszaállításához.
* Sajnos nem nem lehet csatlakozni a helyszíni visszaírási ügyfélhez, mert a jelszóvisszaírás nincs megfelelően konfigurálva. [Jelszóvisszaíró konfigurálása](howto-sspr-writeback.md) a kapcsolat visszaállításához.
* Sajnos úgy tűnik, nem lehet csatlakozni a helyszíni visszaírási ügyfélhez most. Ez található a rendszerünkben ideiglenes problémák okozhatják. Ha a probléma tartósan fennáll, [hibaelhárítása Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) a kapcsolat visszaállításához.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Visszaírja a jelszavakat a helyszíni címtár

Ez a vezérlő határozza meg, hogy a jelszóvisszaíró engedélyezve van-e a könyvtárhoz. A jelszóvisszaíró egy, a helyszíni visszaírási szolgáltatás állapota azt jelzi. Ez a vezérlő akkor hasznos, ha azt szeretné, ideiglenesen letilthatja a jelszóvisszaírást az Azure AD Connect újrakonfigurálása nélkül.

* Ha a kapcsoló beállítása **Igen**, majd a jelszóvisszaíró engedélyezve van, és összevont, átmenő hitelesítést, vagy a jelszó Jelszókivonat szinkronizálása felhasználók képesek-e a jelszavaikat.
* Ha a kapcsoló beállítása **nem**, majd a jelszóvisszaíró le van tiltva, és összevont, átmenő hitelesítést, vagy a jelszó Jelszókivonat szinkronizálása felhasználók számára nem érhetők el a jelszavaikat.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Engedélyezése a felhasználók számára a fiókok zárolásának feloldása új jelszó nélkül

Ez a vezérlő azt jelzi, hogy a felhasználók, akik látogasson el a jelszó-visszaállítási portál meg kell adni a lehetőséget, azok a helyszíni Active Directory-fiókok zárolásának ne kelljen új jelszót kérnek. Alapértelmezés szerint az Azure AD fiókok feloldja a jelszó-visszaállítás végrehajtása során. E két művelet külön ezt a beállítást használja. 

* Ha beállítása **Igen**, majd a felhasználók a jelszavuk és a zárolás feloldásához, vagy a fiók feloldása új jelszó nélkül kapnak.
* Ha beállítása **nem**, majd felhasználók vannak csak tudni elvégezni a kombinált jelszó alaphelyzetbe állítása és fiókok zárolásának feloldása műveletet.

### <a name="on-premises-active-directory-password-filters"></a>A helyszíni Active Directory jelszószűrők

Az Azure AD önkiszolgáló jelszó-visszaállítás felel meg egy rendszergazda által kezdeményezett jelszó-visszaállítás, az Active Directory hajt végre. Ha egy külső jelszószűrő használatával egyéni jelszó szabályok érvényesítése, és szüksége van, hogy a jelszószűrő jelölőnégyzet be van jelölve során az Azure AD önkiszolgáló jelszó-visszaállítási, győződjön meg arról, hogy a külső jelszó szűrő megoldás van-e konfigurálva a alkalmazni a rendszergazdai jelszó alaphelyzetbe állítása a forgatókönyvet. [A Windows Server Active Directory Azure AD jelszóvédelem](concept-password-ban-bad-on-premises.md) alapértelmezés szerint támogatott.

## <a name="password-reset-for-b2b-users"></a>Új jelszó kérését az B2B-felhasználók

Az összes-vállalatközi (B2B) konfiguráció teljes mértékben támogatottak a jelszó-visszaállítás, és módosítsa. B2B-felhasználói jelszó-visszaállítás a következő három esetben támogatják:

   * **Egy fiókpartner-szervezet egy meglévő Azure AD-bérlővel rendelkező felhasználók**: Ha a szervezetben használt együttműködését abban, meglévő Azure AD-bérlővel, hogy *tiszteletben bármilyen jelszóvisszaállítási szabályzatot engedélyezve vannak a bérlőt*. Jelszó-visszaállítás működéséhez az erőforráspartner-szervezet csak kell győződjön meg arról, hogy engedélyezve van-e az Azure AD SSPR. Semmilyen további díjat nem az Office 365-ügyfelek és a lépéseket követve is engedélyezhető a [jelszókezelés használatának első lépései](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) útmutató.
   * **Felhasználók, akik keresztül regisztrálnak** önkiszolgáló: Ha a szervezet, hogy együttműködését abban, használja a [önkiszolgáló](../users-groups-roles/directory-self-service-signup.md) kerülnek a bérlőt a funkciót, azt, hogy értesítse az e-mail-címével, azok regisztrálva a jelszó alaphelyzetbe állítása.
   * **B2B-felhasználók**: Minden olyan új B2B-felhasználók hozta létre az új [Azure AD B2B-funkciói](../active-directory-b2b-what-is-azure-ad-b2b.md) is elérhetik a jelszavaikat az e-mailt, azok regisztrálva a meghívó folyamat során.

Ez a forgatókönyv teszteléséhez nyissa meg https://passwordreset.microsoftonline.com egy partner felhasználók. Ha egy másodlagos vagy hitelesítési e-mail meghatározott rendelkeznek, új jelszó kérésének módja elvárt módon.

> [!NOTE]
> Kapott a vendéghozzáférés az Azure AD-bérlőhöz, a Hotmail.com, Outlook.com vagy más személyes e-mail-címeket, például a Microsoft-fiókok, amelyek nem használhatják az Azure AD SSPR. Szükségük van a jelszó átállításának található információk segítségével a [mikor nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.

## <a name="next-steps"></a>További lépések

Az alábbi cikkekben az Azure AD jelszóátállításáról olvashat további információkat:

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó visszaállítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../user-help/active-directory-passwords-reset-register.md)
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/sspr-authentication-methods.png "Az elérhető Azure AD-hitelesítési módszerek és a szükséges mennyiség"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "A helyszíni integrációs jelszó a jelszóvisszaíró konfigurálása és a hibaelhárítási információk"
