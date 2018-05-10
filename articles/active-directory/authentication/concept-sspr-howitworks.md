---
title: Az önkiszolgáló jelszó-átállítási működésének - Azure Active Directory
description: Az Azure AD az önkiszolgáló jelszó-átállítási részletes bemutatója
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 6f755754097336fc97678940ea1fa0aa28315fab
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Az önkiszolgáló jelszó-változtatási az Azure AD részletes bemutatója

Hogyan nem önkiszolgáló jelszó-változtatási (SSPR) munka? Mi ez a lehetőség jelent a felületen? További tudnivalók az Azure Active Directory (Azure AD) SSPR olvasási továbbra is.

## <a name="how-does-the-password-reset-portal-work"></a>Hogyan alaphelyzetbe állítja a jelszót a munkahelyi portál?

Amikor egy felhasználó megnyitja a a jelszó-visszaállítási portál, a munkafolyamat van kezdődött el meghatározásához:

   * Hogyan lehet honosított a lapot?
   * Érvénytelen a felhasználói fiókot?
   * Milyen a szervezet a felhasználó tartozik?
   * Hol áll a felhasználó jelszavát?
   * A felhasználó rendelkezik licenccel a szolgáltatás használatához?

További információt a jelszó mögötti logika olvassa el a következő lépések lap visszaállítása:

