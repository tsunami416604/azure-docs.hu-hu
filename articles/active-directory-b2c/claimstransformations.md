---
title: ClaimsTransformations – az Azure Active Directory B2C |} A Microsoft Docs
description: Az identitás élmény keretrendszer sémát az Azure Active Directory B2C a ClaimsTransformations elem definíciója.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: bc6cc7b07d3dce43a666b3e5b0a958b41cdd3131
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651508"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **ClaimsTransformations** elem tartalmazza a jogcím átalakítási függvények részeként használható a felhasználói utak listájának egy [egyéni házirendet](active-directory-b2c-overview-custom.md). A jogcímek átalakítását egy adott jogcím alakít át egy másik. A jogcímek átalakítását, adja meg az átalakítási metódus például egy karakterlánc-gyűjtemény hozzáadása vagy módosítása a kis-és a egy karakterláncot.

Szeretne felvenni a függvénylistában jogcímek átalakítása használható a felhasználó Journey, egy ClaimsTransformations XML-elem a szabályzat BuildingBlocks szakaszában kell deklarálni.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

A **ClaimsTransformation** elem tartalmazza a következő attribútumokat:

| Attribútum |Szükséges | Leírás |
| --------- |-------- | ----------- |
| Azonosító |Igen | A jogcím-átalakítást egyedi azonosításához használt azonosító. Az azonosító az egyéb XML-elemeket a házirendben hivatkozik. |
| TransformationMethod | Igen | Az átalakítási módszerek a jogcímek átalakítását. Minden egyes jogcím-átalakítást saját értékekkel rendelkezik. Tekintse meg a [jogcím-átalakítási referencia](#claims-transformations-reference) teljes listáját az elérhető értékek. |

## <a name="claimstransformation"></a>ClaimsTransformation

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


| Elem | Ismétlődések | Leírás |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | Listáját **bemeneti jogcím** elemei, amelyek megadják a jogcímtípusok, amelyeket a rendszer, adjon meg, a jogcímek átalakítását. Ezek az elemek mindegyike tartalmaz egy hivatkozást egy már az ClaimsSchema szakaszban, a szabályzatban meghatározott takar. |
| InputParameters | 0:1 | Listáját **InputParameter** elemeket, amelyeket bemenetként a jogcímek átalakításáról biztosított.  
| OutputClaims | 0:1 | Listáját **kimeneti jogcím** elemei, amelyek megadják a jogcím típusait, amelyek előállítják a ClaimsTransformation meghívása után. Az ilyen elemek egy ClaimsSchema szakasz már definiálva takar hivatkozást tartalmaz. |

### <a name="inputclaims"></a>InputClaims

A **InputClaims** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Bemeneti jogcím | 1:n | Egy várt bemeneti jogcím típusa. |

#### <a name="inputclaim"></a>Bemeneti jogcím

A **bemeneti jogcím** elem tartalmazza a következő attribútumokat:

| Attribútum |Szükséges | Leírás |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Igen | A hivatkozás egy már az ClaimsSchema szakaszban, a szabályzatban meghatározott takar. |
| TransformationClaimType |Igen | Egy azonosító való hivatkozáshoz egy átalakítás jogcím típusa. Minden egyes jogcím-átalakítást saját értékekkel rendelkezik. Tekintse meg a [jogcím-átalakítási referencia](#claims-transformations-reference) teljes listáját az elérhető értékek. |

### <a name="inputparameters"></a>InputParameters

A **InputParameters** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| InputParameter | 1:n | Egy várt bemeneti paraméter. |

#### <a name="inputparameter"></a>InputParameter

| Attribútum | Szükséges |Leírás |
| --------- | ----------- |----------- |
| Azonosító | Igen | A jogcím átalakítási metódus paramétereként hivatkozás azonosítója. Minden egyes jogcím átalakítási metódus saját értékekkel rendelkezik. Tekintse meg a jogcímek átalakítása tábla teljes listáját az elérhető értékek. |
| Adattípus | Igen | A data paraméter, például a karakterláncot, logikai érték beolvasása, Int vagy dátum és idő alapján a DataType enumerálása az egyéni házirend XML-séma típusa. Ez a típus aritmetikai műveletek megfelelő végrehajtásához használatos. Minden egyes jogcím-átalakítást saját értékekkel rendelkezik. Tekintse meg a [jogcím-átalakítási referencia](#claims-transformations-reference) teljes listáját az elérhető értékek. |
| Érték | Igen | Egy érték, amely az átalakítás átadott szó. Egyes értékek tetszőleges, néhányat, válassza a jogcímek átalakítása metódus. |

### <a name="outputclaims"></a>OutputClaims

A **OutputClaims** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | Várt kimenet jogcím típusa. |

#### <a name="outputclaim"></a>OutputClaim 

A **kimeneti jogcím** elem tartalmazza a következő attribútumokat:

| Attribútum |Szükséges | Leírás |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Igen | A hivatkozás egy már az ClaimsSchema szakaszban, a szabályzatban meghatározott takar.
| TransformationClaimType | Igen | Egy azonosító való hivatkozáshoz egy átalakítás jogcím típusa. Minden egyes jogcím-átalakítást saját értékekkel rendelkezik. Tekintse meg a [jogcím-átalakítási referencia](#claims-transformations-reference) teljes listáját az elérhető értékek. |
 
Ha a bemeneti jogcímek és a kimeneti jogcímek ugyanolyan típusú (karakterlánc vagy logikai), használhatja ugyanazt a bemeneti jogcímet a kimeneti jogcímek. Ebben az esetben a jogcímek átalakításáról módosítja a bemenetijogcím kimeneti értékkel.

## <a name="example"></a>Példa

Például előfordulhat, hogy tárolja a szolgáltatások, amelyek a felhasználó elfogadja a használati legutóbbi verzióját. Amikor frissíti a feltételeket, a szolgáltatások, megkérheti a felhasználót, hogy fogadja el az új verzióra. A következő példában a **HasTOSVersionChanged** jogcímek átalakítását értékét hasonlítja össze a **TOSVersion** jogcím értékét a **LastTOSAcceptedVersion**jogcím és a logikai érték beolvasása, majd **TOSVersionChanged** jogcím.

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

## <a name="claims-transformations-reference"></a>Jogcímek átalakítások referencia

A jogcímek átalakítása példákért lásd a következő hivatkozás oldalakat:

- [Logikai érték](boolean-transformations.md)
- [Dátum](date-transformations.md)
- [Egész szám](integer-transformations.md)
- [JSON](json-transformations.md)
- [Általános](general-transformations.md)
- [Közösségi fiók](social-transformations.md)
- [Karakterlánc](string-transformations.md)
- [Publishedresources](stringcollection-transformations.md)

