---
title: Az önkiszolgáló jelszó-visszaállítási működését – Azure Active Directory
description: Az Azure AD önkiszolgáló jelszó-visszaállítási részletes bemutatása
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: f2d0b009c4451a4108222ac7aa7954ba6dd86699
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869007"
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Az önkiszolgáló jelszó-visszaállítás, az Azure AD részletes bemutatása

Nem az önkiszolgáló jelszó-visszaállítás (SSPR) munkahelyi? Mi ezt a lehetőséget jelent a felületen? Tudjon meg többet az Azure Active Directory (Azure AD) az SSPR kihívásokra.

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
2. A felhasználó beírja egy felhasználói Azonosítót, és átadja a captcha.
3. Az Azure AD ellenőrzi, hogy a felhasználó nem használhatja ezt a szolgáltatást felderítéséhez a következő tevékenységek végrehajtásával:
   * Ellenőrzi, hogy a felhasználó rendelkezik-e a szolgáltatás nincs engedélyezve, és rendelkezik az Azure AD-licenccel.
     * Ha a felhasználó nem rendelkezik a szolgáltatás nincs engedélyezve, vagy egy licenccel, a felhasználónak meg kell adnia a saját jelszavuk-rendszergazdától.
   * Ellenőrzi, hogy a felhasználó jogosult-e adatokat a fiók rendszergazdája szabályzatának megfelelően definiált kihívást jelentenek.
     * Ha a szabályzat csak az egyik kihívás van szüksége, majd biztosítja azt, hogy a felhasználó rendelkezik-e a megfelelő adatokat, legalább az egyik kihívást a rendszergazdai házirend engedélyezve van definiálva.
       * Ha a felhasználói feladvány nincs konfigurálva, majd a felhasználó javasolt a rendszergazdától új jelszót kérnek.
     * Ha a szabályzat két lépés szükséges, majd biztosítja azt, hogy a felhasználó rendelkezik-e a megfelelő adatokat a felügyeleti házirend által engedélyezett kihívást legalább két definiált.
       * Ha a felhasználói feladvány nincs konfigurálva, majd a felhasználó javasolt a rendszergazdától új jelszót kérnek.
   * Ellenőrzi, hogy a felhasználó jelszava felügyelt helyszíni (összevont, átmenő hitelesítést, vagy a jelszókivonatok szinkronizálása).
     * Ha a jelszóvisszaíró telepíti, és a felhasználó jelszava a helyszínen kezel, akkor a felhasználó számára engedélyezett a jelszavuk és hitelesítéséhez a folytatáshoz.
     * Ha a jelszóvisszaíró nincs telepítve, és a felhasználó jelszava a helyszínen kezel, akkor a felhasználónak meg kell adnia a rendszergazdától új jelszót kérnek.
4. Ha megállapította, hogy a felhasználó nem tudja sikeresen visszaállítva jelszavukat, majd a felhasználó rendszer végigvezeti a visszaállítás folyamatához.

## <a name="authentication-methods"></a>Hitelesítési módszerek

Ha az SSPR engedélyezve van, választania kell a hitelesítési módszerek az alábbiak közül legalább egyet. Néha hallgassa meg ezek a beállítások "kapuk." néven Erősen ajánlott legalább két hitelesítési módszerek úgy dönt, hogy a felhasználók több beleszólása van.

* E-mail
* Mobiltelefon
* Irodai telefon
* Biztonsági kérdések

