---
title: Az Azure egyszeri bejelentkezés az SAML protokollon
description: Ez a cikk az Azure Active Directory egyszeri bejelentkezéses SAML protokollját ismerteti
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: f1437ec5d9c3fd0ff69be0c884c340cb857ee181
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881282"
---
# <a name="single-sign-on-saml-protocol"></a>Egyszeri bejelentkezésSAML protokoll

Ez a cikk ismerteti az SAML 2.0 hitelesítési kérelmek és válaszok, amelyek az Azure Active Directory (Azure AD) támogatja az egyszeri bejelentkezés.

Az alábbi protokolldiagram az egyszeri bejelentkezési sorrendet írja le. A felhőszolgáltatás (a szolgáltató) http-átirányítási kötést használ egy `AuthnRequest` (hitelesítési kérelem) elem átadására az Azure AD-nek (az identitásszolgáltatónak). Az Azure AD ezután egy `Response` HTTP-bejegyzéskötést használ egy elem a felhőszolgáltatásba való közzétételéhez.

![Egyszeri bejelentkezésmunkafolyamat](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest (Hitelesítéskérése)

Felhasználói hitelesítés kéréséhez a felhőszolgáltatások egy elemet küldenek az `AuthnRequest` Azure AD-nek. A minta SAML `AuthnRequest` 2.0 a következő példához hasonlólehet:

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
| ID (Azonosító) | Kötelező | Az Azure AD ezt az attribútumot használja a `InResponseTo` visszaadott válasz attribútumának feltöltéséhez. Az azonosító nem kezdődhet számmal, ezért a közös stratégia az, hogy egy karakterláncot, például az "id"-t a GUID karakterlánc-ábrázolására készíti elő. Például `id6c1c178c166d486687be4aaf5e482730` egy érvényes azonosító. |
| Verzió | Kötelező | Ezt a paramétert **2.0-ra**kell állítani . |
| IssueInstant | Kötelező | Ez egy DATETime karakterlánc UTC értékkel és [oda-vissza formátummal ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Az Azure AD egy ilyen típusú DateTime-értéket vár, de nem értékeli ki vagy használja az értéket. |
| AssertionConsumerServiceUrl | Optional | Ha meg van adva, `RedirectUri` ennek a paraméternek meg kell egyeznie az Azure AD felhőszolgáltatásával. |
| ForceAuthn között | Optional | Ez egy logikai érték. If true, it means that the user will be forced to re-authenticate, even if they have a valid session with Azure AD. |
| Passzív | Optional | Ez egy logikai érték, amely meghatározza, hogy az Azure AD kell hitelesíteni a felhasználó ta- felhasználói beavatkozás nélkül, a munkamenet-cookie használatával, ha létezik ilyen. Ha ez igaz, az Azure AD megpróbálja hitelesíteni a felhasználót a munkamenet-cookie használatával. |

Az `AuthnRequest` összes többi attribútumot, például a Consent, Destination, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex és ProviderName **attribútumokat a rendszer figyelmen kívül hagyja.**

Az Azure AD `Conditions` is `AuthnRequest`figyelmen kívül hagyja az elemet.

### <a name="issuer"></a>Kiállító

Az `Issuer` elem `AuthnRequest` egy pontosan meg kell egyeznie az egyik **ServicePrincipalNames** a felhőszolgáltatás az Azure AD-ben. Ez általában az alkalmazásregisztráció során megadott **alkalmazásazonosító URI-ra** van beállítva.

Az elemet tartalmazó SAML-részlet a `Issuer` következő mintához hasonlóan néz ki:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDHázirend

Ez az elem egy adott névazonosító-formátumot `AuthnRequest` kér a válaszban, és nem kötelező az Azure AD-nek küldött elemekben.

Egy `NameIdPolicy` elem a következő mintához hasonlóan néz ki:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Ha `NameIDPolicy` meg van adva, `Format` megadhatja a választható attribútumot. Az `Format` attribútum nak csak az alábbi értékek egyike lehet; bármely más érték hibát eredményez.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Az Azure Active Directory a NameID jogcímet páros azonosítóként adja ki.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Az Azure Active Directory e-mail cím formátumban adja ki a NameID-jogcímet.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Ez az érték lehetővé teszi az Azure Active Directory számára a jogcímformátum kiválasztását. Az Azure Active Directory kiadja a NameID-t, mint egy páros azonosítót.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Az Azure Active Directory kiadja a NameID jogcímet, mint egy véletlenszerűen generált értéket, amely egyedi az aktuális SSO-művelet. Ez azt jelenti, hogy az érték ideiglenes, és nem használható a hitelesítő felhasználó azonosítására.

Az Azure AD `AllowCreate` figyelmen kívül hagyja az attribútumot.

### <a name="requestauthncontext"></a>RequestAuthnContext
Az `RequestedAuthnContext` elem megadja a kívánt hitelesítési módszereket. Az Azure `AuthnRequest` AD-nek küldött elemek nem kötelező. Az Azure AD támogatja az `AuthnContextClassRef` értékeket, például `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`a.

### <a name="scoping"></a>Hatókör
Az `Scoping` elem, amely tartalmazza az identitásszolgáltatók listáját, nem kötelező az Azure AD-nek küldött elemekben. `AuthnRequest`

Ha meg van adva, `ProxyCount` ne `IDPListOption` adja `RequesterID` meg az attribútumot vagy elemet, mivel azok nem támogatottak.

### <a name="signature"></a>Aláírás
Ne vegyen fel `Signature` egy `AuthnRequest` elemet az elemekbe, mivel az Azure AD nem támogatja az aláírt hitelesítési kérelmeket.

### <a name="subject"></a>Tárgy
Az Azure AD `Subject` figyelmen kívül hagyja az elemek elemét. `AuthnRequest`

## <a name="response"></a>Válasz
Ha egy kért bejelentkezés sikeresen befejeződik, az Azure AD válaszol a felhőszolgáltatásra. A sikeres bejelentkezési kísérletre adott válasz a következő példához hasonlóan néz ki:

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

Az `Response` elem tartalmazza az engedélyezési kérelem eredményét. Az Azure AD `Version` `IssueInstant` beállítja `Response` a `ID`és az értékeket az elemben. A következő attribútumokat is beállítja:

* `Destination`: Ha a bejelentkezés sikeresen befejeződik, `RedirectUri` akkor ez a szolgáltató (felhőszolgáltatás) beállítása.
* `InResponseTo`: Ez a `ID` választ kezdeményező `AuthnRequest` elem attribútumára van beállítva.

### <a name="issuer"></a>Kiállító

Az Azure AD beállítja az `Issuer` elemet, `https://login.microsoftonline.com/<TenantIDGUID>/` ahol \<TenantIDGUID> az Azure AD-bérlő bérlőazonosítója.

A Kiállító elemmel rendelkező válasz például a következő példához hasonló lehet:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>status

Az `Status` elem a bejelentkezés sikerét vagy sikertelenségét közvetíti. Ez magában `StatusCode` foglalja az elemet, amely egy kódot vagy beágyazott kódokat tartalmaz, amelyek a kérelem állapotát jelölik. Tartalmazza azt `StatusMessage` az elemet is, amely a bejelentkezési folyamat során létrehozott egyéni hibaüzeneteket tartalmaz.

<!-- TODO: Add an authentication protocol error reference -->

A következő minta egy SAML válasz sikertelen bejelentkezési kísérletre.

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

A mellett `ID`a `IssueInstant` `Version`, és az Azure AD `Assertion` a válasz elemében a következő elemeket állítja be.

#### <a name="issuer"></a>Kiállító

Ez van `https://sts.windows.net/<TenantIDGUID>/`beállítva, ahol \<TenantIDGUID> az Azure AD-bérlő bérlőazonosítója.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Aláírás

Az Azure AD aláírja a feltételt a sikeres bejelentkezésre válaszul. Az `Signature` elem olyan digitális aláírást tartalmaz, amelyet a felhőszolgáltatás a forrás hitelesítésére használhat a helyességi feltétel integritásának ellenőrzéséhez.

A digitális aláírás létrehozásához az Azure AD `IDPSSODescriptor` az aláíró kulcsot használja a metaadat-dokumentum elemében.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Tárgy

Ez határozza meg azt a főelemet, amely az állításban szereplő állítások tárgyát képezi. Tartalmaz egy `NameID` elemet, amely a hitelesített felhasználót jelöli. Az `NameID` érték egy célzott azonosító, amely csak a jogkivonat célközönségét tartalmazó szolgáltatóhoz irányul. Ez állandó - vissza lehet vonni, de soha nem rendelt. Az is átlátszatlan, mivel nem fed fel semmit a felhasználóról, és nem használható attribútumlekérdezések azonosítójaként.

Az `Method` `SubjectConfirmation` elem attribútuma mindig `urn:oasis:names:tc:SAML:2.0:cm:bearer`a .

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Feltételek

Ez az elem azokat a feltételeket határozza meg, amelyek meghatározzák az SAML-állítások elfogadható használatát.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

A `NotBefore` `NotOnOrAfter` és az attribútumok határozzák meg azt az időtartamot, amely alatt az állítás érvényes.

* Az `NotBefore` attribútum értéke egyenlő vagy kis mértékben (kisebb, mint egy `IssueInstant` másodperc) későbbi, mint az `Assertion` elem attribútumértéke. Az Azure AD nem veszi figyelembe a maga és a felhőszolgáltatás (szolgáltató) közötti időbeli különbséget, és ehhez az időhöz nem ad hozzá puffert.
* Az `NotOnOrAfter` attribútum értéke 70 perccel később van, `NotBefore` mint az attribútum értéke.

#### <a name="audience"></a>Célközönség

Ez egy URI-t tartalmaz, amely azonosítja a célközönséget. Az Azure AD beállítja ennek `Issuer` az elemnek az értékét, hogy az elem, `AuthnRequest` amely kezdeményezte a bejelentkezést. Az `Audience` érték kiértékeléséhez használja `App ID URI` az alkalmazás regisztrációja során megadott értéket.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Az `Issuer` értékhez `Audience` hasonlóan az értéknek is pontosan meg kell egyeznie az Azure AD-ben a felhőszolgáltatást képviselő egyszerű szolgáltatásnevek egyikével. Ha azonban `Issuer` az elem értéke nem URI-érték, a `Audience` válaszban `Issuer` lévő érték `spn:`a.

#### <a name="attributestatement"></a>Attribútumjelentés

Ez a tulajdonosra vagy a felhasználóra vonatkozó jogcímeket tartalmaz. A következő részlet `AttributeStatement` egy mintaelemet tartalmaz. A három pont azt jelzi, hogy az elem több attribútumot és attribútumértéket is tartalmazhat.

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

* **Névjogcím** – Az `Name` attribútum`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`( ) értéke a hitelesített felhasználó egyszerű `testuser@managedtenant.com`felhasználóneve, például .
* **ObjectIdentifier jogcím** - `ObjectIdentifier` Az attribútum`http://schemas.microsoft.com/identity/claims/objectidentifier`( `ObjectId` ) értéke a címtárobjektum értéke, amely a hitelesített felhasználót képviseli az Azure AD-ben. `ObjectId`a hitelesített felhasználó nem módosítható, globálisan egyedi és újrafelhasználhatja a biztonságos azonosítót.

#### <a name="authnstatement"></a>AuthnStatement kifejezés

Ez az elem azt állítja, hogy az állítás tárgya egy adott módon hitelesített egy adott időpontban.

* Az `AuthnInstant` attribútum azt az időpontot adja meg, amikor a felhasználó hitelesítette az Azure AD-vel.
* Az `AuthnContext` elem a felhasználó hitelesítéséhez használt hitelesítési környezetet határozza meg.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
