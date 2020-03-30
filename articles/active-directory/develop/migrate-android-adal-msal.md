---
title: ADAL a MSAL migrációs útmutató az Android | Azure
description: Ismerje meg, hogyan telepítheti át az Azure Active Directory hitelesítési könyvtár (ADAL) Android-alkalmazását a Microsoft authentication library (MSAL) alkalmazásba.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084055"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>ADAL a MSAL migrációs útmutató az Android

Ez a cikk kiemeli azokat a módosításokat, amelyeket az Azure Active Directory hitelesítési könyvtár (ADAL) a Microsoft authentication library (MSAL) használatához használó alkalmazás áttelepítéséhez szükséges módosításokat kell végrehajtania.

## <a name="difference-highlights"></a>Különbség kiemeli

Az ADAL az Azure Active Directory 1.0-s frissítése ponttal működik. A Microsoft authentication library (MSAL) együttműködik a Microsoft identitás platform - korábbi nevén az Azure Active Directory v2.0 végpont. A Microsoft identity platform annyiban különbözik az Azure Active Directory 1.0-s verziójától, hogy:

Támogatja:
  - Szervezeti identitás (Azure Active Directory)
  - Nem szervezeti identitások, például Outlook.com, Xbox Live és így tovább
  - (Csak B2C) Összevont bejelentkezés a Google, a Facebook, a Twitter és az Amazon segítségével

- A szabványok kompatibilisek-e a következőkkel:
  - OAuth v2.0
  - OpenID Connect (OIDC)

Az MSAL nyilvános API fontos változásokat vezet be, többek között:

- Új modell a jogkivonatok eléréséhez:
  - Az ADAL hozzáférést biztosít `AuthenticationContext`a tokenekhez a , amely a kiszolgálót jelöli. Az MSAL hozzáférést biztosít `PublicClientApplication`a tokenekhez a , amely az ügyfelet jelöli. Az ügyfélfejlesztőknek nem kell `PublicClientApplication` új példányt létrehozniuk minden olyan hatósághoz, amelyhez interakcióra szükségük van. Csak `PublicClientApplication` egy konfiguráció szükséges.
  - A hozzáférési jogkivonatok kérelemezése az erőforrás-azonosítók mellett hatókörök használatával.
  - A növekményes hozzájárulás támogatása. A fejlesztők hatókört kérhetnek, mivel a felhasználó egyre több funkciót fér hozzá az alkalmazáshoz, beleértve azokat is, amelyek nem szerepelnek az alkalmazás regisztrációja során.
  - A hatóságok már nem érvényesítve a futásidőben. Ehelyett a fejlesztő bejelenti az "ismert hatóságok" listáját a fejlesztés során.
- Token API változások:
  - Az ADAL-ban `AcquireToken()` először néma kérést tesz. Ennek hiányában interaktív kérést tesz. Ez a viselkedés azt eredményezte, `AcquireToken`hogy egyes fejlesztők csak a , ami azt eredményezte, hogy a felhasználó váratlanul kéri a hitelesítő adatok időnként. Az MSAL megköveteli, hogy a fejlesztők szándékosan jelenjenek meg, amikor a felhasználó felhasználói felületi kérdést kap.
    - `AcquireTokenSilent`mindig olyan néma kérést eredményez, amely sikeres vagy sikertelen.
    - `AcquireToken`mindig olyan kérést eredményez, amely a felhasználói felületen keresztül kéri a felhasználót.
- Az MSAL támogatja a bejelentkezést egy alapértelmezett böngészőből vagy egy beágyazott webnézetből:
  - Alapértelmezés szerint az eszköz alapértelmezett böngészője használatos. Ez lehetővé teszi az MSAL számára, hogy olyan hitelesítési állapotot (cookie-kat) használjon, amelyek már jelen lehetnek egy vagy több bejelentkezett fióknál. Ha nincs jelen hitelesítési állapot, az MSAL-on keresztüli engedélyezés során történő hitelesítés eredményeképpen hitelesítési állapot (cookie-k) jönnek létre az ugyanabban a böngészőben használt más webes alkalmazások javára.
- Új kivételmodell:
  - A kivételek egyértelműbben meghatározzák a hiba típusát, és azt, hogy a fejlesztőnek mit kell tennie a probléma megoldásához.
- Az MSAL támogatja `AcquireToken` a `AcquireTokenSilent` paraméterobjektumokat és a hívásokat.
- Az MSAL támogatja a deklaratív konfigurációt:
  - Ügyfélazonosító, URI átirányítása.
  - Beágyazott vs alapértelmezett böngésző
  - Hatóságok
  - HTTP-beállítások, például olvasási és kapcsolati időtúllépés

