---
title: Az Azure AD összevonási metaadatok |} Microsoft Docs
description: Ez a cikk ismerteti az Azure Active Directory-tokeneket támogató szolgáltatások közzéteszi Azure Active Directory összevonási metaadat-dokumentum.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: cfc79b451eafe7dcdd0b8f4285f92714138260bb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="federation-metadata"></a>Összevonási metaadatok
Azure Active Directory (Azure AD) tesz közzé egy összevonási metaadat-dokumentum számára, amely konfigurálva van a biztonsági jogkivonatokat, amelyek az Azure AD kibocsát fogadására. Az összevonási metaadatok dokumentum formátuma leírtak a [Web Services összevonási Language (WS-Federation) 1.2-es verziója](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), amely kiterjeszti [a OASIS Security Assertion Markup Language (SAML) 2.0 metaadatok](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Bérlői-specifikus és bérlői független metaadat-végpontok
Az Azure AD bérlő-specifikus és bérlői független végpontok közzéteszi.

Egy adott bérlő bérlői-specifikus végpontok készültek. A bérlő-specifikus az összevonási metaadatok tartalmazzák a bérlő bérlői-specifikus kibocsátó és a végpont párbeszédeket kapcsolatos információkat. Alkalmazások, amely korlátozza a hozzáférést egy egyetlen bérlő bérlői-specifikus végpontok használata.

Bérlői független végpontok minden Azure AD-bérlő közös információkat tartalmaznak. Ezek az információk vonatkoznak a bérlők számára a(z) *login.microsoftonline.com* és a bérlők által megosztott. Bérlői független végpontok javasolt a több-bérlős alkalmazásokhoz, mivel azok nincsenek társítva, semmilyen különleges bérlővel.

## <a name="federation-metadata-endpoints"></a>Összevonási metaadatok végpontok
Az Azure AD közzéteszi az összevonási metaadatokban: `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

A **bérlői-specifikus végpontok**, a `TenantDomainName` a következő típusok egyike lehet:

* A regisztrált tartománynév, az Azure ad bérlői, például: `contoso.onmicrosoft.com`.
* A nem módosítható a tartomány azonosítója, mint bérlői `72f988bf-86f1-41af-91ab-2d7cd011db45`.

A **bérlői független végpontok**, a `TenantDomainName` van `common`. Ez a dokumentum csak login.microsoftonline.com által futtatott összes Azure AD-bérlőre vonatkozó összevonási metaadatok elemeket sorolja fel.

Például lehet, hogy a bérlő-specifikus végpont `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. A bérlői független végpont [ https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml ](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Írja be az URL-címet egy böngészőben megtekintheti a összevonási metaadat-dokumentum.

## <a name="contents-of-federation-metadata"></a>Összevonási metaadatok tartalmát
A következő témakör szolgáltatásokról, amelyek az Azure AD által kiállított jogkivonatokat használják számára szükséges adatokat.

### <a name="entity-id"></a>Entitásazonosító
A `EntityDescriptor` elem tartalmazza-e egy `EntityID` attribútum. Értékét a `EntityID` attribútum a kibocsátó, ez azt jelenti, hogy a biztonsági jogkivonat-szolgáltatás (STS) a jogkivonatot kibocsátó jelöli. Fontos, amikor fogadhatnak jogkivonatot a kibocsátó érvényesítéséhez.

A következő metaadatokat megmutatja, bérlő-specifikus `EntityDescriptor` elem egy `EntityID` elemet.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
A bérlői független végpont Bérlőazonosító lecserélheti egy bérlő-specifikus létrehozása a bérlő azonosítója `EntityID` érték. Az eredményül kapott érték a jogkivonat-kiállítóként ugyanaz lesz. A stratégia lehetővé teszi, hogy egy több-bérlős alkalmazást egy adott bérlő a kibocsátó érvényesítése.

A következő metaadatokat megmutatja, bérlő független `EntityID` elemet. Vegye figyelembe, hogy a `{tenant}` szövegkonstans, nem egy helyőrző.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Jogkivonat-aláíró tanúsítványok
Ha a szolgáltatás egy jogkivonatot, amely egy Azure AD-bérlő által kiadott kap, az aláírás a jogkivonat az összevonási metaadatok dokumentum közzétett aláírókulccsal ellenőrizni kell. Az összevonási metaadatok a bérlők számára használja a jogkivonat-aláíró tanúsítványokat nyilvános részét tartalmazza. A tanúsítvány nyers bájt jelenik meg a `KeyDescriptor` elemet. A jogkivonat-aláíró tanúsítvány érvénytelen, csak ha aláírásra értékének a `use` attribútum `signing`.

Az Azure AD által közzétett összevonási metaadatok dokumentum rendelkezhet több aláírási kulcs, például ha az Azure AD arra készül, hogy frissítse az aláíró tanúsítvány. Ha egy összevonási metaadat-dokumentum tartalmazza az egynél több tanúsítvány, egy szolgáltatás, amely a jogkivonatok érvényesítése támogatnia kell a minden tanúsítvány a dokumentumban.

A következő metaadatokat megmutatja, `KeyDescriptor` elem az aláírási kulcs.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

A `KeyDescriptor` elem jelenik meg, két helyen az összevonási metaadatok dokumentum; a WS-összevonás-specifikus és SAML-specifikus szakaszát. Mindkét szakasz közzétett tanúsítványok ugyanaz lesz.

A WS-összevonás-specifikus a szakaszban egy WS-összevonás metaadat-olvasó olvashatja, a tanúsítványokat egy `RoleDescriptor` rendelkező elemet a `SecurityTokenServiceType` típusa.

A következő metaadatokat megmutatja, `RoleDescriptor` elemet.

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

A SAML-specifikus a szakaszban egy WS-összevonás metaadat-olvasó olvashatja, a tanúsítványokat egy `IDPSSODescriptor` elemet.

A következő metaadatokat megmutatja, `IDPSSODescriptor` elemet.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Nincsenek különbségek vonatkozó bérlői és bérlői független tanúsítványok formátumban.

### <a name="ws-federation-endpoint-url"></a>WS-Federation végponti URL-cím
Az összevonási metaadatok egyszeri bejelentkezés és kijelentkezés a WS-Federation protokoll egyetlen használja az Azure AD az URL-Címeket tartalmaz. Ehhez a végponthoz megjelenik a `PassiveRequestorEndpoint` elemet.

A következő metaadatokat megmutatja, `PassiveRequestorEndpoint` elem egy bérlő-specifikus végpont.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
A bérlői független végpont a WS-összevonás URL-cím jelenik meg a WS-Federation végpont a következő mintában látható módon.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>SAML protokoll végponti URL-cím
Az összevonási metaadatok tartalmazzák az URL-címet, amely az egyszeri bejelentkezés és kijelentkezés a SAML 2.0 protokoll egyetlen használja az Azure AD. Ezeket a végpontokat megjelennek a `IDPSSODescriptor` elemet.

A be- és kijelentkezési URL-cím látható a `SingleSignOnService` és `SingleLogoutService` elemeket.

A következő metaadatokat megmutatja, `PassiveResistorEndpoint` egy bérlő-specifikus végpont.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Hasonlóképpen a végpontokat a közös SAML 2.0 protokoll végpontok a közzétett a bérlő független összevonási metaadatok a következő mintában látható módon.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
