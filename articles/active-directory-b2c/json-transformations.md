---
title: JSON-követelések átalakítási példái egyéni házirendekhez
titleSuffix: Azure AD B2C
description: A JSON az Azure Active Directory B2C Identitáskezelési keretrendszer (IEF) sémájának átalakítási példáit állítja be.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b42c2a414333e7ed262441321a808fc45425fc3b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756761"
---
# <a name="json-claims-transformations"></a>JSON követelések átalakítások

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz az Azure Active Directory B2C (Azure AD B2C) Identitáskezelési keretrendszer séma JSON-jogcímátalakításai használatára. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>Json létrehozása

JSON-karakterlánc létrehozásához használjon jogcímértékeket vagy állandókat. A pontjelölés utáni elérési út karakterláncjelzi, hogy hová szúrja be az adatokat egy JSON-karakterláncba. A pöttyök általi felosztás után a program az egész számokat egy JSON-tömb indexeként értelmezi, a nem egész számokat pedig egy JSON-objektum indexeként értelmezi a program.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | A sorjelölésutáni karakterlánc | sztring | Annak a JSON-nak a Jsonpath-ja, amelybe a jogcímértéket beszúrja. |
| InputParameter (Beviteli paraméter) | A sorjelölésutáni karakterlánc | sztring | Annak a JSON-nak a Jsonpath-ja, amelybe az állandó karakterlánc-érték kerül beillesztésre. |
| OutputClaim (Kimeneti jogcím) | outputClaim | sztring | A létrehozott JSON-karakterlánc. |

A következő példa egy JSON-karakterláncot hoz létre az "e-mail" és az "OTP" jogcímértéke, valamint az állandó karakterláncok alapján.

```XML
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

A következő jogcímek átalakítása egy JSON-karakterlánc-jogcímet ad ki, amely a SendGridnek (egy külső e-mail szolgáltatónak) küldött kérelem törzse lesz. A JSON-objektum szerkezetét az InputParameters és az InputClaims TransformationClaimTypes azonosítói határozzák meg. A dot notation-ban lévő számok tömbökre utalnak. Az értékek az InputClaims értékeiből és az InputParameters "Value" tulajdonságaiból származnak.

- Input követelések :
  - **e-mail**, átalakítási jogcím típusa **personalizations.0.to.0.email**: "someone@example.com
  - **otp**, átalakítási jogcímtípus **personalizations.0.dynamic_template_data.otp** "346349"
- Bemeneti paraméter:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **from.email**:service@contoso.com" "
  - **personalizations.0.subject** "Contoso fiók e-mail ellenőrző kódja"
- Kimeneti igény:
  - **requestBody**: JSON-érték

```JSON
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

Egy megadott elem bekéselése JSON-adatokból.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputJson | sztring | A jogcímátalakítás által az elem leéséhez használt jogcímtípusok. |
| InputParameter (Beviteli paraméter) | állításKivonat | sztring | a kibontandó JSON-elem neve. |
| OutputClaim (Kimeneti jogcím) | kivonatigénylés | sztring | A jogcímátalakítás meghívása után létrehozott Jogcímtípus, a _jogcímkibontás_ bemeneti paraméterben megadott elemérték. |

