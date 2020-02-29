---
title: A JSON-jogcím átalakítására vonatkozó példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: A JSON jogcím-átalakítási példákat tartalmaz a Azure Active Directory B2C IEF-sémája számára.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ad8fcf578ae1c89856a9d7929af0aec813cb4082
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187593"
---
# <a name="json-claims-transformations"></a>JSON-jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework-sémájának JSON-jogcímek átalakítására. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>GenerateJson

JSON-karakterlánc létrehozásához használja a jogcímek vagy állandók értéket. Az elérési út sztringjét követő pont jelölése arra szolgál, hogy hová szúrja be az adatbevitelt egy JSON-karakterláncba. A pontok közötti felosztás után a rendszer minden egész számot értelmez a JSON-tömb és a nem egész számok indexének értelmezésére egy JSON-objektum indexe.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | Bármely, a pont jelölését követő karakterlánc | Karakterlánc | Annak a JSON-nek a JsonPath, amelybe a rendszer beszúrja a jogcím értékét. |
| InputParameter | Bármely, a pont jelölését követő karakterlánc | Karakterlánc | Annak a JSON-nek a JsonPath, amelybe a rendszer beszúrja az állandó sztringet. |
| outputClaim | outputClaim | Karakterlánc | A generált JSON-karakterlánc. |

Az alábbi példa egy JSON-karakterláncot hoz létre az "e-mail" és az "OTP" jogcím értéke alapján, valamint állandó sztringeket is.

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

A következő jogcím-átalakítás egy JSON-karakterlánc-jogcímet eredményez, amely a SendGrid (egy külső gyártótól származó e-mail-szolgáltató) számára küldött kérelem törzse lesz. A JSON-objektum struktúráját az azonosítók a InputParameters és a Szabályzattípushoz TransformationClaimTypes határozza meg. A pont jelölésében szereplő számok tömböket jelentenek. Az értékek a Szabályzattípushoz "Values" és a InputParameters "" érték "tulajdonságaiból származnak.

- Bemeneti jogcímek:
  - **e-mail**, átalakítási jogcím típusának **személyre szabása. 0. to. 0. e-mail**: "someone@example.com"
  - **OTP**, átalakítási jogcím típusának **személyre szabása. 0. dynamic_template_data. OTP** "346349"
- Bemeneti paraméter:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **Feladó. e-mail**: "service@contoso.com"
  - **személyre szabás. 0. tárgy** : "contoso-fiók e-mail-ellenőrző kódja"
- Kimeneti jogcím:
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

Egy megadott elem beolvasása JSON-adatokból.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputJson | Karakterlánc | A jogcím-átalakítás által az elemek beolvasásához használt ClaimTypes. |
| InputParameter | claimToExtract | Karakterlánc | a kinyerni kívánt JSON-elem neve. |
| outputClaim | extractedClaim | Karakterlánc | A jogcímek átalakítását követően létrehozott ClaimType meghívása megtörtént, a _claimToExtract_ bemeneti paraméterben megadott elem értéke. |

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

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | jsonSourceClaim | Karakterlánc | A jogcím-átalakítás által a jogcímek beszerzéséhez használt ClaimTypes. |
| InputParameter | errorOnMissingClaims | Logikai érték | Megadja, hogy a rendszer hibát jelez-e, ha a jogcímek egyike hiányzik. |
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

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputJson | Karakterlánc | A jogcím-átalakítás által a jogcímek beszerzéséhez használt ClaimTypes. |
| InputParameter | claimToExtract | Karakterlánc | A kinyerni kívánt JSON-elem neve. |
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

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputJsonClaim | Karakterlánc | A jogcím-átalakítás által az elemnek a JSON-tömbből való beolvasásához használt ClaimTypes. |
| outputClaim | extractedClaim | Karakterlánc | A ClaimsTransformation után létrehozott ClaimType a JSON-tömb első elemeként lett meghívva. |

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

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | xml | Karakterlánc | A jogcím-átalakítás által az adatok XML-ből JSON-formátumba való átalakításához használt ClaimTypes. |
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
