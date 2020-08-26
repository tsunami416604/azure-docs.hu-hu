---
title: Felügyelt hitelesítés az Androidban | Azure
titlesuffix: Microsoft identity platform
description: A felügyelt hitelesítés & a Microsoft Identity platform Android rendszerre való hitelesítésének áttekintése
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2020
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: 9042318d29b9a7fc8c2064bdf845d6f0d5a4f3e8
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853856"
---
# <a name="brokered-authentication-in-android"></a>Felügyelt hitelesítés az Androidban

A Microsoft hitelesítési közvetítői közül az egyiket kell használnia az eszközre érvényes egyszeri bejelentkezéshez (SSO) való részvételhez és a szervezeti feltételes hozzáférési szabályzatok teljesítéséhez. A közvetítővel való integráció a következő előnyöket nyújtja:

- Eszköz egyszeri bejelentkezés
- Feltételes hozzáférés a következőhöz:
  - Intune-alkalmazásvédelem
  - Eszköz regisztrálása (Workplace Join)
  - Mobileszköz-kezelés
- Eszközre kiterjedő fiókok felügyelete
  -  az Android AccountManager & Fiókbeállítások
  - "Munkahelyi fiók" – egyéni fióktípus

Androidon a Microsoft Authentication Broker a [Microsoft Authenticator app](https://play.google.com/store/apps/details?id=com.azure.authenticator) és a [Intune céges portál](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) részét képező összetevő.

> [!TIP]
> Egyszerre csak egy olyan alkalmazás lesz aktív, amely a közvetítőt üzemelteti. A brókerként aktív alkalmazás a telepítési sorrend alapján van meghatározva az eszközön. Az első telepítendő vagy az eszközön lévő utolsó az aktív közvetítő lesz.

Az alábbi ábra az alkalmazás, a Microsoft Authentication Library (MSAL) és a Microsoft hitelesítési közvetítői közötti kapcsolatot mutatja be.

![A Broker üzembe helyezési diagramja](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Brókert futtató alkalmazások telepítése

A brókerek által üzemeltetett alkalmazások az alkalmazás-áruházból (jellemzően Google Play Áruház) telepíthetők az eszköz tulajdonosával. Egyes API-k (erőforrások) azonban olyan feltételes hozzáférési szabályzatok által védettek, amelyekhez az eszközök szükségesek:

- Regisztrálva (munkahelyhez csatlakoztatva) és/vagy
- Regisztrálva van az Eszközkezelőben vagy
- Regisztrálva van Intune App Protection

Ha egy eszközön még nincs telepítve Broker-alkalmazás, a MSAL arra utasítja a felhasználót, hogy telepítsen egyet, amint az alkalmazás egy jogkivonat interaktív lekérdezését kísérli meg. Ezután az alkalmazásnak el kell végeznie a felhasználónak a lépéseket, hogy az eszköz megfeleljen a szükséges házirendnek.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>A Broker telepítésének és eltávolításának hatásai

### <a name="when-a-broker-is-installed"></a>Ügynök telepítésekor

Ha egy ügynök telepítve van egy eszközön, az összes további interaktív jogkivonat-kérést (hívásokat `acquireToken()` ) a közvetítő kezeli, nem pedig helyileg a MSAL. A MSAL számára korábban elérhető SSO-állapotok nem érhetők el a közvetítő számára. Ennek eredményeképpen a felhasználónak újra hitelesítenie kell magát, vagy ki kell választania egy fiókot az eszközön ismert fiókok meglévő listájából.

A Broker telepítése nem igényli, hogy a felhasználó újra bejelentkezzen. `MsalUiRequiredException`A következő kérelem csak akkor jelenik meg a közvetítőn, ha a felhasználónak meg kell oldania. `MsalUiRequiredException` több okból is kidobják, és interaktív módon kell feloldani. Ezek gyakori okai:

- A felhasználó megváltoztatta a fiókhoz társított jelszót.
- A felhasználó fiókja már nem felel meg a feltételes hozzáférési szabályzatnak.
- A felhasználó visszavonta az alkalmazásnak a fiókjához való társításához szükséges beleegyezését.

### <a name="when-a-broker-is-uninstalled"></a>Ügynök eltávolításakor

Ha a rendszer csak egy Broker-üzemeltető alkalmazást telepít, és a rendszer eltávolítja, akkor a felhasználónak újra be kell jelentkeznie. Az aktív közvetítő eltávolítása eltávolítja a fiókot és a hozzá tartozó jogkivonatokat az eszközről.

Ha a Intune Céges portál telepítve van, és az aktív közvetítőként működik, és Microsoft Authenticator is telepítve van, akkor ha a Intune Céges portál (Active Broker) el van távolítva, a felhasználónak újra be kell jelentkeznie. Ha ismét bejelentkeznek, a Microsoft Authenticator alkalmazás lesz az aktív közvetítő.

## <a name="integrating-with-a-broker"></a>Integráció egy közvetítővel

### <a name="generating-a-redirect-uri-for-a-broker"></a>Átirányítási URI generálása egy bróker számára

Regisztrálnia kell egy átirányítási URI-t, amely kompatibilis a közvetítővel. A közvetítő átirányítási URI-ja tartalmaznia kell az alkalmazás csomagjának nevét, valamint az alkalmazás aláírásának Base64 kódolású ábrázolását.

Az átirányítási URI formátuma: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Generálja Base64 URL-kódolású aláírását az alkalmazás aláíró kulcsaival. Íme néhány példa a hibakeresési aláírási kulcsokat használó parancsokra:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Az alkalmazás aláírásával kapcsolatos információkért tekintse meg az [alkalmazás aláírása](https://developer.android.com/studio/publish/app-signing) című témakört.

> [!IMPORTANT]
> Használja az éles aláírási kulcsot az alkalmazás éles verziójához.

### <a name="configure-msal-to-use-a-broker"></a>MSAL konfigurálása közvetítő használatára

Ha közvetítőt szeretne használni az alkalmazásban, igazolnia kell, hogy konfigurálta a közvetítő átirányítását. Tegyük fel például, hogy a közvetítő által engedélyezett átirányítási URI-t is tartalmazza, és azt is jelzi, hogy regisztrálta a következőt a MSAL konfigurációs fájljában:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> Az új Azure Portal alkalmazás regisztrációs FELÜLETe segít a közvetítő átirányítási URI létrehozásában. Ha a régebbi felhasználói felületen regisztrálta az alkalmazást, vagy a Microsoft-alkalmazás regisztrációs portálján használta, előfordulhat, hogy az átirányítási URI-t kell létrehoznia, és manuálisan kell frissítenie az átirányítási URI-k listáját a portálon.

### <a name="broker-related-exceptions"></a>Ügynökkel kapcsolatos kivételek

A MSAL kétféle módon kommunikál a közvetítővel:

- Ügynökhöz kötött szolgáltatás
- Androidos AccountManager

A MSAL először a Broker Bound szolgáltatást használja, mert a szolgáltatás meghívásakor nincs szükség Android-engedélyekre. Ha a kötött szolgáltatás kötése meghiúsul, a MSAL az Android AccountManager API-t fogja használni. A MSAL csak akkor teszi ezt, ha az alkalmazás már megkapta az `"READ_CONTACTS"` engedélyt.

Ha `MsalClientException` hibakódot kap `"BROKER_BIND_FAILURE"` , két lehetőség közül választhat:

- Kérje meg a felhasználót, hogy tiltsa le az Microsoft Authenticator alkalmazás és a Intune Céges portál energiagazdálkodásának optimalizálását.
- Kérje meg a felhasználót, hogy adja meg az `"READ_CONTACTS"` engedélyt

## <a name="verifying-broker-integration"></a>A Broker integrációjának ellenőrzése

Előfordulhat, hogy nem azonnal egyértelmű, hogy a közvetítő integrációja működik, de a következő lépésekkel ellenőrizheti:

1. Az Android-eszközön végezzen el egy kérelmet a közvetítő használatával.
1. Az Android-eszköz beállításai között keresse meg a által hitelesített fióknak megfelelő újonnan létrehozott fiókot. A fióknak *munkahelyi fiók*típusúnak kell lennie.

Ha meg szeretné ismételni a tesztet, eltávolíthatja a fiókot a beállítások közül.
