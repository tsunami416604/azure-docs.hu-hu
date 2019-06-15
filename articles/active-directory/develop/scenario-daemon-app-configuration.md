---
title: Démon alkalmazás hívó webes API-kat (az alkalmazás konfigurálása) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy démon alkalmazást, hogy a hívások webes API-k (alkalmazások konfigurálása)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd2da6baecdce3ab85a45347f27f573bf814445d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055752"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Démon alkalmazások, hogy a hívások webes API-k – helykódot

Ismerje meg, hogyan konfigurálhatja a kódot, hogy a hívások webes API-kat démon alkalmazás.

## <a name="msal-libraries-supporting-daemon-apps"></a>Az MSAL kódtárak támogató démon alkalmazások

Démon alkalmazások támogatása a Microsoft-kódtárakat a következők:

  Az MSAL könyvtár | Leírás
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | A démon-alkalmazás létrehozása a támogatott platformok azok .NET-keretrendszer és a .NET Core platform (UWP-nem Xamarin.iOS és Xamarin.Android platformokhoz, használnak nyilvános ügyfél alkalmazásokat hozhat létre)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Folyamat – nyilvános előzetes verzióban érhető el a fejlesztést
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Folyamat – nyilvános előzetes verzióban érhető el a fejlesztést

## <a name="configuration-of-the-authority"></a>A szolgáltató konfigurációja

Tekintettel arra, hogy a démon alkalmazások ne használja a delegált engedélyeket, de az Alkalmazásengedélyek, azok *támogatott fióktípus* nem lehet *bármely szervezeti directory fiókok és a személyes Microsoft-fiókok () Ha például a Skype, Xbox, Outlook.com)* . Valójában nincs nincs Bérlői rendszergazda hozzájárulását a démon alkalmazás személyes Microsoft-fiókok. Válassza ki kell *fiókokat a szervezet* vagy *bármely szervezeti fiókok*.

Ezért a jogosultságokat az alkalmazás konfigurációjában megadott bérlő-ed (a bérlő Azonosítóját vagy a szervezetéhez tartozó tartománynév megadása) kell lennie.

Amennyiben, ISV, és adjon meg egy több-bérlős eszközt szeretne, használhat `organizations`. De vegye figyelembe, hogy az ügyfelek számára bemutatják, hogyan biztosítson rendszergazdai jóváhagyás is kell. Lásd: [hozzájárulás kérése egy teljes bérlő](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) részleteiről. Emellett van egy korlátozás az MSAL, amely `organizations` csak akkor engedélyezett, ha az ügyfél-hitelesítő adatok egy alkalmazás titkos kulcs (a tanúsítvány nem). Lásd: [#891 MSAL.NET hiba](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>Az alkalmazások konfigurálásával és a példányosítás

Az MSAL könyvtárak az ügyfél-hitelesítő adatok (titkos kulcs vagy tanúsítvány) a bizalmas ügyfél alkalmazás építési paraméterként ad tovább.

> [!IMPORTANT]
> Akkor is, ha az alkalmazás egy konzolalkalmazás-szolgáltatás fut, ha a démon alkalmazások kell lennie egy bizalmas ügyfélalkalmazás.

### <a name="msalnet"></a>MSAL.NET

Adja hozzá a [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) az alkalmazás NuGet-csomagot.

MSAL.NET névtér használatára

```CSharp
using Microsoft.Identity.Client;
```

A démon alkalmazás megjelenik a egy `IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

A hozhat létre egy alkalmazást egy alkalmazás titkos kód itt látható:

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

A tanúsítvány az alkalmazás létrehozását a kód itt látható:

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

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
> [Démon alkalmazások – az alkalmazás-jogkivonatok beszerzésének](./scenario-daemon-acquire-token.md)