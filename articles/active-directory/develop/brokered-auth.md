---
title: Felügyelt hitelesítés az Androidban | Azure
titleSuffix: Microsoft identity platform
description: A felügyelt hitelesítés & a Microsoft Identity platform Android rendszerre való hitelesítésének áttekintése
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2020
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: 2bb48971e86c2b61742735020469865fa969bee3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258408"
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

Android rendszeren a Microsoft Authentication Broker a [Microsoft Authenticator app](https://play.google.com/store/apps/details?id=com.azure.authenticator) és a [Intune céges portál](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)részét képező összetevő.

Az alábbi ábra az alkalmazás, a Microsoft Authentication Library (MSAL) és a Microsoft hitelesítési közvetítői közötti kapcsolatot mutatja be.

![Ábra, amely bemutatja, hogyan kapcsolódik az alkalmazás a MSAL, a Broker-alkalmazásokhoz és az Android-fiók kezelőjéhez.](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Brókert futtató alkalmazások telepítése

A brókerek által üzemeltetett alkalmazások az alkalmazás-áruházból (jellemzően Google Play Áruház) telepíthetők az eszköz tulajdonosával. Egyes API-k (erőforrások) azonban olyan feltételes hozzáférési szabályzatok által védettek, amelyekhez az eszközök szükségesek:

- Regisztrálva (munkahelyhez csatlakoztatva) és/vagy
- Regisztrálva van az Eszközkezelőben vagy
- Regisztrálva van Intune App Protection

Ha egy eszközön még nincs telepítve Broker-alkalmazás, a MSAL arra utasítja a felhasználót, hogy telepítsen egyet, amint az alkalmazás egy jogkivonat interaktív lekérdezését kísérli meg. Ezután az alkalmazásnak el kell végeznie a felhasználónak a lépéseket, hogy az eszköz megfeleljen a szükséges házirendnek.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>A Broker telepítésének és eltávolításának hatásai

### <a name="when-a-broker-is-installed"></a>Ügynök telepítésekor

Ha egy ügynök telepítve van egy eszközön, az összes további interaktív jogkivonat-kérést (hívásokat `acquireToken()` ) a közvetítő kezeli, nem pedig helyileg a MSAL. A MSAL számára korábban elérhető SSO-állapotok nem érhetők el a közvetítő számára. Ennek eredményeképpen a felhasználónak újra hitelesítenie kell magát, vagy ki kell választania egy fiókot az eszközön ismert fiókok meglévő listájából.

A Broker telepítése nem igényli, hogy a felhasználó újra bejelentkezzen. `MsalUiRequiredException`A következő kérelem csak akkor jelenik meg a közvetítőn, ha a felhasználónak meg kell oldania. `MsalUiRequiredException` több okból is elvégezhető, és interaktív módon kell feloldani. Példa:

- A felhasználó megváltoztatta a fiókhoz társított jelszót.
- A felhasználó fiókja már nem felel meg a feltételes hozzáférési szabályzatnak.
- A felhasználó visszavonta az alkalmazásnak a fiókjához való társításához szükséges beleegyezését.

#### <a name="multiple-brokers"></a>Több közvetítő

Ha több közvetítő is telepítve van egy eszközön, akkor az elsőként telepített közvetítő mindig az aktív közvetítő. Egy eszközön csak egyetlen közvetítő lehet aktív.

### <a name="when-a-broker-is-uninstalled"></a>Ügynök eltávolításakor

Ha a rendszer csak egy Broker-üzemeltető alkalmazást telepít, és a rendszer eltávolítja, akkor a felhasználónak újra be kell jelentkeznie. Az aktív közvetítő eltávolítása eltávolítja a fiókot és a hozzá tartozó jogkivonatokat az eszközről.

Ha a Intune Céges portál telepítve van, és az aktív közvetítőként működik, és Microsoft Authenticator is telepítve van, akkor ha a Intune Céges portál (Active Broker) el van távolítva, a felhasználónak újra be kell jelentkeznie. Ha ismét bejelentkeznek, a Microsoft Authenticator alkalmazás lesz az aktív közvetítő.

## <a name="integrating-with-a-broker"></a>Integráció egy közvetítővel

### <a name="generating-a-redirect-uri-for-a-broker"></a>Átirányítási URI generálása egy bróker számára

Regisztrálnia kell egy átirányítási URI-t, amely kompatibilis a közvetítővel. A közvetítő átirányítási URI-ja tartalmaznia kell az alkalmazás csomagjának nevét és az alkalmazás aláírásának Base64 kódolású ábrázolását.

Az átirányítási URI formátuma: `msauth://<yourpackagename>/<base64urlencodedsignature>`

A [kulcstartó használatával Base64](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) -kódolású aláírási kivonatot hozhatja ki az alkalmazás aláíró kulcsaival, majd a Azure Portal használatával létrehozhatja az ÁTirányítási URI-t az adott kivonat használatával.

Linux és macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Miután létrehozta az aláírási kivonatot a (z) *eszközzel*, a Azure Portal használatával hozza létre az ÁTirányítási URI-t:

1. Jelentkezzen be a [Azure Portalba](https://protal.azure.com) , és válassza ki az Android-alkalmazást a **Alkalmazásregisztrációkban**.
1. Válassza **Authentication**  >  **a hitelesítés platform hozzáadása**  >  **Android**lehetőséget.
1. A megnyíló **Android-alkalmazás konfigurálása** panelen adja meg a korábban létrehozott **aláírási kivonatot** és a **csomag nevét**.
1. Válassza a **Konfigurálás** gombot.

A Azure Portal létrehozza az átirányítási URI-t, és megjeleníti azt az **Android konfigurációs** ablaktáblájának **átirányítási URI** -mezőjében.

Az alkalmazás aláírásával kapcsolatos további információkért lásd: az [alkalmazás aláírása](https://developer.android.com/studio/publish/app-signing) a Android Studio felhasználói útmutatóban.

> [!IMPORTANT]
> Használja az éles aláírási kulcsot az alkalmazás éles verziójához.

### <a name="configure-msal-to-use-a-broker"></a>MSAL konfigurálása közvetítő használatára

Ha közvetítőt szeretne használni az alkalmazásban, igazolnia kell, hogy konfigurálta a közvetítő átirányítását. Tegyük fel például, hogy a közvetítő által engedélyezett átirányítási URI-t is tartalmazza, és azt is jelzi, hogy regisztrálta azt – a MSAL konfigurációs fájljában a következő beállításokkal:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

### <a name="broker-related-exceptions"></a>Ügynökkel kapcsolatos kivételek

A MSAL kétféle módon kommunikál a közvetítővel:

- Ügynökhöz kötött szolgáltatás
- Androidos AccountManager

A MSAL először a közvetítő által kötött szolgáltatást használja, mert a szolgáltatás meghívásakor nincs szükség Android-engedélyekre. Ha a kötött szolgáltatás kötése meghiúsul, a MSAL az Android AccountManager API-t fogja használni. A MSAL csak akkor működik, ha az alkalmazás már megkapta az `"READ_CONTACTS"` engedélyt.

Ha `MsalClientException` hibakódot kap `"BROKER_BIND_FAILURE"` , két lehetőség közül választhat:

- Kérje meg a felhasználót, hogy tiltsa le az Microsoft Authenticator alkalmazás és a Intune Céges portál energiagazdálkodásának optimalizálását.
- Kérje meg a felhasználót, hogy adja meg az `"READ_CONTACTS"` engedélyt

## <a name="verifying-broker-integration"></a>A Broker integrációjának ellenőrzése

Előfordulhat, hogy nem azonnal egyértelmű, hogy a közvetítő integrációja működik, de a következő lépésekkel ellenőrizheti:

1. Az Android-eszközön végezzen el egy kérelmet a közvetítő használatával.
1. Az Android-eszköz beállításai között keresse meg a által hitelesített fióknak megfelelő újonnan létrehozott fiókot. A fióknak *munkahelyi fiók*típusúnak kell lennie.

Ha meg szeretné ismételni a tesztet, eltávolíthatja a fiókot a beállítások közül.

## <a name="next-steps"></a>Következő lépések

Az [androidos eszközökhöz készült megosztott eszköz mód](msal-android-shared-devices.md) lehetővé teszi, hogy egy Android-eszközt úgy konfiguráljon, hogy az egyszerűen több alkalmazott számára is megosztható legyen.
