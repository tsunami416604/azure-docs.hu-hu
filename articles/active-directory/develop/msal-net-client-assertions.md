---
title: Ügyfél állítások (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Információ a bizalmas ügyfélalkalmazások aláírt ügyfélállítások támogatásáról a Microsoft Authentication Library for .NET (MSAL.NET) alkalmazásban.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8c97387bfd2a362d3bf5a6b8a3252242f061da31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050291"
---
# <a name="confidential-client-assertions"></a>Bizalmas ügyfélállítások

Annak érdekében, hogy személyazonosságukat, bizalmas ügyfélalkalmazások cseréje egy titkos Azure AD. A titok lehet:
- Ügyféltitok (alkalmazásjelszó).
- Szabványos jogcímeket tartalmazó aláírt állítások létrehozásához használt tanúsítvány.

Ez a titok közvetlenül is aláírható állítás lehet.

MSAL.NET négy módszerrel rendelkezik a bizalmas ügyfélalkalmazás hitelesítő adatainak vagy érvényesítésének biztosításához:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Bár lehetséges az `WithClientAssertion()` API-t a bizalmas ügyfél jogkivonatai beszerzésére, nem javasoljuk, hogy alapértelmezés szerint használja, mivel fejlettebb, és nagyon specifikus forgatókönyvek kezelésére tervezték, amelyek nem gyakoriak. Az `.WithCertificate()` API használatával MSAL.NET kezelni ezt az Ön számára. Ez az api lehetővé teszi a hitelesítési kérelem testreszabását, ha szükséges, de a legtöbb hitelesítési forgatókönyvhez elegendő lesz a hitelesítési `.WithCertificate()` esetek ben létrehozott alapértelmezett állítás. Ez az API is használható kerülő megoldásként bizonyos esetekben, amikor MSAL.NET nem hajtja végre az aláírási művelet belsőleg.

### <a name="signed-assertions"></a>Aláírt állítások

Az aláírt ügyfél-kijelentés egy aláírt JWT formájában, amely tartalmazza az Azure AD által kódolt, szükséges hitelesítési jogcímeket tartalmazó hasznos adat. A használatához:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Az Azure AD által várt jogcímek a következők:

Jogcím típusa | Érték | Leírás
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | Az "aud" (közönség) jogcím azonosítja azokat a címzetteket, akiknek a JWT-t szánják (itt Azure AD) Lásd [RFC 7519, 4.1.3 szakasz]
Exp | Cs Jún 27 2019 15:04:17 GMT+0200 (román nyári idő) | Az "exp" (lejárati idő) jogcím azonosítja a lejárati időt, vagy amely után a JWT nem fogadható el a feldolgozásra. Lásd [RFC 7519, 4.1.4. szakasz]
Iss | {Ügyfélazonosító} | Az "iss" (kibocsátó) jogcím azonosítja a JWT-t kibocsátó főkötelezettet. A jogcím feldolgozása alkalmazásspecifikus. Az "iss" érték stringoruri értéket tartalmazó kis- és nagybetűket megkülönböztető karakterlánc. [RFC 7519, 4.1.1. szakasz]
Jti | (Guid) | A "jti" (JWT-azonosító) jogcím a JWT egyedi azonosítóját tartalmazza. Az azonosító értéket úgy kell hozzárendelni, hogy biztosítsa, hogy elhanyagolható a valószínűsége annak, hogy ugyanazt az értéket véletlenül egy másik adatobjektumhoz rendelik; ha az alkalmazás több kibocsátót használ, az ütközéseket meg kell akadályozni a különböző kibocsátók által előállított értékek között is. A "jti" követelés felhasználható a JWT visszajátszásának megakadályozására. A "jti" érték egy kis- és nagybetűket megkülönböztető karakterlánc. [RFC 7519, 4.1.7. szakasz]
nbf | Cs Jún 27 2019 14:54:17 GMT+0200 (román nyári idő) | Az "nbf" (nem korábban) állítás azonosítja azt az időt, amely előtt a JWT nem fogadható el feldolgozásra. [RFC 7519, 4.1.5. szakasz]
Al | {Ügyfélazonosító} | A "sub" (tárgy) állítás azonosítja a JWT tárgyát. A JWT-ben található állítások általában a témával kapcsolatos állítások. A tárgy értékét vagy úgy kell hatókörrel tekinteni, hogy a kibocsátó kontextusában helyileg egyedi legyen, vagy globálisan egyedinek kell lennie. Lásd a [7519. számú RFC 4.1.2. szakasza]

Íme egy példa arra, hogyan kell ezeket az állításokat megalkotni:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

Itt van, hogyan kézműves aláírt ügyfél állítás:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Alternatív módszer

Lehetősége van arra is, hogy a [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) használatával hozza létre a helyestnlásdmert. A kód lesz egy elegánsabb, amint az alábbi példában:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

Miután az aláírt ügyfél állítás, akkor használja azt az MSAL apis az alábbiak szerint.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>Ügyféljogcímek

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`alapértelmezés szerint egy aláírt feltételt hoz létre, amely tartalmazza az Azure AD által várt jogcímeket, valamint a további ügyféljogcímeket, amelyeket el szeretne küldeni. Itt van egy kód részlet, hogyan kell csinálni.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Ha a szótárban található jogcímek egyike megegyezik a kötelező jogcímek egyikével, a kiegészítő jogcím értékét veszi figyelembe a jogcím értéke. Felülírja a MSAL.NET által kiszámított jogcímeket.

Ha azt szeretné, hogy a saját jogcímek, beleértve az `false` Azure `mergeWithDefaultClaims` AD által várt kötelező jogcímek, adja át a paraméter.
