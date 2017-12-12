---
title: "Az Azure AD szolgáltatás hitelesítési OAuth2.0 használatával |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan használható a HTTP-üzenetek OAuth2.0 ügyfél hitelesítő adatok grant flow szolgáltatások közötti hitelesítés megvalósításához."
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: 
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: ad2118130ec36aed1561db763946104501eb0f32
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Ügyfél-hitelesítő (közös titkos kulcs vagy tanúsítvány) hívásokról szolgáltatás
Az OAuth 2.0 ügyfél hitelesítő adatok Grant Flow lehetővé teszi egy webszolgáltatás-bővítmény (*bizalmas ügyfél*) a saját hitelesítő adatok használata helyett a felhasználó megszemélyesítésekor, hitelesítésére, miközben egy másik webes szolgáltatás hívása. Ebben a forgatókönyvben az ügyfél nem általában egy középső rétegbeli webes szolgáltatás, a démon szolgáltatás vagy a webhely. A magasabb szintű megbízhatóság az Azure AD is lehetővé teszi a hívó szolgáltatás (és nem a közös titkos kulcs) tanúsítványt használják a hitelesítő adatokat.

## <a name="client-credentials-grant-flow-diagram"></a>Ügyfél hitelesítő adatai megadják folyamatábrája
A következő ábra bemutatja, hogyan az ügyfél hitelesítő adatait adja meg Azure Active Directory (Azure AD) működik folyamata.

