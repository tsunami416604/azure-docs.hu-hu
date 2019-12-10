---
title: A JSON-jogcím átalakítására vonatkozó példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: A JSON jogcím-átalakítási példákat tartalmaz a Azure Active Directory B2C IEF-sémája számára.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0ff6f24e30febd57a3a9740ec72a927225b37933
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948885"
---
# <a name="json-claims-transformations"></a>JSON-jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework-sémájának JSON-jogcímek átalakítására. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Egy megadott elem beolvasása JSON-adatokból.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | sztring | A jogcím-átalakítás által az elemek beolvasásához használt ClaimTypes. |
| InputParameter | claimToExtract | sztring | a kinyerni kívánt JSON-elem neve. |
| outputClaim | extractedClaim | sztring | A jogcímek átalakítását követően létrehozott ClaimType meghívása megtörtént, a _claimToExtract_ bemeneti paraméterben megadott elem értéke. |

A következő példában a jogcím-átalakítás kibontotta a `emailAddress` elemet a JSON-adatokból: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **inputJson**: {"emailAddress": "someone@example.com", "DisplayName": "valaki"}
- Bemeneti paraméter:
    - **claimToExtract**: emailAddress
- Kimeneti jogcímek:
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

A JSON-adatokból származó megadott elemek listájának beolvasása.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | sztring | A jogcím-átalakítás által a jogcímek beszerzéséhez használt ClaimTypes. |
| InputParameter | errorOnMissingClaims | logikai | Megadja, hogy a rendszer hibát jelez-e, ha a jogcímek egyike hiányzik. |
| InputParameter | includeEmptyClaims | sztring | Adja meg, hogy üres jogcímeket kíván-e megadni. |
| InputParameter | jsonSourceKeyName | sztring | Elem kulcsának neve |
| InputParameter | jsonSourceValueName | sztring | Elem értékének neve |
| outputClaim | Gyűjtemény | karakterlánc, int, Boolean és DateTime |A kinyerni kívánt jogcímek listája. A jogcím nevének meg kell egyeznie a _jsonSourceClaim_ bemeneti jogcímben megadott értékkel. |

A következő példában a jogcím-átalakítás kibontja a következő jogcímeket: e-mail (karakterlánc), displayName (karakterlánc), membershipNum (int), aktív (logikai) és születési (datetime) a JSON-adatokból.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```

- Bemeneti jogcímek:
  - **jsonSourceClaim**: [{"Key": "e-mail", "value": "someone@example.com"}, {"Key": "DisplayName", "value": "valaki"}, {"Key": "membershipNum", "value": 6353399}, {"Key": "Active", "value": true}, {"Key": "születésnap", "value": "1980-09-23T00:00:00Z"}]
- Bemeneti paraméterek:
    - **errorOnMissingClaims**: hamis
    - **includeEmptyClaims**: hamis
    - **jsonSourceKeyName**: kulcs
    - **jsonSourceValueName**: érték
- Kimeneti jogcímek:
  - **e-mail**: "someone@example.com"
  - **DisplayName**: "valaki"
  - **membershipNum**: 6353399
  - **aktív**: true
  - **születési dátum**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Lekéri egy megadott numerikus (hosszú) elemet egy JSON-adatból.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | sztring | A jogcím-átalakítás által a jogcímek beszerzéséhez használt ClaimTypes. |
| InputParameter | claimToExtract | sztring | A kinyerni kívánt JSON-elem neve. |
| outputClaim | extractedClaim | hosszú | A ClaimsTransformation után létrehozott ClaimType meghívása után az elem értéke a _claimToExtract_ bemeneti paraméterében van megadva. |

A következő példában a jogcím-átalakítás kibontja a JSON-adatokból a `id` elemet.

```JSON
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **inputJson**: {"emailAddress": "someone@example.com", "DisplayName": "valaki", "id": 6353399}
- Bemeneti paraméterek
    - **claimToExtract**: azonosító
- Kimeneti jogcímek:
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Egy JSON-adattömb első elemének beolvasása.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | sztring | A jogcím-átalakítás által az elemnek a JSON-tömbből való beolvasásához használt ClaimTypes. |
| outputClaim | extractedClaim | sztring | A ClaimsTransformation után létrehozott ClaimType a JSON-tömb első elemeként lett meghívva. |

A következő példában a jogcím-átalakítás kibontja az első elemet (e-mail-címet) a JSON-tömb `["someone@example.com", "Someone", 6353399]`.

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **inputJsonClaim**: ["someone@example.com", "valaki", 6353399]
- Kimeneti jogcímek:
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

XML-adatértékek konvertálása JSON formátumba.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | sztring | A jogcím-átalakítás által az adatok XML-ből JSON-formátumba való átalakításához használt ClaimTypes. |
| outputClaim | json | sztring | A ClaimsTransformation után létrehozott ClaimType JSON formátumban kell meghívnia. |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

A következő példában a jogcím-átalakítás a következő XML-adatmennyiségeket JSON formátumra alakítja át.

#### <a name="example"></a>Példa
Bemeneti jogcím:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Kimeneti jogcím:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

