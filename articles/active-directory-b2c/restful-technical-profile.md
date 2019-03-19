---
title: Egy egyéni házirendek az Azure Active Directory B2C egy REST-alapú technikai profilban meghatározása |} A Microsoft Docs
description: Adja meg az Azure Active Directory B2C egyéni házirendet egy REST-alapú technikai profilban.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7ff14af756a55ccc6bbf40dd39d49c5168f4af1f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076326"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Egyéni Azure Active Directory B2C-házirendek egy REST-alapú technikai profilban meghatározása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory (Azure AD) B2C támogatást nyújt a saját RESTful szolgáltatás. Az Azure AD B2C elküldi az adatok a REST-alapú szolgáltatás, a bemeneti jogcím-gyűjtemény, valamint adatokat fogad vissza egy kimeneti jogcímek gyűjteményben. RESTful szolgáltatás integrációnak köszönhetően a következőket teheti:

- **Felhasználó által bevitt adatok érvényesítéséhez** – megakadályozza, hogy a nem megfelelően formázott megőrizhetők az Azure AD B2C-t. Ha az érték a felhasználó nem érvényes, a RESTful szolgáltatás, amely felszólítja a felhasználót egy bejegyzést adjon meg a hibaüzenetet adja vissza. Ellenőrizheti például, hogy az a felhasználó által megadott e-mail-cím szerepel-e a felhasználó-adatbázisa alapján.
- **Írja felül a bemeneti jogcímek között** – lehetővé teszi, hogy a bemeneti jogcímek között szereplő értékek formázza újra. Például ha egy felhasználó sikeresen megadja a csupa nagybetűvel vagy az utónevet csupa kisbetűket, a neve a formázhatók csak az első betűje nagybetű.
- **Felhasználói adatokat feldúsítani** – lehetővé teszi, hogy további vállalati üzleti alkalmazások integrálását. A RESTful szolgáltatás például kap a felhasználó e-mail címét, az ügyfél-adatbázis lekérdezéséhez és az Azure AD B2C-vel a felhasználói hűség szám visszaadása. A visszatérési jogcímek tárolt, értékeli ki a következő lépésben Vezénylési vagy a hozzáférési jogkivonatot tartalmazza.
- **Egyéni üzleti logika futtatása** – lehetővé teszi a leküldéses értesítések küldéséhez, vállalati adatbázisok frissítése, felhasználó migrálási folyamat futtatása, kezelheti az engedélyeiket, naplózási adatbázisokat, és egyéb műveleteket hajthat végre.

A szabályzat küldhet a bemeneti jogcímek között, a REST API-val. A REST API-t is adhatnak vissza, amelyek később a házirend a kimeneti jogcímek, vagy nagyvállalat, egy hibaüzenet. Megtervezheti a REST-alapú szolgáltatásokkal való integrációt, a következő módon:

- **Ellenőrzési technikai profil** – egy érvényesítési technikai profil meghívja a REST-alapú szolgáltatást. Az ellenőrzési technikai profil ellenőrzi a felhasználó által megadott felhasználói interakciósorozat folytatása előtt. Az érvényesítés technikai profilban, a hibaüzenetet önellenőrzött lapon megjelenített és adott vissza a kimeneti jogcímek.
- **Az exchange-jogcímek** -hívást kezdeményez a RESTful szolgáltatás egy vezénylési lépés keresztül. Ebben a forgatókönyvben nem nincs felhasználói felület jelennek meg a hibaüzenetet. A REST API hibát ad vissza, ha a rendszer átirányítja a felhasználót a függő entitás alkalmazásnak a hiba miatt.

## <a name="protocol"></a>Protokoll