![OAuth2.0 ügyfél hitelesítő adatok megadása folyamata](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Az ügyfélalkalmazás végzi el a hitelesítést az Azure AD hitelesítési karakterlánc-kiállítási végpont, és kéri a hozzáférési tokent.
2. Az Azure AD hitelesítési karakterlánc-kiállítási végpont állít ki a hozzáférési jogkivonat.
3. A hozzáférési jogkivonat a védett erőforrások felé történő hitelesítésre szolgál.
4. A védett erőforrás adatait a webes alkalmazás küld vissza.

## <a name="register-the-services-in-azure-ad"></a>A szolgáltatások regisztrálása az Azure ad-ben
A hívó szolgáltatás és a fogadó szolgáltatás regisztrálása az Azure Active Directory (Azure AD). Részletes útmutatásért lásd: [alkalmazások integrálása az Azure Active Directory](active-directory-integrating-applications.md).

## <a name="request-an-access-token"></a>A kérelem egy hozzáférési jogkivonatot:
Olyan hozzáférési jogkivonatot kérni, használja a bérlői adott HTTP POST az Azure AD-végpont.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Szolgáltatás hozzáférési jogkivonat kérelem
Attól függően, hogy az ügyfélalkalmazás úgy dönt, hogy egy közös titkot, vagy a tanúsítvány védi két esetben van.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Először. eset: egy közös titkot a hozzáférési token kérelem
Egy közös titkos kulcs használata esetén a szolgáltatás hozzáférési kérelmek tartalmazza a következő paraméterekkel:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |Szükséges |A kért grant típusát határozza meg. Az ügyfél-hitelesítő adatok Grant flow, az értéknek kell lennie **client_credentials**. |
| client_id |Szükséges |Adja meg az Azure AD a hívó webszolgáltatás ügyfél-azonosító. A hívó alkalmazás ügyfél-azonosító, a kereséséhez a [Azure-portálon](https://portal.azure.com), kattintson a **Active Directory**kapcsoló directory lehetőséget, kattintson az alkalmazás. A client_id van a *Alkalmazásazonosító* |
| client_secret |Szükséges |Adjon meg egy kulcsot, a hívó szolgáltatás vagy démon webalkalmazás az Azure ad-ben regisztrált. Az Azure-portálon a kulcs létrehozásához kattintson a **Active Directory**directory váltani, kattintson az alkalmazás, kattintson **beállítások**, kattintson a **kulcsok**, és a kulcs hozzáadása.|
| erőforrás |Szükséges |Adja meg az App ID URI az fogadó webszolgáltatás. Az Azure portálon található App ID URI, kattintson a **Active Directory**, directory váltani, kattintson a szolgáltatásalkalmazás, majd **beállítások** és **tulajdonságai** |

#### <a name="example"></a>Példa
A következő HTTP POST kérelmek egy jogkivonatot a https://service.contoso.com/ webszolgáltatáshoz. A `client_id` azonosítja a webszolgáltatás, amelyet a hozzáférési jogkivonat igényel.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>A második esetben: hozzáférési jogkivonat kérelem tanúsítvánnyal
Szolgáltatás hozzáférési kérelmek tanúsítvánnyal tartalmazza a következő paraméterekkel:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |Szükséges |A kért válaszának típusa határozza meg. Az ügyfél-hitelesítő adatok Grant flow, az értéknek kell lennie **client_credentials**. |
| client_id |Szükséges |Adja meg az Azure AD a hívó webszolgáltatás ügyfél-azonosító. A hívó alkalmazás ügyfél-azonosító, a kereséséhez a [Azure-portálon](https://portal.azure.com), kattintson a **Active Directory**kapcsoló directory lehetőséget, kattintson az alkalmazás. A client_id van a *Alkalmazásazonosító* |
| client_assertion_type |Szükséges |Az értéknek kell lennie`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Szükséges | Egy helyességi feltétel (egy JSON Web Token) hozzon létre, és írja alá a tanúsítványt igénylő regisztrálta hitelesítő adatként az alkalmazáshoz. További információ a [tanúsítvány a hitelesítő adatok](active-directory-certificate-credentials.md) megtudhatja, hogyan kell regisztrálni a tanúsítványt, és a helyességi feltétel formátuma.|
| erőforrás | Szükséges |Adja meg az App ID URI az fogadó webszolgáltatás. Az Azure portálon található App ID URI, kattintson a **Active Directory**, kattintson arra a címtárra, kattintson az alkalmazást, majd **konfigurálása**. |

Figyelje meg, hogy a paraméterek megegyeznek-szinte közös titkos kulcs kérése gazdabuszadaptereken azzal a különbséggel, hogy a client_secret paraméter helyébe két paramétert: egy client_assertion_type és client_assertion.

#### <a name="example"></a>Példa
A következő HTTP POST kérelmek egy jogkivonatot a https://service.contoso.com/ webszolgáltatás tanúsítvánnyal. A `client_id` azonosítja a webszolgáltatás, amelyet a hozzáférési jogkivonat igényel.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Szolgáltatás hozzáférési jogkivonat válasz

A sikeres válasz tartalmazza JSON OAuth 2.0 választ a következő paraméterekkel:

| Paraméter | Leírás |
| --- | --- |
| access_token |A kért hozzáférési jogkivonat. A hívó webszolgáltatás a jogkivonat segítségével hitelesíti a fogadó webszolgáltatás. |
| token_type |A jogkivonat típusa értékét jelöli. Az egyetlen típus, amely az Azure AD által támogatott **tulajdonosi**. Tulajdonosi jogkivonatok kapcsolatos további információkért tekintse meg a [OAuth 2.0 hitelesítési keretrendszer: tulajdonosi jogkivonat használati (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejárati idejének. A dátum jelzi másodpercben a 1970-01-01T0:0:0Z UTC, amíg az elévülési időt. Ezt az értéket a gyorsítótárazott jogkivonatok élettartama meghatározására szolgál. |
| not_before |Az időt, amelyből a hozzáférési jogkivonat lesz használható. A dátum jelzi másodpercben a 1970-01-01T0:0:0Z UTC, amíg a token érvényességi ideje.|
| erőforrás |A fogadó webszolgáltatás App ID URI. |

#### <a name="example-of-response"></a>Válasz – példa
A következő példa bemutatja egy olyan hozzáférési jogkivonatot webszolgáltatásnak kérelemre sikerességi válasz.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Lásd még:
* [OAuth 2.0 Azure AD-ben](active-directory-protocols-oauth-code.md)
* [A szolgáltatások közötti hívás egy közös titkot a C# minta](https://github.com/Azure-Samples/active-directory-dotnet-daemon) és [C# nyelven íródtak a szolgáltatások közötti hívás tanúsítvánnyal minta](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
