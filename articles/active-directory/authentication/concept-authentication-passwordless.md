---
title: Az Azure Active Directory beállításának jelentkezzen be (előzetes verzió)
description: Beállításának bejelentkezhet az Azure AD-bA FIDO2 biztonsági hitelesítő adatok vagy a Microsoft Authenticator alkalmazás (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d80b359be0a6249327ba1ba1d51ffbc330bb073
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712084"
---
# <a name="what-is-passwordless"></a>Mi a beállításának?

Többtényezős hitelesítés (MFA) rejlő lehetőségeket a szervezet biztonságának, de a felhasználók kapnak kivételkód felett, hogy meg kellene jegyezni a jelszavukat a további réteggel. Beállításának hitelesítési módszereket kényelmesebb, mivel a jelszót eltávolítva, van valami plusz valamit az Ön vagy a felhasználó által ismert információ helyére.

|   | Hiba van | Valami, vagy tudja |
| --- | --- | --- |
| Jelszó nélküli | Telefonos vagy a biztonsági kulcs | Biometrikus adatokat vagy PIN-kód |

A rendszer ismeri fel, hogy minden egyes szervezet rendelkezik-e a különböző igények, esetén, a hitelesítés. A Microsoft jelenleg kínál a Windows Hello, a Premier szintű beállításának élmény a Windows-számítógépen. Új hitelesítő adatokat a beállításának termékcsalád lehetőséget teszünk: A Microsoft Authenticator alkalmazás és a FIDO2 biztonsági kulcsok.

## <a name="microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás

Lehetővé teszi az alkalmazott phone válik egy beállításának hitelesítési módszert. Előfordulhat, hogy már használni a Microsoft Authenticator alkalmazás egy kényelmes a multi-factor authentication lehetőségként jelszó mellett. De most egy beállításának lehetőséget.

Kapcsolódó bármely iOS vagy Android rendszerű telefon erős, beállításának hitelesítő adatokat, jelentkezzen be a bármilyen platformra vagy a böngésző telefonjára értesítés első. az telefont cserélhet a képernyőn megjelenő számnak megfelelő, majd a saját biometrikai () használatával a felhasználók által Touch vagy arcfelismerési) vagy a PIN-kód megerősítése.

## <a name="fido2-security-keys"></a>FIDO2 biztonsági kulcsok

FIDO2 biztonsági kulcsai, amelyek a bármely méretformátumhoz unphishable szabványokon alapuló beállításának hitelesítési módszert. Gyors identitás Online (FIDO) nyílt szabvány, amely beállításának hitelesítési. Lehetővé teszi a felhasználó és vállalat kihasználhatja a standard jelentkezzen be egy felhasználónevet vagy jelszót használ egy külső kulcs vagy egy eszköz épített platform kulcs nélkül erőforrásaikat.

Nyilvános előzetes verzió az alkalmazottak külső biztonsági kulcsok használatával jelentkezzen be a saját Azure Active Directory csatlakoztatott Windows 10-es gépek (verzió 1809 vagy újabb rendszert futtató), és lekérése a felhőalapú erőforrások be egyszeri bejelentkezés. Akkor is bejelentkezhet a támogatott böngészők.

Áll számos FIDO2 a FIDO Alliance által hitelesített kulcsok, amelyek a Microsoft megköveteli a maximális biztonság és a legjobb élmény biztosításához a szállító által végrehajtandó FIDO2 CTAP specifikáció néhány opcionális bővítmények.

A biztonsági kulcs **kell** megvalósítása a következő szolgáltatásokat és a Microsoft-kompatibilisek a FIDO2 CTAP protokoll bővítményei:

| # | Szolgáltatás / a bővítmény megbízható | Miért van erre szükség? |
| --- | --- | --- |
| 1 | Rezidens kulcs | Ez a funkció lehetővé teszi, hogy a biztonsági kulcsa a hordozható, ahol a biztonsági kulcsot tárolja a hitelesítő adatok. |
| 2 | Ügyfél PIN-kód | Ez a funkció lehetővé teszi egy második tényező a hitelesítő adatok védelmét, és a biztonsági kulcsok, amelyek nem rendelkeznek felhasználói felületet vonatkozik. |
| 3 | HMAC-secret | Ez a bővítmény biztosítja, bejelentkezhet az eszköz offline vagy repülési üzemmód esetén. |
| 4 | Több fiók RP kiszolgálónként | Ez a funkció biztosítja, mint például a Microsoft Account és az Azure Active Directory több szolgáltatás is használhatja ugyanazt a biztonsági kulcsot. |

A következő szolgáltatók FIDO2 biztonsági kulcsait, amely kompatibilis a paswordless élményét ismert különféle méretformátumú kínálnak. A Microsoft javasolja ezen kulcsok biztonsági tulajdonságainak kiértékelése a szállító, valamint a FIDO Alliance szolgálatával.

| Szolgáltató | Kapcsolattartó |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

Ha egy szállító, és szeretné beolvasni az eszköz ezen a listán, lépjen kapcsolatba a [ Fido2Request@Microsoft.com ](mailto:Fido2Request@Microsoft.com).

FIDO2 biztonsági kulcsokat a vállalatok számára, illetve rendkívül bizalmas biztonsági forgatókönyvek vagy alkalmazottak, akik nem hajlandó és használhatják a telefonját második tényezőként remek választás.

## <a name="what-scenarios-work-with-the-preview"></a>Milyen esetekben használható az előzetes verzióra?

1. A rendszergazdák beállításának hitelesítési módszerek engedélyezheti a bérlő számára
1. A rendszergazdák az összes felhasználóknak vagy minden módszer esetén a bérlőn belüli felhasználók/csoportok kiválasztása
1. A végfelhasználók regisztrálhat és azok fiókportálon ezen beállításának hitelesítési módszerek kezelése
1. A végfelhasználók bejelentkezhet, ezeket beállításának hitelesítési módszerek
   1. A Microsoft Authenticator alkalmazás: Lesz mindegyik forgatókönyv, ahol az Azure AD-hitelesítést használ, a munkahelyi minden böngésző, beleértve során a Windows 10-es ki beállítása (OOBE) mezőbe, és az integrált mobilalkalmazásokban bármely operációs rendszerben.
   1. Biztonsági kulcsok: A zárolási képernyőn 1809 vagy újabb verziójú Windows 10-es és a webes a támogatott böngészők, például a Microsoft Edge fog működni.

## <a name="next-steps"></a>További lépések

[A szervezet beállításának beállítások engedélyezése](howto-authentication-passwordless-enable.md)

### <a name="external-links"></a>Külső hivatkozás

[FIDO Alliance](https://fidoalliance.org/)

[FIDO2 CTAP specifikáció](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