## <a name="your-app-registration-and-migration-to-msal"></a>Az alkalmazás regisztrációja és az MSAL-ra való áttérés

Az MSAL használatához nem kell módosítania a meglévő alkalmazásregisztrációt. Ha szeretné kihasználni a növekményes/ progresszív hozzájárulás előnyeit, előfordulhat, hogy át kell tekintenie a regisztrációt, hogy azonosítsa azokat a hatóköröket, amelyeket fokozatosan szeretne kérni. További információ a hatókörökről és a növekményes hozzájárulásról.

Az alkalmazás regisztrációjában a portálon egy **API-engedélyek** lapot fog látni. Ez tartalmazza azoknak az API-knak és engedélyeknek (hatóköröknek) a listáját, amelyekhez az alkalmazás jelenleg úgy van beállítva, hogy hozzáférést kérjen. Az egyes API-engedélyekhez társított hatókörnevek listáját is megjeleníti.

### <a name="user-consent"></a>Felhasználói beleegyezés

Az ADAL és az AAD v1-végpont, a felhasználó beleegyezését az erőforrások saját adtak az első használat kor. Az MSAL és a Microsoft identity platform segítségével a hozzájárulás fokozatosan kérhető. A növekményes hozzájárulás olyan engedélyek esetében hasznos, amelyeket a felhasználó magas jogosultságnak tekinthet, vagy más módon megkérdőjelezheti, ha nem rendelkezik egyértelmű magyarázattal arról, hogy miért van szükség az engedélyre. Az ADAL-ban előfordulhat, hogy ezek az engedélyek azt eredményezték, hogy a felhasználó felhagyott az alkalmazásba való bejelentkezéssel.

> [!TIP]
> Azt javasoljuk, hogy a növekményes hozzájárulás olyan esetekben, ahol meg kell adnia további környezetben a felhasználó arról, hogy miért az alkalmazás engedélyre van szüksége.

### <a name="admin-consent"></a>Rendszergazdai jóváhagyás

A szervezet rendszergazdái beleegyezésüket tehetik az alkalmazás által a szervezet összes tagja nevében igényelt engedélyekhez. Egyes szervezetek csak azt engedélyezik a rendszergazdáknak, hogy hozzájáruljanak az alkalmazásokhoz. A rendszergazdai jóváhagyás megköveteli, hogy az alkalmazás regisztrációjában szerepeljen az alkalmazás által használt összes API-engedély és hatókör.

> [!TIP]
> Annak ellenére, hogy az alkalmazásregisztrációban nem szereplő apple-tartalomhoz kérhet hatókört, javasoljuk, hogy frissítse az alkalmazásregisztrációt úgy, hogy tartalmazza az összes olyan erőforrást és hatókört, amelyhez a felhasználó valaha is engedélyt adhat.

## <a name="migrating-from-resource-ids-to-scopes"></a>Áttelepítés erőforrásazonosítókról hatókörökre

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Az első használatra vonatkozó összes engedély hitelesítése és kérése

Ha jelenleg ADAL-t használ, és nem kell növekményes jóváhagyást használnia, az `acquireToken` MSAL `AcquireTokenParameter` használatának legegyszerűbb módja az, ha kérelmet nyújt be az új objektum használatával, és beállítja az erőforrás-azonosító értékét.

> [!CAUTION]
> Nem lehet mind a hatóköröket, mind az erőforrás-azonosítót beállítani. Ha mindkettőt megpróbálja beállítani, `IllegalArgumentException`az a .

 Ez azt eredményezi, hogy ugyanazt a v1 viselkedés, amit használ. Az alkalmazásregisztrációban kért összes engedélyt a felhasználó kéri az első interakció során.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Csak szükség esetén hitelesítse és kérje az engedélyeket

A növekményes hozzájárulás kihasználásához készítsen listát az alkalmazás által az alkalmazás regisztrációjából használt engedélyekről (hatókörökről), és rendezze őket két listába a következők alapján:

- Mely hatóköröket szeretné kérni a felhasználó első interakciója során az alkalmazással a bejelentkezés során.
- Az alkalmazás egyik fontos funkciójához társított engedélyek, amelyeket a felhasználónak is el kell magyaráznia.

Miután rendszerezte a hatóköröket, rendszerezze az egyes listákat, amelyekhez az erőforrást (API-t) jogkivonatot szeretne kérni. Csakúgy, mint minden más hatókörök, amelyek et szeretné, hogy a felhasználó engedélyezze egy időben.

A paraméterek objektum használt, hogy a kérelmet MSAL támogatja:

