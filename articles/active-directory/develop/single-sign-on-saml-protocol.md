---
title: Az Azure egyszeri bejelentkezési SAML-protokoll |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Active Directoryban az egyszeri bejelentkezés a SAML protokoll
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: dd9bdc4638d1c055706026798acba08d6add08c7
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098748"
---
# <a name="single-sign-on-saml-protocol"></a>Egyszeri bejelentkezéses SAML-protokoll

Ez a cikk ismerteti a SAML 2.0-s hitelesítési kérelmek és válaszok, amelyek az Azure Active Directory (Azure AD) támogatja az egyszeri bejelentkezést.

Az alábbi ábrán a protokoll műveleti sorrend a egyszeri bejelentkezést. A cloud service (a szolgáltató) egy HTTP-átirányítási kötésben használatával adja át egy `AuthnRequest` (hitelesítés) elemet az Azure ad-hez (az identitásszolgáltató). Ezután az Azure AD egy HTTP post közzététele kötés használja egy `Response` elem a felhőszolgáltatáshoz.

![A munkafolyamat az egyszeri bejelentkezés](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

A felhasználói hitelesítést igényel, a cloud services – küldése egy `AuthnRequest` elem az Azure ad-hez. SAML 2.0 minta `AuthnRequest` rákeresve az alábbi példához hasonlóan:

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
| ID (Azonosító) | Szükséges | Azure ad-ben ezt az attribútumot használja feltölti a `InResponseTo` a visszaadott válasz attribútum. Azonosító kell nem kezdődhet számmal, így egy általános stratégia az, hogy egy karakterlánc, például a "id" egy GUID azonosító karakterlánc-ábrázolásra jogosultságokat. Ha például `id6c1c178c166d486687be4aaf5e482730` van egy érvényes azonosítót. |
| Verzió | Szükséges | Ezt a paramétert kell megadni **2.0**. |
| IssueInstant | Szükséges | Egy dátum/idő karakterlánc UTC értékkel és [oda-vissza formátumot ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure ad-ben az ilyen típusú dátum/idő értéket vár, de nem értékeli, vagy használja az értéket. |
| AssertionConsumerServiceUrl | Optional | Ha meg van adva, ezt a paramétert meg kell egyeznie a `RedirectUri` a felhőalapú szolgáltatás, az Azure ad-ben. |
| ForceAuthn | Optional | Ez egy olyan logikai érték. Ha az értéke igaz, az azt jelenti, hogy a felhasználónak meg kell változtatniuk a újra hitelesíteni kell, még akkor is, ha rendelkezik egy érvényes Azure AD-munkamenetet. |
| IsPassive | Optional | Ez az egy logikai érték, amely meghatározza, hogy az Azure AD kell hitelesíteni a felhasználót csendes módban, a felhasználó beavatkozása nélkül a munkamenetcookie-t használja, ha van ilyen. Ha ez igaz, az Azure AD megpróbálja hitelesíteni a felhasználót a munkamenet cookie-k használatával. |

Az összes többi `AuthnRequest` attribútumok, például a jóváhagyás, cél, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex és a ProviderName **figyelmen kívül hagyja**.

Azure ad-ben is figyelmen kívül hagyja a `Conditions` elemében `AuthnRequest`.

### <a name="issuer"></a>Kiállító

A `Issuer` eleme egy `AuthnRequest` pontosan egyeznie kell a **ServicePrincipalNames** a felhőszolgáltatás az Azure ad-ben. Általában ez értékre van állítva a **Alkalmazásazonosító URI-t** alkalmazás regisztrációja során megadott.

A SAML cikkből szerint tartalmazó a `Issuer` elemhez az alábbi mintához hasonlóan néz ki:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Ez az elem egy adott Névazonosító formátuma a válaszban kér, és nem kötelező `AuthnRequest` küldeni az Azure AD elemeket.

A `NameIdPolicy` elemhez az alábbi mintához hasonlóan néz ki:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Ha `NameIDPolicy` nincs megadva, megadhatja, hogy a nem kötelező `Format` attribútum. A `Format` attribútum csak a következők egyike lehet értékek; hiba más értéket eredményez.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Az Azure Active Directory páros azonosítóként a NameID jogcím problémák.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Az Azure Active Directory bocsát ki a NameID jogcím e-mail cím formátumú.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Ez az érték lehetővé teszi az Azure Active Directoryval, válassza ki a jogcím-formátumot. Az Azure Active Directory páros azonosítóként a NameID problémák.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Az Azure Active Directory a NameID jogcím bocsát ki, amely egyedi az aktuális egyszeri bejelentkezési művelet véletlenszerűen létrehozott értéket. Ez azt jelenti, hogy az érték ideiglenes, és azonosíthatja a hitelesítendő felhasználó nem használható.

Az Azure AD figyelmen kívül hagyja a `AllowCreate` attribútum.

### <a name="requestauthncontext"></a>RequestAuthnContext
A `RequestedAuthnContext` elem azt határozza meg a kívánt hitelesítési módszereket. A nem kötelező `AuthnRequest` küldeni az Azure AD elemeket. Az Azure AD csak egyet támogat `AuthnContextClassRef` érték: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Hatókörkezeléshez kapcsolódó
A `Scoping` elem, amely tartalmazza az Identitásszolgáltatók listája, nem kötelező `AuthnRequest` küldeni az Azure AD elemeket.

Ha meg van adva, nem tartalmazza a `ProxyCount` attribútum, `IDPListOption` vagy `RequesterID` elem, mivel azok nem támogatottak.

### <a name="signature"></a>Aláírás
Nem tartalmazza a `Signature` elemében `AuthnRequest` elemek, az Azure AD nem támogatja az aláírt hitelesítési kéréseket.

### <a name="subject"></a>Tárgy
Az Azure AD figyelmen kívül hagyja a `Subject` eleme `AuthnRequest` elemeket.

## <a name="response"></a>Válasz
A kért bejelentkezés sikeresen befejeződik, ha az Azure AD választ a felhőszolgáltatáshoz tesz közzé. Adott válasz egy sikeres bejelentkezési kísérlet után az alábbi mintához hasonlóan néz ki:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
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

### <a name="response"></a>Válasz

A `Response` elem tartalmazza a hitelesítési kérelem eredményét. Az Azure AD-csoportok a `ID`, `Version` és `IssueInstant` lévő értékeknek a `Response` elemet. Állítja be a következő attribútumokat:

* `Destination`: Ha a bejelentkezés sikeresen befejeződik, a beállított érték a `RedirectUri` a szolgáltató (felhőszolgáltatás).
* `InResponseTo`: A beállított érték a `ID` attribútuma a `AuthnRequest` elem által kezdeményezett a választ.

### <a name="issuer"></a>Kiállító

Az Azure AD-csoportok a `Issuer` elem `https://login.microsoftonline.com/<TenantIDGUID>/` ahol <TenantIDGUID> a bérlő az Azure AD-bérlő azonosítója.

Például a kibocsátó elemmel választ az alábbi mintához hasonlóan rákeresve:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>status

A `Status` elem tartalmát, a sikeres vagy sikertelen bejelentkezés. Ez magában foglalja a `StatusCode` elem, amely tartalmazza a kódot vagy egy beágyazott kódot, amely a kérés állapotát jelöli. Ezenkívül tartalmazza a `StatusMessage` elem, amely tartalmazza a bejelentkezési folyamat során létrehozott egyéni hibaüzenetek.

<!-- TODO: Add a authentication protocol error reference -->

Az alábbi minta a SAML-válasz, a sikertelen bejelentkezési kísérlet.

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

### <a name="assertion"></a>helyességi feltétel

Mellett a `ID`, `IssueInstant` és `Version`, az Azure AD állítja be a következő elemeket az `Assertion` elem a válasz.

#### <a name="issuer"></a>Kiállító

A beállított érték `https://sts.windows.net/<TenantIDGUID>/`ahol <TenantIDGUID> a bérlő az Azure AD-bérlő azonosítója.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Aláírás

Az Azure AD aláírja a helyességi feltétel, sikeres bejelentkezés válaszként. A `Signature` elem tartalmazza, amely a felhőalapú szolgáltatás segítségével hitelesíti a helyességi feltétel sértetlenségének ellenőrzéséhez a forrás digitális aláírást.

A digitális aláírását létrehozó, Azure ad-ben a aláíró kulcs használ a `IDPSSODescriptor` elem a hozzá tartozó metaadat-dokumentumban.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Tárgy

Megadja az egyszerű, amely az utasításokat a helyességi feltétel tárgya. Tartalmaz egy `NameID` elemet, amely a hitelesített felhasználó jelöli. A `NameID` egy célzott azonosítója, amely csak a szolgáltató, amely a jogkivonat célközönség van irányítva. Állandó – visszavonhatók, de soha nem kell hozzárendelni. Emellett akkor is nem átlátszó, abban, hogy a felhasználóval kapcsolatos semmit nem fed, és nem használható azonosító attribútum lekérdezések.

A `Method` attribútuma a `SubjectConfirmation` elem értéke mindig `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Feltételek

Ezt az elemet adja meg a használati feltételek SAML helyességi feltételek meghatározó feltételeket.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

A `NotBefore` és `NotOnOrAfter` attribútumok adja meg az időközt, amely során a helyességi feltétel érvénytelen.

* Értékét a `NotBefore` attribútum megegyezik, vagy kismértékben (kevesebb mint egy másodperc) értéke későbbi `IssueInstant` attribútuma a `Assertion` elemet. Az Azure AD nem veszi figyelembe bármely időeltérése magát, és a felhőszolgáltatás (szolgáltató), és nem adható hozzá bármilyen puffer most.
* Értékét a `NotOnOrAfter` attribútum értéke 70 perccel később, mint az érték a `NotBefore` attribútum.

#### <a name="audience"></a>Célközönség

Egy URI-t, amely azonosítja a célközönség tartalmazza. Azure ad-ben ez az elem értékének beállítása értékéhez `Issuer` eleme a `AuthnRequest` , amely a bejelentkezés által kezdeményezett. Kiértékelheti, hogy a `Audience` értékét pedig a értéket, a `App ID URI` , amely alkalmazás regisztrációja során megadott.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Például a `Issuer` érték, a `Audience` érték pontosan egyeznie kell az egyszerű szolgáltatásnevet, amely a felhőszolgáltatás jelöli az Azure ad-ben. Azonban ha értékét a `Issuer` elem nem egy URI értéket a `Audience` értéke a válaszban a `Issuer` érték előtaggal `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

A tulajdonos vagy a felhasználói jogcímek tartalmazza. A következő cikkből szerint tartalmaz egy mintát `AttributeStatement` elemet. A három pontra, az azt jelzi, hogy az elem tartalmazhat, attribútumokat és a attribútumértékek.

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

* **Jogcímszabály neve** -értékét a `Name` attribútum (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) el például a hitelesített felhasználó egyszerű felhasználónevét `testuser@managedtenant.com`.
* **ObjectIdentifier jogcím** -értékét a `ObjectIdentifier` attribútum (`http://schemas.microsoft.com/identity/claims/objectidentifier`) van a `ObjectId` a az Azure ad-ben a hitelesített felhasználó képviselő objektum. `ObjectId` egy nem módosítható, globálisan egyedi, és újra felhasználhatja a hitelesített felhasználó biztonságos azonosítója.

#### <a name="authnstatement"></a>AuthnStatement

Ez az elem használjon esetleg imperatív állításokat, hogy a helyességi feltétel tulajdonos hitelesített egy adott azt jelenti, hogy egy adott időpontban.

* A `AuthnInstant` attribútum meghatározza, hogy az idő, amellyel hitelesíti a felhasználót az Azure ad-ben.
* A `AuthnContext` elem azt határozza meg a hitelesítési környezetet, a felhasználó hitelesítéséhez.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
