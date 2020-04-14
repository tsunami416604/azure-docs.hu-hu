---
title: Azure többtényezős hitelesítés – gyakori kérdések – Azure Active Directory
description: Az Azure többtényezős hitelesítésével kapcsolatos gyakori kérdések és válaszok.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d28f93f316ac2a63be6b3a8eb0b80678bd7607f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271403"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication – gyakran ismételt kérdések

Ez a gyIK választ ad az Azure többtényezős hitelesítésével és a többtényezős hitelesítési szolgáltatás használatával kapcsolatos gyakori kérdésekre. A szolgáltatással, a számlázási modellekkel, a felhasználói élményekkel és a hibaelhárítással kapcsolatos kérdésekre bontva.

> [!IMPORTANT]
> 2019. július 1-jéig a Microsoft a továbbiakban nem ajánlja fel az MFA Server alkalmazást az új telepítésekhez. Azoknak az új ügyfeleknek, akik többtényezős hitelesítést szeretnének megkövetelni a felhasználóiktól, felhőalapú Azure többtényezős hitelesítést kell használniuk. Azok a meglévő ügyfelek, akik július 1-je előtt aktiválták az MFA-kiszolgálót, a szokásos módon letölthetik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon létrehozhatják az aktiválási hitelesítő adatokat.
>
> Az alábbiakban megosztott információk az Azure többtényezős hitelesítési kiszolgálócsak azon felhasználók számára, akik már fut nak az MFA-kiszolgáló.
>
> A fogyasztásalapú licencelés már nem érhető el az új ügyfelek számára 2018.
> 2018. szeptember 1-i hatállyal előfordulhat, hogy új hitelesítési szolgáltatók jönnek létre. A meglévő hitelesítésszolgáltatók továbbra is használhatók és frissíthetők. A többtényezős hitelesítés továbbra is elérhető funkció marad az Azure AD Premium-licencekben.

## <a name="general"></a>Általános kérdések