A **neve** attribútuma a **protokoll** elemet hozzá kell beállítani `Proprietary`. A **kezelő** attribútum kell tartalmaznia a teljes nevet, amely az Azure AD B2C által használt protokoll kezelő sestavení: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Az alábbi példa bemutatja egy REST-alapú technikai profilban:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...    
```

## <a name="input-claims"></a>A bemeneti jogcímek

A **InputClaims** elem tartalmazza a REST API-nak küldendő jogcímek listája. A REST API-ban definiált nevére a jogcím neve is leképezheti. Következő példa bemutatja a házirend és a REST API közötti leképezést. A **givenName** jogcímet küld a REST API-t szolgáltatásként **firstName**, miközben **Vezetéknév** legyen elküldve, **lastName**. A **e-mail** jogcím van beállítva, mert.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

A **InputClaimsTransformations** elemet tartalmazhat egy gyűjteményét **InputClaimsTransformation** elemek, amelyek segítségével módosítsa a bemeneti jogcímek között, és újakat, mielőtt elküldené a REST API létrehozása.

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem tartalmazza a REST API által visszaküldött jogcímek listája. Szükség lehet a jogcím, a REST API-ban definiált nevére a szabályzatban definiált nevének való leképezéséhez. Is használható a REST API-val identitásszolgáltató nem adott vissza jogcímeket, amennyiben beállította a `DefaultValue` attribútum.

A **OutputClaimsTransformations** elemet tartalmazhat egy gyűjteményét **OutputClaimsTransformation** elemek, amelyek segítségével módosíthatja a kimeneti jogcímek, és hozzon létre újakat.

Az alábbi példa bemutatja a jogcím, a REST API által visszaadott:

- A **MembershipId** jogcímet, amelyet le van képezve a **loyaltyNumber** jogcím neve.

A technikai profil is az identitásszolgáltató nem adott vissza jogcímeket adja vissza: 

- A **loyaltyNumberIsNew** jogcímet, amelyet egy alapértelmezett értékre van állítva `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| ServiceUrl | Igen | A REST API-végpont URL-címe | 
| AuthenticationType | Igen | A REST-alapú jogcímszolgáltató által végrehajtott hitelesítés típusa. A lehetséges értékek: `None`, `Basic`, vagy `ClientCertificate`. A `None` érték azt jelzi, hogy a REST API-t nem névtelen. A `Basic` érték azt jelzi, hogy a REST API egyszerű HTTP-hitelesítés védi. Csak ellenőrzött felhasználókat, beleértve az Azure AD B2C-vel, hozzáférhet az API-t. A `ClientCertificate` (ajánlott) érték azt jelzi, hogy a REST API korlátozza a hozzáférést az ügyféltanúsítvány-alapú hitelesítés használatával. Csak a szükséges tanúsítványokat, például az Azure AD B2C-vel rendelkező szolgáltatások érhetik el a szolgáltatást. | 
| SendClaimsIn | Nem | Itt adható meg, hogyan a bemeneti jogcímek között a REST-alapú jogcímszolgáltatótól érkeznek. A lehetséges értékek: `Body` (alapértelmezett), `Form`, `Header`, vagy `QueryString`. A `Body` értéke a bemeneti jogcímek küldött a kéréstörzs JSON formátumban. A `Form` értéke a bemeneti jogcímek, az ampersand a kérelem törzsében szereplő zajlik "és" tagolt formátumú kulcs értékét. A `Header` értéke a bemeneti jogcímek a kérelem fejlécében küldött. A `QueryString` értéke a bemeneti jogcímek a kérelem lekérdezési karakterláncában küldött. | 
| ClaimsFormat | Nem | Megadja a kimeneti jogcímek formátumát. A lehetséges értékek: `Body` (alapértelmezett), `Form`, `Header`, vagy `QueryString`. A `Body` értéke a kimeneti jogcímek küldött a kéréstörzs JSON formátumban. A `Form` értéke a kimeneti jogcímek, az ampersand a kérelem törzsében szereplő zajlik "és" tagolt formátumú kulcs értékét. A `Header` értéke a kimeneti jogcímek a kérelem fejlécében küldött. A `QueryString` értéke a kimeneti jogcímek a kérelem lekérdezési karakterláncában küldött. | 
| DebugMode | Nem | A technikai profil hibakeresési módban fut. Hibakeresési módban a REST API további információkat adhat vissza. Az adatszolgáltató hiba üzenet című szakaszában talál. | 

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

Ha a hitelesítés típusát `None`, a **CryptographicKeys** elem nem szolgál.

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

Ha a hitelesítés típusát `Basic`, a **CryptographicKeys** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Igen | Hitelesítéshez használt felhasználónév. | 
| BasicAuthenticationPassword | Igen | Hitelesítéshez használt jelszót. |

Az alábbi példa bemutatja egy egyszerű hitelesítést technikai profil:

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

Ha a hitelesítés típusát `ClientCertificate`, a **CryptographicKeys** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| ClientCertificate | Igen | A X509 (RSA key set) tanúsítványt használják a hitelesítéshez. | 

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

## <a name="returning-error-message"></a>Chybová zpráva visszaadása

A REST API-t kell adja vissza egy hibaüzenet, például a "a felhasználó nem található a CRM-rendszerrel". A hiba akkor fordul elő, a REST API HTTP 409 (ütközés válasz állapotkódjának) tartalmazó hibaüzenet következő attribútumokkal kell visszaadnia:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| version | Igen | 1.0.0 | 
| status | Igen | 409 | 
| Kód | Nem | A RESTful-végpont szolgáltató, amely hibakód jelenik meg, mikor `DebugMode` engedélyezve van. | 
| requestId | Nem | Egy kérés azonosítója, amely RESTful-végpont szolgáltatójáról jelenik meg, mikor `DebugMode` engedélyezve van. | 
| userMessage | Igen | Egy hibaüzenet a felhasználó számára. | 
| developerMessage | Nem | A problémáról és annak megoldásáról, amely részletes leírása jelenik meg, mikor `DebugMode` engedélyezve van. | 
| moreInfo | Nem | További információt, amely mutató URI-t jelenik meg, mikor `DebugMode` engedélyezve van. | 

Az alábbi példa bemutatja egy REST API-t, amely JSON-formátumú hibaüzenetet ad vissza:

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

Az alábbi példa bemutatja egy C# osztály, amely hibaüzenetet ad vissza:

```C#
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
- [A felhasználói adatbevitel auditáló REST API-val jogcím cseréje az Azure AD B2C felhasználói interakciósorozatban szereplő integrálása](active-directory-b2c-custom-rest-api-netfw.md) 
- [Biztonságos RESTful-szolgáltatásokat egyszerű HTTP-hitelesítés használatával](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [Biztonságos a RESTful szolgáltatás ügyfél-tanúsítványok használatával](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [Forgatókönyv: A felhasználói bevitel auditáló REST API-val jogcím cseréje az Azure AD B2C felhasználói interakciósorozatban szereplő integrálása](active-directory-b2c-rest-api-validation-custom.md)

 














