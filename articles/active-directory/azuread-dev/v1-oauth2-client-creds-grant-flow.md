---
title: Azure AD szolgáltatás szolgáltatás hitelesítési használatával OAuth2.0 | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogy miként valósítható meg a HTTP-üzenetek a szolgáltatás hitelesítéséhez az OAuth2.0 ügyfélhitelesítő adatok megadása szolgáltatásként történő megvalósításához.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: f2d1eaec80c8925eb7b38af848e29e944f1ebf69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154542"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Szolgáltatás szolgáltatáshívások ügyfélhitelesítő adatokkal (megosztott titkos vagy tanúsítvány)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Az OAuth 2.0 ügyfélhitelesítő adatok megadása Grant Flow lehetővé teszi, hogy egy webszolgáltatás (*bizalmas ügyfél*) használja a saját hitelesítő adatait, hanem a felhasználó megszemélyesítése, hitelesítése, ha hívja egy másik webszolgáltatás. Ebben a forgatókönyvben az ügyfél általában egy középső rétegű webszolgáltatás, egy démonszolgáltatás vagy egy webhely. A magasabb szintű biztonság érdekében az Azure AD is lehetővé teszi, hogy a hívó szolgáltatás egy tanúsítványt (nem egy közös titkos kulcsot) a hitelesítő adatok.

## <a name="client-credentials-grant-flow-diagram"></a>Ügyfélhitelesítő adatok megadása –támogatási folyamatábrája
Az alábbi ábra bemutatja, hogyan működik az ügyfél hitelesítő adatok támogatási folyamat az Azure Active Directoryban (Azure AD).