* [Hogyan kezeli az Azure többtényezős hitelesítési kiszolgáló a felhasználói adatokat?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Milyen rövid SMS-kódokat használnak sms-üzenetek küldésére a felhasználóknak?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Hogyan kezeli az Azure többtényezős hitelesítési kiszolgáló a felhasználói adatokat?

A többtényezős hitelesítési kiszolgáló val a felhasználói adatok csak a helyszíni kiszolgálókon tárolódnak. A felhőben nincsenek állandó felhasználói adatok. Amikor a felhasználó kétlépéses ellenőrzést hajt végre, a többtényezős hitelesítési kiszolgáló adatokat küld az Azure többtényezős hitelesítésfelhő-szolgáltatásának hitelesítésre. A többtényezős hitelesítési kiszolgáló és a többtényezős hitelesítésfelhő-szolgáltatás közötti kommunikáció secure sockets layer (SSL) vagy Transport Layer Security (TLS) protokollt használ a 443-as kimenő porton.

Amikor hitelesítési kérelmeket küld a felhőszolgáltatásba, a hitelesítési és használati jelentések hez adatokat gyűjtenek. A kétlépéses ellenőrzési naplók a következő adatmezőket tartalmazzák:

* **Egyedi azonosító** (felhasználónév vagy helyszíni többtényezős hitelesítési kiszolgáló azonosítója)
* **Vezeték- és utónév** (nem kötelező)
* **E-mail cím** (nem kötelező)
* **Telefonszám** (hanghívás vagy SMS-hitelesítés használata esetén)
* **Eszköztoken** (mobilalkalmazás-hitelesítés használata esetén)
* **Hitelesítési mód**
* **Hitelesítés eredménye**
* **Többtényezős hitelesítési kiszolgáló neve**
* **Többtényezős hitelesítési kiszolgáló IP-címe**
* **Ügyfél IP-címe** (ha elérhető)

A választható mezők konfigurálhatók a többtényezős hitelesítési kiszolgálón.

Az ellenőrzés eredménye (sikeres vagy megtagadás), és az oka, ha megtagadták, a hitelesítési adatokkal együtt tárolódnak. Ezek az adatok a hitelesítési és használati jelentésekben érhetők el.

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Milyen rövid SMS-kódokat használnak sms-üzenetek küldésére a felhasználóknak?

Az Egyesült Államokban a következő SMS rövid kódokat használjuk:

* *97671*
* *69829*
* *51789*
* *99399*

Kanadában a következő SMS rövid kódokat használjuk:

* *759731*
* *673801*

Nincs garancia a következetes SMS-re vagy a hangalapú többtényezős hitelesítésre, amely ugyanazzal a számmal történik. Felhasználóink érdekében bármikor hozzáadhatunk vagy eltávolíthatunk rövid kódokat, mivel az SMS-ek kézbesíthetőségének javítása érdekében útvonal-módosításokat végzünk. Az Egyesült Államokon és Kanadán kívül nem támogatjuk a rövid kódokat az Egyesült Államokon és Kanadán kívül.

## <a name="billing"></a>Számlázás

A legtöbb számlázási kérdésre a [többtényezős hitelesítésdíjszabási lapra](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) vagy az [Azure többtényezős hitelesítési verziók és használati csomagok](concept-mfa-licensing.md)dokumentációjára hivatkozva lehet válaszolni.

* [A szervezetemnek díjat kell fizetnie a hitelesítéshez használt telefonhívások és szöveges üzenetek küldéséért?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [A felhasználónkénti számlázási modell díjat számít fel nekem az összes engedélyezett felhasználóért, vagy csak azokért, amelyek kétlépéses ellenőrzést végeztek?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Hogyan működik a többtényezős hitelesítés számlázása?](#how-does-multi-factor-authentication-billing-work)
* [Van ingyenes azure-os többtényezős hitelesítés?](#is-there-a-free-version-of-azure-multi-factor-authentication)
* [A szervezet bármikor válthat a felhasználónkénti és a hitelesítésenkénti fogyasztási számlázási modellek között?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Bármikor válthat a szervezetem a fogyasztásalapú számlázás és az előfizetések (licencalapú modell) között?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [A szervezetnek identitásokat kell használnia és szinkronizálnia az Azure többtényezős hitelesítéshasználatához?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>A szervezetemnek díjat kell fizetnie a hitelesítéshez használt telefonhívások és szöveges üzenetek küldéséért?

Nem, nem számítunk fel díjat az Azure többtényezős hitelesítésén keresztül a felhasználóknak küldött egyéni telefonhívásokért vagy szöveges üzenetekért. Ha hitelesítésenkénti MFA-szolgáltatót használ, minden egyes hitelesítésért díjat kell fizetnie, de a használt módszerért nem.

Előfordulhat, hogy a felhasználóknak a személyes telefonszolgáltatásuknak megfelelően díjat kell fizetniük a kapott telefonhívásokért vagy szöveges üzenetekért.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>A felhasználónkénti számlázási modell díjat számít fel nekem az összes engedélyezett felhasználóért, vagy csak azokért, amelyek kétlépéses ellenőrzést végeztek?

A számlázás a többtényezős hitelesítés használatára konfigurált felhasználók számától függ, függetlenül attól, hogy az adott hónapban kétlépcsős ellenőrzést hajtottak-e végre.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Hogyan működik a többtényezős hitelesítés számlázása?

Amikor létrehoz egy felhasználónkénti vagy hitelesítésenkénti MFA-szolgáltatót, a szervezet Azure-előfizetése a használat alapján havonta kerül számlázásra. Ez a számlázási modell hasonló ahhoz, ahogyan az Azure számlázza a virtuális gépek és webalkalmazások használatát.

Ha előfizetést vásárol az Azure többtényezős hitelesítéshez, a szervezet csak az egyes felhasználók éves licencdíját fizeti. Az MFA-licencek és az Office 365, az Azure AD Premium vagy az Enterprise Mobility + Security csomagok számlázása így van számlázva.

További információ: [Az Azure többtényezős hitelesítésbe beszerezni.](concept-mfa-licensing.md)

### <a name="is-there-a-free-version-of-azure-multi-factor-authentication"></a>Van ingyenes azure-os többtényezős hitelesítés?

A biztonsági alapértelmezések engedélyezhetők az Azure AD ingyenes csomagjában. A biztonsági alapértelmezések esetén minden felhasználó engedélyezve van a többtényezős hitelesítéshez a Microsoft Authenticator alkalmazás használatával. A biztonsági beállításokkal nem használhat szöveges üzenetet vagy telefonos ellenőrzést, csak a Microsoft Authenticator alkalmazást.

További információ: [Mik a biztonsági alapértékek?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>A szervezet bármikor válthat a felhasználónkénti és a hitelesítésenkénti fogyasztási számlázási modellek között?

Ha a szervezet a fogyasztásalapú számlázással önálló szolgáltatásként vásárolja meg az MFA-t, akkor az MFA-szolgáltató létrehozásakor számlázási modellt kell választania. Az MFA-szolgáltató létrehozása után nem módosíthatja a számlázási modellt. 

Ha az MFA-szolgáltató *nem* kapcsolódik egy Azure AD-bérlőhöz, vagy az új MFA-szolgáltatót egy másik Azure AD-bérlőhöz kapcsolja, a felhasználói beállítások és a konfigurációs beállítások nem kerülnek átvitelre. Emellett a meglévő Azure MFA-kiszolgálókat újra aktiválni kell az új MFA szolgáltatón keresztül létrehozott aktiváló hitelesítők adatokkal. Az MFA-kiszolgálók az új MFA szolgáltatóhoz történő kapcsolás céljából történő újbóli aktiválása nincs hatással a telefonhívásban vagy szöveges üzenetben történő hitelesítésekre, a mobilalkalmazás-értesítések viszont minden felhasználó számára megszűnnek működni a mobilalkalmazás újbóli aktiválásáig.

További információ az [MFA-szolgáltatókról az Azure multifactor hitelesítésszolgáltatóval való ismerkedés című részben.](concept-mfa-authprovider.md)

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Bármikor válthat a szervezetem a fogyasztásalapú számlázás és az előfizetések (licencalapú modell) között?

Bizonyos esetekben, igen.

Ha a címtár rendelkezik egy *felhasználónkénti* Azure többtényezős hitelesítésszolgáltatóval, hozzáadhat MFA-licenceket. A licenccel rendelkező felhasználók nem számítanak bele a felhasználónkénti fogyasztásalapú számlázásba. A licenccel nem rendelkező felhasználók továbbra is engedélyezhetők az MFA-szolgáltatón keresztül. Ha többtényezős hitelesítéshasználatára konfigurált összes felhasználóhoz vásárol és rendel licenceket, törölheti az Azure többtényezős hitelesítésszolgáltatót. Bármikor létrehozhat egy másik felhasználónkénti MFA-szolgáltatót, ha a jövőben több felhasználója van, mint licence.

Ha a címtár rendelkezik egy hitelesítési Azure többtényezős hitelesítésszolgáltató, mindig minden egyes hitelesítés, ha az *MFA-szolgáltató* kapcsolódik az előfizetéshez. Az MFA-licenceket hozzárendelheti a felhasználókhoz, de továbbra is minden kétlépéses ellenőrzési kérelemért díjat kell fizetnie, függetlenül attól, hogy az olyan személytől származik-e, akinek többszintű licencvan hozzárendelve, vagy sem.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication"></a>A szervezetnek identitásokat kell használnia és szinkronizálnia az Azure többtényezős hitelesítéshasználatához?

Ha a szervezet egy fogyasztásalapú számlázási modellt használ, az Azure Active Directory nem kötelező, de nem kötelező. Ha az MFA-szolgáltató nincs összekapcsolva egy Azure AD-bérlővel, csak a helyszínen telepítheti az Azure többtényezős hitelesítési kiszolgálót.

Az Azure Active Directory szükséges a licencmodellhez, mert a licencek az Azure AD-bérlőhöz kerülnek, amikor megvásárolja és hozzárendeli azokat a címtárban lévő felhasználókhoz.

## <a name="manage-and-support-user-accounts"></a>Felhasználói fiókok kezelése és támogatása

* [Mit kell mondanom a felhasználóknak, ha nem kapnak választ a telefonjukon?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [Mi a teendő, ha az egyik felhasználóm nem tud bejutni a fiókjába?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [Mi a teendő, ha az egyik felhasználóm elveszíti az alkalmazásjelszavakat használó telefont?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [Mi a teendő, ha a felhasználó nem tud bejelentkezni a nem böngészőalkalmazásokba?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [A felhasználóim azt mondják, hogy néha nem kapják meg a szöveges üzenetet, vagy az ellenőrzés időtúljár.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Módosíthatom azt az időt, ameddig a felhasználóknak be kell írniuk az ellenőrző kódot egy szöveges üzenetből, mielőtt a rendszer idővel kikerül?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Használhatok hardveres jogkivonatokat az Azure többtényezős hitelesítési kiszolgálójával?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Használhatom az Azure többtényezős hitelesítési kiszolgálóját a terminálszolgáltatások védelmére?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Beállítottam a hívóazonosítót az MFA-kiszolgálón, de a felhasználók továbbra is többtényezős hitelesítési hívásokat kapnak egy névtelen hívótól.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Miért kéri a rendszer a felhasználókat a biztonsági adataik regisztrálására?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>Mit kell mondanom a felhasználóknak, ha nem kapnak választ a telefonjukon?

A felhasználók 5 perc alatt akár ötször is próbálkoznak telefonhívással vagy SMS-sel a hitelesítéshez. A Microsoft több szolgáltatót használ hívások és SMS-üzenetek kézbesítésére. Ha ez a megközelítés nem működik, nyisson meg egy támogatási esetet a további hibaelhárításhoz.

A külső biztonsági alkalmazások blokkolhatják az ellenőrző kód szöveges üzenetét vagy telefonhívását is. Ha külső biztonsági alkalmazást használ, próbálja meg letiltani a védelmet, majd kérjen egy másik MFA-ellenőrző kódot.

Ha a fenti lépések nem működnek, ellenőrizze, hogy a felhasználók egynél több ellenőrzési módszerre vannak-e konfigurálva. Próbáljon meg újra bejelentkezni, de válasszon egy másik ellenőrzési módszert a bejelentkezési lapon.

További információt a [végfelhasználói hibaelhárítási útmutatóban](../user-help/multi-factor-authentication-end-user-troubleshoot.md)talál.

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>Mi a teendő, ha az egyik felhasználóm nem tud bejutni a fiókjába?

A felhasználó fiókját úgy állíthatja vissza, hogy újra végigmegy a regisztrációs folyamaton. További információ a [felhasználói és eszközbeállítások kezeléséről az Azure többtényezős hitelesítésével a felhőben.](howto-mfa-userdevicesettings.md)

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>Mi a teendő, ha az egyik felhasználóm elveszíti az alkalmazásjelszavakat használó telefont?

A jogosulatlan hozzáférés megakadályozása érdekében törölje a felhasználó összes alkalmazásjelszavát. Miután a felhasználó rendelkezik egy csereeszközzel, újra létrehozhatja a jelszavakat. További információ a [felhasználói és eszközbeállítások kezeléséről az Azure többtényezős hitelesítésével a felhőben.](howto-mfa-userdevicesettings.md)

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>Mi a teendő, ha a felhasználó nem tud bejelentkezni a nem böngészőalkalmazásokba?

Ha a szervezet továbbra is régi ügyfeleket használ, és [engedélyezte az alkalmazásjelszavak használatát,](howto-mfa-mfasettings.md#app-passwords)akkor a felhasználók nem tudnak bejelentkezni ezekbe az örökölt ügyfelekbe a felhasználónevükkel és jelszavukkal. Ehelyett be kell [állítaniuk az alkalmazásjelszavakat.](../user-help/multi-factor-authentication-end-user-app-passwords.md) A felhasználóknak törölniük kell (törölniük) a bejelentkezési adataikat, újra kell indítaniuk az alkalmazást, majd a szokásos jelszó helyett a felhasználónevükkel és *az alkalmazásjelükkel* kell bejelentkezniük.

Ha a szervezet nem rendelkezik örökölt ügyfelekkel, ne engedélyezze a felhasználóknak az alkalmazásjelszavak létrehozását.

> [!NOTE]
> **Modern hitelesítés Office 2013-ügyfelekszámára**
>
> Az alkalmazásjelszavak csak olyan alkalmazásokhoz szükségesek, amelyek nem támogatják a modern hitelesítést. Az Office 2013-ügyfelek támogatják a modern hitelesítési protokollokat, de konfigurálni kell őket. A modern hitelesítés minden olyan ügyfél számára elérhető, aki az Office 2013 2015 márciusi vagy újabb frissítését futtatja. További információt az Office [365 modern hitelesítéssel frissített blogbejegyzésében](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)talál.

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>A felhasználóim azt mondják, hogy néha nem kapják meg a szöveges üzenetet, vagy az ellenőrzés időtúljár.

Az SMS-üzenetek kézbesítése nem garantált, mert vannak olyan ellenőrizhetetlen tényezők, amelyek befolyásolhatják a szolgáltatás megbízhatóságát. Ezek közé a tényezők közé tartozik a célország vagy régió, a mobiltelefon-szolgáltató és a jelerősség.

A külső biztonsági alkalmazások blokkolhatják az ellenőrző kód szöveges üzenetét vagy telefonhívását is. Ha külső biztonsági alkalmazást használ, próbálja meg letiltani a védelmet, majd kérjen egy másik MFA-ellenőrző kódot.

Ha a felhasználók gyakran problémáik vannak a szöveges üzenetek megbízható fogadásával, késve a Microsoft Authenticator alkalmazást vagy a telefonhívási módszert használják. A Microsoft Hitelesítő mobil- és Wi-Fi-kapcsolaton keresztül is kaphat értesítéseket. Ezenkívül a mobilalkalmazás akkor is képes ellenőrző kódokat generálni, ha az eszköz egyáltalán nem rendelkezik jellel. A Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) és [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) rendszereken érhető el.

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Módosíthatom azt az időt, ameddig a felhasználóknak be kell írniuk az ellenőrző kódot egy szöveges üzenetből, mielőtt a rendszer idővel kikerül?

Bizonyos esetekben, igen.

Az Egyirányú SMS-ek az Azure MFA Server v7.0-s vagy újabb verzióval, konfigurálhatja az időbeli megtadalékbeállítást egy beállítási kulcs beállításával. Miután az MFA felhőszolgáltatás elküldi a szöveges üzenetet, az ellenőrző kód (vagy egyszeri jelkód) visszakerül az MFA-kiszolgálóra. Az MFA-kiszolgáló alapértelmezés szerint 300 másodpercig tárolja a kódot a memóriában. Ha a felhasználó nem adja meg a kódot, mielőtt a 300 másodperc telt el, a hitelesítés megtagadva. Az alábbi lépésekkel módosíthatja az alapértelmezett időelési beállítást:

1. Nyissa meg a következőt: `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Hozzon létre egy *pfsvc_pendingSmsTimeoutSeconds* nevű **duplaszó** beállításkulcsot, és állítsa be azt az időt másodpercben, amerre az Azure MFA-kiszolgáló egyszeri jelkódokat tárol.

>[!TIP]
>
> Ha több MFA-kiszolgálóval rendelkezik, csak az, amelyik feldolgozta az eredeti hitelesítési kérelmet, ismeri a felhasználónak küldött ellenőrző kódot. Amikor a felhasználó beírja a kódot, az érvényesítésre vonatkozó hitelesítési kérelmet ugyanannak a kiszolgálónak kell elküldenie. Ha a kód érvényesítése egy másik kiszolgálóra kerül, a rendszer megtagadja a hitelesítést.

Ha a felhasználók nem válaszolnak az SMS-re a megadott időtúladási időszakon belül, a rendszer elutasítja a hitelesítést.

Egyirányú SMS-hez az Azure MFA a felhőben (beleértve az AD FS adapter vagy a hálózati házirend-kiszolgáló bővítmény), nem konfigurálhatja az időtúltöltési beállítást. Az Azure AD 180 másodpercig tárolja az ellenőrző kódot.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Használhatok hardveres jogkivonatokat az Azure többtényezős hitelesítési kiszolgálójával?

Ha Az Azure többtényezős hitelesítési kiszolgálót használja, importálhatja a harmadik féltől származó nyílt hitelesítés (OATH) időalapú, egyszeri jelszó (TOTP) tokeneket, majd használhatja őket kétlépéses ellenőrzéshez.

Használhatja az ActiveIdentity-jogkivonatokat, amelyek OATH TOTP-tokenek, ha a titkos kulcsot csv-fájlba helyezi, és importálja az Azure többtényezős hitelesítési kiszolgálóra. Az OATH-tokenek az Active Directory összevonási szolgáltatásokkal (ADFS), az Internet Information Server (IIS) űrlapalapú hitelesítéssel és a távoli hitelesítésbetárcsázási felhasználói szolgáltatással (RADIUS) is használhatók, amennyiben az ügyfélrendszer elfogadja a felhasználói bevitelt.

Harmadik féltől származó OATH TOTP-tokeneket a következő formátumokkal importálhat:  

- Hordozható szimmetrikus kulcstartó (PSKC)
- CSV, ha a fájl tartalmaz egy sorozatszámot, egy titkos kulcsot Base 32 formátumban, és egy időintervallumot

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Használhatom az Azure többtényezős hitelesítési kiszolgálóját a terminálszolgáltatások védelmére?

Igen, de windows Server 2012 R2 vagy újabb rendszer használata esetén a terminálszolgáltatásokat csak távoli asztali átjáró (Távoli asztali átjáró) használatával tudja biztonságossá tenni.

A Windows Server 2012 R2 biztonsági változásai megváltoztatták az Azure Multi-Factor Authentication Server csatlakozását a Helyi biztonsági szervezet (LSA) biztonsági csomaghoz a Windows Server 2012 és korábbi verzióiban. A Terminálszolgáltatások Windows Server 2012-es vagy korábbi verzióiban a [Windows-hitelesítéssel biztonságossá](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)tehet egy alkalmazást. Ha Windows Server 2012 R2 rendszert használ, távoli asztali átjáróra van szüksége.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Beállítottam a hívóazonosítót az MFA-kiszolgálón, de a felhasználók továbbra is többtényezős hitelesítési hívásokat kapnak egy névtelen hívótól.

Ha a többtényezős hitelesítési hívásokat a nyilvános telefonhálózaton keresztül bonyolítják le, néha olyan szolgáltatón keresztül irányítják őket, amely nem támogatja a hívóazonosítót. A szolgáltató viselkedése miatt a hívóazonosító nem garantált, annak ellenére, hogy a többtényezős hitelesítési rendszer mindig elküldi azt.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Miért kéri a rendszer a felhasználókat a biztonsági adataik regisztrálására?

Több oka is lehet annak, hogy a felhasználókat rálehet kérni biztonsági adataik regisztrálására:

- A felhasználó az Azure AD-ben a rendszergazda engedélyezte az MFA-t, de még nem rendelkezik a fiókjához regisztrált biztonsági adatokkal.
- A felhasználó engedélyezve van az önkiszolgáló jelszó-visszaállítás az Azure AD-ben. A biztonsági információk segítenek nekik a jelszó visszaállításában a jövőben, ha valaha is elfelejtik.
- A felhasználó hozzáfért egy olyan alkalmazáshoz, amely feltételes hozzáférési házirenddel rendelkezik az MFA megköveteléséhez, és korábban nem regisztrált az MFA-hoz.
- A felhasználó regisztrál egy eszközt az Azure AD-vel (beleértve az Azure AD-s csatlakozást is), és a szervezetnek többfa-t kell igényelnie az eszköz regisztrációhoz, de a felhasználó korábban nem regisztrált az MFA-hoz.
- A felhasználó a Windows Hello for Business alkalmazást generálja a Windows 10-ben (amely hez MFA szükséges), és korábban nem regisztrált az MFA-hoz.
- A szervezet létrehozott és engedélyezett egy MFA regisztrációs házirendet, amely a felhasználóra lett alkalmazva.
- A felhasználó korábban regisztrált az MFA-hoz, de olyan ellenőrzési módszert választott, amelyet a rendszergazda azóta letiltott. A felhasználónak ezért újra át kell mennie az MFA-regisztráción, hogy új alapértelmezett ellenőrzési módszert válasszon.

## <a name="errors"></a>Hibák

* [Mit tegyenek a felhasználók, ha a mobilalkalmazás-értesítések használatakor "A hitelesítési kérelem nem aktivált fiókhoz érhető el" hibaüzenet jelenik meg?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [Mit tegyenek a felhasználók, ha 0x800434D4L hibaüzenetet látnak, amikor nem böngészőalkalmazásba jelentkeznek be?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>Mit tegyenek a felhasználók, ha a mobilalkalmazás-értesítések használatakor "A hitelesítési kérelem nem aktivált fiókhoz érhető el" hibaüzenet jelenik meg?

Kérje meg a felhasználót, hogy végezze el az alábbi eljárást a fiók eltávolításához a Microsoft Hitelesítőből, majd adja hozzá újra:

1. Nyissa meg [az Azure Portal-profiljukat,](https://account.activedirectory.windowsazure.com/profile/) és jelentkezzen be egy szervezeti fiókkal.
2. Válassza a **További biztonsági ellenőrzés lehetőséget.**
3. Távolítsa el a meglévő fiókot a Microsoft Authenticator alkalmazásból.
4. Kattintson **a Konfigurálás**gombra, majd kövesse az utasításokat a Microsoft Hitelesítő újrakonfigurálásához.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>Mit tegyenek a felhasználók, ha 0x800434D4L hibaüzenetet látnak, amikor nem böngészőalkalmazásba jelentkeznek be?

A *0x800434D4L* hiba akkor jelentkezik be, amikor megpróbál bejelentkezni egy helyi számítógépre telepített nem böngészőalkalmazásba, amely nem működik a kétlépéses ellenőrzést igénylő fiókokkal.

A hiba kerülő megoldása, ha külön felhasználói fiókkal rendelkezik a rendszergazdai és nem rendszergazdai műveletekhez. Később a rendszergazdai és a nem rendszergazdai fiók között is összekapcsolhatja a postaládákat, így nem rendszergazdai fiókjával jelentkezhet be az Outlookba. A megoldással kapcsolatos további részletekről megtudhatja, hogy [miként adhatja meg a rendszergazdának a felhasználó postaládájának megnyitását és megtekintését.](https://help.outlook.com/141/gg709759.aspx?sl=1)

## <a name="next-steps"></a>További lépések

Ha a kérdésére itt nem kap választ, a következő támogatási lehetőségek érhetők el:

* Keressen a [Microsoft támogatási tudásbázisban](https://support.microsoft.com) a gyakori technikai problémák megoldásait.
* Keressen technikai kérdéseket és válaszokat a közösségtől, vagy tegye fel saját kérdését az [Azure Active Directory q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html).
* Lépjen kapcsolatba a Microsoft szakemberével az [Azure multi-factor authentication server támogatásán](https://support.microsoft.com/oas/default.aspx?prid=14947)keresztül. Amikor kapcsolatba lép velünk, hasznos, ha a lehető legtöbb információt meg tudja mondani a problémáról. A megadott információk közé tartozik az oldal, ahol a hibát látta, a konkrét hibakód, az adott munkamenet-azonosító és a hibát megkezdő felhasználó azonosítója.
* Ha Ön régi PhoneFactor-ügyfél, és kérdései vannak, vagy segítségre [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) van szüksége a jelszó alaphelyzetbe állításához, használja az e-mail címet egy támogatási eset megnyitásához.