1. A felhasználó kijelöli a **nem fér hozzá a fiókjához** hivatkozásra, vagy közvetlenül kerül [ https://aka.ms/sspr ](https://passwordreset.microsoftonline.com).
   * A böngésző nyelve alapján, a felhasználói élmény megjelenítése a megfelelő nyelvet. A jelszó alaphelyzetbe állítása élmény van, amely támogatja az Office 365 nyelvekre honosított.
2. A felhasználó megadja a felhasználói Azonosítót, és továbbítja a captcha.
3. Az Azure AD ellenőrzi, hogy a felhasználó képes a szolgáltatás használatához a következő ellenőrzések végrehajtásával:
   * Ellenőrzi, hogy a felhasználó rendelkezik-e ez a funkció engedélyezve van, és rendelkezik az Azure AD-licenccel.
     * Ha a felhasználó rendelkezik ezzel a funkcióval vagy nem egy licenccel, a felhasználónak kapcsolatba kell lépnie a jelszó alaphelyzetbe állításához a rendszergazda.
   * Ellenőrzi, hogy a felhasználó rendelkezik-e a jobb oldali kihívást jelentenek a rendszergazda nyilatkozatnak a fiókot a megadott adatokat.
     * Ha a házirend csak egy ellenőrző igényel, majd biztosítja, hogy a felhasználó rendelkezik-e a megfelelő adatokat, legalább az egyik kihívás a rendszergazda házirend engedélyezve van definiálva.
       * Ha a felhasználó kihívás nincs konfigurálva, majd a felhasználó ajánlatos a jelszavuk a rendszergazdától.
     * Ha a házirend két kihívást igényel, majd biztosítja, hogy a felhasználó rendelkezik-e a megfelelő adatokat, a rendszergazda házirend által engedélyezett kihívásai legalább két definiált.
       * Ha a felhasználó kihívás nincs konfigurálva, majd a felhasználó ajánlatos a jelszavuk a rendszergazdától.
   * Ellenőrzi, hogy ha a felhasználó jelszava felügyelt helyszíni (összevont, átmenő hitelesítést, vagy a Jelszókivonat szinkronizálása).
     * Ha visszaírási van telepítve, és a jelszó helyszínen felügyelt, akkor a felhasználó hitelesítésére és jelszavuk folytatja-e.
     * Ha visszaírási nincs telepítve, és a jelszó helyszínen felügyelt, majd a felhasználónak kapcsolatba kell lépnie a jelszó alaphelyzetbe állításához a rendszergazda.
4. Ha megállapította, hogy a felhasználó nem tudja alaphelyzetbe állítása sikeresen megtörtént a jelszavát, majd a rendszer végigvezeti a felhasználót a visszaállítási folyamat során.

## <a name="authentication-methods"></a>Hitelesítési módszerek

Ha az önkiszolgáló jelszó-Változtatási engedélyezve van, ki kell választania a hitelesítési módszerek az alábbiak közül legalább egyet. Egyes esetekben hallott ezek a beállítások néven "kapuk." Erősen ajánlott legalább két hitelesítési módszerek úgy dönt, hogy a felhasználók több beleszólása van.

* E-mail
* Mobiltelefon
* Irodai telefon
* Biztonsági kérdések

![Hitelesítés][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>A címtár melyik mezők használ a megadott hitelesítési adat?

* **Irodai telefon**: az irodai telefon felel meg.
    * Nem állítható be ez a mező maguk a felhasználók. Egy rendszergazdának meg kell határozni.
* **Mobiltelefon**: megfelel-e a hitelesítéshez megadott telefonját (nem nyilvánosan láthatóvá) vagy a mobiltelefon (nyilvánosan láthatóvá).
    * A szolgáltatás először megkeresi a hitelesítéshez megadott telefonját, és ezután visszatér a mobiltelefon, ha a hitelesítéshez megadott telefonját jelent-e.
* **Másodlagos e-mail cím**: megfelel a hitelesítési e-mail (nem nyilvánosan láthatóvá) vagy a másodlagos e-mail cím.
    * A szolgáltatás a hitelesítési e-mail először keres, és ezután nem másodlagos e-mail cím.

Alapértelmezés szerint csak a felhő attribútumok irodai telefon és mobiltelefonra szinkronizálva van a felhő címtárhoz a hitelesítési adatok a helyi címtárban lévő.

Felhasználók csak jelszó is visszaállítása, ha a hitelesítési módszereket, amelyek a rendszergazda engedélyezte, és megköveteli az adatok.

Ha a felhasználók nem szeretné, hogy a címtárban a mobiltelefonszám, de továbbra is szeretnék használni szeretné a jelszó alaphelyzetbe állítása, a rendszergazdák nem kitölti azt a könyvtárban. Felhasználók majd fel kell töltenie a **hitelesítéshez megadott telefonját** keresztül attribútumot a [jelszó-változtatási regisztrációs portálra](https://aka.ms/ssprsetup). A rendszergazdák láthatják ezeket az információkat a profil, de nem máshol lesz közzétéve.

### <a name="the-number-of-authentication-methods-required"></a>Hitelesítési módszer szükséges száma

Ez a beállítás a rendelkezésre álló hitelesítési módszerek vagy a felhasználó alaphelyzetbe állítása vagy a jelszavát zárolásának feloldásához keresztül kell haladnia kapuk minimális számát határozza meg. Egy vagy két beállítható.

Felhasználók eldönthetik, adja meg a további hitelesítési módszerek, ha a rendszergazda engedélyezi a hitelesítési módszert.

Ha a felhasználó nem rendelkezik a minimálisan szükséges módszerek regisztrált, láthatják, amely arra utasítja, kérje a rendszergazda jelszó visszaállítása hibalap.

#### <a name="change-authentication-methods"></a>Módosítsa a hitelesítési módszerek

Ha egy házirendet, amelynek csak egy szükséges hitelesítési módszer az alaphelyzetbe állítása vagy zárolásának feloldásához először regisztrálva, és módosíthatja, hogy a két módszer, mi történik?

| Több regisztrált módszer | Szükséges módszerek száma | Eredmény |
| :---: | :---: | :---: |
| legalább 1 | 1 | **Képes** alaphelyzetbe állítása vagy feloldása |
| 1 | 2 | **Nem lehet** alaphelyzetbe állítása vagy feloldása |
| 2 vagy több | 2 | **Képes** alaphelyzetbe állítása vagy feloldása |

Ha módosítja a hitelesítési módszereket, amelyek a felhasználó típusú, akaratlanul leállhat a felhasználók nem tudnak önkiszolgáló jelszó-Változtatási használja, ha nincs telepítve a minimális mennyiségű adat.

Példa: 
1. Az eredeti házirenddel szükséges két hitelesítési módszerekkel együtt van konfigurálva. Csak a munkahelyi számának és a biztonsági kérdések használ. 
2. A rendszergazdának a házirend már nem a biztonsági kérdések megváltozik, de lehetővé teszi, hogy a mobiltelefon és egy másodlagos e-mail.
3. A mobileszköz és a másodlagos e-mail mezői nem rendelkező felhasználók a jelszavuk nem állítható alaphelyzetbe.

### <a name="how-secure-are-my-security-questions"></a>Hogy mennyire vannak biztonságban vannak a biztonsági kérdések?

Ha a biztonsági kérdések használata együtt őket egy másik módszerrel használatát javasoljuk. Biztonsági kérdések lehet más módszernél kevésbé biztonságos, mert néhány felhasználó ismerheti egy másik felhasználó kérdésekre adott válaszokat.

> [!NOTE] 
> Biztonsági kérdések tárolja a könyvtárban olyan felhasználói objektum közvetlenül és biztonságosan, és képes csak válaszolni felhasználók regisztráció során. Nincs semmilyen módszer, olvassa el, vagy módosíthatja a felhasználói kérdések és válaszok a rendszergazda.
>

### <a name="security-question-localization"></a>Biztonsági kérdés honosítása

Hajtsa végre az előre definiált kérdések az Office 365 nyelveken a teljes készletének honosítva vannak, és a felhasználó a böngésző nyelve alapulnak:

* Melyik városban ismerkedett meg az első házastársával/párjával?
* Melyik városban találkozak először a szülei?
* Melyik városban lakik a legközelebbi rokona?
* Az édesapja melyik városban született?
* Melyik városban volt az első munkahelye?
* Melyik városban született az édesanyja?
* Hol töltötte 1999 szilveszterét?
* Mi volt a kedvenc középiskolai tanárának vezetékneve?
* Milyen felsőoktatási intézménybe jelentkezett, de nem vették fel?
* Hogy hívták a helyet, ahol az első lakodalmát tartották?
* Édesapja második keresztneve?
* Mi a kedvenc étele?
* Anyai nagyapja vezeték- és keresztneve?
* Mi az édesanyja második keresztneve?
* Mi az a legidősebb testvérének születési éve és hónapja? (pl. 1985. November)
* Mi a legidősebb testvérének második keresztneve?
* Apai nagyapja vezeték- és keresztneve?
* Mi a legfiatalabb tesvérének második keresztneve?
* Melyik iskolában végezte el a hatodik osztályt?
* Gyerekkori legjobb barátjának vezeték- és keresztneve?
* Első társának vezeték- és keresztneve?
* Mi volt a kedvenc általános iskolai tanárának vezetékneve?
* Mi volt az első autója vagy motorkerékpárja márkája és típusa?
* Mi volt az első iskolájának neve?
* Mi a neve annak a kórháznak, ahol született?
* Első gyerekkori otthonának utcaneve?
* Ki volt a kedvenc gyerekkori hőse?
* Kedvenc plüssállatának neve?
* Mi volt az első háziállatának neve?
* Mi volt a gyerekkori beceneve?
* Mi volt a kedvenc sportja a középiskolában?
* Mi volt az első munkahelye?
* Mi volt a gyerekkori telefonszámának utolsó négy számjegye?
* Gyerekkorában mi szeretett volna lenni, ha majd felnő?
* Ki volt a leghíresebb ember, akivel valaha is találkozott?

### <a name="custom-security-questions"></a>Egyéni biztonsági kérdések

Egyéni biztonsági kérdések nem a különböző területi beállításokhoz honosított. Minden egyéni kérdések jelennek meg nyelvével azonos nyelven, szerepel a felügyeleti felhasználói felület, akkor is, ha a felhasználó a böngésző nyelve nem egyezik. Ha honosított kérdése van szüksége, az előre definiált kérdéseket kell használnia.

Egy egyéni biztonsági kérdés hossza legfeljebb 200 karakter lehet.

### <a name="security-question-requirements"></a>Biztonsági kérdés követelmények

* A minimális válasz karakteres korlátot három karakterből áll.
* A válasz maximális karakteres korlátot 40 karakter.
* Felhasználók ugyanezt a kérdést egynél többször nem válaszol.
* Felhasználók nem adhatók meg az azonos egynél több kérdésre adott válasz.
* Bármely karakterkészlet segítségével határozza meg a kérdések és a válaszok, beleértve a Unicode-karaktereket.
* A megadott kérdéseket száma nagyobb vagy egyenlő, melyeket regisztrálásához szükséges kérdések számát kell lennie.

## <a name="registration"></a>Regisztráció

### <a name="require-users-to-register-when-they-sign-in"></a>Felhasználói bejelentkezéskor regisztráció megkövetelése

Ahhoz, hogy ezt a beállítást, a felhasználó, aki engedélyezve van a jelszó alaphelyzetbe állítása a jelszóátállítás regisztrációját befejeződik, ha az Azure AD használatával alkalmazások bejelentkeznek az rendelkezik. Ez az alábbiakat tartalmazza:

* Office 365
* Azure Portal
* Hozzáférési panel
* Összevont alkalmazásokhoz
* Azure AD használatával egyéni alkalmazások

Ha regisztrációs igénylő le van tiltva, a felhasználók továbbra is futtathatja manuálisan regisztrálhatják az elérhetőségét. Vagy keresse fel a következőkre [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup) vagy válassza ki a **regisztrálása jelszóváltoztatásra** hivatkozásra a **profil** a hozzáférési Panel lapján.

> [!NOTE]
> Felhasználók kiválasztásával esetben elvetheti a jelszó-visszaállítási portál **Mégse** vagy zárja be az ablakot. Azonban meg kell regisztrálni a minden egyes bejelentkezéskor mindaddig, amíg a regisztrálást.
>
> Ez nem a felhasználó kapcsolat megszüntetése, ha már bejelentkezett a.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Állítsa be a hány nap elteltével a felhasználó felkérést kap a hitelesítési adataikat megerősítése

Ez a beállítás azt határozza meg, mennyi ideig beállítása és reconfirming hitelesítési adatok között, és csak akkor, ha engedélyezi a **felhasználói bejelentkezéskor regisztráció megkövetelése** lehetőséget.

Érvényes értékek: 0 és 730 nap, "0", azaz a felhasználók soha nem a rendszer kéri a hitelesítési adatokat megerősítése.

## <a name="notifications"></a>Értesítések

### <a name="notify-users-on-password-resets"></a>Értesítse a felhasználókat új jelszó kérésekor?

Ha ez a beállítás értéke **Igen**, majd a jelszó alaphelyzetbe állításával a felhasználó kap egy e-mailt, amely értesíti őket, hogy a jelszó megváltozott. Az e-mailt küld az önkiszolgáló jelszó-Változtatási portálján keresztül az elsődleges és másodlagos e-mail címét, amelyek a fájl az Azure ad-ben. A visszaállítási értesítést senki más nem esemény.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Az összes rendszergazda értesítése, ha más rendszergazdák a jelszavak alaphelyzetbe állítása

Ha ez a beállítás értéke **Igen**, majd *minden rendszergazda* az elsődleges e-mail címéhez a fájlt e-mailt kap az Azure ad-ben. Az e-mailben értesíti őket, hogy egy másik rendszergazda SSPR használatával megváltozott a jelszavát.

Példa: Nincsenek négy rendszergazdák környezetben. A rendszergazda SSPR segítségével alaphelyzetbe állítja a jelszavát. A rendszergazdák B, C és D, amely azokat a jelszó-változtatási riasztást küld e-mailt kapni.

## <a name="on-premises-integration"></a>Helyszíni integráció

Ha telepítését, konfigurálását és az Azure AD Connect engedélyezése, lehetősége van a következő további helyszíni Integrációk. Ha ezek a beállítások szürkén jelennek meg, majd visszaírási nem megfelelően van konfigurálva. További információkért lásd: [jelszóvisszaírás konfigurálása](howto-sspr-writeback.md#configure-password-writeback).

![Visszaírásához.][Writeback]

Ezen a lapon a helyszíni visszaírási ügyfél az alábbi üzenetek egyike jelenik meg a jelenlegi konfiguráció alapján gyors állapotának tartalmazza:

* A helyszíni visszaírási ügyfél megfelelően működik, és.
* Az Azure AD online állapotban, és a helyszíni visszaírási ügyfél csatlakozik. Azonban úgy tűnik, az Azure AD Connect telepített verziója elavult. Érdemes lehet [frissítés Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) annak érdekében, hogy rendelkezik a legújabb kapcsolati funkciók és a fontos hibajavításokat tartalmaz.
* A helyszíni visszaírási ügyfélállapot sajnos jelenleg nem lehet ellenőrizni, mert az Azure AD Connect telepített verziója elavult. [Az Azure AD Connect frissítése](./../connect/active-directory-aadconnect-upgrade-previous-version.md) tudjanak a kapcsolat állapotának ellenőrzése.
* Sajnos úgy tűnik, nem lehet kapcsolódni a helyszíni visszaírási ügyfél most. [Hibaelhárítás az Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) a kapcsolat helyreállítására.
* Sajnos nem lehet kapcsolódni a helyszíni visszaírási ügyfél mert jelszóvisszaírás nem megfelelően van konfigurálva. [Konfigurálja a jelszóvisszaírás](howto-sspr-writeback.md#configure-password-writeback) a kapcsolat helyreállítására.
* Sajnos úgy tűnik, nem lehet kapcsolódni a helyszíni visszaírási ügyfél most. Ezt az End ideiglenes problémák okozhatják. Ha a probléma továbbra is fennáll, [hibaelhárítása az Azure AD Connectet](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) a kapcsolat helyreállítására.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Jelszavakat írhasson a helyszíni címtár

A vezérlő határozza meg, hogy engedélyezve van-e a jelszóvisszaírás ennél a címtárnál. Ha visszaírási, azt jelzi a helyszíni visszaírási szolgáltatás állapotát. Ez akkor hasznos, ha szeretné ideiglenesen letilthatja a jelszóvisszaírást anélkül, hogy az Azure AD Connect újrakonfigurálása.

* Ha a kapcsoló értéke **Igen**, majd a visszaírás engedélyezve van, és összevont, átmenő hitelesítést, vagy a jelszó szinkronizálva kivonatoló felhasználók visszaállíthassák a jelszavukat.
* Ha a kapcsoló értéke **nem**, majd a visszaírás le van tiltva, és összevont, átmenő hitelesítést, vagy a jelszó szinkronizálva kivonatoló felhasználók nem képesek visszaállíthassák a jelszavukat.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Engedélyezése a felhasználók számára a fiókok zárolásának feloldása nélkül a jelszó alaphelyzetbe állításával

Ez a vezérlő jelzi, hogy a felhasználók, akik látogasson el a jelszó-változtatási portál meg kell adni a lehetőséget, a helyszíni Active Directory-fiókok zárolásának anélkül, hogy a jelszó alaphelyzetbe állítása. Alapértelmezés szerint az Azure AD fiókok feloldja, amikor a jelszó alaphelyzetbe állítása hajt végre. E két művelet külön ezt a beállítást használja. 

* Ha beállítása **Igen**, akkor a felhasználók kapnak, hogy a jelszó alaphelyzetbe állítása és a fiók zárolását kívánja feloldani, vagy a fiók zárolásának anélkül, hogy a jelszó alaphelyzetbe állítása.
* Ha beállítása **nem**, majd felhasználók vannak csak tudják végrehajtani a kombinált jelszó alaphelyzetbe állítása és fiókok zárolásának feloldása műveletet.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Hogyan alaphelyzetbe állítja a jelszót B2B felhasználók számára?
Jelszó alaphelyzetbe állítása és módosítása teljes mértékben támogatottak összes üzleti vállalatközi (B2B) konfigurációja. A következő három esetben támogatott B2B felhasználói jelszó-visszaállítás:

   * **A fiókpartner-szervezet az Azure AD-bérlő felhasználóit**: Ha a szervezetben, hogy együttműködve egy meglévő Azure AD-bérlő azt *tiszteletben tartják a bérlőre engedélyezve vannak függetlenül jelszó alaphelyzetbe állítása házirendjei*. Jelszó-változtatási működjön a fiókpartner-szervezet csak győződjön meg arról, hogy engedélyezve van-e az Azure AD SSPR van szüksége. Nem kell külön fizetni az Office 365-ügyfelek van, és a lépések elvégzésével engedélyezheti a [Ismerkedés a jelszókezeléssel](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) útmutató.
   * **Felhasználók, akik keresztül regisztráljon** önkiszolgáló regisztrációs: Ha a szervezet, hogy együttműködve használt a [önkiszolgáló regisztrációs](../active-directory-self-service-signup.md) a bérlő feltölti a beállítást, azt hogy azok az e-mailt, azok regisztrálva a jelszó alaphelyzetbe állítása.
   * **B2B felhasználók**: az új létrehozott új B2B felhasználók [Azure AD B2B képességek](../active-directory-b2b-what-is-azure-ad-b2b.md) is tudnak visszaállíthassák a jelszavukat, az e-mailt a meghívott felhasználó során regisztrálják azokat.

Ez a forgatókönyv teszteléséhez Ugrás http://passwordreset.microsoftonline.com ezen partner felhasználók egyike. Ha egy másodlagos e-mail vagy a megadott hitelesítési e-mail, jelszó-változtatási működik megfelelően.

> [!NOTE]
> Hozzáférést kapott Vendég az Azure AD-bérlő, például Hotmail.com, Outlook.com-os vagy más személyes e-mail-címeket, amelyek Microsoft-fiókok nem képesek használni az Azure AD SSPR. Jelszó visszaállítása található információk segítségével van szükségük a [mikor nem tud bejelentkezni Microsoft-fiókja](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.

## <a name="next-steps"></a>További lépések

Az alábbi cikkekben az Azure AD jelszóátállításáról olvashat további információkat:

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó visszaállítása vagy módosítása](../active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../active-directory-passwords-reset-register.md)
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
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "Helyszíni integráció jelszó visszaírási konfigurálása és a hibaelhárítási információkat"
