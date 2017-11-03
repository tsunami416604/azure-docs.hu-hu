---
title: "Hogyan működik? Az Azure AD SSPR |} Microsoft Docs"
description: "Az Azure AD az önkiszolgáló jelszó-átállítási részletes bemutatója"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 71310534ec62b62bcd408d75060859c79bc470cf
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Az önkiszolgáló jelszó-változtatási az Azure AD részletes bemutatója

Hogyan működik az önkiszolgáló jelszó-Változtatási? Mi ez a lehetőség jelent a felületen? Továbbra is olvasási további tudnivalók az Azure AD az önkiszolgáló jelszó alaphelyzetbe állítása.

## <a name="how-does-the-password-reset-portal-work"></a>Hogyan alaphelyzetbe állítja a jelszót a munkahelyi portál

Amikor a felhasználók megnyitják a jelszó-visszaállítási portál, a munkafolyamat van kezdődött el meghatározásához:

   * Hogyan lehet honosított a lapot?
   * Érvénytelen a felhasználói fiókot?
   * Milyen a szervezet a felhasználó tartozik?
   * Ha a felhasználó jelszava felügyelt?
   * A felhasználó rendelkezik licenccel a szolgáltatás használatához?


További információt a jelszó mögötti logika olvassa el a következő lépések lap visszaállítása.