![OAuth2.0 Ügyfél hitelesítő adatai – támogatási folyamat](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Az ügyfélalkalmazás hitelesíti magát az Azure AD token kiállítási végpont, és egy hozzáférési jogkivonatot kér.
2. Az Azure AD token kiállítási végpont jattaszi a hozzáférési jogkivonatot.
3. A hozzáférési jogkivonat a biztonságos erőforrás hitelesítésére szolgál.
4. A védett erőforrásból származó adatok at visszaadja az ügyfélalkalmazásnak.

## <a name="register-the-services-in-azure-ad"></a>A szolgáltatások regisztrálása az Azure AD-ben
Regisztrálja a hívó szolgáltatást és a fogadó szolgáltatást az Azure Active Directoryban (Azure AD). Részletes útmutatást az [Alkalmazások integrálása az Azure Active Directoryval című témakörben talál.](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

## <a name="request-an-access-token"></a>Hozzáférési jogkivonat kérése
Hozzáférési jogkivonat kérése, egy HTTP-posta a bérlő-specifikus Azure AD-végpont.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem
Két eset attól függően, hogy az ügyfélalkalmazás úgy dönt, hogy egy közös titok vagy egy tanúsítvány által védett.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: Hozzáférési jogkivonat-kérelem megosztott titkos titokkal
Megosztott titkos kulcs használata esetén a szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |kötelező |Megadja a kért támogatás típusát. Az ügyfélhitelesítő adatok megadása folyamatban az értéknek **client_credentials**kell lennie. |
| client_id |kötelező |A hívási webszolgáltatás Azure AD-ügyfélazonosítóját adja meg. A hívó alkalmazás ügyfélazonosítójának megkereséséhez az [Azure Portalon](https://portal.azure.com)kattintson az **Azure Active Directory**elemre, kattintson az **Alkalmazásregisztrációk**elemre, kattintson az alkalmazásra. A client_id az *alkalmazásazonosító* |
| client_secret |kötelező |Adja meg a hívó webszolgáltatás vagy démonalkalmazás az Azure AD-ben regisztrált kulcs. Kulcs létrehozásához az Azure Portalon kattintson az **Azure Active Directory**elemre, kattintson az **alkalmazásregisztrációk**elemre, kattintson az alkalmazásra, kattintson a **Beállítások**gombra, kattintson a **Kulcsok**gombra, és adjon hozzá egy kulcsot.  URL-kódolja ezt a titkot, amikor biztosítja azt. |
| Erőforrás |kötelező |Adja meg a fogadó webszolgáltatás alkalmazásazonosító-URI-ját. Az alkalmazásazonosító URI jának megkereséséhez az Azure Portalon kattintson az **Azure Active Directory**elemre, kattintson az **Alkalmazásregisztrációk**elemre, kattintson a szolgáltatásalkalmazásra, majd a **Beállítások** és **tulajdonságok**parancsra. |

#### <a name="example"></a>Példa
A következő HTTP POST hozzáférési `https://service.contoso.com/` [jogkivonatot](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) kér a webszolgáltatáshoz. A `client_id` azonosítja a hozzáférési jogkivonatot kérő webszolgáltatást.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: Hozzáférési jogkivonat-kérelem tanúsítvánnyal
A szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem tanúsítvánnyal a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |kötelező |Megadja a kért válasz típusát. Az ügyfélhitelesítő adatok megadása folyamatban az értéknek **client_credentials**kell lennie. |
| client_id |kötelező |A hívási webszolgáltatás Azure AD-ügyfélazonosítóját adja meg. A hívó alkalmazás ügyfélazonosítójának megkereséséhez az [Azure Portalon](https://portal.azure.com)kattintson az **Azure Active Directory**elemre, kattintson az **Alkalmazásregisztrációk**elemre, kattintson az alkalmazásra. A client_id az *alkalmazásazonosító* |
| client_assertion_type |kötelező |Az értéket`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |kötelező | Egy állítás (egy JSON webtoken), amelyet létre kell hoznia, és alá kell írnia az alkalmazás hitelesítő adataiként regisztrált tanúsítvánnyal. A [tanúsítvány hitelesítő adatairól megtudhatja,](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) hogyan regisztrálhatja a tanúsítványt és a feltétel formátumát.|
| Erőforrás | kötelező |Adja meg a fogadó webszolgáltatás alkalmazásazonosító-URI-ját. Az alkalmazásazonosító URI jának megkereséséhez az Azure Portalon kattintson az **Azure Active Directory**elemre, kattintson az **Alkalmazásregisztrációk**elemre, kattintson a szolgáltatásalkalmazásra, majd a **Beállítások** és **tulajdonságok**parancsra. |

Figyelje meg, hogy a paraméterek majdnem megegyeznek a megosztott titok kérésének esetében, azzal a különbséggel, hogy a client_secret paramétert két paraméter váltja fel: a client_assertion_type és a client_assertion.

#### <a name="example"></a>Példa
A következő HTTP POST hozzáférési `https://service.contoso.com/` jogkivonatot kér a tanúsítványt nyújtó webszolgáltatáshoz. A `client_id` azonosítja a hozzáférési jogkivonatot kérő webszolgáltatást.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Szolgáltatás-szolgáltatás szintű hozzáférési jogkivonat válasza

A sikeres válasz json OAuth 2.0 választ tartalmaz a következő paraméterekkel:

| Paraméter | Leírás |
| --- | --- |
| access_token |A kért hozzáférési jogkivonat. A hívó webszolgáltatás használhatja ezt a jogkivonatot a fogadó webszolgáltatás hitelesítéséhez. |
| token_type |A token típusának értékét jelzi. Az Azure AD által támogatott egyetlen típus a **bemutatóra.** A tulajdonosi jogkivonatokról további információt az [OAuth 2.0 engedélyezési keretrendszer: Tulajdonosi jogkivonat-használat (RFC 6750) című](https://www.rfc-editor.org/rfc/rfc6750.txt)témakörben talál. |
| expires_in |Mennyi ideig érvényes a hozzáférési jogkivonat (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejártának időpontja. A dátum az 1970-01-01T0:0:0Z UTC és a lejárati idő között eltelt másodpercek számaként jelenik meg. Ez az érték határozza meg a gyorsítótárazott jogkivonatok élettartamát. |
| not_before |Az az időpont, amelytől kezdve a hozzáférési jogkivonat használhatóvá válik. A dátum az 1970-01-01T0:0:0Z UTC-től a token érvényességi idejéig eltelt másodpercek számaként jelenik meg.|
| Erőforrás |A fogadó webszolgáltatás alkalmazásazonosító-URI-ja. |

#### <a name="example-of-response"></a>Példa a válaszra
A következő példa egy webszolgáltatás hozzáférési jogkivonatra vonatkozó kérelem sikeres válaszát mutatja be.

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
* [OAuth 2.0 az Azure AD-ben](v1-protocols-oauth-code.md)
* [Minta C# a szolgáltatás szolgáltatás hívás egy közös titkos és](https://github.com/Azure-Samples/active-directory-dotnet-daemon) [minta C# a szolgáltatás szolgáltatás hívás a tanúsítvány](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
