---
title: Hogyan lehet engedélyezni az alkalmazások közötti Egyszeri használatú szolgáltatások használatát Androidon az ADAL használatával | Microsoft dokumentumok
description: Az ADAL SDK funkcióinak használata az alkalmazások egyszeri bejelentkezésének engedélyezéséhez.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 0b87a9cd0ae29281faad4209f4449d547921835d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154814"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Útmutató: Alkalmazásközi Egyszeri szolgáltatások engedélyezése Androidon az ADAL használatával

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Egyszeri bejelentkezés (SSO) lehetővé teszi a felhasználók számára, hogy csak egyszer adja meg a hitelesítő adatait, és ezeket a hitelesítő adatokat automatikusan működik az alkalmazások között, és a platformok között, hogy más alkalmazások is használhatják (például a Microsoft-fiókok vagy a munkahelyi fiók a Microsoft 365) nem a kiadótól.

A Microsoft identitásplatformja az SDK-kkal együtt megkönnyíti az SSO engedélyezését a saját alkalmazáscsomagján belül, vagy a közvetítői képességgel és a hitelesítő alkalmazásokkal az egész eszközön.

Ebben a útmutatóban megtudhatja, hogyan konfigurálhatja az SDK-t az alkalmazáson belül, hogy egyszeri bejelentkezést biztosítson az ügyfeleknek.

## <a name="prerequisites"></a>Előfeltételek

Ez a how-to feltételezi, hogy tudod, hogyan kell:

