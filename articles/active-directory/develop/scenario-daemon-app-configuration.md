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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a9b1a54725dff8fd7b8a6c5023bc7eb0ba84fc8
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596760"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Webes API-kat meghívó Daemon-alkalmazás – kód konfigurálása

Megtudhatja, hogyan konfigurálhatja a webes API-kat meghívó Daemon-alkalmazás kódját.

## <a name="msal-libraries-supporting-daemon-apps"></a>Daemon-alkalmazásokat támogató MSAL-kódtárak

A Daemon-alkalmazásokat támogató Microsoft-kódtárak a következők:

  MSAL-könyvtár | Leírás
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | A Daemon-alkalmazások létrehozásához támogatott platformok a .NET-keretrendszer és a .NET Core-platformok (a UWP, a Xamarin. iOS és a Xamarin. Android is, mivel ezek a platformok nyilvános ügyfélalkalmazások létrehozásához használhatók)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL. Python | Fejlesztés folyamatban – nyilvános előzetes verzió
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL. Java | Fejlesztés folyamatban – nyilvános előzetes verzió

## <a name="configuration-of-the-authority"></a>A szolgáltató konfigurációja

Mivel a démon-alkalmazások nem használnak delegált engedélyeket, de az alkalmazás engedélyei, a *támogatott fióktípus* nem lehet *fiók a szervezeti címtárban és a személyes Microsoft-fiókokban (például Skype, Xbox, Outlook.com)* . Valójában nincs bérlői rendszergazda, aki beleegyezik a Microsoft személyes fiókjaihoz tartozó Daemon-alkalmazásba. A szervezetem vagy a fiókjaim *fiókjait* *minden szervezetnél*ki kell választania.

Ezért az alkalmazás konfigurációjában megadott szolgáltatónak bérlő-ED típusúnak kell lennie (a bérlő AZONOSÍTÓját vagy a szervezethez társított tartománynevet kell megadnia).

Ha Ön ISV, és több-bérlős eszközt szeretne biztosítani, használhatja a `organizations`. Ne feledje azonban, hogy az ügyfeleket is meg kell magyaráznia, hogy hogyan adhat meg rendszergazdai jóváhagyást. További részletekért lásd: a [teljes bérlők](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) belefoglalásának kérelmezése. A MSAL esetében jelenleg is van korlátozás: `organizations` csak akkor engedélyezett, ha az ügyfél hitelesítő adatai egy alkalmazás titkos kulcsa (nem tanúsítvány).

## <a name="application-configuration-and-instantiation"></a>Alkalmazás konfigurációja és példánya

A MSAL-könyvtárakban az ügyfél hitelesítő adatai (titkos vagy tanúsítvány) a bizalmas ügyfélalkalmazás-építés paramétereként lesznek átadva.

> [!IMPORTANT]
> Még akkor is, ha az alkalmazás egy szolgáltatásként futó konzolos alkalmazás, ha az egy démon alkalmazás, amelyet bizalmas ügyfélalkalmazásnak kell lennie.

### <a name="configuration-file"></a>Konfigurációs fájl

A konfigurációs fájl határozza meg a következőket:

- a hatóság vagy a felhőalapú példány és a tenantId
- az alkalmazás regisztrációja során kapott ClientID
- ügyfél titkos kulcsa vagy tanúsítvány
 
# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

a [appSettings. JSON](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) fájlt a [.net Core Console Daemon](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) mintából.

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Vagy egy clientSecret vagy egy certificateName kell megadnia. Mindkét beállítás kizárólagos.

# <a name="pythontabpython"></a>[Python](#tab/python)