- `Scope`: Azoknak a hatóköröknek a listája, amelyekhez engedélyt kíván kérni, és hozzáférési jogkivonatot szeretne kapni.
- `ExtraScopesToConsent`: Egy további lista a hatókörök, amelyek engedélyt szeretne kérni, miközben egy másik erőforrás hozzáférési jogkivonatát kéri. Ez a hatókörök listája lehetővé teszi, hogy minimálisra csökkentse a felhasználói engedély kérésének számát. Ez kevesebb felhasználói engedélyt vagy hozzájárulási kérdést jelent.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Áttelepítés a AuthenticationContext környezetből publicclientapplications-be

### <a name="constructing-publicclientapplication"></a>PublicClientApplication összeállítása

Az MSAL használatakor a . `PublicClientApplication` Ez az objektum modellezi az alkalmazás identitását, és egy vagy több hatóságnak történő kérésekre szolgál. Ezzel az objektummal konfigurálhatja az ügyfélidentitást, átirányítja az URI-t, az alapértelmezett jogosultságot, hogy használja-e az eszköz böngészőjét és a beágyazott webnézetet, a naplószintet és egyebeket.

Ezt az objektumot deklaratív módon konfigurálhatja a JSON-nal, amelyet fájlként vagy erőforrásként az APK-n belül biztosít.

Bár ez az objektum nem egytárcsa, `Executors` belsőleg használja megosztott interaktív és csendes kérelmek.

### <a name="business-to-business"></a>Üzletről vállalkozásra

Az ADAL-ban minden szervezetnek, amelytől hozzáférési jogkivonatokat kér, külön példányára van szüksége. `AuthenticationContext` Az MSAL-ban ez már nem követelmény. Megadhatja azt a jogosultságot, amelytől jogkivonatot szeretne kérni a csendes vagy interaktív kérés részeként.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>A hatóság-érvényesítésről az ismert hatóságokra való áttérés

Az MSAL nem rendelkezik a hitelesítés engedélyezésére vagy letiltására. A jogosultságérvényesítés az ADAL egyik szolgáltatása, és az MSAL korai kiadásaiban, amely megakadályozza, hogy a kód jogkivonatokat kérjen egy potenciálisan rosszindulatú hatóságtól. Az MSAL most lekéri a Microsoft által ismert hatóságok listáját, és egyesíti a listát a konfigurációban megadott hatóságokkal.

> [!TIP]
> Ha Ön Egy Azure Business to Consumer (B2C) felhasználó, ez azt jelenti, hogy már nem kell letiltania a jogosultságérvényesítést. Ehelyett az MSAL-konfigurációban adja meg a támogatott Azure AD B2C-szabályzatokat.

Ha olyan szolgáltatót próbál használni, amelyet a Microsoft nem ismer, és amely `UnknownAuthorityException`nem szerepel a konfigurációban, a .

### <a name="logging"></a>Naplózás
Most már deklaratív módon konfigurálhatja a naplózást a konfiguráció részeként, így:

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>Áttelepítés a UserInfo-ról a fiókba

Az ADAL-ban `AuthenticationResult` `UserInfo` a hitelesített fiókkal kapcsolatos információk lekéréséhez használt objektumot biztosít. A "felhasználó" kifejezést, ami emberi vagy szoftveres ügynököt jelentett, úgy alkalmazták, hogy nehéz volt azt közölni, hogy egyes alkalmazások egyetlen felhasználót támogatnak (akár emberi, akár szoftverügynök), amely több fiókkal rendelkezik.

Fontolja meg egy bankszámlát. Előfordulhat, hogy egynél több számlája is van egynél több pénzintézetnél. Amikor számlát nyit, ön (a felhasználó) olyan hitelesítő adatokat kap, például egy ATM-kártyát & PIN-kódot, amelyek az egyenleg, a számlakifizetések és így tovább eléréséhez használatosak az egyes fiókokhoz. Ezek a hitelesítő adatok csak az azokat kibocsátó pénzintézetnél használhatók fel.

Analógia, mint a számlák egy pénzügyi intézmény, fiókok a Microsoft identitás platform on keresztül érhető el hitelesítő adatokkal. Ezeket a hitelesítő adatokat vagy a Microsoft regisztrálta, vagy a Microsoft adta ki. Vagy a Microsoft egy szervezet nevében.

