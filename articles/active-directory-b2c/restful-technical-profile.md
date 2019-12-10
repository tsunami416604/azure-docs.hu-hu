---
title: REST-technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Megadhat egy REST-es technikai profilt egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa14854807727506f5d697d7871c97e219c096a3
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950884"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>REST-technikai profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt a saját REST-szolgáltatásához. Azure AD B2C adatokat küld a REST-szolgáltatásnak a bemeneti jogcímek gyűjteményében, és visszafogadja az adatokat a kimeneti jogcímek gyűjteményében. A REST-szolgáltatás integrációja révén a következőket teheti:

- **Felhasználói bemeneti adatok érvényesítése** – megakadályozza, hogy a helytelenül formázott adatok megmaradjanak Azure ad B2Cba. Ha a felhasználó értéke érvénytelen, a REST-szolgáltatás egy hibaüzenetet ad vissza, amely arra utasítja a felhasználót, hogy adjon meg egy bejegyzést. Ellenőrizheti például, hogy a felhasználó által megadott e-mail-cím szerepel-e az ügyfél adatbázisában.
- **Bemeneti jogcímek felülírása** – lehetővé teszi a bemeneti jogcímek értékeinek újraformázását. Ha például egy felhasználó az első nevet adja meg az összes kisbetűs vagy az összes nagybetűvel, a nevet csak az első nagybetűvel formázhatja.
- A felhasználói adatmennyiség **gazdagítása** – lehetővé teszi a vállalati üzletági alkalmazások további integrálását. A REST-szolgáltatás például megkaphatja a felhasználó e-mail-címét, lekérdezheti az ügyfél adatbázisát, és visszaküldheti a felhasználó hűségi számát Azure AD B2Cra. A visszatérési jogcímek a következő hangolási lépések során, vagy a hozzáférési jogkivonatban is tárolhatók.
- **Egyéni üzleti logika futtatása** – lehetővé teszi leküldéses értesítések küldését, vállalati adatbázisok frissítését, felhasználói áttelepítési folyamat futtatását, az engedélyek felügyeletét, az adatbázisok naplózását és egyéb műveletek végrehajtását.

Előfordulhat, hogy a házirend bemeneti jogcímeket küld a REST APInak. A REST API olyan kimeneti jogcímeket is tartalmazhat, amelyeket később használhat a házirendben, vagy egy hibaüzenetet is tud adni. A REST-szolgáltatásokkal való integrációt a következő módokon lehet megtervezni:

- **Érvényesítési technikai profil** – egy érvényesítési műszaki profil meghívja a REST-szolgáltatást. Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott, a felhasználói utazás előtt megjelenő adatmennyiséget. Az érvényesítési technikai profillal egy önérvényesített oldalon megjelenik egy hibaüzenet, amelyet a rendszer a kimeneti jogcímek között ad vissza.
- **Jogcímek cseréje** – a REST-szolgáltatás hívása egy előkészítési lépésen keresztül történik. Ebben az esetben nincs felhasználói felület a hibaüzenet megjelenítéséhez. Ha a REST API hibát ad vissza, a rendszer visszairányítja a felhasználót a függő entitás alkalmazására a hibaüzenettel.

## <a name="protocol"></a>Protocol (Protokoll)

