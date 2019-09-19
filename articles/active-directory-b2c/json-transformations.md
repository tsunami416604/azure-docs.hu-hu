---
title: A JSON-jogcímek átalakítására vonatkozó példák a Azure Active Directory B2C Identity Experience Framework sémája esetében | Microsoft Docs
description: A JSON-jogcímek átalakítási példái a Azure Active Directory B2C identitás-keretrendszer sémájának.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ff70b2f54304c83f70ff578e1947d752aafb34a7
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064169"
---
# <a name="json-claims-transformations"></a>JSON-jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework-sémájának JSON-jogcímek átalakítására. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Egy megadott elem beolvasása JSON-adatokból.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | Karakterlánc | A jogcím-átalakítás által az elemek beolvasásához használt ClaimTypes. |
| InputParameter | claimToExtract | Karakterlánc | a kinyerni kívánt JSON-elem neve. |
| outputClaim | extractedClaim | Karakterlánc | A jogcímek átalakítását követően létrehozott ClaimType meghívása megtörtént, a _claimToExtract_ bemeneti paraméterben megadott elem értéke. |

A következő példában a jogcím-átalakító kibontotta `emailAddress` az elemet a JSON-adatokból:`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

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
  - **inputJson**: {"emailAddress": "someone@example.com", "DisplayName": "Valaki"}
- Bemeneti paraméter:
    - **claimToExtract**: emailAddress
- Kimeneti jogcímek:
  - **extractedClaim**:someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

A JSON-adatokból származó megadott elemek listájának beolvasása.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | Karakterlánc | A jogcím-átalakítás által a jogcímek beszerzéséhez használt ClaimTypes. |
| InputParameter | errorOnMissingClaims | boolean | Megadja, hogy a rendszer hibát jelez-e, ha a jogcímek egyike hiányzik. |
| InputParameter | includeEmptyClaims | Karakterlánc | Adja meg, hogy üres jogcímeket kíván-e megadni. |
| InputParameter | jsonSourceKeyName | Karakterlánc | Elem kulcsának neve |
| InputParameter | jsonSourceValueName | Karakterlánc | Elem értékének neve |
| outputClaim | Collection | karakterlánc, int, Boolean és DateTime |A kinyerni kívánt jogcímek listája. A jogcím nevének meg kell egyeznie a _jsonSourceClaim_ bemeneti jogcímben megadott értékkel. |

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
  - **jsonSourceClaim**: [{"Key": "e-mail", "value": "someone@example.com"}, {"Key": "DisplayName", "value": "valaki"}, {"Key": "membershipNum", "value": 6353399}, {"Key": "Active", "value": true}, {"Key": "születésnap", "value": "1980-09-23T00:00:00Z "}]
- Bemeneti paraméterek:
    - **errorOnMissingClaims**: hamis
    - **includeEmptyClaims**: hamis
    - **jsonSourceKeyName**: key
    - **jsonSourceValueName**: value
- Kimeneti jogcímek:
  - **e-mail cím**:someone@example.com""
  - **DisplayName**: Valaki
  - **membershipNum**: 6353399
  - **aktív**: true
  - **születési dátum**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Lekéri egy megadott numerikus (hosszú) elemet egy JSON-adatból.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | Karakterlánc | A jogcím-átalakítás által a jogcímek beszerzéséhez használt ClaimTypes. |
| InputParameter | claimToExtract | Karakterlánc | A kinyerni kívánt JSON-elem neve. |
| outputClaim | extractedClaim | long | A ClaimsTransformation után létrehozott ClaimType meghívása után az elem értéke a _claimToExtract_ bemeneti paraméterében van megadva. |

A következő példában a jogcím-átalakítás kibontja `id` az elemet a JSON-adatokból.

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
  - **inputJson**: {"emailAddress": "someone@example.com", "DisplayName": "Valaki", "azonosító": 6353399}
- Bemeneti paraméterek
    - **claimToExtract**: azonosító
- Kimeneti jogcímek:
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Egy JSON-adattömb első elemének beolvasása.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | Karakterlánc | A jogcím-átalakítás által az elemnek a JSON-tömbből való beolvasásához használt ClaimTypes. |
| outputClaim | extractedClaim | Karakterlánc | A ClaimsTransformation után létrehozott ClaimType a JSON-tömb első elemeként lett meghívva. |

A következő példában a jogcím-átalakítás kibontja az első elemet (e-mail-címet) `["someone@example.com", "Someone", 6353399]`a JSON-tömbből.

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
  - **extractedClaim**:someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

XML-adatértékek konvertálása JSON formátumba.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | Karakterlánc | A jogcím-átalakítás által az adatok XML-ből JSON-formátumba való átalakításához használt ClaimTypes. |
| outputClaim | JSON | Karakterlánc | A ClaimsTransformation után létrehozott ClaimType JSON formátumban kell meghívnia. |

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

