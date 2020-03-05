---
title: Jelszó nélküli bejelentkezés Azure Active Directory (előzetes verzió)
description: További tudnivalók a jelszó nélküli bejelentkezés lehetőségeiről Azure Active Directory FIDO2 biztonsági kulcsok vagy a Microsoft Authenticator alkalmazás használatával
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffe3d359dffea3278337de00bea42e0b959e10f6
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269516"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>A Azure Active Directory jelszóval nem rendelkező hitelesítési beállításai

A többtényezős hitelesítés (MFA) egy nagyszerű módszer a szervezet biztonságossá tételére, de a felhasználók gyakran frusztráltak a további biztonsági réteggel szemben, hogy emlékezzenek a jelszavára. A jelszóval nem rendelkező hitelesítési módszerek sokkal kényelmesebbek, mert a jelszó el lett távolítva, és lecserélve valamire, vagy valamire, amit tud.

|   | Valamilyen dolog | Amit Ön vagy ismer |
| --- | --- | --- |
| Jelszó nélküli | Windows 10-es eszköz, telefon vagy biztonsági kulcs | Biometrikus vagy PIN-kód |

A hitelesítéshez minden szervezetnek eltérő igényeire van szüksége. A Microsoft a következő három jelszavas hitelesítési lehetőséget kínálja:

- Vállalati Windows Hello
- A Microsoft Authenticator alkalmazás
- FIDO2 biztonsági kulcsok

