---
title: Az ADAL használatával Android alkalmazások közötti SSO engedélyezése |} A Microsoft Docs
description: Hogyan az alkalmazások egyszeri bejelentkezés engedélyezése ADAL SDK funkcióinak használatát.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: c7d1665eece966053f52e118bb0d6f7f5ec4cba6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957863"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Útmutató: az ADAL használatával Android alkalmazások közötti SSO engedélyezése

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Egyszeri bejelentkezés (SSO) lehetővé teszi a felhasználóknak csak egyszer adja meg a hitelesítő adataikat, és ezeket a hitelesítő adatokat automatikusan működnek a különböző alkalmazások és több platformon, amely a más alkalmazások (például Microsoft Accounts vagy egy munkahelyi fiókot a Microsoft 365-ből) nélkül a közzétevő számít.

A Microsoft identity platform, az SDK-k, valamint megkönnyíti az egyszeri bejelentkezés engedélyezése belül a saját suite, az alkalmazásokat, vagy a közvetítő képesség and Authenticator alkalmazásokhoz, a teljes eszköz között.

Ebben az útmutatóban megismerheti, hogyan egyszeri Bejelentkezést biztosítani ügyfeleinek az alkalmazáson belül az SDK konfigurálására fogjuk.

## <a name="prerequisites"></a>Előfeltételek

Ebben az útmutatóban azt feltételezi, hogy tudja, hogyan lehet:

