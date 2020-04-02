---
title: ClaimsTransformations - Azure Active Directory B2C | Microsoft dokumentumok
description: A ClaimsTransformations elem meghatározása az Azure Active Directory B2C identitáskezelési keretrendszer-sémájában.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2a919996d00f8ef3fa00109944b60d53b63d95ff
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529128"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **ClaimsTransformations** elem tartalmazza a jogcímek átalakítási függvényeinek listáját, amelyek [egy egyéni házirend](custom-policy-overview.md)részeként használhatók a felhasználói utazásokban. A jogcímek átalakítása egy adott jogcímet egy másikba konvertál. A jogcímek átalakításában megadhatja az átalakítási módszert, például hozzáadhat egy elemet egy karakterlánc-gyűjteményhez, vagy módosítja egy karakterlánc esetét.

A felhasználói utaksorán használható jogcímátalakítási függvények listájának felvételéhez a jogcímátalakítások XML-elemét a házirend BuildingBlocks szakaszában kell deklarálni.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

A **ClaimsTransformation** elem a következő attribútumokat tartalmazza:

| Attribútum |Kötelező | Leírás |
| --------- |-------- | ----------- |
| Azonosító |Igen | A jogcím-átalakítás egyedi azonosítására használt azonosító. Az azonosítóra a házirend más XML-elemei hivatkoznak. |
| TransformationMetódus | Igen | A jogcím-átalakításban használandó átalakítási módszer. Minden jogcímátalakításnak saját értékei vannak. Tekintse meg a [jogcím-átalakítási hivatkozás](#claims-transformations-reference) a rendelkezésre álló értékek teljes listáját. |

## <a name="claimstransformation"></a>Jogcímátalakítás

A **ClaimsTransformation** elem a következő elemeket tartalmazza:

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| Elem | Események | Leírás |
| ------- | -------- | ----------- |
| InputClaims (InputClaims) | 0:1 | **Az InputClaim-elemek** listája, amelyek meghatározzák a jogcím-átalakítás bemeneteként figyelembe vett jogcímtípusokat. Ezen elemek mindegyike tartalmaz egy hivatkozást egy Olyan Jogcímtípusra, amely már definiálva van a házirend ClaimsSchema szakaszában. |
| InputParameters (InputParameters) | 0:1 | **Az InputParameter** elemek listája, amelyek a jogcím-átalakítás bemeneteként vannak megadva.
| Kimeneti jogcímek | 0:1 | **OutputClaim** elemek listája, amelyek meghatározzák a ClaimsTransformation meghívása után létrehozott jogcímtípusokat. Ezen elemek mindegyike egy olyan Jogcímtípusra mutató, a ClaimsSchema szakaszban már definiált Jogcímtípusra mutató hivatkozást tartalmaz. |

### <a name="inputclaims"></a>InputClaims (InputClaims)

Az **InputClaims** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| InputClaim (Bemeneti jogcím) | 1:n | Egy várt bemeneti jogcímtípus. |

#### <a name="inputclaim"></a>InputClaim (Bemeneti jogcím)

Az **InputClaim** elem a következő attribútumokat tartalmazza:

| Attribútum |Kötelező | Leírás |
| --------- | ----------- | ----------- |
| Jogcímtípushivatkozásazonosító |Igen | Hivatkozás egy olyan Jogcímtípusra, amely már definiálva van a házirend ClaimsSchema szakaszában. |
| TransformationClaimType |Igen | Átalakítási jogcímtípusra hivatkozó azonosító. Minden jogcímátalakításnak saját értékei vannak. Tekintse meg a [jogcím-átalakítási hivatkozás](#claims-transformations-reference) a rendelkezésre álló értékek teljes listáját. |

### <a name="inputparameters"></a>InputParameters (InputParameters)

Az **InputParameters** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| InputParameter (Beviteli paraméter) | 1:n | Egy várt bemeneti paraméter. |

#### <a name="inputparameter"></a>InputParameter (Beviteli paraméter)

| Attribútum | Kötelező |Leírás |
| --------- | ----------- |----------- |
| Azonosító | Igen | Olyan azonosító, amely a jogcímátalakítási módszer egyik paraméterére hivatkozik. Minden jogcímátalakítási módszernek saját értékei vannak. Tekintse meg a jogcím-átalakítási tábla a rendelkezésre álló értékek teljes listáját. |
| DataType | Igen | A paraméter adattípusa, például karakterlánc, logikai, int vagy datetime az egyéni házirend XML-sémájában a DataType enumerálása szerint. Ez a típus a számtani műveletek helyes végrehajtására szolgál. Minden jogcímátalakításnak saját értékei vannak. Tekintse meg a [jogcím-átalakítási hivatkozás](#claims-transformations-reference) a rendelkezésre álló értékek teljes listáját. |
| Érték | Igen | Az átalakításnak szó szerint átadott érték. Néhány érték tetszőleges, néhányat a jogcímek átalakítási módszeréből választhat ki. |

### <a name="outputclaims"></a>Kimeneti jogcímek

A **OutputClaims** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| OutputClaim (Kimeneti jogcím) | 0:n | Egy várt kimeneti jogcímtípus. |

#### <a name="outputclaim"></a>OutputClaim (Kimeneti jogcím)

A **OutputClaim** elem a következő attribútumokat tartalmazza:

| Attribútum |Kötelező | Leírás |
| --------- | ----------- |----------- |
| Jogcímtípushivatkozásazonosító | Igen | Hivatkozás egy olyan Jogcímtípusra, amely már definiálva van a házirend ClaimsSchema szakaszában.
| TransformationClaimType | Igen | Átalakítási jogcímtípusra hivatkozó azonosító. Minden jogcímátalakításnak saját értékei vannak. Tekintse meg a [jogcím-átalakítási hivatkozás](#claims-transformations-reference) a rendelkezésre álló értékek teljes listáját. |

Ha a bemeneti jogcím és a kimeneti jogcím azonos típusú (karakterlánc vagy logikai), használhatja ugyanazt a bemeneti jogcím, mint a kimeneti jogcím. Ebben az esetben a jogcímek átalakítása módosítja a bemeneti jogcímet a kimeneti értékkel.

## <a name="example"></a>Példa

Például tárolhatja a szolgáltatási feltételek utolsó olyan verzióját, amelyet a felhasználó elfogadott. A szolgáltatási feltételek frissítésekénk megkérheti a felhasználót, hogy fogadja el az új verziót. A következő példában a **HasTOSVersionChanged** jogcímek átalakítása összehasonlítja a **TOSVersion** jogcím értékét a **LastTOSAcceptedVersion** jogcím értékével, majd visszaadja a boolean **TOSVersionChanged** jogcímet.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>Jogcímátalakítások hivatkozása

A jogcímek átalakítására vonatkozó példákat a következő referencialapokon talál:

- [Logikai](boolean-transformations.md)
- [Dátum](date-transformations.md)
- [Egész](integer-transformations.md)
- [JSON](json-transformations.md)
- [Telefonszám](phone-number-claims-transformations.md)
- [Általános](general-transformations.md)
- [Közösségi fiók](social-transformations.md)
- [Sztring](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

