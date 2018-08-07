---
title: Az ADAL használatával Android alkalmazások közötti SSO engedélyezése |} A Microsoft Docs
description: 'Hogyan használható az ADAL SDK funkcióit az egyszeri bejelentkezés engedélyezése az alkalmazások között. '
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
ms.date: 06/13/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 4abf6bd2d82753e22d4fde92e219109274ce36be
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581554"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Az ADAL használatával Android alkalmazások közötti SSO engedélyezése
Egyszeri bejelentkezés (SSO) biztosít, hogy a felhasználók csak egyszer adja meg a hitelesítő adataikat, és automatikusan a különböző alkalmazások munkahelyi hitelesítő adatokat kell már az iparági szabványnak számító. A nehézség a felhasználónevüket és jelszavukat módosítani kis képernyőn, gyakran alkalommal kombinálva egy további tényezőt (2FA) például telefonhívást vagy a rendszer kódot, eredményez, ha a felhasználók elégedetlenségének bejelentkezéshez egynél több alkalommal van.

Emellett ha alkalmaz egy identity platform, amely más alkalmazások is használhatnak, például Microsoft Accounts vagy egy munkahelyi fiókot a Microsoft365, ügyfelek várható, hogy azokat, függetlenül attól, hogy a közzétevő az alkalmazások közötti használható hitelesítő adatokat.

A Microsoft Identity platform, a Microsoft Identity SDK-k, valamint az egyszeri bejelentkezés az ügyfelek vagy a saját alkalmazások suite belül lenyűgöző lehetővé teszi vagy, mint a közvetítő funkció és Authenticator alkalmazások, a teljes az eszköz.

Ez a forgatókönyv jelzi az egyszeri Bejelentkezést biztosítani ügyfeleinek az alkalmazáson belül az SDK konfigurálása.

