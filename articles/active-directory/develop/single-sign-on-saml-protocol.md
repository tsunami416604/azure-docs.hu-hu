---
title: Azure egyszeri bejelentkezéses SAML-protokoll | Microsoft Docs
description: Ez a cikk az egyszeri bejelentkezési SAML protokollt ismerteti Azure Active Directory
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: cecb78a82eb2925813bdc7f6df2503fae94b6437
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375677"
---
# <a name="single-sign-on-saml-protocol"></a>Egyszeri bejelentkezéses SAML protokoll

Ez a cikk az SAML 2,0 hitelesítési kéréseit és válaszait ismerteti, amelyeket Azure Active Directory (Azure AD) támogat az egyszeri bejelentkezéshez.

Az alábbi protokoll-diagram az egyszeri bejelentkezési sorozatot ismerteti. A Cloud Service (a szolgáltató) egy HTTP-átirányítási kötést használ egy `AuthnRequest` (hitelesítési kérelem) elem átadására az Azure AD-be (az identitás-szolgáltató). Az Azure AD ezután egy HTTP Post-kötést használ a `Response` elemnek a Cloud Service-be való közzétételéhez.

![Egyszeri bejelentkezés munkafolyamata](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Felhasználói hitelesítés igényléséhez a Cloud Services `AuthnRequest` elemet küld az Azure AD-nek. Az SAML-2,0 minta `AuthnRequest` a következő példához hasonlóan néz ki:

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
| ID (Azonosító) | Kötelező | Az Azure AD ezt az attribútumot használja a visszaadott válasz `InResponseTo` attribútumának feltöltéséhez. Az azonosító nem kezdődhet számmal, ezért a közös stratégia egy olyan karakterláncot, mint az "id", egy GUID karakterlánc-ábrázolására. Például `id6c1c178c166d486687be4aaf5e482730` érvényes azonosító. |
| Verzió | Kötelező | Ezt a paramétert **2,0**-re kell állítani. |
| IssueInstant | Kötelező | Ez egy UTC-értékkel rendelkező DateTime karakterlánc, amely az ["o" formátummal](https://msdn.microsoft.com/library/az4se3k1.aspx)rendelkezik. Az Azure AD egy ilyen típusú DateTime értéket vár, de nem értékeli vagy nem használja az értéket. |
| AssertionConsumerServiceUrl | Optional | Ha meg van adni, ennek a paraméternek meg kell egyeznie a Cloud Service `RedirectUri` az Azure AD-ben. |
| ForceAuthn | Optional | Ez egy logikai érték. Ha az értéke igaz, az azt jelenti, hogy a felhasználónak újra hitelesítenie kell magát, még akkor is, ha az Azure AD-vel érvényes munkamenetük van. |
| IsPassive | Optional | Ez egy logikai érték, amely azt határozza meg, hogy az Azure AD-nak csendes felhasználói beavatkozás nélkül kell-e hitelesítenie a felhasználót a munkamenet-cookie-val, ha van ilyen. Ha ez igaz, az Azure AD megkísérli hitelesíteni a felhasználót a munkamenet-cookie használatával. |

A **rendszer figyelmen kívül hagyja**az összes többi `AuthnRequest` attribútumot, például a beleegyezett, a célhelyet, a AssertionConsumerServiceIndex, a AttributeConsumerServiceIndex és a ProviderName.

Az Azure AD a `AuthnRequest``Conditions` elemét is figyelmen kívül hagyja.

### <a name="issuer"></a>Kiállító

Egy `AuthnRequest` `Issuer` elemének pontosan egyeznie kell az Azure AD felhőalapú szolgáltatásának egyik **ServicePrincipalNames** . Ez általában az alkalmazás regisztrációja során megadott **alkalmazás-azonosító URI-ra** van beállítva.

A `Issuer` elemet tartalmazó SAML-kivonat az alábbi példához hasonlóan néz ki:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Ez az elem egy adott név-azonosító formátumot kér a válaszban, és nem kötelező `AuthnRequest` az Azure AD-ba küldött elemek esetében.

A `NameIdPolicy` elem az alábbi példához hasonlóan néz ki:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Ha `NameIDPolicy` van megadva, a választható `Format` attribútumot is megadhatja. A `Format` attribútumnak a következő értékek egyike lehet: bármely más érték hibát eredményez.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory a NameID-jogcímet páros-azonosítóként adja ki.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: a Azure Active Directory a NameID-jogcímet e-mail cím formátumban adja ki.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: ez az érték engedélyezi a Azure Active Directory számára a jogcím formátumának kiválasztását. Azure Active Directory a NameID páros-azonosítóként adja ki a problémát.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory a NameID jogcím véletlenszerűen generált értékként jelenik meg, amely egyedi az aktuális egyszeri bejelentkezési művelethez. Ez azt jelenti, hogy az érték ideiglenes, és nem használható a hitelesítő felhasználó azonosítására.

Az Azure AD figyelmen kívül hagyja a `AllowCreate` attribútumot.

### <a name="requestauthncontext"></a>RequestAuthnContext
A `RequestedAuthnContext` elem határozza meg a kívánt hitelesítési módszereket. A `AuthnRequest` az Azure AD-be eljuttatott elemek esetében nem kötelező. Az Azure AD olyan `AuthnContextClassRef` értékeket támogat, mint például a `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Tartalmazó
Az `Scoping` elem, amely tartalmazza az identitás-szolgáltatók listáját, nem kötelező az Azure AD-ben eljuttatott `AuthnRequest` elemek esetében.

Ha meg van adni, ne adja meg a `ProxyCount` attribútumot, `IDPListOption` vagy `RequesterID` elemet, mivel azok nem támogatottak.

### <a name="signature"></a>Aláírás
Ne tartalmazzon `Signature` elemet `AuthnRequest` elemekben, mivel az Azure AD nem támogatja az aláírt hitelesítési kérelmeket.

### <a name="subject"></a>Tárgy
Az Azure AD figyelmen kívül hagyja `AuthnRequest` elemek `Subject` elemét.

## <a name="response"></a>Válasz
Ha a kért bejelentkezés sikeresen befejeződik, az Azure AD választ küld a Cloud Service-nek. A sikeres bejelentkezési kísérletre adott válasz az alábbi példához hasonlóan néz ki:

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

A `Response` elem az engedélyezési kérelem eredményét tartalmazza. Az Azure AD a `Response` elemben lévő `ID`, `Version` és `IssueInstant` értékeket állítja be. A következő attribútumokat is beállítja:

* `Destination`: Ha a bejelentkezés sikeresen befejeződött, ez a szolgáltató (Cloud Service) `RedirectUri`ra van beállítva.
* `InResponseTo`: a választ kezdeményező `AuthnRequest` elem `ID` attribútumára van beállítva.

### <a name="issuer"></a>Kiállító

Az Azure AD úgy állítja be a `Issuer` elemet, hogy `https://login.microsoftonline.com/<TenantIDGUID>/`, ahol a \<TenantIDGUID > Az Azure AD-bérlő bérlői azonosítója.

A kiállítói elemmel kapcsolatos válasz például a következő mintához hasonlóan néz ki:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>status

A `Status` elem a bejelentkezés sikerességét vagy sikertelenségét közvetíti. Tartalmazza a `StatusCode` elemet, amely tartalmaz egy kódot vagy egy beágyazott kódot, amely a kérelem állapotát jelöli. Emellett tartalmazza a `StatusMessage` elemet is, amely a bejelentkezési folyamat során létrehozott egyéni hibaüzeneteket tartalmazza.

<!-- TODO: Add an authentication protocol error reference -->

Az alábbi példa egy SAML-válasz egy sikertelen bejelentkezési kísérletre.

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

### <a name="assertion"></a>Állítás

A `ID`, `IssueInstant` és `Version`mellett az Azure AD az alábbi elemeket állítja be a válasz `Assertion` elemében.

#### <a name="issuer"></a>Kiállító

Ez úgy van beállítva, hogy `https://sts.windows.net/<TenantIDGUID>/`, hogy \<TenantIDGUID > Az Azure AD-bérlő bérlői azonosítója.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Aláírás

Az Azure AD a sikeres bejelentkezésre adott válaszként aláírja az állítást. A `Signature` elem egy digitális aláírást tartalmaz, amelyet a felhőalapú szolgáltatás a forrás hitelesítésére használhat a kikényszerítés integritásának ellenőrzéséhez.

A digitális aláírás létrehozásához az Azure AD az aláíró kulcsot használja a metaadat-dokumentum `IDPSSODescriptor` elemében.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Tárgy

Ez határozza meg az utasításban szereplő utasítások tárgyát képező rendszerbiztonsági tag. `NameID` elemet tartalmaz, amely a hitelesített felhasználót jelöli. A `NameID` érték olyan célzott azonosító, amely csak a jogkivonat célközönségét képező szolgáltatóhoz van irányítva. Állandó – visszavonható, de soha nem lesz hozzárendelve. Az is átlátszatlan, hogy nem fedi fel a felhasználót, és nem használható azonosítóként az attribútumok lekérdezéséhez.

A `SubjectConfirmation` elem `Method` attribútuma mindig `urn:oasis:names:tc:SAML:2.0:cm:bearer`re van állítva.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Feltételek

Ez az elem olyan feltételeket határoz meg, amelyek meghatározzák az SAML-érvényesítések elfogadható használatát.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

A `NotBefore` és `NotOnOrAfter` attribútumok határozzák meg azt az időközt, amely alatt az érvényesítés érvényes.

* A `NotBefore` attribútum értéke a `Assertion` elem `IssueInstant` attribútumának értékeként vagy valamivel kisebb (egy másodpercnél rövidebb) értékkel egyenlő. Az Azure AD nem veszi figyelembe a saját maga és a felhőalapú szolgáltatás (szolgáltató) közötti időeltérést, és nem ad hozzá puffert ehhez az időponthoz.
* A `NotOnOrAfter` attribútum értéke 70 perccel későbbi, mint a `NotBefore` attribútum értéke.

#### <a name="audience"></a>Célközönség

Ez egy olyan URI-t tartalmaz, amely a célközönséget azonosítja. Az Azure AD az elem értékét a bejelentkezést kezdeményező `AuthnRequest` `Issuer` elemének értékére állítja be. Az `Audience` érték kiértékeléséhez használja az alkalmazás regisztrációja során megadott `App ID URI` értékét.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

A `Issuer` értékhez hasonlóan a `Audience` értéknek pontosan egyeznie kell az Azure AD-ben a Cloud Service-t jelképező egyszerű szolgáltatásnév egyikével. Ha azonban a `Issuer` elem értéke nem URI-érték, a válaszban szereplő `Audience` érték az `Issuer` érték `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Ez a tulajdonossal vagy a felhasználóval kapcsolatos jogcímeket tartalmaz. Az alábbi részlet egy minta `AttributeStatement` elemet tartalmaz. A három pont azt jelzi, hogy az elem több attribútumot és attribútumérték is tartalmazhat.

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

* **Név jogcím** – a `Name` attribútum (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) értéke a hitelesített felhasználó egyszerű felhasználóneve, például `testuser@managedtenant.com`.
* **Típusinformáció jogcím** – a `ObjectIdentifier` attribútum (`http://schemas.microsoft.com/identity/claims/objectidentifier`) értéke az Azure ad-ben a hitelesített felhasználót jelképező Directory-objektum `ObjectId`. a `ObjectId` egy nem módosítható, globálisan egyedi, és a hitelesített felhasználó biztonságos azonosítóját használja.

#### <a name="authnstatement"></a>AuthnStatement

Ez az elem azt állítja be, hogy az állítás tárgyát egy adott időpontban hitelesítették.

* A `AuthnInstant` attribútum azt az időpontot határozza meg, amikor a felhasználó az Azure AD-vel lett hitelesítve.
* A `AuthnContext` elem határozza meg a felhasználó hitelesítéséhez használt hitelesítési környezetet.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
