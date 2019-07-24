---
title: Daemon-alkalmazás webes API-k meghívása (alkalmazás konfigurációja) – Microsoft Identity platform
description: Megtudhatja, hogyan hozhat létre olyan Daemon-alkalmazást, amely webes API-kat hív meg (alkalmazás konfigurációja)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 705545fd5167087be1a001c45f58907d6ff225e8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277825"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Webes API-kat meghívó Daemon-alkalmazás – kód konfigurálása

Megtudhatja, hogyan konfigurálhatja a webes API-kat meghívó Daemon-alkalmazás kódját.

## <a name="msal-libraries-supporting-daemon-apps"></a>Daemon-alkalmazásokat támogató MSAL-kódtárak

A Daemon-alkalmazásokat támogató Microsoft-kódtárak a következők:

  MSAL-könyvtár | Leírás
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | A Daemon-alkalmazások létrehozásához támogatott platformok a .NET-keretrendszer és a .NET Core-platformok (a UWP, a Xamarin. iOS és a Xamarin. Android is, mivel ezek a platformok nyilvános ügyfélalkalmazások létrehozásához használhatók)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Fejlesztés folyamatban – nyilvános előzetes verzió
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Fejlesztés folyamatban – nyilvános előzetes verzió

## <a name="configuration-of-the-authority"></a>A szolgáltató konfigurációja

Mivel a démon-alkalmazások nem használnak delegált engedélyeket, de az alkalmazás engedélyei, a *támogatott fióktípus* nem lehet *fiók a szervezeti címtárban és a személyes Microsoft-fiókokban (például Skype, Xbox, Outlook.com)* . Valójában nincs bérlői rendszergazda, aki beleegyezik a Microsoft személyes fiókjaihoz tartozó Daemon-alkalmazásba. A szervezetem vagy a  fiókjaim fiókjait *minden szervezetnél*ki kell választania.

Ezért az alkalmazás konfigurációjában megadott szolgáltatónak bérlő-ED típusúnak kell lennie (a bérlő AZONOSÍTÓját vagy a szervezethez társított tartománynevet kell megadnia).

Ha Ön ISV, és több-bérlős eszközt szeretne biztosítani, használja `organizations`a következőt:. Ne feledje azonban, hogy az ügyfeleket is meg kell magyaráznia, hogy hogyan adhat meg rendszergazdai jóváhagyást. További részletekért lásd: a [teljes bérlők](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) belefoglalásának kérelmezése. Emellett a MSAL `organizations` jelenleg csak akkor engedélyezett, ha az ügyfél hitelesítő adatai egy alkalmazás titkos kulcsa (nem tanúsítvány). Lásd: [MSAL.net bug #891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>Alkalmazás konfigurációja és példánya

A MSAL-könyvtárakban az ügyfél hitelesítő adatai (titkos vagy tanúsítvány) a bizalmas ügyfélalkalmazás-építés paramétereként lesznek átadva.

> [!IMPORTANT]
> Még akkor is, ha az alkalmazás egy szolgáltatásként futó konzolos alkalmazás, ha az egy démon alkalmazás, amelyet bizalmas ügyfélalkalmazásnak kell lennie.

### <a name="msalnet"></a>MSAL.NET

Adja hozzá a [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-csomagot az alkalmazáshoz.

MSAL.NET-névtér használata

```CSharp
using Microsoft.Identity.Client;
```

A démon alkalmazást egy`IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

Az alábbi kód segítségével hozhat létre alkalmazást az alkalmazás titkos kódjával:

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Az alábbi kód segítségével hozhat létre egy tanúsítványt az alkalmazáshoz:

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

Végül, az ügyfél titka vagy tanúsítványa helyett a bizalmas ügyfélalkalmazás is igazolhatja személyazonosságát az ügyfél-kijelentések használatával. Ez a speciális forgatókönyv részletesen szerepel az [ügyfél](msal-net-client-assertions.md) -kijelentésekben


### <a name="msalpython"></a>MSAL.Python

```Python
# Create a preferably long-lived app instance which maintains a token cache.

app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache

    )
```

### <a name="msaljava"></a>MSAL.Java

```Java
PrivateKey key = getPrivateKey();
X509Certificate publicCertificate = getPublicCertificate();

// create clientCredential with public and private key
IClientCredential credential = ClientCredentialFactory.create(key, publicCertificate);

ConfidentialClientApplication cca = ConfidentialClientApplication
  .builder(CLIENT_ID, credential)
  .authority(AUTHORITY_MICROSOFT)
  .build();
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – jogkivonatok beszerzése az alkalmazáshoz](./scenario-daemon-acquire-token.md)
