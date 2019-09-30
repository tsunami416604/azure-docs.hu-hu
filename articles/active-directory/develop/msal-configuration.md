---
title: Az androidos Microsoft Authentication Library (MSAL) konfigurációs fájljának ismertetése | Azure
description: Az androidos Microsoft Authentication Library (MSAL) konfigurációs fájl áttekintése, amely a Azure Active Directory alkalmazás konfigurációját jelöli.
services: active-directory
documentationcenter: ''
author: shoatman
manager: nadima
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2851adf14f5ccaec576a325cefcef8523be03bc
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679749"
---
# <a name="android-microsoft-authentication-library-msal-configuration-file"></a>Androidos Microsoft Authentication Library (MSAL) konfigurációs fájl

A MSAL egy [alapértelmezett konfigurációs JSON-fájllal](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) rendelkezik, amelyet testreszabva meghatározhatja a nyilvános ügyfélalkalmazás viselkedését olyan dolgokhoz, mint például az alapértelmezett szolgáltató, amelyet használni fog, és így tovább.

Ez a cikk segítséget nyújt a konfigurációs fájl különböző beállításainak megismerésében és a MSAL-alapú alkalmazásban használandó konfigurációs fájl megadásában.

## <a name="configuration-settings"></a>Konfigurációs beállítások

### <a name="general-settings"></a>Általános beállítások

| Tulajdonság | Adattípus | Szükséges | Megjegyzések |
|-----------|------------|-------------|-------|
| `client_id` | Sztring | Igen | Az alkalmazás ügyfél-azonosítója az [alkalmazás regisztrációs oldaláról](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | Sztring | Igen | Az [alkalmazás-regisztrációs oldalának](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) átirányítási URI-ja |
| `authorities` | @ No__t-0Authority > listázása | Nem | Az alkalmazás által igényelt hatóságok listája |
| `authorization_user_agent` | AuthorizationAgent (enumerálás) | Nem | Lehetséges értékek: `DEFAULT`, `BROWSER`, `WEBVIEW` |
| `http` | HttpConfiguration | Nem | @No__t-0 `connect_timeout` és `read_timeout` konfigurálása |
| `logging` | LoggingConfiguration | Nem | Meghatározza a naplózás részletességi szintjét. A választható konfigurációk a következők: `pii_enabled`, amely logikai értéket vesz igénybe, és `log_level`, amely `ERROR`, `WARNING`, `INFO` vagy `VERBOSE` értéket vesz igénybe. |

### <a name="client_id"></a>client_id

Az alkalmazás regisztrálásakor létrehozott ügyfél-azonosító vagy alkalmazás-azonosító.

### <a name="redirect_uri"></a>redirect_uri

Az alkalmazás regisztrálásakor regisztrált átirányítási URI. Ha az átirányítási URI egy Broker-alkalmazáshoz tartozik, akkor a [nyilvános ügyfélalkalmazások átirányítási URI](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) -ja című témakörben találja, hogy a megfelelő átirányítási URI-formátumot használja-e a Broker-alkalmazáshoz.

### <a name="authorities"></a>hatóságok

Az Ön által ismert és megbízható hitelesítésszolgáltatók listája. Az itt felsorolt hatóságok mellett a MSAL is lekérdezi a Microsoftot, hogy lekérje a Microsoft számára ismert felhők és hatóságok listáját. A hatóságok ezen listájában adja meg a szolgáltató típusát és a további választható paramétereket (például `"audience"`), amelyeknek az alkalmazás regisztrációja alapján kell összehangolni az alkalmazás célközönségével. A következő példa a hatóságok listáját tartalmazza:

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

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>A HRE-szolgáltató & célközönségének leképezése a Microsoft Identity platform-végpontokra

| Type | Célközönség | Bérlőazonosító | Authority_Url | Eredményül kapott végpont | Megjegyzések |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | https://login.microsoftonline.com/common | a `common` egy bérlői alias, ahol a fiók. Például egy adott Azure Active Directory bérlő vagy a Microsoft-fiók rendszer. |
| AAD | AzureADMyOrg | contoso.com | | https://login.microsoftonline.com/contoso.com | Csak a contoso.com lévő fiókok kaphatnak jogkivonatot. A bérlői AZONOSÍTÓként bármely ellenőrzött tartomány vagy bérlői GUID is használható. |
| AAD | AzureADMultipleOrgs | | | https://login.microsoftonline.com/organizations | Ehhez a végponthoz csak Azure Active Directory fiókok használhatók. A Microsoft-fiókok lehetnek szervezetek tagjai. Ha egy szervezet erőforrásának Microsoft-fiók használatával szeretne tokent beszerezni, akkor határozza meg azt a szervezeti bérlőt, amelyről a jogkivonatot szeretné használni. |
| AAD | PersonalMicrosoftAccount | | | https://login.microsoftonline.com/consumers | Csak a Microsoft-fiókok használhatják ezt a végpontot. |
| B2C | | | Eredményül kapott végpont | https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/ | Csak a contoso.onmicrosoft.com-bérlőben lévő fiókok kaphatnak jogkivonatot. Ebben a példában a B2C-szabályzat a szolgáltatói URL elérési útjának részét képezi. |

> [!NOTE]
> A MSAL nem engedélyezhető és nem tiltható le a hitelesítésszolgáltató ellenőrzése.
> A hatóságok vagy ismertek a konfiguráción vagy a Microsoft által a metaadatokon keresztül ismert fejlesztőként.
> Ha a MSAL egy jogkivonatra vonatkozó kérelmet kap egy ismeretlen szolgáltatónak, `UnknownAuthority` típusú eredmény `MsalClientException`.

#### <a name="authority-properties"></a>Szolgáltató tulajdonságai

| Tulajdonság | Adattípus  | Szükséges | Megjegyzések |
|-----------|-------------|-----------|--------|
| `type` | Sztring | Igen | A célközönség vagy fiók típusának tükrözése az alkalmazás céljaira. Lehetséges értékek: `AAD`, `B2C` |
| `audience` | Object | Nem | Csak akkor érvényes, ha a Type = `AAD` értéket adja meg. Megadja az alkalmazás céljainak identitását. Az alkalmazás regisztrációs értékének használata |
| `authority_url` | Sztring | Igen | Csak akkor szükséges, ha Type = `B2C`. Megadja az alkalmazás által használandó szolgáltatói URL-címet vagy szabályzatot  |
| `default` | boolean | Igen | Legalább egy `"default":true` szükséges, ha egy vagy több hatóság van megadva. |

#### <a name="audience-properties"></a>Célközönség tulajdonságai

| Tulajdonság | Adattípus  | Szükséges | Megjegyzések |
|-----------|-------------|------------|-------|
| `type` | Sztring | Igen | Meghatározza azt a célközönséget, amelyet az alkalmazás szeretne megcélozni. Lehetséges értékek: `AzureADandPersonalMicrosoftAccount`, `PersonalMicrosoftAccount`, `AzureADMultipleOrgs`, `AzureADMyOrg` |
| `tenant_id` | Sztring | Igen | Csak akkor szükséges, ha `"type":"AzureADMyOrg"`. Más @no__t – 0 érték esetén nem kötelező. Ez lehet egy bérlői tartomány, például `contoso.com`, vagy egy bérlői azonosító, például `72f988bf-86f1-41af-91ab-2d7cd011db46`) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Azt jelzi, hogy egy fiókba való bejelentkezéskor vagy az erőforráshoz való hozzáférés engedélyezésekor a rendszer a beágyazott webnézetet vagy az eszköz alapértelmezett böngészőjét használja-e.

