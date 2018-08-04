---
title: Azure AD szolgáltatások közötti hitelesítés használatával OAuth2.0 |} A Microsoft Docs
description: Ez a cikk ismerteti a HTTP-üzenetek használata a szolgáltatások közötti hitelesítés a OAuth2.0 ügyfél-hitelesítő adatok megadási folyamatában használatával megvalósításához.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8a331aab32d8bc662026d49e16f63224fbbf6d41
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503032"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Szolgáltatások közötti hívások ügyfél-hitelesítő adatok (közös titkos kulcsot vagy tanúsítvány) használatával
Az OAuth 2.0 ügyfél hitelesítő adatok engedélyezési folyamatát engedélyezi a webszolgáltatás (*bizalmas ügyfél*) a saját hitelesítő adatait használja a felhasználó megszemélyesítése helyett egy másik webszolgáltatás hívásakor. Ebben a forgatókönyvben az ügyfél nem általában egy középső rétegű webszolgáltatás, démonszolgáltatás vagy webhely. A magasabb szintű megbízhatóságra Azure ad-ben is lehetővé teszi a hívó szolgáltatás (és nem egy közös titkos kulcsot) egy tanúsítvány használandó hitelesítő adatokat.

## <a name="client-credentials-grant-flow-diagram"></a>Ügyfél hitelesítő adatainak megadása folyamatábrája
Az alábbi diagram bemutatja, hogyan adhat az ügyfél-hitelesítő adatok a flow működését az Azure Active Directoryban (Azure AD).

