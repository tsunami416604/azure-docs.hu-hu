---
title: "SAML protokoll Azure az egyszeri bejelentkezés |} Microsoft Docs"
description: "Ez a cikk ismerteti az egyszeri bejelentkezést a SAML protokoll, az Azure Active Directoryban"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: f41402fc2cb282975b93071d998365fdb0a21941
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# Egyszeri bejelentkezés SAML protokoll
Ez a cikk ismerteti a SAML 2.0 hitelesítési kérések és válaszok, amelyek az Azure Active Directory (Azure AD) támogatja az egyszeri bejelentkezést.

A protokoll az alábbi ábrán az egyszeri bejelentkezés feladatütemezési ismerteti. A felhőszolgáltatás (a service provider) egy HTTP-átirányítás kötés használatával adjon át egy `AuthnRequest` (hitelesítési kérelem) elemet, az Azure AD (az identitásszolgáltató). Az Azure AD majd elküldeni a kötés HTTP post használ egy `Response` elem a felhőalapú szolgáltatáshoz.

![Az egyszeri bejelentkezés munkafolyamat](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## AuthnRequest
A felhasználói hitelesítést igényel, a cloud services – küldés egy `AuthnRequest` elem az Azure ad Szolgáltatásba. Egy minta SAML 2.0 `AuthnRequest` nézhet ki:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```


| Paraméter |  | Leírás |
| --- | --- | --- |
| ID (Azonosító) |Szükséges |Az Azure AD használja ezt az attribútumot feltölti a `InResponseTo` a visszaadott válasz attribútum. Azonosító nem kezdődhetnek egy szám, így a közös stratégia illesztenie egy karakterlánc például a "id" GUID karakterláncos ábrázolása. Például `id6c1c178c166d486687be4aaf5e482730` van egy érvényes azonosítót. |
| Verzió |Szükséges |Ez legyen **2.0**. |
| IssueInstant |Szükséges |Ez egy dátum és idő (UTC) értékkel és [körbejárási formátumban ("no")](https://msdn.microsoft.com/library/az4se3k1.aspx). Az Azure AD egy DateTime értéket az ilyen típusú vár, de nem kiértékelheti vagy az értéke. |
| AssertionConsumerServiceUrl |Nem kötelező |Ha a megadott, ennek egyeznie kell a `RedirectUri` a felhőalapú szolgáltatás, az Azure ad-ben. |
| ForceAuthn |Nem kötelező | Ez egy olyan logikai érték. Igaz értéke esetén ez azt jelenti, hogy a felhasználó lenne kényszerítve újra hitelesíteni, még akkor is, ha azok rendelkeznek-e egy érvényes Azure AD-munkamenetet. |
| IsPassive |Nem kötelező |Ez az egy logikai érték, amely meghatározza, hogy az Azure AD kell a felhasználói beavatkozás nélküli hitelesítés, felhasználói beavatkozás nélkül a munkamenet cookie-k használata, ha van ilyen. Ha ez igaz, az Azure AD megpróbál hitelesíteni a felhasználót, a munkamenet cookie-k használatával. |

Minden más `AuthnRequest` attribútumok, például a hozzájárulási, cél, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex és ProviderName **figyelmen kívül hagyva**.

Az Azure AD is figyelmen kívül hagyja a `Conditions` elemében `AuthnRequest`.

### Kiállító
A `Issuer` eleme egy `AuthnRequest` pontosan egyeznie kell a **ServicePrincipalNames** az Azure AD-ben a felhőalapú szolgáltatáshoz. Általában értékre van állítva a **App ID URI** regisztrációja során meghatározott.

Egy minta SAML cikkből tartalmazó a `Issuer` elem néz ki:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### NameIDPolicy
Ez az elem egy adott név azonosítóformátumának a válaszban szereplő kér, és nem kötelező a `AuthnRequest` az Azure AD elküldött elemek.

Egy minta `NameIdPolicy` elem néz ki:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Ha `NameIDPolicy` van megadva, megadhatja, hogy a nem kötelező `Format` attribútum. A `Format` attribútum csak a következők egyike lehet érték; a hiba más értéket eredményez.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Az azure Active Directory páros azonosítóként ad ki a NameID jogcímet.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Az azure Active Directory az e-mail cím formátumú NameID jogcímet ad ki.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Ez az érték lehetővé teszi az Azure Active Directory válassza ki a jogcímet formátumot. Az Azure Active Directory a NameID páros azonosítóként ad ki.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Az azure Active Directory egy véletlenszerűen generált érték, amely egyedi az aktuális művelet SSO, ad ki a NameID jogcímet. Ez azt jelenti, hogy az érték ideiglenes, és nem használható a hitelesítendő felhasználó azonosításához.

Az Azure AD figyelmen kívül hagyja a `AllowCreate` attribútum.

### RequestAuthnContext
A `RequestedAuthnContext` elem azt adja meg a kívánt hitelesítési módszereket. Nem kötelező a `AuthnRequest` az Azure AD elküldött elemek. Az Azure AD támogatja a csak egy `AuthnContextClassRef` érték: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### Hatókör
A `Scoping` elem, amely identitás-szolgáltatóktól listáját tartalmazza, nem kötelező megadni a `AuthnRequest` az Azure AD elküldött elemek.

Ha a megadott, nem tartalmaznak a `ProxyCount` attribútum, `IDPListOption` vagy `RequesterID` elem, való használata nem támogatott.

### Aláírás
Ne vegyen fel egy `Signature` elemében `AuthnRequest` elemek, mert nem támogatja az Azure AD aláírt hitelesítési kérelmek.

### Tárgy
Az Azure AD figyelmen kívül hagyja a `Subject` eleme `AuthnRequest` elemek.

## Válasz
Amikor a kért bejelentkezés sikeresen befejeződött, az Azure AD küldi a felhőalapú szolgáltatást választ. Egy sikeres bejelentkezési kísérlet mintát választ így néz ki:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### Válasz
A `Response` elemet tartalmaz, a hitelesítési kérelem eredményét. Az Azure AD-készletek a `ID`, `Version` és `IssueInstant` értékei a `Response` elemet. Állítja be a következő attribútumokat:

* `Destination`: Ha a bejelentkezés sikeresen befejeződött, értékre van állítva a `RedirectUri` a szolgáltató (felhőszolgáltatás).
* `InResponseTo`: Ez a `ID` attribútuma a `AuthnRequest` elem, amely a válasz kezdeményezett.

### Kiállító
Az Azure AD-készletek a `Issuer` elem `https://login.microsoftonline.com/<TenantIDGUID>/` ahol <TenantIDGUID> a bérlő azonosítója az Azure AD-bérlő.

Például kibocsátó elemet tartalmazó mintát választ nézhet ki:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### status
A `Status` elem tartalmát, a sikeres vagy sikertelen volt-e a bejelentkezésre. Ez magában foglalja a `StatusCode` elemmel rendelkezik, ami egy kódot vagy egy beágyazott kódot, amelyek megfelelnek a kérés állapotát tartalmazza. Ezenkívül tartalmazza a `StatusMessage` elem, amely tartalmazza az egyéni hibaüzeneteket, amelyek akkor jönnek létre, a bejelentkezés során.

<!-- TODO: Add a authentication protocol error reference -->

Sikertelen bejelentkezési kísérlet SAML választ a következő:

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### helyességi feltétel
Kívül a `ID`, `IssueInstant` és `Version`, az Azure AD a következő elemeket állít be a `Assertion` elem a válasz.

#### Kiállító
A beállított érték `https://sts.windows.net/<TenantIDGUID>/`ahol <TenantIDGUID> a bérlő azonosítója az Azure AD-bérlő.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### Aláírás
Az Azure AD-szal válaszul egy sikeres bejelentkezés állítás. A `Signature` elem, amely a felhőszolgáltatás segítségével hitelesíti a helyességi feltétel sértetlenségének ellenőrzéséhez a forrás digitális aláírást tartalmaz.

A digitális aláírásának létrehozásához az Azure AD az aláírási kulcs használja a `IDPSSODescriptor` eleme a metaadat-dokumentum.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### Tárgy
Azt határozza meg, hogy a rendszerbiztonsági tag, amely a helyességi feltétel lévő utasítások tárgya. Tartalmaz egy `NameID` elemet, amely a hitelesített felhasználó jelöli. A `NameID` egy célzott azonosítója, amely csak a szolgáltató, amely a felhasználók, akik a token van átirányítva. Fontos állandó - visszavonhatók, de soha nem rendelik. Akkor is nem átlátszó, abban, hogy a felhasználó semmit nem fed, és nem használható azonosító attribútum lekérdezések.

A `Method` attribútuma a `SubjectConfirmation` elem értéke mindig `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### Feltételek
Ez az elem megadja a SAML helyességi feltételek használati meghatározó feltételeket.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

A `NotBefore` és `NotOnOrAfter` attribútumok adja meg az időközt, amely alatt a helyességi feltétel érvénytelen.

* Értékét a `NotBefore` attribútum megegyezik a vagy némileg (kevesebb mint egy második) értéke legkésőbb `IssueInstant` attribútuma a `Assertion` elemet. Az Azure AD nem derül ki bármely eltérést és a felhőszolgáltatás (szolgáltató) között, és a puffer nem adja hozzá a idő.
* Értékét a `NotOnOrAfter` attribútum 70 perccel értékének a `NotBefore` attribútum.

#### Célközönség
URI, amely azonosítja a célközönség tartalmazza. Az Azure AD beállítja az elem értékének értékének `Issuer` eleme a `AuthnRequest` , amely a bejelentkezés által kezdeményezett. Kiértékelheti a `Audience` értéket, az értéket a `App ID URI` alkalmazás regisztráció során megadott.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Például a `Issuer` érték, a `Audience` értéke pontosan egyeznie kell az Azure AD felhőalapú szolgáltatás jelölő egyszerű szolgáltatásnevet. Azonban ha értékének a `Issuer` elem nincs URI érték, a `Audience` a válaszban szereplő érték a `Issuer` érték előtagként `spn:`.

#### AttributeStatement
A tulajdonos vagy a felhasználói jogcímek tartalmazza. A következő cikkből tartalmaz egy mintát `AttributeStatement` elemet. A három pont azt jelzi, hogy az elem tartalmazhatnak több attribútumok és attribútumértékek.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Jogcímszabály neve** : értékének a `Name` attribútum (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) van, mint az egyszerű felhasználónév a hitelesített felhasználó `testuser@managedtenant.com`.
* **Típusinformáció jogcím** : értékének a `ObjectIdentifier` attribútum (`http://schemas.microsoft.com/identity/claims/objectidentifier`) van a `ObjectId` a Azure AD-ben a hitelesített felhasználó képviselő objektum. `ObjectId`egy nem módosítható, globálisan egyedi, és újra felhasználhatja a hitelesített felhasználó biztonságos azonosítója.

#### AuthnStatement
Ez az elem állításokat, hogy a helyességi feltétel tulajdonos hitelesített egy adott azt jelenti, hogy egy adott időpontban.

* A `AuthnInstant` attribútum meghatározza, hogy az idő, amellyel hitelesíti a felhasználót az Azure ad-val.
* A `AuthnContext` elem megadja a hitelesítési környezetet, a felhasználó hitelesítésére használt.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```