- A régi portál használata az Azure Active Directory (Azure AD) az alkalmazás üzembe helyezése. További információ: [alkalmazás regisztrálása az Azure AD-1.0-s verziójú végpont](quickstart-v1-add-azure-ad-app.md)
- Az alkalmazás integrálja az [Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Egyszeri bejelentkezés fogalmak

### <a name="identity-brokers"></a>Identitásszervezőinek

A Microsoft biztosít az alkalmazások minden mobilplatformra, amelyek lehetővé teszik az adatközponthíd-képzés hitelesítő adatokat a különböző alkalmazások különböző szállítóktól származó és amelyek speciális funkcióival megkövetelhető egy adott biztonságos hely, ahonnan a hitelesítő adatok ellenőrzése. Ezek az úgynevezett **közvetítők**.

IOS és Android rendszeren közvetítők szolgáltatáson keresztül letölthető alkalmazás, hogy ügyfeleink egymástól függetlenül telepítése vagy a felhasználók számára egy olyan cég, az egyes felügyeli, vagy minden, az eszköz az eszközre leküldve. A biztonság, és az egyes alkalmazások vagy informatikai felügyeleti konfiguráció alapján a teljes eszköz kezelése közvetítők támogatása. A Windows Ez a funkció-szervezőként ismert szakszóval Webeshitelesítés-szervező a az operációs rendszer beépített biztosítja.

#### <a name="broker-assisted-login"></a>Személyes Broker bejelentkezési

Bejelentkezések Broker támogatású olyan bejelentkezési élmény a közvetítő alkalmazás belül, és a storage és a közvetítő biztonsági hitelesítő adatok megosztása az eszközön minden olyan alkalmazás, amely a alkalmazni az identity platform használatával. Folyamatban van az alkalmazások ennek következménye, jelentkezzen be a felhasználók a közvetítő fogja alkalmazni. IOS és Android rendszeren ezeket a közvetítők szolgáltatáson keresztül letölthető alkalmazás, hogy ügyfelek egymástól függetlenül telepítése vagy egy olyan cég, kezeli az eszközt a felhasználó által lehet leküldeni az eszközre. Ez az alkalmazástípus egyik példája a Microsoft Authenticator alkalmazás IOS rendszerű eszközökön. A Windows Ez a funkció-szervezőként ismert szakszóval Webeshitelesítés-szervező a az operációs rendszer beépített biztosítja.
A felhasználói élményt platformonként és néha a felhasználók számára zavaró lehet ha nem megfelelően kezeli. Ismerkedjen meg valószínűleg legtöbb ezt a mintát, ha a Facebook-alkalmazás telepítve van, és a Facebook Connect használata egy másik alkalmazás. Az identity platform hasonló mintát alkalmaz.

Az Android a fiók kiválasztásakor felett, ami kevésbé zavaró a felhasználónak az alkalmazás jelenik meg.

#### <a name="how-the-broker-gets-invoked"></a>Hogyan a közvetítő meghívása beolvasása

Ha az eszközt, például a Microsoft Authenticator alkalmazás kompatibilis ügynök telepítve van az identitás SDK-k automatikusan elvégzi a munkáját meghívása a közvetítő az Ön számára, amikor egy felhasználó azt jelzi, hogy jelentkezzen be kívánják minden olyan fiók az identity platform használatával.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Hogyan biztosítja a Microsoft, az alkalmazás érvénytelen.

Szükséges egy alkalmazás hívás identitását a közvetítő elengedhetetlen az eszközkapcsolat broker bejelentkezésekben biztonsági. iOS és Android rendszerhez nem tesszük kötelezővé egyedi azonosítók, amelyek csak egy adott alkalmazás esetében érvényes, így rosszindulatú alkalmazások, előfordulhat, hogy "agresszívebb" egy megbízható alkalmazás azonosítója és a megbízható alkalmazás webszolgáltatásokban a jogkivonatokat fogadni. Annak érdekében, hogy a Microsoft mindig kommunikál a megfelelő alkalmazás a futásideje, meg kell adnia a fejlesztőnek adja meg egy egyéni redirectURI, amikor az alkalmazás regisztrálása a Microsoft. **Az alábbiakban tárgyalt hogyan a fejlesztők formázhatják az átirányítási URI-t kell.** Az egyéni redirectURI a tanúsítvány ujjlenyomatát, az alkalmazás tartalmaz, és a Google Play Store biztosítja, hogy az alkalmazás egyedi legyen. Amikor egy alkalmazás a közvetítő hívja, a közvetítő megkérdezi, hogy biztosítsa a tanúsítvány ujjlenyomatát, amelynek a neve a közvetítő számára az Android operációs rendszer. A közvetítő a tanúsítvány-ujjlenyomat által a Microsoftnak a identitásrendszer hívásában. Ha az alkalmazás tanúsítvány-ujjlenyomata nem egyezik meg a tanúsítvány ujjlenyomatát a regisztráció során fejlesztője által készített velünk a kapcsolatot, a tokeneket a az alkalmazás által kért erőforráshoz való hozzáférés megtagadva. Ez az ellenőrzés biztosítja, hogy csak az alkalmazás fejlesztője által regisztrált jogkivonatokat kap-e.

Felügyelt SSO bejelentkezések rendelkezik a következő előnyökkel jár:

* Felhasználói függetlenül attól, gyártó az alkalmazások közötti SSO teljesen.
* Az alkalmazás használhatja összetettebb üzleti funkciók, például a feltételes hozzáférés és az Intune esetében.
* Az alkalmazás az üzleti felhasználók is támogatja a Tanúsítványalapú hitelesítés.
* Biztonságosabb bejelentkezés rendelkezik, mint az alkalmazás és a felhasználó identitásának ellenőrzése a közvetítő alkalmazás további biztonsági algoritmusokkal és a titkosítás.

Itt látható az SDK-k működését a közvetítő alkalmazások engedélyezhető az SSO-ábrázolása:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Egyszeri bejelentkezés bekapcsolásával Broker támogatott egyszeri bejelentkezés

Lehetővé teszi az alkalmazás számára az eszközön telepített bármely brokert alapértelmezés szerint ki van kapcsolva. Annak érdekében, hogy az alkalmazás a közvetítő használja, néhány további konfigurációs lehetőségek és kód hozzáadása az alkalmazáshoz.

A követendő lépések a következők:

1. Az MS SDK az alkalmazás kódjában hívása a közvetítő mód engedélyezése
2. Hozzon létre új átirányítási URI-t, és adja meg, hogy az alkalmazás és az alkalmazás regisztrációját
3. Az Android-jegyzékfájlban a megfelelő engedélyek beállítása

#### <a name="step-1-enable-broker-mode-in-your-application"></a>1. lépés: Az alkalmazás átvitelszervező-mód engedélyezése

Lehetővé teszi, hogy az alkalmazás a közvetítő használata be van kapcsolva a "beállítások" vagy a kezdeti beállítás a hitelesítési példány létrehozásakor. Ehhez az alkalmazásban:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>2. lépés: Új átirányítási URI az az URL-séma létrehozása

Annak érdekében, hogy a megfelelő alkalmazás recevies által visszaadott a hitelesítő adatok tokeneket és ott van szükség győződjön meg arról, az alkalmazásnak a hívás úgy, hogy az Android operációs rendszer ellenőrizheti. Az Android operációs rendszer a tanúsítvány kivonata használ, a Google Play áruházban. Ez a tanúsítvány kivonata nem lehet megtévesztésre egy engedélyezetlen alkalmazás. Az URI-ját a közvetítő alkalmazás, valamint a Microsoft biztosítja, hogy a megfelelő alkalmazáshoz a jogkivonatok lesznek visszaadva. Egy egyedi átirányítási URI-ja regisztrálni kell az alkalmazás.

Az átirányítási URI-t, a megfelelő formátumban kell lennie:

`msauth://packagename/Base64UrlencodedSignature`

például: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

A regisztráció segítségével regisztrálhatja az átirányítási URI-t a [az Azure portal](https://portal.azure.com/). További információ az Azure AD-alkalmazás regisztrációjának: [integrálása az Azure Active Directoryval](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>3. lépés: Az alkalmazás a megfelelő engedélyek beállítása

A közvetítő alkalmazás Android hitelesítő adatok kezelése a különböző alkalmazások az Android operációs rendszer fiókkezelő funkcióját használja. Annak érdekében, hogy a közvetítő használata az Android a alkalmazásjegyzékbe AccountManager fiókokat engedélyekkel kell rendelkeznie. Ezek az engedélyek tárgyalja részletesen ismertetjük a [Google Account Manager itt található dokumentáció](http://developer.android.com/reference/android/accounts/AccountManager.html)

Ezeket az engedélyeket, a következők:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Egyszeri bejelentkezés konfigurálása!

Most már az identitás SDK automatikusan is hitelesítő adatok megosztása az alkalmazások és a közvetítő meghívása az eszközön telepítve, ha.

## <a name="next-steps"></a>További lépések

* Ismerje meg [egyszeri bejelentkezéses SAML-protokoll](single-sign-on-saml-protocol.md)