![Ügyfél OAuth2.0 adatok megadási folyamatában](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Az ügyfélalkalmazás végzi el a hitelesítést az Azure AD-kiállítási végpont, és a egy hozzáférési jogkivonatot kér.
2. Az Azure AD-kiállítási végpont kiállítja a hozzáférési jogkivonatot.
3. A hozzáférési jogkivonat segítségével hitelesíti a védett erőforráshoz.
4. Az ügyfélalkalmazás adják vissza az adatokat a védett erőforrás.

## <a name="register-the-services-in-azure-ad"></a>A szolgáltatások regisztrálása az Azure ad-ben
A hívó és a fogadó szolgáltatást is regisztrálni az Azure Active Directoryban (Azure AD). Részletes útmutatásért lásd: [alkalmazások integrálása az Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="request-an-access-token"></a>Hozzáférési jogkivonat kérése
Egy hozzáférési jogkivonatot kér, használja egy HTTP POST-bérlő-specifikus Azure AD-végpont.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Szolgáltatások közötti hozzáférési jogkivonat kérése
Nincsenek két esetben attól függően, hogy az ügyfélalkalmazás úgy dönt, hogy egy közös titkos kulcsot, vagy a tanúsítvány védi.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Először. eset: egy közös titkos kulcsot a hozzáférési jogkivonat kérése
A közös titkos kulcsot használja, amikor egy szolgáltatások közötti hozzáférési jogkivonat kérése a következő paramétereket tartalmaz:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges |Adja meg a kért engedélyezési típus. Ügyfélhitelesítő adatok folyamatokban értéke nem lehet **client_credentials**. |
| client_id |szükséges |Itt adhatja meg az Azure AD a hívó webszolgáltatás ügyfél-azonosító. A hívó alkalmazás ügyfél-azonosító, ebben található az a [az Azure portal](https://portal.azure.com), kattintson a **Azure Active Directory**, kattintson a **alkalmazásregisztrációk**, arra az alkalmazásra. A client_id van a *alkalmazás azonosítója* |
| client_secret |szükséges |Adjon meg egy kulcsot, a hívó webes szolgáltatás- vagy démonalkalmazásban alkalmazás Azure AD-ben regisztrált. Hozzon létre egy kulcsot az Azure Portalon, kattintson a **Azure Active Directory**, kattintson a **alkalmazásregisztrációk**, kattintson az alkalmazást, majd **beállítások**, kattintson a **kulcsok** , és adjon hozzá egy kulcsot.  URL-kódolása ebben titkos megadásakor azt. |
| erőforrás |szükséges |Adja meg az Alkalmazásazonosító URI-t a fogadó webszolgáltatás. Az Alkalmazásazonosító URI-t, az Azure Portalon kattintson **Azure Active Directory**, kattintson a **alkalmazásregisztrációk**, kattintson a szolgáltatásalkalmazás, majd **beállítások** és  **Tulajdonságok**. |

#### <a name="example"></a>Példa
A következő HTTP POST kéréseket a hozzáférési jogkivonatot a https://service.contoso.com/ webes szolgáltatás. A `client_id` azonosítja a webszolgáltatás, amelyet a hozzáférési jogkivonatot kér.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>A második eset: a tanúsítványhoz a hozzáférési jogkivonat kérése
A service to service hozzáférési jogkivonat kérése tanúsítvánnyal az alábbi paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges |Adja meg a kért választípus. Ügyfélhitelesítő adatok folyamatokban értéke nem lehet **client_credentials**. |
| client_id |szükséges |Itt adhatja meg az Azure AD a hívó webszolgáltatás ügyfél-azonosító. A hívó alkalmazás ügyfél-azonosító, ebben található az a [az Azure portal](https://portal.azure.com), kattintson a **Azure Active Directory**, kattintson a **alkalmazásregisztrációk**, arra az alkalmazásra. A client_id van a *alkalmazás azonosítója* |
| client_assertion_type |szükséges |Az értéknek kell lennie `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |szükséges | Egy helyességi feltétel (egy JSON Web Token) létrehozására és aláírására a tanúsítványt igénylő regisztrált hitelesítő adatként az alkalmazáshoz. További információ [hitelesítő tanúsítvány](active-directory-certificate-credentials.md) megtudhatja, hogyan regisztrálhat a tanúsítvány és a helyességi feltétel formátumát.|
| erőforrás | szükséges |Adja meg az Alkalmazásazonosító URI-t a fogadó webszolgáltatás. Az Alkalmazásazonosító URI-t, az Azure Portalon kattintson **Azure Active Directory**, kattintson a **alkalmazásregisztrációk**, kattintson a szolgáltatásalkalmazás, majd **beállítások** és  **Tulajdonságok**. |

Figyelje meg, hogy paraméterei szinte teljesen megegyezik a kérés által közös titkos kulcsot is azzal a különbséggel, hogy a titkos ügyfélkódot paraméter váltotta fel két paramétert: egy client_assertion_type és client_assertion.

#### <a name="example"></a>Példa
A következő HTTP POST kéréseket a hozzáférési jogkivonatot a https://service.contoso.com/ webszolgáltatás-tanúsítvánnyal. A `client_id` azonosítja a webszolgáltatás, amelyet a hozzáférési jogkivonatot kér.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Szolgáltatások – hozzáférési Token válasz

Sikerességi válasz tartalmaz egy JSON OAuth 2.0-válaszban az alábbi paraméterekkel:

| Paraméter | Leírás |
| --- | --- |
| access_token |A kért hozzáférési jogkivonatot. A hívó webszolgáltatás a jogkivonat használatával hitelesítik magukat a fogadó webszolgáltatás. |
| token_type |Typ tokenu értékét jelöli. Az egyetlen típus, amely az Azure AD által támogatott **tulajdonosi**. További információ a tulajdonosi jogkivonatokat: A [OAuth 2.0 engedélyezési keretrendszer: tulajdonosi jogkivonat-használat (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejáratának időpontja. A dátum jelenik meg a másodpercek számát, 1970-01-01T0:0:0Z UTC a lejárati időpontig. Ez az érték a gyorsítótárazott jogkivonatok élettartama meghatározására szolgál. |
| not_before |Az időt, amelyből a hozzáférési jogkivonat használhatóvá válik. A dátum jelenik meg a másodpercek számát, 1970-01 – amíg a token érvényességi ideje (UTC) 01T0:0:0Z.|
| erőforrás |Az Alkalmazásazonosító URI-t a fogadó webszolgáltatás. |

#### <a name="example-of-response"></a>Példa a válasz
Az alábbi példa bemutatja egy web Service hozzáférési jogkivonatot kér sikeres válasz.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Lásd még
* [OAuth 2.0, Azure AD-ben](active-directory-protocols-oauth-code.md)
* [A szolgáltatások közötti hívás az a közös titkos kulcsot a C# minta](https://github.com/Azure-Samples/active-directory-dotnet-daemon) és [minta C#-tanúsítvánnyal a szolgáltatások közötti hívások](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