A következő példában a jogcímek `emailAddress` transzformációja kivonta az elemet a JSON-adatokból:`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

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

- Bemeneti igények:
  - **inputJson**: {"emailAddress": " ",someone@example.com"displayName": "Valaki"}
- Bemeneti paraméter:
    - **claimToExtract**: emailAddress
- Kimeneti jogcímek:
  - **kivontKövetelés**:someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

A json-adatokból a megadott elemek listájának bekéselése.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | jsonSourceClaim | sztring | A jogcímek átalakítása által a jogcímek leképezéséhez használt jogcímtípusok. |
| InputParameter (Beviteli paraméter) | errorOnMissingClaims | logikai | Itt adható meg, hogy hiba legyen-e, ha valamelyik jogcím hiányzik. |
| InputParameter (Beviteli paraméter) | includeEmptyClaims | sztring | Adja meg, hogy szerepeljen-e üres jogcím. |
| InputParameter (Beviteli paraméter) | jsonSourceKeyName | sztring | Elemkulcs neve |
| InputParameter (Beviteli paraméter) | jsonSourceValueName | sztring | Elemérték neve |
| OutputClaim (Kimeneti jogcím) | Gyűjtemény | karakterlánc, int, logikai és datetime |A kivonattal kapcsolatos állítások listája. A jogcím nevének meg kell egyeznie a _jsonSourceClaim_ bemeneti jogcímben megadott névvel. |

A következő példában a jogcímek átalakítása kinyeri a következő jogcímeket: e-mail (karakterlánc), displayName (karakterlánc), membershipNum (int), aktív (logikai) és születési dátum (datetime) a JSON-adatokból.

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

- Bemeneti igények:
  - **jsonSourceClaim**: [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00:00:00:00:00:000:00:00:00:00"Z"}}
- Bemeneti paraméterek:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: kulcs
    - **jsonSourceValueName**: érték
- Kimeneti jogcímek:
  - **e-mail**: "someone@example.com"
  - **displayName**: "Valaki"
  - **membershipNum**: 6353399
  - **aktív**: igaz
  - **születési idő**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Meghatározott numerikus (hosszú) elem bekéselése JSON-adatokból.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputJson | sztring | A jogcímátalakítás által a jogcím átalakításához használt jogcímtípusok. |
| InputParameter (Beviteli paraméter) | állításKivonat | sztring | A kibontandó JSON-elem neve. |
| OutputClaim (Kimeneti jogcím) | kivonatigénylés | long | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus, a _jogcímkibontás_ bemeneti paraméterekben megadott elemértéke. |

A következő példában a jogcímek `id` átalakítása kinyeri az elemet a JSON-adatokból.

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

- Bemeneti igények:
  - **inputJson**: {"emailAddress": " ",someone@example.com"displayName": "Valaki", "id" : 6353399}
- Bemeneti paraméterek
    - **claimToExtract**: id
- Kimeneti jogcímek:
    - **kivonatIgénylés**: 6353399

## <a name="getsingleitemfromjson"></a>GetSingleItemFromJson

Leveszi az első elemet egy JSON-adatból.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputJson | sztring | A jogcímátalakítás által használt jogcímtípusok az elem JSON-adatokból való leéséhez. |
| OutputClaim (Kimeneti jogcím) | kulcs | sztring | A JSON első elemkulcsa. |
| OutputClaim (Kimeneti jogcím) | érték | sztring | A JSON első elemértéke. |

A következő példában a jogcímek transzformációja kinyeri az első elemet (adott nevet) a JSON-adatokból.

```XML
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti igények:
  - **inputJson**: {"givenName": "Emilty", "lastName": "Smith"}
- Kimeneti jogcímek:
  - **kulcs**: givenName
  - **érték**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Leveszi az első elemet egy JSON-adattömbből.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputJsonClaim | sztring | A jogcímátalakítás által használt jogcímtípusok az elem JSON-tömbből való leéséhez. |
| OutputClaim (Kimeneti jogcím) | kivonatigénylés | sztring | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus, a JSON-tömb első eleme. |

A következő példában a jogcímek átalakítása kinyeri az első elemet `["someone@example.com", "Someone", 6353399]`(e-mail címet) a JSON-tömbből.

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

- Bemeneti igények:
  - **inputJsonClaim**:someone@example.com[" ", "Valaki", 6353399]
- Kimeneti jogcímek:
  - **kivontKövetelés**:someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

XML-adatokat konvertál JSON formátumba.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | xml | sztring | A jogcímek átalakítása által használt jogcímtípusok az adatok XML-ről JSON formátumra történő konvertálására. |
| OutputClaim (Kimeneti jogcím) | json | sztring | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus, az adatok JSON formátumban. |

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

A következő példában a jogcímek átalakítása a következő XML-adatokat JSON formátumba konvertálja.

#### <a name="example"></a>Példa
Bemeneti igény:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Kimeneti igény:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