Az előző dokumentum céljából feltételezzük, hogy tudja, hogyan integrálható az alkalmazás a [a Microsoft Identity Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>A Microsoft Identity platform SSO-fogalmak
### <a name="microsoft-identity-brokers"></a>A Microsoft Identitásszervezői
A Microsoft, amely lehetővé teszi az adatközponthíd-képzés hitelesítő adatokat a különböző alkalmazások különböző szállítóktól származó alkalmazások minden mobilplatformra biztosít, és lehetővé teszi, hogy speciális amelyek speciális funkcióival megkövetelhető egy adott biztonságos hely, ahonnan a hitelesítő adatok ellenőrzése. Az SDK meghívja ezeket **közvetítők**. IOS és Android rendszeren a közvetítők szolgáltatáson keresztül letölthető alkalmazás, hogy ügyfeleink egymástól függetlenül telepítése vagy egy vállalat vagy azok egy részét az eszköz az alkalmazott felügyelő lehet leküldeni az eszközre. Ezeket a közvetítők, és az egyes alkalmazások vagy a rendszergazdák kívánják alapján a teljes eszköz kezelése biztonsági támogatja. A Windows Ez a funkció-szervezőként ismert szakszóval Webeshitelesítés-szervező a az operációs rendszer beépített biztosítja.

#### <a name="broker-assisted-logins"></a>A közvetítő személyes bejelentkezések
Bejelentkezések Broker támogatású olyan bejelentkezési élmény a közvetítő alkalmazás belül, és a storage és a közvetítő biztonsági hitelesítő adatok megosztása az eszközön minden olyan alkalmazás, amely a alkalmazni a Microsoft Identity platform használatával. Folyamatban van az alkalmazások ennek következménye, jelentkezzen be a felhasználók a közvetítő fogja alkalmazni. IOS és Android rendszeren ezeket a közvetítők szolgáltatáson keresztül letölthető alkalmazás, hogy ügyfelek egymástól függetlenül telepítése vagy egy olyan cég, kezeli az eszközt a felhasználó által lehet leküldeni az eszközre. Ez az alkalmazástípus egyik példája a Microsoft Authenticator alkalmazás IOS rendszerű eszközökön. A Windows Ez a funkció-szervezőként ismert szakszóval Webeshitelesítés-szervező a az operációs rendszer beépített biztosítja.
A felhasználói élményt platformonként és néha a felhasználók számára zavaró lehet ha nem megfelelően kezeli. Ismerkedjen meg valószínűleg legtöbb ezt a mintát, ha a Facebook-alkalmazás telepítve van, és a Facebook Connect használata egy másik alkalmazás. A Microsoft Identity platform hasonló mintát alkalmaz.

Az Android a fiók kiválasztásakor felett, ami kevésbé zavaró a felhasználónak az alkalmazás jelenik meg.

#### <a name="how-the-broker-gets-invoked"></a>Hogyan a közvetítő meghívása beolvasása
Ha az eszközt, például a Microsoft Authenticator alkalmazás kompatibilis ügynök telepítve van a Microsoft Identity SDK-k automatikusan elvégzi az Ön számára a közvetítő meghívása, amikor egy felhasználó azt jelzi, hogy jelentkezzen be a Microsoft minden olyan fiók használatával történő munkájának Identity platform. 
 
 #### <a name="how-microsoft-ensures-the-application-is-valid"></a>Hogyan biztosítja a Microsoft, az alkalmazás érvénytelen.
 
 Szükséges egy alkalmazás hívás identitását a közvetítő elengedhetetlen az eszközkapcsolat broker bejelentkezésekben biztonsági. iOS és Android rendszerhez nem tesszük kötelezővé egyedi azonosítók, amelyek csak egy adott alkalmazás esetében érvényes, így rosszindulatú alkalmazások, előfordulhat, hogy "agresszívebb" egy megbízható alkalmazás azonosítója és a megbízható alkalmazás webszolgáltatásokban a jogkivonatokat fogadni. Annak érdekében, hogy a Microsoft mindig kommunikál a megfelelő alkalmazás a futásideje, meg kell adnia a fejlesztőnek adja meg egy egyéni redirectURI, amikor az alkalmazás regisztrálása a Microsoft. **Az alábbiakban tárgyalt hogyan a fejlesztők formázhatják az átirányítási URI-t kell.** Az egyéni redirectURI a tanúsítvány ujjlenyomatát, az alkalmazás tartalmaz, és a Google Play Store biztosítja, hogy az alkalmazás egyedi legyen. Amikor egy alkalmazás a közvetítő hívja, a közvetítő megkérdezi, hogy biztosítsa a tanúsítvány ujjlenyomatát, amelynek a neve a közvetítő számára az Android operációs rendszer. A közvetítő a tanúsítvány-ujjlenyomat által a Microsoftnak a identitásrendszer hívásában. Ha az alkalmazás tanúsítvány-ujjlenyomata nem egyezik meg a tanúsítvány ujjlenyomatát a regisztráció során fejlesztője által készített velünk a kapcsolatot, a tokeneket a az alkalmazás által kért erőforráshoz való hozzáférés megtagadva. Ez az ellenőrzés biztosítja, hogy csak az alkalmazás fejlesztője által regisztrált jogkivonatokat kap-e.

Felügyelt SSO bejelentkezések rendelkezik a következő előnyökkel jár:

* Felhasználói függetlenül attól, gyártó az alkalmazások közötti SSO teljesen.
* Az alkalmazás használhatja összetettebb üzleti funkciók, például a feltételes hozzáférés és az Intune esetében.
* Az alkalmazás az üzleti felhasználók is támogatja a Tanúsítványalapú hitelesítés.
* Biztonságosabb bejelentkezés rendelkezik, mint az alkalmazás és a felhasználó identitásának ellenőrzése a közvetítő alkalmazás további biztonsági algoritmusokkal és a titkosítás.

Íme egy ábrázolása a Microsoft Identity SDK-k működését a közvetítő alkalmazásokkal való egyszeri bejelentkezés engedélyezése:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
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

Képességekkel, jobb megismerésében és egyszeri bejelentkezés megvalósítása az alkalmazásban, a Microsoft Identity platform és az SDK-k használatával képesnek kell lennie a háttér-információkat.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Egyszeri bejelentkezés bekapcsolásával Broker támogatott egyszeri bejelentkezés
Az, hogy az alkalmazás számára az eszközön telepített bármely brokert **alapértelmezés szerint ki van kapcsolva**. Annak érdekében, hogy az alkalmazás a közvetítő használja, néhány további konfigurációs lehetőségek és kód hozzáadása az alkalmazáshoz.

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
Most már a Microsoft Identity SDK automatikusan is hitelesítő adatok megosztása az alkalmazások és a közvetítő meghívása az eszközön telepítve, ha.