A **protokoll** elem **Name** attribútumát `Proprietary`értékre kell állítani. A **kezelő** attribútumnak tartalmaznia kell a Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`által használt protokollkezelő-szerelvény teljesen minősített nevét.

Az alábbi példa egy REST-technikai profilt mutat be:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem tartalmazza a REST API küldendő jogcímek listáját. A jogcím nevét a REST APIban definiált névre is leképezheti. A következő példa a házirend és a REST API közötti leképezést mutatja be. A rendszer a **givenName** -jogcímet **firstName**-ként elküldi a REST APInak, míg a **vezetéknevet** **utónévként**küldjük el. Az **e-mail-** jogcím a következőképpen van beállítva:.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

A **InputClaimsTransformations** elem olyan **InputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak előállítására szolgálnak a REST API való elküldés előtt.

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem a REST API által visszaadott jogcímek listáját tartalmazza. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét a REST APIban definiált névre. Olyan jogcímeket is tartalmazhat, amelyeket nem ad vissza a REST API Identity Provider, ha a `DefaultValue` attribútumot állítja be.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

A következő példa a REST API által visszaadott jogcímet mutatja be:

- A **loyaltyNumber** jogcím nevére leképezett **MembershipId** jogcím.

A technikai profil a jogcímeket is visszaadja, amelyeket az identitás-szolgáltató nem ad vissza:

- Az a **loyaltyNumberIsNew** jogcím, amelynek alapértelmezett értéke `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| serviceUrl | Igen | Az REST API végpont URL-címe |
| AuthenticationType | Igen | A REST-alapú jogcím-szolgáltató által végrehajtott hitelesítés típusa. Lehetséges értékek: `None`, `Basic`vagy `ClientCertificate`. A `None` érték azt jelzi, hogy a REST API nem anonim. A `Basic` érték azt jelzi, hogy a REST API a HTTP alapszintű hitelesítéssel védett. Csak ellenőrzött felhasználók férhetnek hozzá az API-hoz, beleértve a Azure AD B2C is. A `ClientCertificate` (ajánlott) érték azt jelzi, hogy a REST API ügyféltanúsítvány-alapú hitelesítéssel korlátozza a hozzáférést. Csak a megfelelő tanúsítvánnyal rendelkező szolgáltatások férhetnek hozzá a szolgáltatáshoz, például a Azure AD B2C. |
| SendClaimsIn | Nem | Megadja, hogy a rendszer hogyan küldje el a bemeneti jogcímeket a REST-jogcím-szolgáltatónak. Lehetséges értékek: `Body` (alapértelmezett), `Form`, `Header`vagy `QueryString`. A `Body` érték a kérelem törzsében a JSON formátumban elküldett bemeneti jogcím. Az `Form` érték az a bemeneti jogcím, amelyet a rendszer a kérés törzsében küld el egy "&", a kulcs értékének formátuma. A `Header` érték a kérelem fejlécében elküldhető bemeneti jogcím. A `QueryString` érték a kérelem lekérdezési karakterláncában elküldhető bemeneti jogcím. |
| ClaimsFormat | Nem | Megadja a kimeneti jogcímek formátumát. Lehetséges értékek: `Body` (alapértelmezett), `Form`, `Header`vagy `QueryString`. A `Body` érték a kérelem törzsében a JSON formátumban elküldhető kimeneti jogcím. A `Form` érték a kérés törzsében az "&" és a kulcs értékének megadásával elválasztott kimeneti jogcím. A `Header` érték a kérelem fejlécében elküldhető kimeneti jogcím. A `QueryString` érték a kérelem lekérdezési karakterláncában elküldhető kimeneti jogcím. |
| DebugMode | Nem | A technikai profilt hibakeresési módban futtatja. Hibakeresési módban a REST API további információkat adhat vissza. Lásd a visszatérési hibaüzenet szakaszt. |

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

Ha a hitelesítés típusa `None`értékre van beállítva, a rendszer nem használja a **CryptographicKeys** elemet.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Ha a hitelesítés típusa `Basic`értékre van beállítva, a **CryptographicKeys** elem a következő attribútumokat tartalmazza:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Igen | A hitelesítéshez használt Felhasználónév. |
| BasicAuthenticationPassword | Igen | A hitelesítéshez használt jelszó. |

Az alábbi példa egy egyszerű hitelesítéssel rendelkező technikai profilt mutat be:

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Ha a hitelesítés típusa `ClientCertificate`értékre van beállítva, a **CryptographicKeys** elem a következő attribútumot tartalmazza:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| ClientCertificate | Igen | A hitelesítéshez használandó X509-tanúsítvány (RSA-kulcs). |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>Hibaüzenet küldése

Előfordulhat, hogy a REST API hibaüzenetet kell visszaadnia, például "a felhasználó nem található a CRM rendszerben". Hiba esetén a REST API a következő attribútumokkal rendelkező HTTP 409 hibaüzenetet (ütközési válasz állapotkódot) kell visszaadnia:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| version | Igen | 1.0.0 |
| status | Igen | 409 |
| kód | Nem | A REST-végpont szolgáltatójának hibakódja, amely akkor jelenik meg, ha `DebugMode` engedélyezve van. |
| Kérelemazonosító | Nem | A REST végpont-szolgáltatótól származó kérelem azonosítója, amely akkor jelenik meg, ha `DebugMode` engedélyezve van. |
| userMessage | Igen | Egy hibaüzenet jelenik meg, amely megjelenik a felhasználó számára. |
| developerMessage | Nem | A probléma részletes leírása és a kijavítása, amely akkor jelenik meg, ha `DebugMode` engedélyezve van. |
| moreInfo | Nem | Olyan URI, amely a `DebugMode` engedélyezésekor megjelenő további információkra mutat. |

Az alábbi példa egy olyan REST API mutat be, amely a JSON-ban formázott hibaüzenetet adja vissza:

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

A következő példa egy olyan C# osztályt mutat be, amely egy hibaüzenetet ad vissza:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="examples"></a>Példák:
- [REST API jogcímek cseréjének integrálása a Azure AD B2C felhasználói úton a felhasználói bevitel ellenőrzéseként](active-directory-b2c-custom-rest-api-netfw.md)
- [A REST-szolgáltatások biztonságossá tétele HTTP alapszintű hitelesítés használatával](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [A REST-szolgáltatás biztonságossá tétele Ügyféltanúsítványok használatával](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [Bemutató: REST API jogcím-cserék integrálása a Azure AD B2C felhasználói úton a felhasználói adatok érvényesítése során](active-directory-b2c-rest-api-validation-custom.md)

 














