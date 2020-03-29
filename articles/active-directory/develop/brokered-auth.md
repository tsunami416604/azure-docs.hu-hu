---
title: Közvetített hitelesítés Androidban | Azure
titlesuffix: Microsoft identity platform
description: Az Android ra vonatkozó közvetített hitelesítés & engedélyezés áttekintése a Microsoft identitásplatformon
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: a734589178438fd65d9a2d156fd91fc82807f578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697897"
---
# <a name="brokered-authentication-in-android"></a>Közvetített hitelesítés Androidon

A Microsoft egyik hitelesítési brókerét kell használnia az eszközszintű egyszeri bejelentkezésben (SSO) való részvételhez és a szervezeti feltételes hozzáférési házirendek teljesítéséhez. A brókerrel való integráció a következő előnyöket nyújtja:

- Egyszeri bejelentkezés az eszközön
- Feltételes hozzáférés:
  - Intune-alkalmazásvédelem
  - Eszközregisztráció (munkahelyi csatlakozás)
  - Mobileszköz-kezelés
- Az egész eszközre kiterjedő fiókkezelés
  -  androidos AccountManager & fiókbeállításokon keresztül
  - "Munkafiók" - egyéni fióktípus

Androidon a Microsoft Authentication Broker a Microsoft [Authenticator Alkalmazás](https://play.google.com/store/apps/details?id=com.azure.authenticator) és az [Intune vállalati portál](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) részét képező összetevő

> [!TIP]
> Csak egy alkalmazás, amely otthont ad a bróker lesz aktív, mint a bróker egy időben. Melyik alkalmazás aktív, mint egy bróker határozza meg a telepítési sorrendben az eszközön. Az első telepítendő, vagy az utolsó jelen a készüléken, lesz az aktív bróker.

Az alábbi ábra az alkalmazás, a Microsoft hitelesítési könyvtár (MSAL) és a Microsoft hitelesítési brókerei közötti kapcsolatot mutatja be.

![Bróker telepítési diagramja](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Brókert fogadó alkalmazások telepítése

A bróker-hosting alkalmazásokat az eszköz tulajdonosa bármikor telepítheti az alkalmazásboltból (általában a Google Play Áruházból). Egyes API-kat (erőforrásokat) azonban feltételes hozzáférési házirendek védenek, amelyekhez az eszközöknek a következőknek kell lenniük:

- Regisztrált (munkahelyhez csatlakozott) és/vagy
- Regisztrált az Eszközkezelés szolgáltatásba vagy
- Regisztrált az Intune alkalmazásvédelmében

Ha egy eszköz még nem rendelkezik egy bróker alkalmazás telepítve, MSAL utasítja a felhasználót, hogy telepítse az egyik, amint az alkalmazás megpróbálja beszerezni a token interaktívan. Az alkalmazásnak ezután végig kell vezetnie a felhasználót a szükséges szabályzatnak való megfeleléshez szükséges lépéseken.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>A bróker telepítésének és eltávolításának hatásai

### <a name="when-a-broker-is-installed"></a>Amikor egy bróker telepítve van

Amikor egy bróker telepítve van egy eszközön, az `acquireToken()`összes további interaktív token kérelmek (hívások) kezeli a bróker, nem pedig helyileg Az MSAL. Az MSAL számára korábban elérhető SSO-állapot nem érhető el a bróker számára. Ennek eredményeképpen a felhasználónak újra hitelesítenie kell magát, vagy ki kell választania egy fiókot az eszköz által ismert fiókok meglévő listájából.

A közvetítő telepítése nem követeli meg a felhasználótól, hogy újra jelentkezzen be. Csak akkor, ha a `MsalUiRequiredException` felhasználónak meg kell oldania a következő kérést, hogy a bróker. `MsalUiRequiredException`több okból is kifolyólag, és interaktív módon kell megoldani. Íme néhány gyakori ok:

- A felhasználó megváltoztatta a fiókjához társított jelszót.
- A felhasználó fiókja már nem felel meg a feltételes hozzáférési szabályzatnak.
- A felhasználó visszavonta a beleegyezését ahhoz, hogy az alkalmazás társítva legyen a fiókjával.

### <a name="when-a-broker-is-uninstalled"></a>Amikor egy brókert eltávolítanak

Ha csak egy bróker hosting alkalmazás van telepítve, és eltávolítjuk, akkor a felhasználónak újra be kell jelentkeznie. Az aktív bróker eltávolítása eltávolítja a fiókot és a kapcsolódó jogkivonatokat az eszközről.

Ha az Intune Céges portál telepítve van, és aktív közvetítőként működik, és a Microsoft Authenticator is telepítve van, akkor ha az Intune Vállalati portál (aktív közvetítő) eltávolítása a felhasználónak újra be kell jelentkeznie. Miután újra bejelentkeztek, a Microsoft Authenticator alkalmazás lesz az aktív bróker.

## <a name="integrating-with-a-broker"></a>Integrálás egy brókerrel

### <a name="generating-a-redirect-uri-for-a-broker"></a>Átirányítási URI létrehozása közvetítő számára

Regisztrálnia kell egy átirányítási URI-t, amely kompatibilis a brókerrel. A közvetítő átirányítási URI-jának tartalmaznia kell az alkalmazás csomagnevét, valamint az alkalmazás aláírásának base64 kódolású ábrázolását.

Az átirányítási URI formátuma:`msauth://<yourpackagename>/<base64urlencodedsignature>`

Hozza létre a Base64 URL-re kódolt aláírását az alkalmazás aláírási kulcsaival. Íme néhány példa parancs, amely a hibakeresési aláíró kulcsokat használja:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Az alkalmazás aláírásáról az [Alkalmazás](https://developer.android.com/studio/publish/app-signing) aláírása című témakörben talál.

> [!IMPORTANT]
> Használja az éles aláírási kulcsot az alkalmazás éles verziójához.

### <a name="configure-msal-to-use-a-broker"></a>Konfigurálja az MSAL-t bróker használatára

Ahhoz, hogy egy bróker az alkalmazásban, meg kell tanúsítania, hogy konfigurálta a bróker átirányítás. Tüntesse fel például a bróker által engedélyezett átirányítási URI-t – és jelezze, hogy regisztrálta –, és az MSAL konfigurációs fájlba beillesztve a következőket tartalmazza:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> Az új Azure Portal alkalmazás regisztrációs felhasználói felülete segít a közvetítő átirányítási URI létrehozásában. Ha a régebbi felhasználói felülethasználatával regisztrálta az alkalmazást, vagy ezt a Microsoft alkalmazásregisztrációs portálhasználatával tette, előfordulhat, hogy létre kell hoznia az átirányítási URI-t, és manuálisan frissítenie kell az átirányítási URI-k listáját a portálon.

### <a name="broker-related-exceptions"></a>Brókerrel kapcsolatos kivételek

Az MSAL kétféleképpen kommunikál a brókerrel:

- Közvetítőhöz kötött szolgáltatás
- Android-fiókkezelő

Az MSAL először a közvetítőhöz kötött szolgáltatást használja, mert a szolgáltatás hívásához nincs szükség Android-engedélyekre. Ha a kötött szolgáltatáshoz való kötés sikertelen, az MSAL az Android AccountManager API-t fogja használni. Az MSAL csak akkor teszi ezt, `"READ_CONTACTS"` ha az alkalmazás már megkapta az engedélyt.

Ha hibakódot `MsalClientException` `"BROKER_BIND_FAILURE"`kap, akkor két lehetőség van:

- Kérje meg a felhasználót, hogy tiltsa le az energiaoptimalizálást a Microsoft Authenticator alkalmazás és az Intune vállalati portálja számára.
- Kérje meg a `"READ_CONTACTS"` felhasználót, hogy adja meg az engedélyt
