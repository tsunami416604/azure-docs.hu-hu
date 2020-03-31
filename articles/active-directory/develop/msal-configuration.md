---
title: Android MSAL konfigurációs fájl | Azure
titleSuffix: Microsoft identity platform
description: Az Android Microsoft Authentication Library (MSAL) konfigurációs fájl áttekintése, amely egy alkalmazás konfigurációját jelöli az Azure Active Directoryban.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 9e35ba5a3f3705a52e80262da9bbfbfda489bf83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050379"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Android Microsoft Hitelesítési könyvtár konfigurációs fájlja

Az Android Microsoft Authentication Library (MSAL) egy [alapértelmezett konfigurációs JSON-fájllal](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) van elszállítva, amelyet testre szabhat a nyilvános ügyfélalkalmazás viselkedésének meghatározásához olyan dolgokhoz, mint például az alapértelmezett jogosultság, a használni kívánt hatóságok és így tovább.

Ez a cikk segít megérteni a konfigurációs fájl különböző beállításait, és azt, hogy miként adható meg az MSAL-alapú alkalmazásban használandó konfigurációs fájl.

## <a name="configuration-settings"></a>Konfigurációs beállítások

### <a name="general-settings"></a>Általános beállítások

| Tulajdonság | Adattípus | Kötelező | Megjegyzések |
|-----------|------------|-------------|-------|
| `client_id` | Sztring | Igen | Az alkalmazás ügyfélazonosítója az [Alkalmazás regisztrációs lapjáról](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | Sztring | Igen | Az alkalmazás átirányítási URI-ja az [Alkalmazás regisztrációs lapjáról](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `authorities` | Felügyeleti\<> listázása | Nem | Az alkalmazás igényeinek megfelelő hatóságok listája |
| `authorization_user_agent` | AuthorizationAgent (felsorakozó) | Nem | Lehetséges `DEFAULT`értékek: `BROWSER`, ,`WEBVIEW` |
| `http` | HttpConfiguration | Nem | Konfigurálja `HttpUrlConnection` `connect_timeout` és`read_timeout` |
| `logging` | Naplózáskonfiguráció | Nem | A naplózás immára vonatkozó részletességet adja meg. A `pii_enabled`választható konfigurációk a következők: , `log_level`amely logikai értéket vesz fel, és a , amely , `ERROR` `WARNING`, , `INFO`vagy `VERBOSE`. |

### <a name="client_id"></a>client_id

Az alkalmazás regisztrálásakor létrehozott ügyfélazonosító vagy alkalmazásazonosító.

### <a name="redirect_uri"></a>redirect_uri

Az átirányítási URI, amelyet az alkalmazás regisztrálásakor regisztrált. Ha az átirányítási URI egy bróker alkalmazás, olvassa el a [nyilvános ügyfélalkalmazások átirányítása](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) URI-t, hogy megbizonyosodjon arról, hogy a megfelelő átirányítási URI-formátumot használja a brókeralkalmazásához.

### <a name="authorities"></a>Hatóságok

Az Ön által ismert és megbízható hatóságok listája. Az itt felsorolt hatóságokon kívül az MSAL a Microsoftot is megkérdezi, hogy a Microsoft által ismert felhők és hatóságok listáját is megkapja. Ebben a hatóságlistában adja meg a hatóság típusát és az `"audience"`olyan további választható paramétereket, mint például a , amelyeknek az alkalmazás regisztrációja alapján igazodniuk kell az alkalmazás közönségéhez. Az alábbi példa a hatóságok listája:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken or acquireTokenSilent call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenantId": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>AAD-jogosultság & közönség hozzárendelése a Microsoft identity platform végpontjaihoz

| Típus | Célközönség | Bérlőazonosító | Authority_Url | Az eredményül kapott végpont | Megjegyzések |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureAD ésPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common`egy bérlői alias, ahol a fiók van. Például egy adott Azure Active Directory-bérlő vagy a Microsoft-fiókrendszer. |
| AAD | Azureadmyorg között | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Csak a contoso.com fiók a token megszerzéséhez. Bármely ellenőrzött tartomány, vagy a bérlő guid, lehet használni, mint a bérlőazonosító. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | Ezzel a végpontdal csak Azure Active Directory-fiókok használhatók. A Microsoft-fiókok szervezetek tagjai lehetnek. Ha egy szervezet ben lévő erőforrás Microsoft-fiókjával szerez jogkivonatot, adja meg azt a szervezeti bérlőt, amelytől a jogkivonatot szeretné. |
| AAD | PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | Ezt a végpontot csak Microsoft-fiókok használhatják. |
| B2C | | | Az eredményül kapott végpont megtekintése | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Csak a contoso.onmicrosoft.com bérlőben lévő fiókok szerezhetnek jogkivonatot. Ebben a példában a B2C-házirend a Hatóság URL-elérési útjának része. |

> [!NOTE]
> A hitelesítés nem engedélyezhető és nem tiltható le az MSAL-ban.
> A hatóságok vagy fejlesztőként ismertek a konfiguráción keresztül megadottak szerint, vagy a Microsoft által metaadatokon keresztül ismert.
> Ha az MSAL jogkivonat-kérelmet kap egy `MsalClientException` ismeretlen `UnknownAuthority` hatósághoz, egy típusú eredmény jelenik meg.

#### <a name="authority-properties"></a>Jogosultságtulajdonságai

| Tulajdonság | Adattípus  | Kötelező | Megjegyzések |
|-----------|-------------|-----------|--------|
| `type` | Sztring | Igen | Tükrözi az alkalmazáscélok célközönségét vagy fióktípusát. Lehetséges értékek: `AAD`,`B2C` |
| `audience` | Objektum | Nem | Csak akkor érvényes, ha a típus=`AAD`. Megadja az alkalmazás által megcélult identitást. Az alkalmazásregisztrációból származó érték használata |
| `authority_url` | Sztring | Igen | Csak akkor szükséges, ha type=`B2C`. Megadja azt a hatósági URL-címet vagy szabályzatot, amelyet az alkalmazásnak használnia kell  |
| `default` | logikai | Igen | Egy `"default":true` vagy több hatóság megadása esetén egyetlen szükséges. |

#### <a name="audience-properties"></a>Közönség tulajdonságai

| Tulajdonság | Adattípus  | Kötelező | Megjegyzések |
|-----------|-------------|------------|-------|
| `type` | Sztring | Igen | Megadja azt a célközönséget, amelyet az alkalmazás meg szeretne célozni. Lehetséges `AzureADandPersonalMicrosoftAccount`értékek: `PersonalMicrosoftAccount` `AzureADMultipleOrgs`, , ,`AzureADMyOrg` |
| `tenant_id` | Sztring | Igen | Csak akkor `"type":"AzureADMyOrg"`szükséges, ha . Más `type` értékek hez nem kötelező. Ez lehet egy bérlői `contoso.com`tartomány, például a `72f988bf-86f1-41af-91ab-2d7cd011db46`, vagy egy bérlőazonosító, például ) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Azt jelzi, hogy beágyazott webnézetet vagy az eszközön lévő alapértelmezett böngészőt használja-e, amikor bejelentkezik egy fiókba, vagy engedélyezi az erőforráshoz való hozzáférést.

Lehetséges értékek:
- `DEFAULT`: A rendszerböngészőt részesíti előnyben. A beágyazott webnézetet használja, ha a böngésző nem érhető el az eszközön.
- `WEBVIEW`: Használja a beágyazott webnézetet.
- `BROWSER`: Az eszköz alapértelmezett böngészőjét használja.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

A több nemzeti felhőt `true`támogató ügyfelek számára adja meg a . A Microsoft identity platform ezután automatikusan átirányítja a megfelelő nemzeti felhő az engedélyezés és a token-beváltás során. A bejelentkezett fiók nemzeti felhőjét a társított hatóság vizsgálatával `AuthenticationResult`határozhatja meg. Vegye figyelembe, hogy a `AuthenticationResult` nem adja meg annak az erőforrásnak a nemzeti felhőspecifikus végpontcímét, amelyhez jogkivonatot kér.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Logikai érték, amely azt jelzi, hogy microsoft identity-közvetítővel kompatibilis közvetítői átirányítási URI-t használ-e. Állítsa `false` be, ha nem szeretné használni a bróker az alkalmazáson belül.

Ha az AAD-jogosultságot úgy használja, hogy a Célközönség a beállítás, `"MicrosoftPersonalAccount"`a bróker nem lesz használva.

### <a name="http"></a>http

A HTTP-időmegtokok globális beállításainak konfigurálása, például:

| Tulajdonság | Adattípus | Kötelező | Megjegyzések |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | Nem | Idő ezredmásodpercben |
| `read_timeout` | int | Nem | Idő ezredmásodpercben |

### <a name="logging"></a>naplózás

A következő globális beállítások a naplózásra vonatkozóak:

| Tulajdonság | Adattípus  | Kötelező | Megjegyzések |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | logikai | Nem | Személyes adatok kibocsátásának e |
| `log_level`   | logikai | Nem | A kimenethez küldött naplóüzenetek |
| `logcat_enabled` | logikai | Nem | Azt jelzi, hogy a naplózási felületen kívül ki kell-e adni a macskát |

### <a name="account_mode"></a>account_mode

Itt adható meg, hogy egyszerre hány fiók használható az alkalmazáson belül. Lehetséges értékek:

- `MULTIPLE`(Alapértelmezett)
- `SINGLE`

Ha olyan `PublicClientApplication` fiókmódot hoz létre, amely nem felel meg ennek a beállításnak, az kivételt eredményez.

Az egy- és többfiók közötti különbségekről az [Egy- és többfiókos alkalmazások című témakörben](single-multi-account.md)talál további információt.

### <a name="browser_safelist"></a>browser_safelist

Az MSAL-lal kompatibilis böngészők engedélyezési listája. Ezek a böngészők helyesen kezelik az egyéni leképezések átirányításait. A listához hozzáadható. Az alapértelmezett beállítás az alábbi alapértelmezett konfigurációban található.
``
## <a name="the-default-msal-configuration-file"></a>Az alapértelmezett MSAL konfigurációs fájl

Az msal-lal szállított alapértelmezett MSAL-konfiguráció az alábbiakban látható. A legújabb verziót a [GitHubon láthatja.](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)

Ezt a konfigurációt az Ön által megadott értékek egészítik ki. A megadott értékek felülbírálják az alapértelmezett értékeket.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Példa alapkonfigurációra

A következő példa egy alapkonfigurációt mutat be, amely meghatározza az ügyfélazonosítót, az átirányítási URI-t, a ttól függ, hogy egy közvetítő átirányítás regisztrálva van-e, és a hatóságok listáját.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Konfigurációs fájl használata

1. Hozzon létre egy konfigurációs fájlt. Javasoljuk, hogy hozza létre az `res/raw/auth_config.json`egyéni konfigurációs fájlt a rendszerben. De bárhová beteheted, ahová akarod.
2. Mondja meg az MSAL-nak, hogy `PublicClientApplication`hol keresse a konfigurációt a létrehozásához. Példa:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
