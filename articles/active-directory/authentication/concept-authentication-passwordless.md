---
title: Az Azure Active Directory jelszó nélküli bejelentkezése (előzetes verzió)
description: Ismerje meg az Azure Active Directoryba való jelszó nélküli bejelentkezés lehetőségeit a FIDO2 biztonsági kulcsokkal vagy a Microsoft Authenticator alkalmazással
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07ba38a5d7e8e8a89ba122efb1734c1f13a94d48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332191"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Jelszó nélküli hitelesítési lehetőségek az Azure Active Directoryhoz

A többtényezős hitelesítés (MFA) nagyszerű módja a szervezet védelmének, de a felhasználók gyakran frusztrálják a további biztonsági réteget, ráadásul meg kell emlékezniük a jelszavukra. Jelszó nélküli hitelesítési módszerek sokkal kényelmesebb, mert a jelszót eltávolítjuk, és helyébe valami van, plusz valami van, vagy valami, amit tud.

|   | Valami, ami van | Valami, ami vagy tudod |
| --- | --- | --- |
| Jelszó nélküli | Windows 10-es eszköz, telefon vagy biztonsági kulcs | Biometrikus vagy PIN-kód |

Minden szervezetnek különböző igényei vannak a hitelesítéssel kapcsolatban. A Microsoft a következő három jelszó nélküli hitelesítési lehetőséget kínálja, amelyek integrálhatók az Azure Active Directoryba (Azure AD):

- Vállalati Windows Hello
- A Microsoft Authenticator alkalmazás
- FIDO2 biztonsági kulcsok