- Az alkalmazás kiépítése az Azure Active Directory (Azure AD) örökölt portálján keresztül. További információ: [App regisztrálása](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
- Integrálja alkalmazását az [Azure AD Android SDK-val.](https://github.com/AzureAD/azure-activedirectory-library-for-android)

## <a name="single-sign-on-concepts"></a>Egyszeri bejelentkezési fogalmak

### <a name="identity-brokers"></a>Identitás brókerek

A Microsoft minden olyan mobilplatformhoz biztosít alkalmazásokat, amelyek lehetővé teszik a hitelesítő adatok áthidalását a különböző szállítóktól származó alkalmazások között, valamint olyan továbbfejlesztett funkciókat, amelyek egyetlen biztonságos helyet igényelnek a hitelesítő adatok érvényesítéséhez. Ezek az úgynevezett **brókerek**.

IOS és Android rendszeren a brókerek olyan letölthető alkalmazásokon keresztül kerülnek rendelkezésre, amelyeket az ügyfelek önállóan telepítenek, vagy egy olyan vállalat tol az eszközre, amely az alkalmazottak számára kezeli az eszközök egy részét vagy egészét. A brókerek támogatják a biztonság kezelését csak bizonyos alkalmazások vagy a teljes eszköz informatikai rendszergazdai konfiguráció alapján. A Windows rendszerben ezt a funkciót az operációs rendszerbe beépített fiókválasztó biztosítja, amelyet technikailag webhitelesítési brókerként ismernek.

#### <a name="broker-assisted-login"></a>Bróker által támogatott bejelentkezés

A bróker által támogatott bejelentkezések olyan bejelentkezési élmények, amelyek a közvetítőalkalmazáson belül fordulnak elő, és a közvetítő tárolóját és biztonságát használják a hitelesítő adatok megosztására az eszközön lévő összes alkalmazás között, amelyek az identitásplatformot alkalmazzák. A következmény, hogy az alkalmazások támaszkodnak a bróker, hogy jelentkezzen be a felhasználók. IOS és Android rendszeren ezek a brókerek letölthető alkalmazásokon keresztül kerülnek rendelkezésre, amelyeket az ügyfelek önállóan telepítenek, vagy egy olyan vállalat tolhatja az eszközre, amely kezeli az eszközt a felhasználó számára. Az ilyen típusú alkalmazások ra van példa a Microsoft Authenticator alkalmazás iOS rendszeren. A Windows rendszerben ezt a funkciót az operációs rendszerbe beépített fiókválasztó biztosítja, amelyet technikailag webhitelesítési brókerként ismernek.
A tapasztalat platformonként változik, és néha zavaró lehet a felhasználók számára, ha nem megfelelően kezelik. Valószínűleg akkor ismeri leginkább ezt a mintát, ha telepítve van a Facebook alkalmazás, és egy másik alkalmazásból használja a Facebook Connect alkalmazást. Az identitásplatform ugyanazt a mintát használja.

Androidon a fiókválasztó az alkalmazás tetején jelenik meg, ami kevésbé zavarja a felhasználót.

#### <a name="how-the-broker-gets-invoked"></a>Hogyan kap a bróker kap meghívni

Ha egy kompatibilis bróker telepítve van az eszközön, mint például a Microsoft Authenticator alkalmazás, az identitás SDK-k automatikusan elvégeza munkát hivatkozva a bróker az Ön számára, ha a felhasználó azt jelzi, hogy szeretne bejelentkezni bármilyen fiókkal az identitás platform.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Hogyan biztosítja a Microsoft az alkalmazás érvényességét?

Annak szükségességét, hogy biztosítsák a személyazonosságát egy alkalmazás hívja a bróker elengedhetetlen a biztonság biztosított bróker segített bejelentkezések. Az iOS és az Android nem kényszerít iOS-azonosítókat, amelyek csak egy adott alkalmazásra érvényesek, így a rosszindulatú alkalmazások "meghamisulhatnak" egy jogszerű alkalmazás azonosítóján, és megkapják a jogszerű alkalmazásnak szánt jogkivonatokat. Annak érdekében, hogy a Microsoft futásidőben mindig a megfelelő alkalmazással kommunikáljon, a fejlesztőnek egyéni redirectURI-t kell biztosítania az alkalmazás Microsoftnál történő regisztrálásakor. **Az alábbiakban részletesen tárgyaljuk, hogy a fejlesztők hogyan készítsék el ezt az átirányítási URI-t.** Ez az egyéni redirectURI tartalmazza az alkalmazás tanúsítványujjlenyomatát, és a Google Play Áruház biztosítja, hogy egyedi legyen az alkalmazásban. Amikor egy alkalmazás felhívja a bróker, a bróker kéri az Android operációs rendszer, hogy azt a tanúsítvány ujjlenyomat, hogy az úgynevezett bróker. A bróker biztosítja ezt a tanúsítvány ujjlenyomatát a Microsoft nak az identitásrendszer hívásában. Ha a tanúsítvány ujjlenyomata az alkalmazás nem egyezik meg a tanúsítvány ujjlenyomata a fejlesztő által a regisztráció során, a hozzáférés megtagadva a jogkivonatokat az erőforrás az alkalmazás által kért. Ez az ellenőrzés biztosítja, hogy csak a fejlesztő által regisztrált alkalmazás kap jogkivonatokat.

A közvetített SSO bejelentkezések a következő előnyökkel járnak:

* A felhasználó az összes alkalmazásában sso-t tapasztal, függetlenül a ttól, hogy a szállító.User experiences SSO across all their applications no matter the vendor.
* Az alkalmazás speciálisabb üzleti funkciókat, például feltételes hozzáférést és az Intune-forgatókönyvek támogatását is használhatja.
* Az alkalmazás támogatja a tanúsítványalapú hitelesítést az üzleti felhasználók számára.
* Biztonságosabb bejelentkezési élmény, mint az alkalmazás és a felhasználó identitását a közvetítő alkalmazás további biztonsági algoritmusok és titkosítás ellenőrzése.

Itt van egy ábrázolása, hogy az SDK-k működnek együtt a bróker alkalmazások, hogy SSO:

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

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Az SSO bekapcsolása a közvetítő által támogatott SSO-hoz

Az alkalmazás az eszközre telepített brókerek használatára való képessége alapértelmezés szerint ki van kapcsolva. Annak érdekében, hogy használja az alkalmazást a bróker, meg kell tennie néhány további konfigurációt, és adjunk hozzá néhány kódot az alkalmazáshoz.

A következő lépések a következők:

1. Közvetítői mód engedélyezése az alkalmazáskód ms SDK-hívásában
2. Hozzon létre egy új átirányítási URI-t, és biztosítsa, hogy mind az alkalmazás, mind az alkalmazás regisztrációja
3. A megfelelő engedélyek beállítása az Android-jegyzékben

#### <a name="step-1-enable-broker-mode-in-your-application"></a>1. lépés: Bróker mód engedélyezése az alkalmazásban

Az alkalmazás a közvetítő használatára való képessége be van kapcsolva, amikor létrehozza a hitelesítési példány "beállításait" vagy kezdeti beállítását. Ehhez az alkalmazásban:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>2. lépés: Új átirányítási URI létrehozása az URL-sémával

Annak érdekében, hogy a megfelelő alkalmazás megkapja a visszaadott hitelesítő adatok tokenek, szükség van, hogy ellenőrizze a hívást vissza az alkalmazás oly módon, hogy az Android operációs rendszer ellenőrizheti. Az Android operációs rendszer a tanúsítvány kivonatát használja a Google Play áruházban. A tanúsítvány ezen kivonatát nem hamisítható meg engedélyezetlen alkalmazás. A közvetítői alkalmazás URI-jával együtt a Microsoft biztosítja, hogy a jogkivonatok a megfelelő alkalmazásba kerüljön. Az alkalmazáson regisztrálandó egyedi átirányítási URI-t kell regisztrálni.

Az átirányítási URI-nak a következő formában kell lennie:

`msauth://packagename/Base64UrlencodedSignature`

pl.: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Ezt az átirányítási URI-t regisztrálhatja az alkalmazásregisztrációban az [Azure Portal](https://portal.azure.com/)használatával. Az Azure AD-alkalmazások regisztrációjáról az [Integráció az Azure Active Directoryval](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)című témakörben talál további információt.

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>3. lépés: A megfelelő engedélyek beállítása az alkalmazásban

Az Android brókeralkalmazása az Android operációs rendszer Fiókkezelő funkcióját használja a hitelesítő adatok kezelésére az alkalmazások között. Annak érdekében, hogy a bróker androidos az alkalmazás jegyzékfájl kell engedélyekkel használni AccountManager számlák. Ezeket az engedélyeket részletesen tárgyalja a [Google fiókkezelődokumentációjában itt](https://developer.android.com/reference/android/accounts/AccountManager.html)

Ezek az engedélyek különösen a következők:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Beállította az SSO-t!

Most az identitás SDK automatikusan megosztja hitelesítő adatait az alkalmazások között, és meghívja a közvetítő, ha jelen van az eszközön.

## <a name="next-steps"></a>További lépések

* Tudnivalók [az egyszeri bejelentkezési SAML protokollról](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