Lehetséges értékek:
- `DEFAULT`: A rendszerböngészőt részesíti előnyben. A a beágyazott webes nézetet használja, ha egy böngésző nem érhető el az eszközön.
- `WEBVIEW`: A beágyazott webes nézet használata.
- `BROWSER`: Az eszköz alapértelmezett böngészőjét használja.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

Több nemzeti felhőt támogató ügyfelek esetén a `true` értéket kell megadnia. A Microsoft Identity platform ezután automatikusan átirányítja a megfelelő nemzeti felhőbe az engedélyezés és a token beváltásakor. A bejelentkezett fiók nemzeti felhőjét a `AuthenticationResult` társított hatóság vizsgálatával határozhatja meg. Vegye figyelembe, hogy a `AuthenticationResult` nem adja meg azon erőforrás országos Felhőbeli végpontjának a számát, amelyhez jogkivonatot kér.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Logikai érték, amely azt jelzi, hogy a Microsoft Identity Broker kompatibilis-e a Broker átirányítási URI-ja. Állítsa `false` értékre, ha nem szeretné használni a közvetítőt az alkalmazáson belül.

Ha a HRE-szolgáltatót `"MicrosoftPersonalAccount"` értékre állítja, akkor a közvetítő nem lesz használatban.

### <a name="http"></a>http

Konfigurálja a HTTP-időtúllépések globális beállításait, például:

| Tulajdonság | Adattípus | Szükséges | Megjegyzések |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | Nem | Idő ezredmásodpercben |
| `read_timeout` | int | Nem | Idő ezredmásodpercben |

### <a name="logging"></a>Naplózás

A következő globális beállítások állnak a naplózásra:

| Tulajdonság | Adattípus  | Szükséges | Megjegyzések |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | Nem | Személyes adatkibocsátás |
| `log_level`   | boolean | Nem | A kimenetre küldött naplófájlok |
| `logcat_enabled` | boolean | Nem | Azt jelzi, hogy a log Cat kimenete a naplózási felületen kívül |

### <a name="account_mode"></a>account_mode

Meghatározza, hogy egyszerre hány fiókot lehet használni az alkalmazáson belül. A lehetséges értékek a következők:

- @no__t – 0 (alapértelmezett)
- `SINGLE`

Egy `PublicClientApplication` létrehozása olyan fiók mód használatával, amely nem felel meg ennek a beállításnak, kivételt fog eredményezni.

További információ az egy-és több fiók közötti különbségekről: [egyetlen és több fiókból származó alkalmazás](single-multi-account.md).

### <a name="browser_safelist"></a>browser_safelist

A MSAL kompatibilis böngészők engedélyezési listája. Ezek a böngészők megfelelően kezelik az átirányításokat az egyéni leképezésekhez. Ehhez a listához adhat hozzá. Az alapértelmezett beállítás az alábbi alapértelmezett konfigurációban van megadva.
``
## <a name="the-default-msal-configuration-file"></a>Az alapértelmezett MSAL-konfigurációs fájl

A MSAL által szállított alapértelmezett MSAL-konfiguráció alább látható. A legújabb verziót a [githubon](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)tekintheti meg.

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
## <a name="example-basic-configuration"></a>Egyszerű konfiguráció – példa

Az alábbi példa egy alapszintű konfigurációt mutat be, amely meghatározza az ügyfél-azonosítót, az átirányítási URI-t, azt, hogy regisztrálva van-e a közvetítői átirányítás, valamint a hatóságok listája

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

1. Hozzon létre egy konfigurációs fájlt. Javasoljuk, hogy hozza létre az egyéni konfigurációs fájlt `res/raw/auth_config.json`-ban. De tetszőleges helyre helyezheti.
2. Adja meg a MSAL, hogy hol érdemes megkeresni a konfigurációt a `PublicClientApplication` összeállításakor. Példa:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