1. Felhasználó kattint a nem fér hozzá a fiók vagy is megnőnek közvetlenül [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
2. A megfelelő nyelven jelenik meg a felhasználói élmény, a böngésző területi beállítás alapján. A jelszó alaphelyzetbe állítása élmény azonos nyelvekre honosított, Office 365 támogatja.
3. Felhasználó beírja egy felhasználói Azonosítót, és továbbítja a captcha.
4. Az Azure AD ellenőrzi, hogy ha a felhasználó nem tud a szolgáltatás használatához a következő ellenőrzések végrehajtásával:
   * Ellenőrzi, hogy a felhasználó rendelkezik-e ez a funkció engedélyezve van, és az Azure AD-licenccel.
     * Ha a felhasználó nem rendelkezik ezzel a funkcióval vagy egy licenccel, a felhasználónak kapcsolatba kell lépnie a jelszó alaphelyzetbe állításához a rendszergazda.
   * Ellenőrzi, hogy a felhasználó rendelkezik-e a jobb oldali kihívást jelentenek a rendszergazda nyilatkozatnak a fiókot a megadott adatokat.
     * Ha a házirend csak egy ellenőrző igényel, majd biztosítható, hogy a felhasználó rendelkezik-e a megfelelő adatokat, legalább az egyik kihívás a rendszergazda házirend engedélyezve van definiálva.
       * Ha a felhasználó nincs konfigurálva, majd a felhasználó ajánlatos a jelszavuk a rendszergazdától.
     * Ha a házirend két kihívást igényel, majd biztosítható, hogy a felhasználó rendelkezik-e a megfelelő adatokat, a rendszergazda házirend által engedélyezett kihívásai legalább két definiált.
       * Ha a felhasználó nem úgy van beállítva, akkor azt a felhasználó nem javasoljuk, hogy a jelszó alaphelyzetbe állítása a rendszergazdától.
   * Ellenőrzi, hogy a felhasználó jelszavát a helyszíni (összevont vagy Jelszókivonat szinkronizálása) felügyelt.
     * Ha visszaírási van telepítve, és a jelszót a helyszínen kezelt, majd a felhasználó az engedélyezett hitelesítéséhez és a jelszó alaphelyzetbe állítása.
     * Ha visszaírási nincs telepítve, és a helyszíni kezeli a jelszót, majd a felhasználónak kapcsolatba kell lépnie a jelszó alaphelyzetbe állításához a rendszergazda.
5. Ha megállapította, hogy a felhasználó nem tudja alaphelyzetbe állítása sikeresen megtörtént a jelszavát, majd a rendszer végigvezeti a felhasználót a visszaállítási folyamat során.

## <a name="authentication-methods"></a>Hitelesítési módszerek

Ha az önkiszolgáló jelszó alaphelyzetbe állítása (SSPR) engedélyezve van, ki kell választania legalább egy olyan hitelesítési módszerek az alábbiak közül. Néha előfordulhat, hogy hallhassa kapuk néven ezeket a beállításokat. Erősen ajánlott legalább két hitelesítési módszer kiválasztása, hogy a felhasználók több beleszólása van.

* E-mail cím
* Mobiltelefon
* Irodai telefon
* Biztonsági kérdések

![Hitelesítés][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-authentication-data"></a>Milyen mezők szerepelnek a directory hitelesítési adatok

* Irodai telefon megfelel-e irodai telefon
    * A felhasználók nem tudnak ez mezőben magukat egy rendszergazdának kell megadni
* Mobiltelefon felel meg a hitelesítéshez megadott telefonját (nem nyilvánosan láthatóvá) vagy a mobiltelefonjára (nyilvánosan láthatóvá)
    * A szolgáltatás először megkeresi a hitelesítéshez megadott telefonját, majd visszavált a mobiltelefon, ha nem jelent-e
* Másodlagos E-mail-cím megfelel a hitelesítési E-mail (nem nyilvánosan láthatóvá) vagy a másodlagos E-mail
    * A szolgáltatás hitelesítési E-mail először megkeresi, majd vissza a másodlagos E-mail sikertelen

Alapértelmezés szerint csak a felhő attribútumok irodai telefon és mobiltelefonra szinkronizálva van a felhő címtárhoz a hitelesítési adatok a helyi címtárban lévő.

Felhasználók csak jelszó is visszaállítása, ha a hitelesítési módszereket, amelyek a rendszergazda engedélyezte, és megköveteli az adatok.

Ha a felhasználó nem szeretné, hogy a címtárban a mobiltelefonszám, de továbbra is használni szeretné, jelszó-visszaállításhoz, a rendszergazdák nem kell tölteni azt a könyvtárban, és a felhasználó majd fel kell töltenie a **hitelesítéshez megadott telefonját** keresztül attribútumot a [jelszó-változtatási regisztrációs portálra](http://aka.ms/ssprsetup). A rendszergazdák láthatják ezeket az információkat a profil, de nem máshol lesz közzétéve.

### <a name="number-of-authentication-methods-required"></a>Több hitelesítési módszer szükséges

Ez a beállítás meghatározza, hogy a rendelkezésre álló hitelesítési módszerek és a kapuk a felhasználó alaphelyzetbe állítása vagy feloldása a jelszavát kell végighaladnia és 1 vagy 2 megadható minimális száma.

Adja meg a további hitelesítési módszerek, ha engedélyezve vannak a rendszergazda felhasználók maguk dönthetik.

Ha a felhasználó nem rendelkezik a minimálisan szükséges módszerek regisztrált, láthatják, amely arra utasítja, kérje a rendszergazda számára, hogy a jelszó visszaállítása hibalap.

### <a name="how-secure-are-my-security-questions"></a>Hogy mennyire vannak biztonságban vannak a biztonsági kérdések

Biztonsági kérdések használata, ha ajánlott azokat használja egy másik módszerrel módon el más módszernél kevésbé biztonságos, mivel a felhasználók némelyike előfordulhat, hogy tudja, egy másik felhasználó kérdésekre adott válaszokat.

> [!NOTE] 
> Biztonsági kérdések tárolja a könyvtárban olyan felhasználói objektum közvetlenül és biztonságosan, és képes csak válaszolni felhasználók regisztráció során. Nincs semmilyen módszer, olvassa el, vagy módosíthatja a felhasználói kérdések és válaszok a rendszergazda.
>

### <a name="security-question-localization"></a>Biztonsági kérdés honosítása

Hajtsa végre az összes előre definiált kérdésekre az Office 365 nyelvek a felhasználó a böngésző nyelve alapján a teljes készletének honosítva vannak.

* Melyik városban ismerkedett meg az első házastársával/párjával?
* Melyik városban találkozak először a szülei?
* Melyik városban lakik a legközelebbi rokona?
* Az édesapja melyik városban született?
* Melyik városban volt az első munkahelye?
* Melyik városban született az édesanyja?
* Hol töltötte 1999 szilveszterét?
* Mi az, hogy magas volt a kedvenc tanárának vezetékneve * iskolai?
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

Egyéni biztonsági kérdések nem a különböző területi beállításokhoz honosított. Összes egyéni kérdést a bevitel a rendszergazda felhasználói felületén akkor is, ha a felhasználó a böngésző nyelve eltérő nyelvével azonos nyelven jelennek meg. Ha honosított kérdése van szüksége, használja az előre definiált kérdéseket.

Egy egyéni biztonsági kérdés hossza legfeljebb 200 karakter lehet.

### <a name="security-question-requirements"></a>Biztonsági kérdés követelmények

* Minimális válasz karakteres korlátot érték 3 karakter
* A válasz maximális karakteres korlátot 40 karakter
* Felhasználók esetleg nem fogadja a hívást ugyanezt a kérdést egynél többször
* Felhasználók nem rendelkezhetnek a azonos egynél több kérdésre adott válasz
* Bármely karakterkészlet meghatározása a kérdések és válaszok, beleértve a Unicode-karaktereket is használható
* Megadott kérdéseket száma nagyobb vagy egyenlő regisztrálnia kérdések számát kell lennie.

## <a name="registration"></a>Regisztráció

### <a name="require-users-to-register-when-signing-in"></a>Szükséges a felhasználóknak regisztrálniuk a bejelentkezéskor?

A beállítás engedélyezése szükséges, a felhasználó, aki engedélyezve van a jelszó alaphelyzetbe állítása a jelszó befejezéséhez regisztrációs alaphelyzetbe állítása, ha azok bejelentkezni az Azure AD használatával jelentkezzen be, mint az alábbi alkalmazások:

* Office 365
* Azure Portal
* Hozzáférési panel
* Összevont alkalmazásokhoz
* Egyéni alkalmazások az Azure AD használatával

Ha le van tiltva a felhasználók továbbra is futtathatja manuálisan regisztrálhatják az elérhetőségét ellátogatva [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) vagy kattintson a **regisztrálhatnak a jelszóváltoztatásra** alatt a profil lapon a hozzáférési panel.

> [!NOTE]
> Felhasználók a jelszó-visszaállítási portál esetben elvetheti a Mégse gombra kattintva vagy az ablak bezárása, de a rendszer kéri, minden alkalommal, amikor azok bejelentkezéshez, amíg a regisztrációs művelet befejeződik.
>

### <a name="number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését

Ez a beállítás azt határozza meg, mennyi ideig beállítása és reconfirming hitelesítési adatok között, és csak akkor érhető el, ha engedélyezi a **felhasználói bejelentkezéskor regisztráció megkövetelése** lehetőséget.

Érvényes értékek: 0-730 nap 0, ami azt jelenti, ne jelenjen meg a felhasználók számára a hitelesítési adatokat megerősítése

## <a name="notifications"></a>Értesítések

### <a name="notify-users-on-password-resets"></a>Értesítse a felhasználókat új jelszó kérésekor?

Ha ez a beállítás értéke Igen, a jelszó alaphelyzetbe állításával a felhasználó kap egy e-mailt, amely értesíti őket, hogy a jelszó változott az önkiszolgáló jelszó-Változtatási portálján keresztül az elsődleges és másodlagos e-mail címét, a fájl az Azure ad-ben. A visszaállítási értesítést senki más nem esemény.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Az összes rendszergazda értesítése, ha más rendszergazdák a jelszavak alaphelyzetbe állítása

Ha a beállítás értéke Igen, majd **minden rendszergazda** az elsődleges e-mail címéhez a fájlt e-mailt kap az Azure ad-ben, amely értesíti őket, hogy egy másik rendszergazda megváltozott a jelszavát, használja az önkiszolgáló jelszó-Változtatási.

Példa: Nincsenek négy rendszergazdák környezetben. "A" rendszergazda az önkiszolgáló jelszó-Változtatási használatával jelszavának alaphelyzetbe állítása. A rendszergazdák B, C és D azokat, azonban ez a riasztás e-mailt kapni.

## <a name="on-premises-integration"></a>Helyszíni integráció

Ha van telepítve, konfigurálásához és az Azure AD Connect engedélyezve, akkor a következő beállítások helyszíni Integrációk.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Jelszavakat írhasson a helyszíni címtár

Szabályozza-e a jelszóvisszaírás engedélyezve van a könyvtárhoz, és ha visszaírási egy, a helyszíni visszaírási szolgáltatás állapotát jelzi. Ez akkor hasznos, ha azt szeretné, hogy ideiglenesen letilthatja a jelszóvisszaírást az Azure AD Connect újrakonfigurálása nélkül.

* Ha a kapcsoló értéke Igen, majd visszaírás engedélyezve van, és összevont és és jelszó kivonatoló szinkronizált felhasználók visszaállíthassák a jelszavukat.
* Ha a kapcsoló értéke nem, majd visszaírás le van tiltva, és összevont és jelszó kivonatoló szinkronizált felhasználók nem képesek visszaállíthassák a jelszavukat.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Engedélyezése a felhasználók számára a fiókok zárolásának feloldása nélkül a jelszó alaphelyzetbe állításával

Határozza meg, függetlenül attól, felhasználók, akik látogasson el a jelszó-változtatási portál meg kell adni a lehetőséget, a helyszíni Active Directory-fiókok zárolásának nélkül a jelszó alaphelyzetbe állításával. Alapértelmezés szerint az Azure AD feloldja a fiókok, a jelszó-visszaállítás végrehajtásakor, ez a beállítás lehetővé teszi a műveletek két külön. 

* Ha a "yes" értékre, majd felhasználók kapnak, hogy a jelszó alaphelyzetbe állítása és a fiók zárolását kívánja feloldani, vagy a jelszó alaphelyzetbe állításával feloldásához.
* Ha beállítása "nem", akkor a felhasználók csak tudják végrehajtani egy kombinált jelszó alaphelyzetbe állítása és fiókok zárolásának feloldása műveletet.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Hogyan alaphelyzetbe állítja a jelszót B2B felhasználók számára?
Bármilyen B2B konfigurációjának teljes mértékben támogatottak a jelszó alaphelyzetbe állítása és módosítása. A következő három esetben B2B felhasználói jelszó-visszaállításhoz támogatottak.

1. **Egy partner szervezet munkatársa, a meglévő Azure AD-bérlő felhasználóit** – Ha a szervezetben, amelyek együttműködve egy meglévő Azure AD-bérlő azt **tiszteletben tartják a bérlőre engedélyezve vannak függetlenül jelszó alaphelyzetbe állítása házirendjei**. A jelszó alaphelyzetbe állítása működjön, az erőforráspartner szervezet csak kell győződjön meg arról, hogy az Azure AD SSPR engedélyezve van, amely nem kell külön fizetni az Office 365-ügyfelek, és a lépések elvégzésével engedélyezheti a [Ismerkedés a Jelszókezeléssel](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) útmutató.
2. **Felhasználókra iratkozott fel használatával [önkiszolgáló regisztrációs](active-directory-self-service-signup.md)**  – Ha a szervezet, amelyek együttműködve használt a [önkiszolgáló regisztrációs](active-directory-self-service-signup.md) a bérlő feltölti a beállítást, azt hogy azok visszaállítani az e-mailt regisztrálják azokat.
3. **B2B felhasználók** -az új létrehozott új B2B felhasználók [Azure AD B2B képességek](active-directory-b2b-what-is-azure-ad-b2b.md) is tudnak visszaállíthassák a jelszavukat, az e-mailt a meghívott felhasználó során regisztrálják azokat.

Ez a forgatókönyv teszteléséhez Ugrás http://passwordreset.microsoftonline.com ezen partner felhasználók egyike. Mindaddig, amíg egy másodlagos e-mail vagy a megadott hitelesítési e-mail rendelkeznek, jelszó-átállítási akkor működik megfelelően.

## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozásokat követve az Azure AD jelszóátállításáról olvashat további információkat.

* [Hogyan végezze el a sikeres bevezetéshez az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-best-practices.md)
* [A jelszó megváltoztatására](active-directory-passwords-update-your-own-password.md).
* [Az önkiszolgáló jelszó-változtatási regisztrációs](active-directory-passwords-reset-register.md).
* [Licencelés kérdése van?](active-directory-passwords-licensing.md)
* [Milyen adatok SSPR használja, és milyen adatokat kell tölteni a felhasználók számára?](active-directory-passwords-data.md)
* [Hitelesítési módszerek állnak rendelkezésre a felhasználók számára?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Mik a házirend-beállításokban az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-policy.md)
* [A jelszóvisszaírás és miért fontos információk?](active-directory-passwords-writeback.md)
* [Hogyan jelentést az önkiszolgáló jelszó-Változtatási tevékenység?](active-directory-passwords-reporting.md)
* [Mik az önkiszolgáló jelszó-Változtatási közül az összes, és mit azokat a következőket:?](active-directory-passwords-how-it-works.md)
* [Szerintem valami nem működik. Hogyan hibáinak elhárítása az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-troubleshoot.md)
* [A rendszer nem jelzett valahol máshol kérdést kell](active-directory-passwords-faq.md)

[Authentication]: ./media/active-directory-passwords-how-it-works/sspr-authentication-methods.png "Azure AD hitelesítési módszer áll rendelkezésre, és a szükséges mennyiség"