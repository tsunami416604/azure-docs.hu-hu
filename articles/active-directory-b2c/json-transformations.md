---
title: JSON-jogcímek átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C |} A Microsoft Docs
description: JSON átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C jogcímmel.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b6a00719fea78d5872dc00a874951c4760d9207f
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381532"
---
# <a name="json-claims-transformations"></a>JSON-átalakításokat jogcímek

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat az identitás-kezelőfelületi keretrendszer séma a JSON a jogcímek átalakítása az Azure Active Directory (Azure AD) B2C-ben. További információkért lásd: [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

A megadott elem le egy JSON-adatokat.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | inputJson | sztring | A ClaimTypes lekérni az elemet használják a jogcímek átalakítását. |
| InputParameter | claimToExtract | sztring | a JSON-elemeiről kinyerni neve. |
| kimeneti jogcím | extractedClaim | sztring | Az a jogcím-átalakítás után előállított takar meghívása az elem értékének megadott a _claimToExtract_ bemeneti paraméter. |

A következő példában a jogcímek átalakításáról ki kell olvasni az `emailAddress` elem a JSON-adatokat: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

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

- A bemeneti jogcímek:
    - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Személy"}
- A bemeneti paraméter:
    - **claimToExtract**: e-mail cím
- Kimeneti jogcímek: 
    - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

A Json-adatokat a megadott elemek listájának beolvasása.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | jsonSourceClaim | sztring | A ClaimTypes lekérni a jogcímeket a jogcím-átalakítási által használt. |
| InputParameter | errorOnMissingClaims | logikai | Megadja, hogy hiba állíthatunk munkába, ha a jogcímek az egyik hiányzik. |
| InputParameter | includeEmptyClaims | sztring | Adja meg, hogy üres jogcímeket tartalmaznak. |
| InputParameter | jsonSourceKeyName | sztring | Elem kulcs neve |
| InputParameter | jsonSourceValueName | sztring | Elem érték neve |
| kimeneti jogcím | Gyűjtemény | karakterlánc, int, logikai és dátum/idő |Jogcím kicsomagolásához listája. A jogcím nevét meg kell egyeznie a megadott _jsonSourceClaim_ bemeneti jogcímet. |

A következő példában a jogcímek átalakításáról kinyeri a következő jogcímek: e-mailek (karakterlánc), displayName (karakterlánc), membershipNum (int), aktív (boolean) és születési dátum (dátum és idő), a JSON-adatokat.

```JSON
[{"key":"email","value":"someone@example.com"}, "key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="type" />
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

- A bemeneti jogcímek:
    - **jsonSourceClaim**: [{"key": "email", "value": "someone@example.com"}, "key": "displayName", "value": "Személy"}, {"key": "membershipNum", "value": 6353399}, {"key": "active", "value": true}, {"key": "születési", "value": "1980-09-23T00:00:00Z"}]
- Bemeneti paraméterek:
    - **errorOnMissingClaims**: False (hamis)
    - **includeEmptyClaims**: False (hamis)
    - **jsonSourceKeyName**: kulcs
    - **jsonSourceValueName**: érték
- Kimeneti jogcímek:
    - **e-mailek**: "someone@example.com"
    - **displayName**: "Személy"
    - **membershipNum**: 6353399
    - **aktív**: igaz
    - **születési dátum**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Egy megadott numerikus (hosszú) elemnek egy JSON-adatok beolvasása.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | inputJson | sztring | A ClaimTypes beolvasni a jogcímet a jogcím-átalakítási által használt. |
| InputParameter | claimToExtract | sztring | A JSON-elem kibontásához neve. |
| kimeneti jogcím | extractedClaim | hossz | A takar, amelyek a ClaimsTransformation meghívása után jön létre, az elem érték szerepel a _claimToExtract_ -bemeneti paraméterekhez. |

A következő példában a jogcímek átalakításáról kinyeri a `id` elem a JSON-adatokat.

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

- A bemeneti jogcímek:
    - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Személy", "id": 6353399}
- Bemeneti paraméterek
    - **claimToExtract**: azonosítója
- Kimeneti jogcímek: 
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Az első elem beolvasása egy JSON-adatok tömb.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | inputJsonClaim | sztring | A ClaimTypes, az elem beolvasására a JSON-tömböt használják a jogcímek átalakítását. |
| kimeneti jogcím | extractedClaim | sztring | A ClaimType ez ClaimsTransformation meghívása után előállított, a JSON-tömb első elemében. |

A következő példában a jogcímek átalakításáról kinyeri az első elem (e-mail-cím) a JSON-tömböt `["someone@example.com", "Someone", 6353399]`.

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

- A bemeneti jogcímek:
    - **inputJsonClaim**: ["someone@example.com", "Személy", 6353399]
- Kimeneti jogcímek: 
    - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

XML-adatok konvertálása JSON formátumban.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | xml | sztring | A ClaimTypes, amelyek használják a jogcímek átalakítása XML-ből származó adatok átalakítása JSON-formátumban. |
| kimeneti jogcím | JSON | sztring | A takar, amelyek a ClaimsTransformation meghívása után jön létre, az adatok JSON formátumban. |

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

A következő példában a jogcímek átalakításáról alakítja át a következő XML-adatok JSON formátumban.

#### <a name="example"></a>Példa
A bemeneti jogcím:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Kimeneti jogcímek:

```JSON
{
  {
    "user": {
      "name":"Someone",
      "email":"someone@example.com"
    }
  }
}
```