![Hitelesítés: biztonság és kényelem](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Vállalati Windows Hello

A vállalati Windows Hello ideális olyan információkkal dolgozó munkatársak számára, akik saját kijelölt Windows-számítógéppel rendelkeznek. A biometrikus és PIN-kód közvetlenül a felhasználó SZÁMÍTÓGÉPéhez van kötve, ami megakadályozza a hozzáférést a tulajdonostól eltérő személyek számára. A nyilvános kulcsokra épülő infrastruktúrával (PKI) való integrációval és az egyszeri bejelentkezés (SSO) beépített támogatásával a Windows Hello for Business kényelmes módszert kínál a vállalati erőforrások zökkenőmentes elérésére a helyszínen és a felhőben.

A vállalati Windows Hello [tervezési útmutató](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) segítségével döntéseket hozhat a vállalati Windows Hello üzembe helyezésének típusáról és a szükséges lehetőségekről.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator alkalmazás

Annak engedélyezése, hogy az alkalmazott telefonja jelszavas hitelesítésen alapuló hitelesítési módszer legyen. Előfordulhat, hogy a jelszó mellett már használja a Microsoft Authenticator alkalmazást kényelmes multi-Factor Authentication beállításként. A hitelesítő alkalmazást jelszóval nem rendelkező megoldásként is használhatja.

![Bejelentkezés a Microsoft Edge-be a Microsoft Authenticator alkalmazással](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

A hitelesítő alkalmazás minden iOS-vagy Android-telefont erős, jelszóval nem rendelkező hitelesítő adatba kapcsol. A felhasználók bármilyen platformra vagy böngészőbe bejelentkezhetnek, ha értesítést küldenek a telefonjára, és a képernyőn megjelenő számot a telefonján megjelenő telefonszámra, majd a biometrikus (érintés vagy arc) vagy a PIN-kód használatával megerősítik.

## <a name="fido2-security-keys"></a>FIDO2 biztonsági kulcsok

A FIDO2 biztonsági kulcsai egy nem adattípusra épülő, szabványos jelszavas hitelesítési módszer, amely bármilyen típusú tényezőt tartalmazhat. A gyors identitású online (pont) egy nyílt szabvány a jelszó nélküli hitelesítéshez. A parancs lehetővé teszi, hogy a felhasználók és a szervezetek a standard használatával bejelentkezzenek az erőforrásaik számára Felhasználónév vagy jelszó nélkül, egy külső biztonsági kulccsal vagy egy eszközbe épített platform-kulccsal.

A nyilvános előzetes verzióban az alkalmazottak a biztonsági kulcsok használatával jelentkezhetnek be az Azure AD-be vagy hibrid Azure AD-hez csatlakoztatott Windows 10-es eszközökre, és egyszeri bejelentkezést kapnak a felhőbe és a helyszíni erőforrásokhoz. A felhasználók a támogatott böngészőkbe is bejelentkezhetnek. A FIDO2 biztonsági kulcsai nagyszerű lehetőséget biztosítanak olyan nagyvállalatok számára, akik nagyon érzékenyek a biztonságra, vagy olyan forgatókönyvekkel vagy alkalmazottakkal rendelkeznek, akik nem hajlandók vagy nem tudják használni a telefont második tényezőként.

![Bejelentkezés a Microsoft Edge-be egy biztonsági kulccsal](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Noha a FIDO2 által hitelesített kulcsok sok kulcsot használnak, a Microsoft a FIDO2 ügyfél és a hitelesítő protokoll (CTAP) specifikációjának bizonyos opcionális kiterjesztését igényli, amely biztosítja a maximális biztonságot és a legjobb élmény.

A biztonsági kulcsnak a következő szolgáltatásokat és bővítményeket **kell** megvalósítania a FIDO2 CTAP-protokollból, hogy a Microsoft-kompatibilis legyen:

| # | Szolgáltatás/bővítmény megbízhatósága | Miért szükséges a funkció vagy a bővítmény? |
| --- | --- | --- |
| 1 | Rezidens kulcs | Ez a funkció lehetővé teszi, hogy a biztonsági kulcs hordozható legyen, ahol a hitelesítő adatokat a biztonsági kulcs tárolja. |
| 2 | Ügyfél PIN-kódja | Ez a funkció lehetővé teszi, hogy a hitelesítő adatait egy második tényezővel megvédje, és azokra a biztonsági kulcsokra vonatkozzon, amelyek nem rendelkeznek felhasználói felülettel. |
| 3 | HMAC – titkos kód | Ez a bővítmény biztosítja, hogy bejelentkezzen az eszközre, amikor az offline vagy a repülőgép üzemmódban van. |
| 4 | Több fiók/RP | Ez a funkció biztosítja, hogy ugyanazt a biztonsági kulcsot használja több szolgáltatáshoz, például a Microsoft-fiókhoz és a Azure Active Directory. |

A következő szolgáltatók olyan FIDO2 biztonsági kulcsokat kínálnak, amelyekről ismert, hogy kompatibilisek a jelszóval nem rendelkező felülettel. Javasoljuk, hogy a kulcsok biztonsági tulajdonságainak kiértékeléséhez vegye fel a kapcsolatot a gyártóval, valamint a következőt:.

| Szolgáltató | Kapcsolattartó |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID fájlok | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> Ha NFC-alapú biztonsági kulcsok megvásárlását és megtervezését tervezi, akkor a biztonsági kulcshoz támogatott NFC-olvasóra van szükség. Az NFC-olvasó nem Azure-követelmény vagy-korlátozás. A támogatott NFC-olvasók listáját az NFC-alapú biztonsági kulcs gyártójánál találja.

Ha Ön szállító, és szeretné lekérni az eszközt a támogatott eszközök listáján, forduljon a [Fido2Request@Microsoft.comhoz ](mailto:Fido2Request@Microsoft.com).

## <a name="what-scenarios-work-with-the-preview"></a>Milyen forgatókönyvek működnek az előzetes verzióban?

- A rendszergazdák engedélyezhetik a jelszóval nem rendelkező hitelesítési módszereket a bérlők számára
- A rendszergazdák az összes felhasználót megcélozhatja, vagy kiválaszthatják a bérlőn belüli felhasználókat és csoportokat az egyes módszereknél
- A végfelhasználók a fiók-portálon regisztrálhatják és kezelhetik ezeket a jelszó nélküli hitelesítési módszereket
- A végfelhasználók ezekkel a jelszó nélküli hitelesítési módszerekkel jelentkezhetnek be
   - Microsoft Authenticator alkalmazás: olyan helyzetekben működik, ahol az Azure AD-hitelesítés használatban van, beleértve az összes böngészőt, a Windows 10-es (OOBE) beállítása során, valamint az integrált Mobile apps bármely operációs rendszeren.
   - Biztonsági kulcsok: a Windows 10 és a web zárolási képernyőjén használható böngészők, például a Microsoft Edge (örökölt és új Edge).

## <a name="next-steps"></a>Következő lépések

[A FIDO2 biztonsági kulcs passwordlesss beállításainak engedélyezése a szervezetben](howto-authentication-passwordless-security-key.md)

[Telefonos jelszó-megadási lehetőségek engedélyezése a szervezetben](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Külső hivatkozások

[A-Szövetség](https://fidoalliance.org/)

[FIDO2 CTAP-specifikáció](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