Amennyiben a Microsoft-identitásplatform ebben az analógiában különbözik a pénzügyi intézménytől, az az, hogy a Microsoft identitásplatform olyan keretrendszert biztosít, amely lehetővé teszi a felhasználó számára, hogy egy fiókot és a hozzá tartozó hitelesítő adatokat használjon a hozzá tartozó erőforrások eléréséhez. több személy és szervezet számára. Ez olyan, mintha egy bank, egy másik pénzintézet által kibocsátott kártyát használhatnál. Ez azért működik, mert az összes szóban forgó szervezet a Microsoft identitásplatformot használja, amely lehetővé teszi egy fiók használatát több szervezetben. Például:

Sam Contoso.com dolgozik, de kezeli az Azure Fabrikam.com virtuális gépeit. Ahhoz, hogy Sam kezelni tudja Fabrikam virtuális gépeit, engedélyt kell kérnie a hozzáférésükhöz. Ez a hozzáférés úgy adható meg, hogy Sam fiókját hozzáadja Fabrikam.com, és olyan szerepkört biztosít a fiókjának, amely lehetővé teszi számára, hogy a virtuális gépekkel dolgozzon. Ez az Azure Portalon történik.

Sam Contoso.com fiókjának hozzáadása Fabrikam.com tagjaként egy új rekord létrehozását eredményezné a Fabrikam.com Azure Active Directory for Sam szolgáltatásában. Sam rekord az Azure Active Directoryban ismert, mint egy felhasználói objektum. Ebben az esetben a felhasználói objektum sam felhasználói objektumra mutatna Contoso.com. Sam Fabrikam felhasználói objektuma Sam helyi ábrázolása, és a Sam-hez társított fiókadatait Fabrikam.com keretében kell tárolni. Contoso.com Sam címe Senior DevOps Consultant. A Fabrikam, Sam címe vállalkozó-virtuális gépek. a Contoso.com Sam nem felelős, és nem jogosult a virtuális gépek kezelésére. Fabrikam.com ez az egyetlen feladata. Sam azonban még mindig csak egy hitelesítő adatok nyomon követni, amelyek a hitelesítő adatok által kiadott Contoso.com.

A sikeres `acquireToken` hívás után megjelenik egy hivatkozás `IAccount` egy objektumra, `acquireTokenSilent` amely későbbi kérelmekben használható.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Ha olyan alkalmazással rendelkezik, amely hozzáfér az egyes bérlők fiókja kapcsolatos jogcímekhez, amelyekben a fiók képviselteti magát, objektumokat jeleníthet `IAccount` meg. `IMultiTenantAccount` Ez a felület `ITenantProfiles`biztosít egy térképet, bérlői azonosító, amely lehetővé teszi, hogy hozzáférjen a jogcímek, amelyek a fiókhoz tartozik az egyes bérlők, akikkért egy jogkivonatot, az aktuális fiókhoz képest.

A jogcímek a `IAccount` gyökere, és `IMultiTenantAccount` mindig tartalmazza a követeléseket a hazai bérlő. Ha még nem kért egy jogkivonatot az otthoni bérlőn belül, ez a gyűjtemény üres lesz.

## <a name="other-changes"></a>További változások

### <a name="use-the-new-authenticationcallback"></a>Az új AuthenticationCallback használata

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}


```

## <a name="migrate-to-the-new-exceptions"></a>Áttelepítés az új kivételekre

Az ADAL-ban van egy kivételtípus, `AuthenticationException`amely tartalmazza a `ADALError` felsorolási érték beolvasásának módját.
Az MSAL-ban a kivételek hierarchiája van, és mindegyiknek megvan a saját speciális hibakódkészlete.

MSAL kivételek listája

|Kivétel  | Leírás  |
|---------|---------|
| `MsalException`     | Az MSAL által okozott alapértelmezett ellenőrzött kivétel.  |
| `MsalClientException`     | Dobott, ha a hiba ügyféloldali. |
| `MsalArgumentException`     | Ha egy vagy több bemeneti argumentum érvénytelen, akkor a szöveg előkerül. |
| `MsalClientException`     | Dobott, ha a hiba ügyféloldali. |
| `MsalServiceException`     | Dobott, ha a hiba szerver oldalon. |
| `MsalUserCancelException`     | Akkor dobva, ha a felhasználó megszakította a hitelesítési folyamatot.  |
| `MsalUiRequiredException`     | Ha a token nem frissíthető csendesen, akkor kidobva.  |
| `MsalDeclinedScopeException`     | Ha a kiszolgáló egy vagy több kért hatókört elutasított, a rendszer ellett.  |
| `MsalIntuneAppProtectionPolicyRequiredException` | Akkor jelenik meg, ha az erőforráson engedélyezve van a MAMCA védelmi házirend. |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError a MsalException hibakódra

### <a name="adal-logging-to-msal-logging"></a>ADAL naplózás az MSAL naplózásba

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```