Amikor bizalmas ügyfelet hoz létre az ügyfél titkos kulcsaival, a konfigurációs fájl a [confidential_client_secret_sample. a L15-](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/0.6.1/sample/confidential_client_secret_sample.py#L4-L15) minta alapján a githubon.

```Json
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "scope": ["https://graph.microsoft.com/.default"],
    "secret": "The secret generated by AAD during your confidential app registration"
}
```

Ha tanúsítványokkal rendelkező bizalmas ügyfelet hoz létre, akkor ez a kivonat a [confidential_client_certificate_sample. a # L4 – L15](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/4b34fd660e5574eb876cfba63a1b927ae375efd6/sample/confidential_client_certificate_sample.py#L4-L15) minta a githubban.

```Json
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "scope": ["https://graph.microsoft.com/.default"],
    "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
    "private_key_file": "filename.pem"
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Itt látható a msal4j dev-mintákban használt osztály a következő minták konfigurálásához: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
public class TestData {

    final static String TENANT_SPECIFIC_AUTHORITY = "https://login.microsoftonline.com/<TenantId>/";
    final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
    final static String CONFIDENTIAL_CLIENT_ID = "";
    final static String CONFIDENTIAL_CLIENT_SECRET = "";
}
```

Vagy egy CONFIDENTIAL_CLIENT_ID vagy egy CONFIDENTIAL_CLIENT_SECRET kell megadnia. Mindkét beállítás kizárólagos.

---

### <a name="instantiation-of-the-msal-application"></a>A MSAL alkalmazás példánya

A MSAL alkalmazás létrehozásához a következőket kell tennie:

- a MSAL-csomag hozzáadása, hivatkozása vagy importálása (a nyelvtől függően)
- Ezután az építkezés eltérő lesz attól függően, hogy az ügyfél titkos kulcsait vagy tanúsítványait használja (vagy speciális forgatókönyvként, aláírt kijelentésként)

A démon alkalmazást egy `IConfidentialClientApplication` fogja megjeleníteni

#### <a name="reference-the-package"></a>A csomagra mutató hivatkozás

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Adja hozzá a [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-csomagot az alkalmazáshoz.
A MSAL.NET-ben a bizalmas ügyfélalkalmazás a `IConfidentialClientApplication` felület által reprezentált.
MSAL.NET-névtér használata a forráskódban

```CSharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import msal
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-secrets"></a>A bizalmas ügyfélalkalmazás példánya az ügyfél titkos kulcsaival

Az alábbi kód segítségével hozhatja létre a bizalmas ügyfélalkalmazás egy ügyfél titkos kulcsával:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(TestData.CONFIDENTIAL_CLIENT_SECRET))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-certificate"></a>A bizalmas ügyfélalkalmazás példányának példánya ügyféltanúsítvány-alapú alkalmazással

Az alábbi kód segítségével hozhat létre egy tanúsítványt az alkalmazáshoz:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

A MSAL. A Java-ban kétféle Build található a bizalmas ügyfélalkalmazás tanúsítványokkal történő létrehozásához:

```Java

InputStream pkcs12Certificate = ... ; /* containing PCKS12 formatted certificate*/
string certificatePassword = ... ;    /* contains the password to access the certificate */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(pkcs12Certificate, certificatePassword))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

vagy

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(rsaPrivateKey, publicKeyCertificate))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="advanced-scenario---instantiate-the-confidential-client-application-with-client-assertions"></a>Speciális forgatókönyv – a bizalmas ügyfélalkalmazás példánya az ügyfél-kijelentésekkel

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Az ügyfél titkos kulcsa vagy tanúsítványa helyett a bizalmas ügyfélalkalmazás is igazolhatja személyazonosságát az ügyfél-kijelentések használatával.

A MSAL.NET kétféle módszerrel biztosítanak aláírt állításokat a bizalmas ügyfélalkalmazás számára:

- `.WithClientAssertion()`
- `.WithClientClaims()`

@No__t_0 használatakor meg kell adnia egy aláírt JWT. Ez a speciális forgatókönyv részletesen szerepel az [ügyfél-kijelentésekben](msal-net-client-assertions.md)

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

@No__t_0 használatakor a MSAL.NET saját maga fogja kiszámítani az Azure AD által várt jogcímeket, valamint az elküldeni kívánt további ügyfél-jogcímeket tartalmazó aláírt állítást.
Az alábbi kódrészlettel végezheti el az alábbiakat:

```CSharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

További részletekért lásd: [ügyfél-kijelentések](msal-net-client-assertions.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

A MSAL Pythonban megadhatja az ügyfél jogcímeit azon jogcímek használatával, amelyeket az `ConfidentialClientApplication` titkos kulcsa aláír.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {“client_ip”: “x.x.x.x”}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Részletekért lásd: MSAL. Python dokumentációja a [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)

# <a name="javatabjava"></a>[Java](#tab/java)

a msal4j nyilvános előzetes verzióban érhető el. Az aláírt kijelentések még nem támogatottak.

---

## <a name="next-steps"></a>Következő lépések

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – jogkivonatok beszerzése az alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – jogkivonatok beszerzése az alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – jogkivonatok beszerzése az alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