![Hitelesítés: Biztonság kontra kényelem](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Vállalati Windows Hello

A Windows Hello for Business ideális olyan informatikai dolgozók számára, akik saját Windows pc-vel rendelkeznek. A biometrikus és PIN-kód közvetlenül a felhasználó számítógépéhez van kötve, ami megakadályozza a hozzáférést a tulajdonoson kívül mástól. A nyilvános kulcsú infrastruktúra (PKI) integrációjával és az egyszeri bejelentkezés (SSO) beépített támogatásával a Windows Hello for Business kényelmes módszert kínál a vállalati erőforrások zökkenőmentes eléréséhez a helyszínen és a felhőben.

![Példa a Windows Hello vállalati verzióval való bejelentkezésre](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

A következő lépések bemutatják, hogyan működik a bejelentkezési folyamat az Azure Active Directoryval.

![A Windows Hello vállalati verzióval való felhasználói bejelentkezés lépéseit ismerő diagram](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. A felhasználó biometrikus vagy PIN-kódos kézmozdulattal jelentkezik be a Windows rendszerbe. A kézmozdulat feloldja a Windows Hello for Business titkos kulcsát, és elküldi a felhőalapú hitelesítés biztonsági támogatási szolgáltatójának, a *felhőalapú hozzáférési pont szolgáltatónak.*
1. A felhőbeli HOZZÁFÉRÉSI-szolgáltató nonce-t kér az Azure AD-től.
1. Az Azure AD egy 5 percig érvényes nonce értéket ad vissza.
1. A felhőalapú HOZZÁFÉRÉSI-szolgáltatás-szolgáltató aláírja a nonce a felhasználó személyes kulcsa használatával, és visszaadja az aláírt nonce az Azure AD.
1. Az Azure AD érvényesíti az aláírt nonce a felhasználó biztonságosan regisztrált nyilvános kulcs a nonce aláírással. Az aláírás érvényesítése után az Azure AD majd érvényesíti a visszaküldött aláírt nonce. A nonce érvényesítésekor az Azure AD létrehoz egy elsődleges frissítési jogkivonatot (PRT) az eszköz átviteli kulcsára titkosított munkamenetkulccsal, és visszaadja azt a felhőalapú hozzáférési pont-szolgáltatónak.
1. A felhőalapú hozzáférési pont szolgáltató a titkosított PRT-t munkamenetkulccsal kapja meg. Az eszköz személyes átviteli kulcsával a felhőalapú hozzáférési pont szolgáltató visszafejti a munkamenetkulcsot, és az eszköz platformmegbízhatósági moduljával (TPM) védi a munkamenetkulcsot.
1. A felhőalapú hozzáférési pont szolgáltató sikeres hitelesítési választ ad vissza a Windows rendszernek. A felhasználó ezután anélkül férhet hozzá a Windows, valamint a felhőbeli és helyszíni alkalmazásokhoz, hogy újra hitelesítenie kellene magát.The user is then able to access Windows as well as cloud and on-premises applications without the need to authenticate again (SSO).

A Windows Hello vállalati verzió [tervezési útmutatója](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) segítségével meghozhatja a Windows Hello vállalati verzió telepítésének típusát és a figyelembe veendő lehetőségeket.

## <a name="microsoft-authenticator-app"></a>Microsoft Hitelesítő alkalmazás

Lehetővé teszi, hogy az alkalmazott telefonja jelszó nélküli hitelesítési módszerré váljon. Lehet, hogy már használja a Microsoft Authenticator App, mint egy kényelmes többtényezős hitelesítési lehetőség mellett egy jelszót. A Hitelesítő alkalmazást jelszó nélküli beállításként is használhatja.

![Bejelentkezés a Microsoft Edge-be a Microsoft Authenticator alkalmazással](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

A Hitelesítő alkalmazás minden iOS vagy Android telefont erős, jelszó nélküli hitelesítő adattá alakít. A felhasználók bármely platformra vagy böngészőbe bejelentkezhetnek, ha értesítést kapnak a telefonjukon, amely megfelel a képernyőn megjelenő számnak a telefonjukon lévőszámnak, majd a biometrikus (érintés vagy arc) vagy a PIN-kód segítségével megerősítik. A telepítés részleteiért olvassa el [A Microsoft Authenticator alkalmazás letöltése és telepítése](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) című témakört.

Az Authenticator alkalmazással történő jelszó nélküli hitelesítés a Windows Hello for Business alapvető mintájával megegyezik. Ez egy kicsit bonyolultabb, mivel a felhasználót azonosítani kell, hogy az Azure AD megtalálja a Microsoft Authenticator Alkalmazás használt verzióját:

![A Microsoft Authenticator alkalmazással való felhasználói bejelentkezés lépéseit ismertetni kívánt diagram](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. A felhasználó megadja a felhasználónevét.
1. Az Azure AD észleli, hogy a felhasználó erős hitelesítő adatokkal rendelkezik, és elindítja az erős hitelesítő adatok folyamata.
1. Az értesítést az Apple Push Notification Service (APNS) keresztül küldi el az alkalmazásnak iOS-eszközökön, vagy a Firebase Cloud Messaging (FCM) segítségével Android-eszközökön.
1. A felhasználó megkapja a leküldéses értesítést, és megnyitja az alkalmazást.
1. Az alkalmazás meghívja az Azure AD-t, és megkapja a jelenlétigazolási kihívást, és nonce.
1. A felhasználó a személyes kulcs feloldásához beírja a kihívást a biometrikus vagy PIN-kód megadásával.
1. The nonce is signed with the private key and sent back to Azure AD.
1. Az Azure AD nyilvános/titkos kulcs-érvényesítést hajt végre, és egy jogkivonatot ad vissza.

## <a name="fido2-security-keys"></a>FIDO2 biztonsági kulcsok

FiDO2 biztonsági kulcsok egy nem phishable szabványokon alapuló jelszó nélküli hitelesítési módszer, amely jöhet bármilyen formában tényező. A Fast Identity Online (FIDO) a jelszó nélküli hitelesítés nyílt szabványa. A FIDO lehetővé teszi a felhasználók és szervezetek számára, hogy a szabvány segítségével jelentkezzenek be az erőforrásaikba felhasználónév vagy jelszó nélkül egy külső biztonsági kulccsal vagy egy eszközbe épített platformkulccsal.

A nyilvános előzetes verzióhoz az alkalmazottak biztonsági kulcsok használatával bejelentkezhetnek az Azure AD-be vagy a hibrid Azure AD-hez csatlakozott Windows 10-es eszközökre, és egyszeri bejelentkezést kaphatnak a felhőbeli és helyszíni erőforrásaikba. A felhasználók a támogatott böngészőkbe is bejelentkezhetnek. FiDO2 biztonsági kulcsok egy nagyszerű lehetőség a vállalatok számára, akik nagyon biztonsági érzékeny, vagy forgatókönyvek vagy alkalmazottak, akik nem hajlandóak, vagy képes használni a telefont, mint a második tényező.

![Bejelentkezés a Microsoft Edge-be biztonsági kulccsal](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

A következő eljárást akkor használja, ha a felhasználó FIDO2 biztonsági kulccsal jelentkezik be:

![A FIDO2 biztonsági kulccsal való felhasználói bejelentkezés lépéseit ismertetni kívánt diagram](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. A felhasználó a FIDO2 biztonsági kulcsot csatlakoztatja a számítógépéhez.
2. A Windows észleli a FIDO2 biztonsági kulcsot.
3. A Windows hitelesítési kérelmet küld.
4. Az Azure AD küld vissza egy nonce.
5. A felhasználó befejezi a gesztust, hogy feloldja a FIDO2 biztonsági kulcs biztonságos enklávéjában tárolt személyes kulcsot.
6. A FIDO2 biztonsági kulcs aláírja a nonce a személyes kulcsot.
7. Az elsődleges frissítési jogkivonat (PRT) token kérelem aláírt nonce küldi az Azure AD.The primary refresh token (PRT) token request with signed nonce is sent to Azure AD.
8. Az Azure AD ellenőrzi az aláírt nonce a FIDO2 nyilvános kulcs használatával.
9. Az Azure AD visszaadja a PRT-t a helyszíni erőforrásokhoz való hozzáférés engedélyezéséhez.

Bár sok kulcs, amely FIDO2 által hitelesített FIDO Alliance, a Microsoft igényel néhány opcionális kiterjesztésea FIDO2 Client-to-Authenticator Protokoll (CTAP) specifikáció kell végrehajtani a gyártó, hogy biztosítsák a maximális biztonságot és a legjobb Tapasztalat.

A biztonsági **kulcsnak** a FIDO2 CTAP protokoll következő szolgáltatásait és bővítményeit kell megvalósítania ahhoz, hogy a Microsoft kompatibilis legyen:

| # | Szolgáltatás / Bővítmény megbízhatósága | Miért van szükség erre a funkcióra vagy bővítményre? |
| --- | --- | --- |
| 1 | Rezidens kulcs | Ez a szolgáltatás lehetővé teszi, hogy a biztonsági kulcs hordozható legyen, ahol a hitelesítő adatok a biztonsági kulcson tárolódnak. |
| 2 | Ügyfél pin | Ez a szolgáltatás lehetővé teszi a hitelesítő adatok védelmét egy második tényezővel, és olyan biztonsági kulcsokra vonatkozik, amelyek nem rendelkeznek felhasználói felülettel. |
| 3 | hmac-titkos | Ez a bővítmény biztosítja, hogy akkor jelentkezzen be az eszközre, amikor az nem működik, vagy repülőgép módban van. |
| 4 | Több fiók RP-nként | Ez a funkció biztosítja, hogy ugyanazt a biztonsági kulcsot több szolgáltatás, például a Microsoft-fiók és az Azure Active Directory között is használhatja. |

A következő szolgáltatók kínálnak FIDO2 biztonsági kulcsok különböző formában tényezők, amelyekről ismert, hogy kompatibilis a jelszó nélküli élményt. Javasoljuk, hogy értékelje a biztonsági tulajdonságait ezeka kulcsok kapcsolatba lépve a szállító, valamint a FIDO Szövetség.

| Szolgáltató | Kapcsolattartó |
| --- | --- |
| Yubico között | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| Hid | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Nyugalom | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Thales Csoport) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| A OneSpan Zrt. | [https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf](https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf) |
| IDmelon Technologies Kft. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> Ha NFC-alapú biztonsági kulcsokat vásárol és szeretne használni, a biztonsági kulcshoz támogatott NFC-olvasóra van szüksége. Az NFC-olvasó nem azure-követelmény vagy korlátozás. A támogatott NFC-olvasók listájáról érdeklődjön a forgalmazónál az NFC-alapú biztonsági kulcsról.

Ha Ön gyártó, és szeretné, hogy az eszköz a [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)támogatott eszközök listáját, forduljon a.

## <a name="what-scenarios-work-with-the-preview"></a>Milyen forgatókönyvek működnek az előzetes verzióval?

- A rendszergazdák jelszó nélküli hitelesítési módszereket engedélyezhetnek a bérlőjüknek
- A rendszergazdák megcélozhatják az összes felhasználót, vagy kiválaszthatják a bérlőn belüli felhasználókat/csoportokat az egyes módszerekhez
- A végfelhasználók regisztrálhatják és kezelhetik ezeket a jelszó nélküli hitelesítési módszereket a fiókportáljukon
- A végfelhasználók ezekkel a jelszó nélküli hitelesítési módszerekkel jelentkezhetnek be
   - Microsoft Authenticator App: Olyan esetekben működik, ahol az Azure AD-hitelesítést használják, beleértve az összes böngészőt, a Windows 10 Oobe (OOBE) beállítása során, valamint az integrált mobilalkalmazásokat bármely operációs rendszeren.
   - Biztonsági kulcsok: Dolgozzon a Windows 10 zárolási képernyőjén és a weben olyan támogatott böngészőkben, mint a Microsoft Edge (mind az örökölt, mind az új Edge).

## <a name="choose-a-passwordless-method"></a>Válasszon jelszó nélküli módszert

A három jelszó nélküli beállítás közötti választás a vállalat biztonsági, platform- és alkalmazáskövetelményeitől függ.

Íme néhány tényező, amelyet figyelembe kell vennie a Microsoft jelszó nélküli technológiájának kiválasztásakor:

||**Vállalati Windows Hello**|**Jelszó nélküli bejelentkezés a Microsoft Authenticator alkalmazással**|**FIDO2 biztonsági kulcsok**|
|:-|:-|:-|:-|
|**Előfeltétel**| Windows 10, 1809-es vagy újabb verzió<br>Azure Active Directory| A Microsoft Authenticator alkalmazás<br>Telefon (androidos és Android-alapú eszközök androidos 6.0 vagy újabb verziók at.)|Windows 10, 1809-es vagy újabb verzió<br>Azure Active Directory|
|**Mód**|Platform|Szoftverek|Hardver|
|**Rendszerek és eszközök**|Beépített platformmegbízhatósági modullal (TPM) rendelkező számítógép<br>PIN- és biometrikus felismerés |PIN- és biometrikus felismerés telefonon|FiDO2 biztonsági eszközök, amelyek microsoftos kompatibilisek|
|**Felhasználó felület**|Jelentkezzen be PIN-kóddal vagy biometrikus felismeréssel (arc-, írisz vagy ujjlenyomat) Windows-eszközökkel.<br>A Windows Hello-hitelesítés az eszközhöz van kötve; a felhasználónak szüksége van mind az eszközre, mind egy bejelentkezési összetevőre, például egy PIN-kódra vagy biometrikus tényezőre a vállalati erőforrások eléréséhez.|Jelentkezzen be mobiltelefonon ujjlenyomat-vizsgálattal, arc- vagy íriszfelismeréssel vagy PIN-kóddal.<br>A felhasználók bejelentkeznek a munkahelyi vagy személyes fiókba számítógépükről vagy mobiltelefonjukról.|Bejelentkezés FIDO2 biztonsági eszközzel (biometria, PIN-kód és NFC)<br>A felhasználó hozzáférhet a szervezet vezérlőialapján, és pin-kód, biometria alapján hitelesítheti magát olyan eszközök használatával, mint az USB biztonsági kulcsok és az NFC-kompatibilis intelligens kártyák, kulcsok vagy viselhető eszközök.|
|**Engedélyezett forgatókönyvek**| Jelszó nélküli felhasználói élmény a Windows-eszközzel.<br>Az eszközre és az alkalmazásokra való egyszeri bejelentkezésre alkalmas dedikált munkahelyi pc-re alkalmazható.|Jelszó nélküli bárhol megoldás segítségével mobiltelefon.<br>Bármilyen eszközről elérhető a munkahelyi vagy személyes alkalmazások az interneten.|Jelszó nélküli tapasztalat a biometrikus, PIN-kódot és NFC-t használó dolgozók számára.<br>A megosztott számítógépekre vonatkozik, és ahol a mobiltelefon nem járható út (például a help desk személyzete, a nyilvános kioszk vagy a kórházi csapat)|

Az alábbi táblázat segítségével választhatja ki, hogy melyik módszer támogatja a követelményeket és a felhasználókat.

|Persona|Forgatókönyv|Környezet|Jelszó nélküli technológia|
|:-|:-|:-|:-|
|**Felügyelet**|Biztonságos hozzáférés egy eszközhöz a felügyeleti feladatokhoz|Hozzárendelt Windows 10-eszköz|Windows Hello Vállalati verzió és/vagy FIDO2 biztonsági kulcs|
|**Felügyelet**|Felügyeleti feladatok nem Windows rendszerű eszközökön| Mobil vagy nem windowsos eszköz|Jelszó nélküli bejelentkezés a Microsoft Authenticator alkalmazással|
|**Információs dolgozó**|Termelékenységi munka|Hozzárendelt Windows 10-eszköz|Windows Hello Vállalati verzió és/vagy FIDO2 biztonsági kulcs|
|**Információs dolgozó**|Termelékenységi munka| Mobil vagy nem windowsos eszköz|Jelszó nélküli bejelentkezés a Microsoft Authenticator alkalmazással|
|**Frontline dolgozó**|Kioszkok gyárban, üzemben, kiskereskedelemben vagy adatbevitelben|Megosztott Windows 10-eszközök|FIDO2 biztonsági kulcsok|

## <a name="next-steps"></a>További lépések

[A FIDO2 biztonságikulcs jelszó nélküli beállításainak engedélyezése a szervezetben](howto-authentication-passwordless-security-key.md)

[Telefonalapú jelszó nélküli beállítások engedélyezése a szervezetben](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Külső hivatkozások

[FIDO Szövetség](https://fidoalliance.org/)

[FIDO2 CTAP specifikáció](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