![Hitelesítés][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>Milyen mezőket a hitelesítési adatok használhatók a címtárban?

* **Irodai telefon**: az irodai telefon felel meg.
    * Felhasználók nem tudnak adja meg a mezőben saját magukat. Egy rendszergazdának kell definiálni.
* **Mobiltelefon**: felel meg a hitelesítéshez használt telefon (nem nyilvánosan láthatók) vagy a mobiltelefon (nyilvánosan láthatók).
    * A szolgáltatás először megkeresi a hitelesítéshez használt telefon, és ezután visszatér a létrejött a mobiltelefon, ha a hitelesítéshez használt telefon jelent-e.
* **Másodlagos e-mail cím**: felel meg a hitelesítő e-mail-cím (nem nyilvánosan láthatók) vagy a másodlagos e-mail cím.
    * A szolgáltatás először megkeresi a hitelesítési e-mail címére, és vissza a másodlagos e-mail cím, akkor sikertelen lesz.

Alapértelmezés szerint csak a felhőben attribútumok irodai telefon és mobiltelefon szinkronizálva van a felhőalapú címtárral hitelesítési adatokat a helyszíni címtárból.

Felhasználók csak alaphelyzetbe állíthatja a jelszavát Ha szerepel a hitelesítési módszereket, amelyek a rendszergazda engedélyezte, és a szükséges adatok rendelkeznek.

Ha a felhasználók nem szeretnének a mobiltelefonszám lesznek láthatók a címtárban, de azok továbbra is azt szeretné használni a jelszó-visszaállításhoz, a rendszergazdák nem kitölti ezt a címtárban. Felhasználók majd fel kell töltenie saját **hitelesítéshez használt telefon** keresztül attribútum a [jelszóátállítási regisztrációs portál](https://aka.ms/ssprsetup). A rendszergazdák ezt az információt a felhasználói profil látható, de nincs közzétéve máshol.

### <a name="the-number-of-authentication-methods-required"></a>A szükséges hitelesítési módszerek száma

Ez a beállítás meghatározza, hogy a rendelkezésre álló hitelesítési módszerek vagy visszaállítására vagy zárolásának feloldásához jelszó keresztül kell haladnia a felhasználó kapuk minimális számát. Egy vagy két állítható.

Adja meg a további hitelesítési módszereket, ha a rendszergazda a hitelesítési módszer lehetővé teszi a felhasználók választhat.

Ha egy felhasználó nincs regisztrálva a minimálisan szükséges módszerek, láthatják, amelyek szólítja fel őket, hogy a rendszergazdák új jelszót kérnek kérelem hibalap.

#### <a name="change-authentication-methods"></a>Módosítsa a hitelesítési módszerek

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
3. A mobileszköz és a másodlagos e-mail-cím mezői nem rendelkező felhasználók a jelszavuk nem állítható alaphelyzetbe.

### <a name="how-secure-are-my-security-questions"></a>Mennyire vannak biztonságban vannak a biztonsági kérdések megválaszolása?

Biztonsági kérdések használatakor javasoljuk egy másik módszerrel együtt használja őket. Biztonsági kérdések kevésbé biztonságos, mint más módszerekkel is lehet, mivel vannak, akik előfordulhat, hogy egy másik felhasználó kérdésekre adott válaszokat.

> [!NOTE] 
> Biztonsági kérdések a címtárban lévő felhasználói objektum a privát módon és biztonságosan tárolja, és csak választ a felhasználók regisztrációja során. Nincs lehetőség a rendszergazda számára olvasására vagy módosítására egy felhasználói kérdések és válaszok.
>

### <a name="security-question-localization"></a>Biztonsági kérdés honosítási

Az előre meghatározott kérdések az alábbi Office 365 nyelveken a teljes körű honosítva vannak, és a felhasználó a böngésző nyelve alapján:

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
* Mi a legidősebb testvérének születési éve és hónapja? (Példa: 1985. November)
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

Egyéni biztonsági kérdések nem különböző területi beállításokhoz honosított. Minden egyéni kérdéseket vannak feltüntetve ugyanazt a nyelvet, szerepel a rendszergazdai felhasználói felület, akkor is, ha a felhasználó a böngésző nyelve nem egyezik. Ha honosított kérdésekre, az előre definiált kérdésekre kell használnia.

Egyéni biztonsági kérdések maximális hossza 200 karakternél hosszabb.

Eltérő honosított jelszó-visszaállítási portál és kérdések megjelenítése nyelv hozzáfűzése "? mkt =<Locale>" végéig a jelszó alaphelyzetbe állítása URL-cím a spanyol, azaz a következő példában [ https://passwordreset.microsoftonline.com/?mkt=es-us ](https://passwordreset.microsoftonline.com/?mkt=es-us).

### <a name="security-question-requirements"></a>Biztonsági kérdés követelmények

* A minimális válasz karakterszámot három karakterből áll.
* A válasz maximális karakterszámot 40 karakterből áll.
* Felhasználók ugyanezt a kérdést egynél több alkalommal nem válaszol.
* Felhasználók nem adhatók meg a kérdéshez eltérő válaszokat.
* Bármely karakterkészlet segítségével határozza meg a kérdéseket és válaszokat, beleértve a Unicode-karaktereket.
* A megadott kérdések száma nagyobb vagy egyenlő, amelyek korábban regisztrálásához szükséges kérdések száma kell lennie.

## <a name="registration"></a>Regisztráció

### <a name="require-users-to-register-when-they-sign-in"></a>Szükséges a felhasználóknak regisztrálniuk a bejelentkezéskor

Ezt a beállítást engedélyezi, hogy a felhasználó, aki engedélyezve van a jelszó-visszaállítás elvégzéséhez a jelszóátállítás regisztrációját, ha bejelentkeznek az alkalmazások Azure AD-vel rendelkezik. Ez az alábbiakat tartalmazza:

* Office 365
* Azure Portal
* Hozzáférési panel
* Összevont alkalmazások
* Egyéni alkalmazások Azure AD-vel

Regisztráció megkövetelése le van tiltva, ha a felhasználók manuálisan még mindig regisztrálhatnak az elérhetőségét. Vagy keresse fel a következőkre [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup) vagy válassza ki a **regisztráció új jelszó kéréséhez** hivatkozásra a **profil** fülre a hozzáférési panelen.

> [!NOTE]
> Felhasználó elvetheti a jelszó-visszaállítási portál kiválasztásával **Mégse** vagy zárja be az ablakot. De figyelmeztetést kapnak minden egyes bejelentkezéskor mindaddig, amíg azok a regisztráció befejezéséhez regisztrálja.
>
> Ez nem a felhasználói kapcsolat megszüntetése, ha már bejelentkezett a.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Adja meg, hány nap elteltével a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését

Ez a beállítás határozza meg azt a beállítást, és a hitelesítési adatok reconfirming közötti időszakot, és csak akkor, ha engedélyezi a **szükséges a felhasználóknak regisztrálniuk a bejelentkezéskor** lehetőséget.

Érvényes értékek: 0 és 730 nap, a "0", azaz a felhasználók soha nem kéri a hitelesítési adataik ismételt megerősítését.

## <a name="notifications"></a>Értesítések

### <a name="notify-users-on-password-resets"></a>Értesítse a felhasználókat új jelszó kérésekor?

Ha ez a beállítás értéke **Igen**, majd az új jelszó felhasználó kap egy e-mailben értesíti őket, hogy módosította a jelszavát. Az e-mailt küld az SSPR-portálon keresztül elsődleges és másodlagos e-mail-címüket, amelyek a fájl az Azure ad-ben. A visszaállítási értesítést senki más nem esemény.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérjenek

Ha ez a beállítás értéke **Igen**, majd *minden rendszergazda* kap egy e-mailt a fájl elsődleges e-mail-címére az Azure ad-ben. Az e-mailben értesíti őket, hogy egy másik rendszergazda az SSPR megváltoztatta a jelszavát.

Példa: Nincsenek négy rendszergazdák környezet. A rendszergazda SSPR segítségével alaphelyzetbe állítja a jelszavát. A rendszergazdák B, C és D kapnak egy e-mailt, amely riasztást küld, azokat a jelszó-visszaállítás.

## <a name="on-premises-integration"></a>Helyszíni integráció

Ha telepítése, konfigurálása és az Azure AD Connect engedélyezéséhez, akkor a következő beállítások a helyszíni Integrációk. Ha ezek a beállítások szürkén jelennek meg, majd a jelszóvisszaíró nincs megfelelően konfigurálva. További információkért lásd: [konfigurálása a jelszóvisszaíró](howto-sspr-writeback.md#configure-password-writeback).

![A Jelszóvisszaíró][Writeback]

Ez az oldal a helyszíni visszaírási ügyfélhez az alábbi üzenetek egyike jelenik meg a jelenlegi konfiguráció alapján gyors állapotának tartalmazza:

* A helyszíni visszaírási ügyfél működik-e.
* Az Azure AD online állapotban, és a helyszíni visszaírási ügyfélhez csatlakozik. Azonban úgy tűnik, az Azure AD Connect telepített verziója elavult. Érdemes lehet [frissítése az Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) , gondoskodjon arról, hogy a legújabb kapcsolati funkciók és a fontos hibajavításokat tartalmaz.
* Sajnos jelenleg nem lehet ellenőrizni a helyszíni visszaírási ügyfél állapotát, mert az Azure AD Connect telepített verziója elavult. [Azure AD Connect frissítése](./../connect/active-directory-aadconnect-upgrade-previous-version.md) tudni ellenőrizni a kapcsolat állapotát.
* Sajnos úgy tűnik, nem lehet csatlakozni a helyszíni visszaírási ügyfélhez most. [Az Azure AD Connect – hibaelhárítás](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) a kapcsolat visszaállításához.
* Sajnos nem nem lehet csatlakozni a helyszíni visszaírási ügyfélhez, mert a jelszóvisszaírás nincs megfelelően konfigurálva. [Jelszóvisszaíró konfigurálása](howto-sspr-writeback.md#configure-password-writeback) a kapcsolat visszaállításához.
* Sajnos úgy tűnik, nem lehet csatlakozni a helyszíni visszaírási ügyfélhez most. Ez található a rendszerünkben ideiglenes problémák okozhatják. Ha a probléma tartósan fennáll, [hibaelhárítása Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) a kapcsolat visszaállításához.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Visszaírja a jelszavakat a helyszíni címtár

Ez a vezérlő határozza meg, hogy a jelszóvisszaíró engedélyezve van-e a könyvtárhoz. A jelszóvisszaíró egy, a helyszíni visszaírási szolgáltatás állapota azt jelzi. Ez akkor hasznos, ha azt szeretné, ideiglenesen letilthatja a jelszóvisszaírást az Azure AD Connect újrakonfigurálása nélkül.

* Ha a kapcsoló beállítása **Igen**, majd a jelszóvisszaíró engedélyezve van, és összevont, átmenő hitelesítést, vagy a jelszó Jelszókivonat szinkronizálása felhasználók képesek-e a jelszavaikat.
* Ha a kapcsoló beállítása **nem**, majd a jelszóvisszaíró le van tiltva, és összevont, átmenő hitelesítést, vagy a jelszó Jelszókivonat szinkronizálása felhasználók számára nem érhetők el a jelszavaikat.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Engedélyezése a felhasználók számára a fiókok zárolásának feloldása új jelszó nélkül

Ez a vezérlő azt jelzi, hogy a felhasználók, akik látogasson el a jelszó-visszaállítási portál meg kell adni a lehetőséget, azok a helyszíni Active Directory-fiókok zárolásának ne kelljen új jelszót kérnek. Alapértelmezés szerint az Azure AD fiókok feloldja a jelszó-visszaállítás végrehajtása során. E két művelet külön ezt a beállítást használja. 

* Ha beállítása **Igen**, majd a felhasználók a jelszavuk és a zárolás feloldásához, vagy a fiók feloldása új jelszó nélkül kapnak.
* Ha beállítása **nem**, majd felhasználók vannak csak tudni elvégezni a kombinált jelszó alaphelyzetbe állítása és fiókok zárolásának feloldása műveletet.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Nem a jelszó-visszaállítás munkahelyi B2B-felhasználók számára?
Az összes-vállalatközi (B2B) konfiguráció teljes mértékben támogatottak a jelszó-visszaállítás, és módosítsa. B2B-felhasználói jelszó-visszaállítás a következő három esetben támogatják:

   * **Egy fiókpartner-szervezet egy meglévő Azure AD-bérlővel rendelkező felhasználók**: Ha a szervezetben használt együttműködését abban, meglévő Azure AD-bérlővel, hogy *tiszteletben bármilyen jelszóvisszaállítási szabályzatot engedélyezve vannak a bérlőt*. Jelszó-visszaállítás működéséhez az erőforráspartner-szervezet csak kell győződjön meg arról, hogy engedélyezve van-e az Azure AD SSPR. Semmilyen további díjat nem az Office 365-ügyfelek és a lépéseket követve is engedélyezhető a [jelszókezelés használatának első lépései](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) útmutató.
   * **Felhasználók, akik keresztül regisztrálnak** önkiszolgáló: Ha a szervezet, hogy partneri használt a [önkiszolgáló](../users-groups-roles/directory-self-service-signup.md) kerülnek a bérlőt a funkciót, azt, hogy értesítse az e-mail-címével, azok regisztrálva a jelszó alaphelyzetbe állítása.
   * **B2B-felhasználók**: minden olyan új B2B-felhasználók hozta létre az új [Azure AD B2B-funkciói](../active-directory-b2b-what-is-azure-ad-b2b.md) is elérhetik a jelszavaikat az e-mailt, azok regisztrálva a meghívó folyamat során.

Ez a forgatókönyv teszteléséhez nyissa meg http://passwordreset.microsoftonline.com egy partner felhasználók. Ha egy másodlagos vagy hitelesítési e-mail meghatározott rendelkeznek, új jelszó kérésének módja elvárt módon.

> [!NOTE]
> Kapott a vendéghozzáférés az Azure AD-bérlőhöz, a Hotmail.com, Outlook.com vagy más személyes e-mail-címeket, például a Microsoft-fiókok, amelyek nem használhatják az Azure AD SSPR. Szükségük van a jelszó átállításának található információk segítségével a [mikor nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.

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
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "A helyszíni integrációs jelszó a jelszóvisszaíró konfigurálása és a hibaelhárítási információk"
