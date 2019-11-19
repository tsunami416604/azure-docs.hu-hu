---
title: Ügyfél-kijelentések a .NET-hez készült Microsoft Authentication Library-ben
titleSuffix: Microsoft identity platform
description: Ismerje meg a .NET-hez készült Microsoft Authentication Library (MSAL.NET) szolgáltatásban a bizalmas ügyfélalkalmazások támogatásához aláírt ügyfél-kijelentéseket.
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
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66ff02e4c95594f0155ab31e3c99a0eb269626d9
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168123"
---
# <a name="confidential-client-assertions"></a>Bizalmas ügyfél-kijelentések

Személyazonosságuk igazolása érdekében a bizalmas ügyfélalkalmazások az Azure AD-vel titkos kulcsot cserélnek. A titok a következőket teheti:
- Az ügyfél titkos kulcsa (alkalmazás jelszava).
- Egy tanúsítvány, amely egy standard jogcímeket tartalmazó aláírt jogcím összeállítására szolgál.

Ez a titok közvetlenül is aláírható.

A MSAL.NET négy módszerrel biztosíthatja a bizalmas ügyfélalkalmazás számára a hitelesítő adatokat vagy kijelentéseket:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Habár lehetséges, hogy a `WithClientAssertion()` API-t használja a bizalmas ügyfélhez tartozó jogkivonatok beszerzéséhez, azt nem javasoljuk, hogy alapértelmezés szerint ne használja, mert ez fejlettebb, és a nagyon konkrét, nem gyakori forgatókönyvek kezelésére szolgál. A `.WithCertificate()` API használata lehetővé teszi, hogy a MSAL.NET kezelje ezt. Ez az API lehetővé teszi, hogy szükség esetén testre szabja a hitelesítési kérelmet, de a `.WithCertificate()` által létrehozott alapértelmezett állítások a legtöbb hitelesítési forgatókönyv esetén elegendőek lesznek. Ez az API megkerülő megoldásként is használható olyan helyzetekben, ahol a MSAL.NET nem tudja belsőleg végrehajtani az aláírási műveletet.

### <a name="signed-assertions"></a>Aláírt kijelentések

Az aláírt ügyfél egy aláírt JWT formáját ölti az Azure AD által megkövetelt, Base64 kódolású, szükséges hitelesítési jogcímeket tartalmazó adattartalommal. A használatához:

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Az Azure AD által várt jogcímek a következők:

Jogcím típusa | Érték | Leírás
---------- | ---------- | ----------
aud | https://login.microsoftonline.com/{tenantId}/v2.0 | Az "AUD" (célközönség) jogcím azonosítja azokat a címzetteket, amelyeket a JWT szántak (itt az Azure AD) lásd: [RFC 7519, 4.1.3. szakasz].
exp | Thu Jun 27 2019 15:04:17 GMT + 0200 (romantikus nyári idő) | Az "exp" (lejárati idő) jogcím azt a lejárati időt azonosítja, amely után a JWT nem fogadható el feldolgozásra. Lásd: [RFC 7519, 4.1.4. szakasz]
ISS | ClientID | Az "ISS" (kibocsátói) jogcím azonosítja a JWT kiállító rendszerbiztonsági tag. A jogcím feldolgozása alkalmazásspecifikus. Az "ISS" érték egy kis-és nagybetűket megkülönböztető karakterlánc, amely egy StringOrURI értéket tartalmaz. [RFC 7519, 4.1.1. szakasz]
JTI | (GUID) | A "kezdeményezés" (JWT ID) jogcím egyedi azonosítót biztosít a JWT számára. Az azonosító értékét olyan módon kell hozzárendelni, amely biztosítja, hogy az adott érték egy másik adatobjektumhoz is legyen véletlenül hozzárendelve. Ha az alkalmazás több kiállítót használ, az ütközéseket meg kell akadályozni a különböző kibocsátók által előállított értékek között. A "kezdeményezés" jogcímet arra használhatja, hogy megakadályozza a JWT lejátszását. A "kezdeményezés" érték kis-és nagybetűket megkülönböztető karakterlánc. [RFC 7519, szakasz 4.1.7]
nbf | Thu Jun 27 2019 14:54:17 GMT + 0200 (romantikus nyári idő) | A "NBF" (nem korábban) jogcím azt az időpontot határozza meg, ameddig a JWT nem fogadható el a feldolgozáshoz. [RFC 7519, szakasz 4.1.5]
sub | ClientID | A "Sub" (tárgy) jogcím azonosítja a JWT tárgyát. A JWT lévő jogcímek általában a tárgyra vonatkozó utasítások. A tulajdonos értékének hatóköre csak helyileg egyedi lehet a kiállító kontextusában, vagy globálisan egyedinek kell lennie. A lásd: [RFC 7519, 4.1.2. szakasz]

Íme egy példa a jogcímek kiépítésére:

```CSharp
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

Az alábbi lépésekből megtudhatja, hogyan lehet aláírt ügyfél-kijelentést készíteni:

```CSharp
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

Lehetősége van arra is, hogy a [Microsoft. IdentityModel. JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) használatával hozza létre az állítást. A kód az alábbi példában látható módon elegáns lesz:

```CSharp
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

Ha már rendelkezik az aláírt ügyfél-állítással, használhatja a MSAL API-kkal az alább látható módon.

```CSharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

a `WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` alapértelmezés szerint az Azure AD által várt jogcímeket, valamint az elküldeni kívánt további ügyfelek jogcímeit tartalmazó aláírt állítást hoz létre. Az alábbi kódrészlettel teheti ezt meg.

```CSharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Ha a bejelentkezett szótár egyik jogcíme megegyezik a kötelező jogcímek egyikével, a rendszer a további jogcím értékét is figyelembe veszi. Felülbírálja a MSAL.NET által kiszámított jogcímeket.

Ha meg szeretné adni saját jogcímeit, beleértve az Azure AD által várt kötelező jogcímeket, adja meg `false` a `mergeWithDefaultClaims` paraméter számára.
