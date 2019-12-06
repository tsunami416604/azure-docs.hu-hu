---
title: Önkiszolgáló jelszó-visszaállítás – Azure Active Directory
description: Hogyan működik az önkiszolgáló jelszó-visszaállítási funkció
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
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848578"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Működés: Azure AD önkiszolgáló jelszó-visszaállítás

Hogyan működik az önkiszolgáló jelszó-visszaállítás (SSPR)? Mit jelent ez a beállítás az illesztőfelületen? Folytassa az olvasást a Azure Active Directory (Azure AD) SSPR kapcsolatos további információkért.

## <a name="how-does-the-password-reset-portal-work"></a>Hogyan működik a jelszó-visszaállítási portál?

Amikor egy felhasználó a jelszó-visszaállítási portálra lép, a rendszer a következő lépésekkel határozza meg a munkafolyamatot:

   * Hogyan kell honosítani a lapot?
   * Érvényes a felhasználói fiók?
   * Milyen szervezethez tartozik a felhasználó?
   * Hol van a felhasználó jelszava felügyelve?
   * A felhasználó licenccel rendelkezik a szolgáltatás használatához?

Olvassa el a következő lépéseket a jelszó-visszaállítási oldal mögötti logika megismeréséhez:

1. A felhasználó kiválasztja a **nem tud hozzáférni a fiók** hivatkozásához, vagy közvetlenül a [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
   * A böngésző területi beállítása alapján a felhasználói élmény a megfelelő nyelven jelenik meg. A jelszó-visszaállítási élmény az Office 365 által támogatott nyelvekre van honosítva.
   * Ha egy másik honosított nyelven szeretné megtekinteni a jelszó-változtatási portált, fűzze hozzá a "? MKT =" kifejezést a jelszó-visszaállítási URL-cím végéhez a következő példával: spanyol [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us).
2. A felhasználó beírja a felhasználói azonosítót, és átadja a CAPTCHA-t.
3. Az Azure AD ellenőrzi, hogy a felhasználó használhatja-e ezt a szolgáltatást a következő ellenőrzések végrehajtásával:
   * Ellenőrzi, hogy a felhasználó rendelkezik-e ezzel a szolgáltatással, és van-e hozzárendelve Azure AD-licenc.
     * Ha a felhasználó nem engedélyezte ezt a funkciót, vagy licence van hozzárendelve, a felhasználónak kapcsolatba kell lépnie a rendszergazdával a jelszavuk alaphelyzetbe állításához.
   * Ellenőrzi, hogy a felhasználó rendelkezik-e a megfelelő hitelesítési módszerekkel a fiókjában a rendszergazdai házirendnek megfelelően.
     * Ha a házirend csak egy metódust igényel, akkor gondoskodik arról, hogy a felhasználó a rendszergazdai házirend által engedélyezett hitelesítési módszerek közül legalább az egyikhez a megfelelő adatmennyiséget adja meg.
       * Ha a hitelesítési módszerek nincsenek konfigurálva, a felhasználónak javasoljuk, hogy lépjen kapcsolatba a rendszergazdával a jelszavának alaphelyzetbe állításához.
     * Ha a házirend két módszert igényel, akkor biztosíthatja, hogy a felhasználó a rendszergazdai házirend által engedélyezettnél legalább két hitelesítési módszerhez megadott megfelelő adatmennyiséggel rendelkezik.
       * Ha a hitelesítési módszerek nincsenek konfigurálva, a felhasználónak javasoljuk, hogy lépjen kapcsolatba a rendszergazdával a jelszavának alaphelyzetbe állításához.
     * Ha a felhasználóhoz egy Azure-rendszergazdai szerepkör van rendelve, akkor a rendszer kikényszeríti a kétkapus erős jelszavas házirendet. A szabályzattal kapcsolatos további információkért tekintse meg a [rendszergazdai házirend-visszaállítási különbségek](concept-sspr-policy.md#administrator-reset-policy-differences)című szakaszt.
   * Ellenőrzi, hogy a felhasználó jelszava felügyelt-e a helyszínen (összevont, átmenő hitelesítés vagy jelszó-kivonat szinkronizálva).
     * Ha a visszaírási telepítve van, és a felhasználó jelszava felügyelve van a helyszínen, akkor a felhasználó folytathatja a jelszó hitelesítését és alaphelyzetbe állítását.
     * Ha a visszaírási nincs telepítve, és a felhasználó jelszava felügyelve van a helyszínen, a felhasználónak kapcsolatba kell lépnie a rendszergazdával a jelszavuk alaphelyzetbe állításához.
4. Ha megállapítható, hogy a felhasználó sikeresen visszaállíthatja a jelszavát, akkor a felhasználó az alaphelyzetbe állítási folyamaton keresztül vezérli.

## <a name="authentication-methods"></a>Hitelesítési módok

Ha a SSPR engedélyezve van, ki kell választania legalább egyet a következő beállítások közül a hitelesítési módszerekhez. Időnként ezeket a beállításokat "Gates"-ként is hallja. Javasoljuk, hogy **két vagy több hitelesítési módszert válasszon** , hogy a felhasználók nagyobb rugalmasságot biztosítsanak abban az esetben, ha nem tudnak hozzáférni az egyikhez. Az alább felsorolt módszerekkel kapcsolatos további információk a következő cikkben találhatók: [Mi a hitelesítési módszer?](concept-authentication-methods.md).

* Mobilalkalmazás-értesítés
* Mobilalkalmazás kódja
* E-mail cím
* Mobiltelefon
* Irodai telefon
* Biztonsági kérdések

A felhasználók csak akkor állíthatják alaphelyzetbe a jelszavukat, ha a rendszergazda által engedélyezett hitelesítési módszerekben találhatók.

> [!IMPORTANT]
> Az 2019-as naptól kezdve a telefonhívási lehetőségek nem lesznek elérhetők az MFA és a SSPR felhasználók számára ingyenes/próbaverziós Azure AD-bérlők esetében. Ez a változás nem érinti az SMS-üzeneteket. A telefonos hívás továbbra is elérhető lesz a fizetős Azure AD-bérlők felhasználói számára. Ez a változás csak az ingyenes/próbaverziós Azure AD-bérlőket befolyásolja.

> [!WARNING]
> Az Azure-rendszergazdai szerepkörökhöz hozzárendelt fiókokat a [felügyeleti szabályzatok közötti különbségek](concept-sspr-policy.md#administrator-reset-policy-differences)szakaszban meghatározott módszerek szerint kell használni.

![A hitelesítési módszerek kiválasztása a Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>A szükséges hitelesítési módszerek száma

Ez a beállítás határozza meg a rendelkezésre álló hitelesítési módszerek vagy kapuk minimális számát, ha a felhasználónak át kell lépnie a jelszó alaphelyzetbe állításához vagy feloldásához. Egy vagy kettő is beállítható.

A felhasználók dönthetnek úgy, hogy további hitelesítési módszereket biztosítanak, ha a rendszergazda engedélyezi ezt a hitelesítési módszert.

Ha a felhasználó nem rendelkezik a regisztrált minimálisan szükséges módszerekkel, akkor egy hibaüzenet jelenik meg, amely arra utasítja a felhasználót, hogy kérje, hogy a rendszergazda állítsa vissza a jelszavát.

#### <a name="mobile-app-and-sspr"></a>Mobile App és SSPR

Ha mobil alkalmazást használ, például a Microsoft Authenticator alkalmazást a jelszó-visszaállítási módszerként, vegye figyelembe az alábbi figyelmeztetéseket:

* Ha a rendszergazdáknak a jelszó alaphelyzetbe állításához egy metódust kell használni, az ellenőrzési kód az egyetlen elérhető lehetőség.
* Ha a rendszergazdáknak két módszert kell használniuk a jelszó alaphelyzetbe állításához, a felhasználók a többi engedélyezett módszer **mellett értesítési** **vagy** ellenőrzési kódokat is használhatnak.

| Az új jelszó kéréséhez szükséges módszerek száma | Egy | Kettő |
| :---: | :---: | :---: |
| Elérhető a Mobile App szolgáltatásai | Kód | Kód vagy értesítés |

A felhasználók nem regisztrálhatják a mobil alkalmazásaikat az önkiszolgáló jelszó-visszaállítás [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)való regisztrálásakor. A felhasználók a [https://aka.ms/mfasetup](https://aka.ms/mfasetup)címen regisztrálhatják a mobil alkalmazást, vagy az új biztonsági adatok regisztrációjának előzetes verzióját [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)címen.

> [!WARNING]
> Engedélyeznie kell a [konvergens regisztrációt az önkiszolgáló jelszó-visszaállításhoz és az Azure multi-Factor Authenticationhoz (nyilvános előzetes verzió)](concept-registration-mfa-sspr-converged.md) , mielőtt a felhasználók el tudják érni az új felhasználói élményt a [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!IMPORTANT]
> Egy 1 kapus házirend konfigurálásakor a hitelesítő alkalmazás nem választható ki egyetlen hitelesítési módszerként. Hasonlóképpen, a hitelesítő alkalmazás és csak egy további metódus nem választható ki 2 kapus házirend konfigurálásakor.
> Ezután a hitelesítő alkalmazást metódusként tartalmazó SSPR szabályzatok konfigurálásakor legalább egy további módszert ki kell választani egy 1 kapus házirend konfigurálásakor, és legalább két további módszert kell kijelölni egy 2 kapus házirend konfigurálásakor.
> Ennek a követelménynek az oka az, hogy az aktuális SSPR-regisztrációs felület nem tartalmazza a hitelesítő alkalmazás regisztrálásának lehetőségét. A hitelesítő alkalmazás regisztrálásának lehetősége az új, [konvergens regisztráció az önkiszolgáló jelszó-visszaállításhoz és az Azure multi-Factor Authentication (nyilvános előzetes verzió)](concept-registration-mfa-sspr-converged.md)része.
> Ha olyan házirendeket tesz lehetővé, amelyek csak a hitelesítő alkalmazást (1 kapus házirendek esetében) vagy a hitelesítő alkalmazást használják, és csak egy további módszer (a 2 kapus házirendek esetében), a felhasználók nem regisztrálhatnak a SSPR, amíg nem konfigurálták az új szolgáltatás használatára. regisztrációs élmény.

### <a name="change-authentication-methods"></a>Hitelesítési módszerek módosítása

Ha olyan házirenddel indul, amely csak egy szükséges hitelesítési módszert tartalmaz a regisztrációhoz vagy a zárolás feloldásához, és ezt két módszerre módosítja, mi történik?

| Regisztrált metódusok száma | Szükséges metódusok száma | Eredmény |
| :---: | :---: | :---: |
| 1 vagy több | 1 | Alaphelyzetbe állítás vagy zárolás feloldása |
| 1 | 2 | **Nem lehet** alaphelyzetbe állítani vagy feloldani |
| 2 vagy több | 2 | Alaphelyzetbe állítás vagy zárolás feloldása |

Ha megváltoztatja a felhasználó által használható hitelesítési módszerek típusait, a felhasználók véletlenül sem használhatják a SSPR, ha nem rendelkeznek a rendelkezésre álló minimális mennyiségű adattal.

Példa:
1. Az eredeti házirend két hitelesítési módszerrel van konfigurálva. Csak az Office-telefonszámot és a biztonsági kérdéseket használja. 
2. A rendszergazda úgy módosítja a szabályzatot, hogy a továbbiakban ne használja a biztonsági kérdéseket, de lehetővé teszi a mobiltelefon és egy másodlagos e-mail használatát.
3. A mobiltelefon vagy a másodlagos e-mail-mezők nélkül feltöltött felhasználók nem állíthatják alaphelyzetbe a jelszavukat.

## <a name="registration"></a>Regisztráció

### <a name="require-users-to-register-when-they-sign-in"></a>A felhasználók regisztrálásának megkövetelése a bejelentkezéskor

Ha engedélyezi ezt a beállítást, a felhasználónak be kell fejeznie a jelszó-visszaállítási regisztrációt, ha az Azure AD-t használó alkalmazásokba jelentkeznek be. A munkafolyamat a következő alkalmazásokat tartalmazza:

* Office 365
* Azure Portal
* Hozzáférési panel
* Összevont alkalmazások
* Az Azure AD-t használó egyéni alkalmazások

Ha a regisztráció megkövetelése le van tiltva, a felhasználók manuálisan is regisztrálhatnak. Vagy ellátogathatnak [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) vagy kiválaszthatják a **jelszó-visszaállítási hivatkozás regisztrálása** hivatkozásra a hozzáférés panel **profil** lapján.

> [!NOTE]
> A felhasználó a **Mégse gombra** kattintva vagy az ablak bezárásával elvetheti a jelszó-átállítási regisztrációs portált. A rendszer azonban minden egyes bejelentkezés alkalmával regisztrálja őket, amíg a regisztrációt el nem végzik.
>
> Ez a megszakítás nem szakítja meg a felhasználó kapcsolódását, ha már be vannak jelentkezve.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Azon napok száma, amely után a felhasználóknak meg kell adniuk a hitelesítési adataik ismételt megerősítését

Ez a beállítás határozza meg a hitelesítési adatok beállítása és megerősítése között eltelt időt, és csak akkor érhető el, ha engedélyezi a **felhasználók regisztrációját a bejelentkezéskor** lehetőségre.

Az érvényes értékek 0 – 730 nap, a "0" kifejezéssel pedig azt jelenti, hogy a felhasználók soha nem kérik újra a hitelesítési adataik megerősítését.

## <a name="notifications"></a>Értesítések

### <a name="notify-users-on-password-resets"></a>Értesítse a felhasználókat új jelszó kérésekor?

Ha ez a beállítás **Igen**értékre van állítva, akkor a felhasználók a jelszavuk módosítására vonatkozó e-mailt kapnak. Az e-mailt a SSPR-portálon keresztül küldi el a rendszer az Azure AD-ban található fájl elsődleges és másodlagos e-mail-címeire. Az alaphelyzetbe állítási eseményről senki más nem kap értesítést.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Minden rendszergazda értesítése, ha más rendszergazdák visszaállítják a jelszavukat

Ha ez a beállítás az **Igen**értékre van állítva, akkor az *összes rendszergazda* e-mailt kap az elsődleges e-mail-CÍMÉre az Azure ad-ban található fájlban. Az e-mail értesíti őket arról, hogy egy másik rendszergazda módosította a jelszavát a SSPR használatával.

Példa: egy környezetben négy rendszergazda található. A rendszergazda A SSPR használatával visszaállítja a jelszavukat. A B, C és D rendszergazdák e-mailben értesítést kapnak a jelszó-visszaállításról.

## <a name="on-premises-integration"></a>Helyszíni integráció

Ha a Azure AD Connect telepítését, konfigurálását és engedélyezését választja, akkor a következő további lehetőségek állnak rendelkezésre a helyszíni integrációhoz. Ha ezek a beállítások szürkén jelennek meg, akkor a visszaírási nincs megfelelően konfigurálva. További információ: a [jelszó visszaírási beállítása](howto-sspr-writeback.md).

![A jelszó visszaírási ellenőrzése engedélyezve van és működik][Writeback]

Ez az oldal a helyszíni visszaírási-ügyfél gyors állapotát mutatja be, az alábbi üzenetek egyike jelenik meg a jelenlegi konfiguráció alapján:

* A helyszíni visszaírási-ügyfél működik.
* Az Azure AD online állapotban van, és kapcsolódik a helyszíni visszaírási-ügyfélhez. Azonban a Azure AD Connect telepített verziójához hasonlóan elavult. Vegye fontolóra [Azure ad Connect frissítését](../hybrid/how-to-upgrade-previous-version.md) , és győződjön meg arról, hogy rendelkezik a legújabb csatlakozási funkciókkal és fontos hibajavításokkal.
* Sajnos a helyszíni visszaírási-ügyfél állapota nem ellenőrizhető, mert a Azure AD Connect telepített verziója elavult. [Frissítse Azure ad Connect](../hybrid/how-to-upgrade-previous-version.md) a kapcsolódási állapotának ellenőrzését.
* Sajnos úgy tűnik, hogy jelenleg nem tudunk csatlakozni a helyszíni visszaírási-ügyfélhez. A kapcsolatok visszaállításával [kapcsolatos Azure ad Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) .
* Sajnos nem lehet csatlakozni a helyszíni visszaírási-ügyfélhez, mert a jelszó visszaírási nincs megfelelően konfigurálva. [Konfigurálja a jelszó visszaírási](howto-sspr-writeback.md) a kapcsolódás visszaállításához.
* Sajnos úgy tűnik, hogy jelenleg nem tudunk csatlakozni a helyszíni visszaírási-ügyfélhez. Ennek oka az lehet, hogy a végén átmenetileg problémák léptek fel. Ha a probléma továbbra is fennáll, hárítsa el a [Azure ad Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) a kapcsolódás visszaállításához.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Jelszavak visszaírása a helyszíni címtárba

Ez a vezérlő meghatározza, hogy engedélyezve van-e a jelszó visszaírási ehhez a címtárhoz. Ha a visszaírási be van kapcsolva, a helyszíni visszaírási szolgáltatás állapotát jelzi. Ez a vezérlő akkor hasznos, ha átmenetileg le szeretné tiltani a jelszó-visszaírási anélkül, hogy újra kellene konfigurálnia Azure AD Connect.

* Ha a kapcsoló értéke **Igen**, akkor a visszaírási engedélyezve van, és az összevont, a továbbított hitelesítés vagy a jelszó-kivonatoló szinkronizált felhasználók képesek visszaállítani a jelszavukat.
* Ha a kapcsoló **nem**értékre van állítva, akkor a visszaírási le van tiltva, és az összevont, az átmenő hitelesítés vagy a jelszó-kivonat szinkronizált felhasználói nem tudják visszaállítani a jelszavukat.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Fiókok feloldásának engedélyezése a felhasználók számára a jelszó alaphelyzetbe állítása nélkül

Ez a vezérlő azt jelöli, hogy a jelszó-visszaállítási portálra felkeresett felhasználók számára elérhető-e a helyszíni Active Directory-fiókok zárolásának feloldása anélkül, hogy új jelszót kellene megadniuk. Alapértelmezés szerint az Azure AD feloldja a fiókokat, amikor a jelszó-visszaállítást végzi. Ezzel a beállítással elkülönítheti a két műveletet.

* Ha az **Igen**értékre van állítva, a felhasználók lehetőséget kapnak a jelszó alaphelyzetbe állítására és a fiók zárolásának feloldására, illetve a fiók zárolásának feloldására a jelszó alaphelyzetbe állítása nélkül.
* Ha a **nem**értékre van állítva, akkor a felhasználók csak a jelszó-visszaállítási és a fiók-feloldási műveletet tudják elvégezni.

### <a name="on-premises-active-directory-password-filters"></a>Helyszíni Active Directory jelszavak szűrői

Az Azure AD önkiszolgáló jelszó-visszaállítási szolgáltatása a rendszergazda által kezdeményezett jelszó-visszaállítás megfelelőjét hajtja végre Active Directoryban. Ha harmadik féltől származó jelszavas szűrőt használ az egyéni jelszavak érvényesítéséhez, és az Azure AD önkiszolgáló jelszó-visszaállítás során meg kell adni ezt a jelszavas szűrőt, győződjön meg arról, hogy a harmadik féltől származó jelszó-szűrési megoldás az alkalmazásra van konfigurálva rendszergazdai jelszó-visszaállítási forgatókönyv. Alapértelmezés szerint a [Windows Server Active Directory Azure ad jelszavas védelme](concept-password-ban-bad-on-premises.md) is támogatott.

## <a name="password-reset-for-b2b-users"></a>Jelszó alaphelyzetbe állítása B2B-felhasználók számára

A jelszó alaphelyzetbe állítása és módosítása teljes mértékben támogatott az összes vállalatközi (B2B) konfigurációban. A B2B felhasználói jelszó alaphelyzetbe állítása a következő három esetben támogatott:

* **Meglévő Azure ad-Bérlővel rendelkező partnerszervezet felhasználói**: Ha a szervezet, amelyhez a partnert használja, meglévő Azure ad-Bérlővel rendelkezik, a *jelszó-visszaállítási házirendeket a bérlőn is be kell tartani*. A jelszó-visszaállítás működéséhez a partner szervezetnek csak meg kell győződnie arról, hogy az Azure AD SSPR engedélyezve van. Az Office 365-ügyfelek díjmentesen használhatók, és az első [lépések a jelszó-kezelési](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) útmutató lépéseit követve engedélyezhetők.
* Felhasználók, akik önkiszolgáló regisztráción **keresztül regisztrálhatnak** : Ha a szervezet, amellyel az önkiszolgáló bejelentkezési funkciót felhasználva bekerül egy bérlőbe, visszaállíthatja a jelszót a regisztrált e-mail [-](../users-groups-roles/directory-self-service-signup.md) címre.
* **B2B-felhasználók**: az új [Azure ad B2B-funkciók](../active-directory-b2b-what-is-azure-ad-b2b.md) használatával létrehozott összes új B2B-felhasználó is visszaállíthatja a jelszavukat a Meghívási folyamat során regisztrált e-mail-címre.

A forgatókönyv teszteléséhez lépjen a https://passwordreset.microsoftonline.com az egyik partner felhasználóval. Ha egy másodlagos e-mail-vagy hitelesítési e-mail-cím van megadva, a jelszó-visszaállítás a várt módon működik.

> [!NOTE]
> Az Azure ad-bérlőhöz (például Hotmail.com, Outlook.com vagy más személyes e-mail-címekhez) hozzáféréssel rendelkező Microsoft-fiókok nem tudják használni az Azure AD-SSPR. A jelszó alaphelyzetbe állításához a következő információkat kell használnia, [Ha nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikkbe.

## <a name="next-steps"></a>Következő lépések

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
* [Úgy gondolom, hogy valami megszakadt. Hogyan a SSPR hibáinak megoldása?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Az elérhető Azure AD-hitelesítési módszerek és a szükséges mennyiség"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "Helyszíni integrációs jelszó visszaírási konfigurációs és hibaelhárítási információk"
