---
title: Ismerje meg a másik tokent, és az Azure AD által támogatott jogcímtípusok |} A Microsoft Docs
description: Ismertetése és értékelése a szerint az Azure Active Directory (AAD) kiállított SAML 2.0 és a JSON Web tokenek (JWT) tokenek jogcímeit készült útmutató
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 2c67e40cf540062aeadd533962c0fb296648fb86
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100002"
---
# <a name="azure-ad-saml-token-reference"></a>Az Azure AD SAML-jogkivonatok referenciájából

Az Azure Active Directory (Azure AD) számos különböző típusú feldolgozása minden hitelesítési folyamat során a biztonsági jogkivonatokat bocsát ki. Ez a dokumentum ismerteti a formátumát, a biztonsági jellemzőkkel és a különböző típusú jogkivonat tartalma.

## <a name="claims-in-saml-tokens"></a>SAML-jogkivonatokat a jogcím

> [!div class="mx-codeBreakAll"]
| Name (Név) | Egyenértékű JWT-jogcím | Leírás | Példa |
| --- | --- | --- | ------------|
|Célközönség | `aud` |A jogkivonat az illetékes címzett. Az alkalmazás, amely megkapja a token ellenőriznie kell, hogy a célközönség érték helyességéről, valamint egy másik közönség számára készült jogkivonatokat elutasítása. | `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>`  |
| Hitelesítési időpont | |A dátum és idő, mikor történt a hitelesítés rögzíti. | `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | 
|Hitelesítési módszer | `amr` |Azonosítja a jogkivonat tárgyában hitelesítésének módját. | `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` |
|Utónév | `given_name` |Itt az első vagy az "adott" a felhasználó neve az Azure AD-felhasználói objektum vannak megadva. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">`<br>`<AttributeValue>Frank<AttributeValue>`  |
|Csoportok | `groups` |A tulajdonos csoporttagságok képviselő objektumazonosítók biztosít. Ezek az értékek egyedi (lásd az Objektumazonosító) és biztonságosan használható például az erőforrás hozzáférési kényszerítése a hozzáférés kezelésében. A csoportok jogcím szerepel a csoportok alkalmazásonkénti alapon, az alkalmazásjegyzékben "groupMembershipClaims" tulajdonságát keresztül vannak konfigurálva. NULL értékű kizárja az összes csoport, csak az Active Directory biztonsági csoport tagságát, és a egy értéket "All" lesz tartalmazzák a biztonsági csoportok és az Office 365 terjesztési listák "SecurityGroup" értéket tartalmazza. <br><br> **Megjegyzések**: <br> Ha a felhasználó szerepel csoportok számának megfelelően egy határértéket (150 a SAML, 200 a JWT) és a egy kerettúllépési jogcímet a jogcím-adatforrások mutat, amely tartalmazza azokat a csoportokat a felhasználó Graph-végpont fog bővülni. (a. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` |
| Kereten túli kijelző csoportok | `groups:src1` | A jogkivonat-kérelmeket, amelyek nem korlátozott hossza (lásd: `hasgroups` fent), de továbbra is túl nagy a jogkivonatot, a felhasználó a teljes csoportok listáját mutató hivatkozást tartalmaz. Az SAML ez kerülnek be a helyére egy új jogcímet a `groups` jogcím. | `<Attribute Name=" http://schemas.microsoft.com/claims/groups.link">`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` |
|Identitásszolgáltató | `idp` |A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. Ez az érték megegyezik a kiállító jogcím értékét, kivéve, ha a felhasználói fiók, mint a kibocsátó egy másik bérlőben található. | `<Attribute Name=" http://schemas.microsoft.com/identity/claims/identityprovider">`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` |
|IssuedAt | `iat` |Az idő, amikor a jogkivonat kiállított tárolja. Gyakran token frissessége mérésére szolgál. | `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` |
|Kiállító | `iss` |Azonosítja a biztonsági jogkivonat-szolgáltatás (STS) hoz létre, és a jogkivonatát adja vissza. A jogkivonatokat, amely az Azure AD ad vissza a kibocsátó sts.windows.net. A globálisan egyedi Azonosítót a kibocsátó jogcím értékét a rendszer a bérlő Azonosítóját az Azure AD-címtár. A bérlő azonosítója nem módosítható és megbízható azonosítója, amelyet a könyvtárban. | `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` |
|Vezetéknév | `family_name` |Az utolsó neve, Vezetéknév vagy felhasználó család neve biztosít az Azure AD-felhasználói objektum. | `<Attribute Name=" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">`<br>`<AttributeValue>Miller<AttributeValue>` |
|Name (Név) | `unique_name` |A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. Ez az érték nem garantált egy bérlőn belül egyedinek kell lennie, és célja, hogy csak megjelenítési célokra használhatók. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>`|
|Objektumazonosító | `oid` |Tartalmazza az objektum egyedi azonosítója az Azure ad-ben. Ez az érték nem módosítható és nem hozzárendelni és újra felhasználható. Az Objektumazonosító segítségével azonosíthatja a lekérdezésekben az Azure AD-objektum. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` |
|Szerepkörök | `roles` |Az összes alkalmazás-szerepkörök, amelyek a tárgy megkapta-e közvetlen és közvetett csoporttagság, és a szerepköralapú hozzáférés-vezérlés kikényszerítésére használható jelöli. Alkalmazás-szerepkörök keresztül definiált alkalmazás alapon, az `appRoles` az alkalmazásjegyzékben tulajdonságát. A `value` egyes alkalmazás-szerepkörökhöz tulajdonság értékét, amely a szerepkör jogcím jelenik meg. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`|
|Tárgy | `sub` |Az egyszerű arról, hogy mely a token használjon esetleg imperatív állításokat információkat, például a felhasználó az alkalmazás azonosítja. Ez az érték nem módosítható és nem lehet hozzárendelni, vagy újra felhasználhatók, tehát használat biztonságosan hitelesítési ellenőrzések elvégzéséhez. A tulajdonos mindig szerepel a jogkivonatokat az Azure AD-problémák, mert javasoljuk ennek az értéknek egy általános célú engedélyezési rendszerben. <br> `SubjectConfirmation` jogcím nem áll. Leírja, hogyan ellenőrzése a jogkivonat tárgyában. `Bearer` azt jelzi, hogy a jogkivonat birtokában megerősíti a tulajdonos. | `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>`|
|Bérlőazonosító | `tid` |Nem módosítható, egyszer használatos azonosító, amely azonosítja a directory-bérlővel, amely kiállította a jogkivonatot. Ez az érték használatával egy több-bérlős alkalmazásban a bérlő-specifikus directory erőforrásainak eléréséhez. Ez az érték segítségével például a bérlő a Graph API-hívással azonosíthatja. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>`|
|Jogkivonat élettartama | `nbf`, `exp` |A jogkivonat érvényességi idejét határozza meg. A szolgáltatás, amely érvényesíti a jogkivonatot kell győződjön meg arról, hogy az aktuális dátumot a jogkivonat élettartamát, más azt kell utasítania a token belül van-e. A szolgáltatás lehetővé teheti a akár öt percet a jogkivonat élettartama tartományon kívül áthidalhatók az idő ("idő döntés") a különbségeket az Azure AD között és a szolgáltatás. | `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br>|

## <a name="sample-saml-token"></a>Példa SAML-jogkivonat

Ez a minta egy tipikus SAML-jogkivonat.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="https://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="https://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

## <a name="related-content"></a>Kapcsolódó tartalom
* Tekintse meg az Azure AD Graph [házirend műveletek](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) és a [házirend entitás](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), tudhat meg többet az Azure AD Graph API-n keresztül jogkivonat élettartama házirend kezelése.
* További információ és példák többek között a mintákat, PowerShell-parancsmagok használatával házirendek kezelése a [konfigurálható jogkivonatok élettartamának, az Azure ad-ben](active-directory-configurable-token-lifetimes.md). 
* Adjon hozzá [egyéni és az opcionális jogcímek](active-directory-optional-claims.md) való az alkalmazás a jogkivonatokat.
* Használat [egyszeri bejelentkezés (SSO) az SAML](single-sign-on-saml-protocol.md).
* Használja a [Azure egyszeri bejelentkezési SAML-protokoll](single-sign-out-saml-protocol.md)
