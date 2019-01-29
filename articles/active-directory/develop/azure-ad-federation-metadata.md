---
title: Az Azure AD összevonási metaadatok |} A Microsoft Docs
description: Ez a cikk bemutatja az összevonási metaadatok dokumentuma az Azure Active Directory közzétevő szolgáltatásokhoz, fogadja el az Azure Active Directory-jogkivonatokat.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: cd695419cca5df6b729a2ada31743e7de9c0a177
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101608"
---
# <a name="federation-metadata"></a>Összevonási metaadatok
Az Azure Active Directory (Azure AD) tesz közzé egy összevonási metaadatok dokumentuma szolgáltatásokhoz, amelyek a biztonsági jogkivonatokat, amelyek az Azure AD kibocsát fogadására van konfigurálva. Összevonási metaadatok dokumentum formátuma leírtak a [Web Services Federation Language (WS-Federation) 1.2-es verziójában](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), amely kiterjeszti a [OASIS a Security Assertion Markup Language (SAML) 2.0-smetaadatai](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Bérlő-specifikus és bérlői független metaadatok végpontok
Az Azure AD bérlő-specifikus és bérlői független végpont teszi közzé.

Egy adott bérlő bérlőspecifikus végpontok lettek kialakítva. A bérlő-specifikus összevonási metaadatok a bérlő, beleértve a bérlő-specifikus kibocsátó és a végpont információkat kapcsolatos információkat tartalmaz. Az alkalmazásokat, amelyek egyetlen-bérlőhöz való hozzáférés korlátozása a bérlő-specifikus végpontok használata.

Bérlő független végpontok minden Azure AD-bérlőt közös információkat tartalmaznak. Ez az információ vonatkozik üzemeltetett bérlők *login.microsoftonline.com* és bérlők között megoszlik. Bérlő független végpontok használata akkor javasolt több-bérlős alkalmazások, mivel azok, amelyek nem tartoznak semmilyen adott bérlővel.

## <a name="federation-metadata-endpoints"></a>Összevonási metaadatok végpontok
Az Azure AD összevonási metaadatok, közzéteszi `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

A **bérlőspecifikus végpontok**, a `TenantDomainName` a következők egyike lehet:

* Egy regisztrált tartománynévvel, az Azure ad-bérlőben, például: `contoso.onmicrosoft.com`.
* A nem módosítható bérlőazonosító annak a tartománynak, mint például `72f988bf-86f1-41af-91ab-2d7cd011db45`.

A **bérlői független végpontok**, a `TenantDomainName` van `common`. Ez a dokumentum csak a közös login.microsoftonline.com üzemeltetett összes az Azure AD-bérlő összevonási metaadatok elemeket sorolja fel.

Például lehet, hogy egy bérlő-specifikus végpont `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. A bérlő független végpont [ https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml ](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Az összevonási metaadatok dokumentuma megtekintéséhez írja be az URL-címet egy böngészőben.

## <a name="contents-of-federation-metadata"></a>Összevonási metaadatok tartalmát
A következő szakaszt, amely az Azure AD által kiállított jogkivonatokat használják szolgáltatások számára szükséges állapotinformációk biztosít.

### <a name="entity-id"></a>Entitásazonosító
A `EntityDescriptor` elem tartalmaz egy `EntityID` attribútum. Értékét a `EntityID` attribútumot jelöli, hogy a biztonsági jogkivonat-szolgáltatás (STS), amely kiállította a jogkivonatot a kibocsátó. Fontos, ha jogkivonatot kap a kibocsátó érvényesítéséhez.

A következő metaadatokat látható egy minta bérlőspecifikus `EntityDescriptor` elemet egy `EntityID` elemet.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Bérlő független végpontját a Bérlőazonosító lecserélheti a bérlő Azonosítóját hozhat létre a bérlő-specifikus `EntityID` értéket. Az eredményül kapott érték megegyezik a jogkivonat kibocsátója lesz. A stratégia lehetővé teszi, hogy egy több-bérlős alkalmazás egy adott bérlő esetében a kibocsátó érvényesítése.

A következő metaadatokat látható egy minta bérlői független `EntityID` elemet. Vegye figyelembe, hogy a `{tenant}` szövegkonstans, nem egy helyőrző.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Jogkivonat-aláíró tanúsítványok
Amikor egy szolgáltatást kap egy jogkivonatot, amely egy Azure AD-bérlő által kiadott, a jogkivonat aláírása van közzétéve az összevonási metaadatok dokumentuma aláíró kulcs érvényesíteni kell. Az összevonási metaadatok tartalmazzák, amelyek biztosítják a bérlők a jogkivonat-aláíró tanúsítványok nyilvános részét. A tanúsítvány nyers bájt jelenik meg a `KeyDescriptor` elemet. A jogkivonat-aláíró tanúsítvány érvénytelen, csak akkor, ha az aláíráshoz értékét a `use` attribútum `signing`.

Az Azure AD által közzétett összevonási metaadatok dokumentum rendelkezhet több aláírási kulcsokat, például amikor előkészíti a Azure ad-ben az aláíró tanúsítvány frissítését. Amikor egy összevonási metaadatok dokumentuma egynél több tanúsítvány is tartalmaz, egy szolgáltatás, amely érvényesíti a jogkivonatok támogatnia kell a összes tanúsítványt a dokumentumban.

A következő metaadatokat mintáját szemlélteti `KeyDescriptor` aláírási kulccsal rendelkező elemet.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

A `KeyDescriptor` elem jelenik meg az összevonási metaadatok dokumentuma; a WS-összevonás-specifikus és az SAML-specifikus szakaszban két helyen lehet. Mindkét szakaszban közzétett tanúsítványok azonos lesz.

A WS-összevonás-specifikus a szakaszban egy WS-összevonás metaadat-olvasó olvashatja, a tanúsítványokat egy `RoleDescriptor` elemet a `SecurityTokenServiceType` típusa.

A következő metaadatokat mintáját szemlélteti `RoleDescriptor` elemet.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

Az SAML-specifikus szakaszban a WS-összevonási metaadatok olvasó olvashatja, a tanúsítványokat egy `IDPSSODescriptor` elemet.

A következő metaadatokat mintáját szemlélteti `IDPSSODescriptor` elemet.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Nem bérlőspecifikus és bérlői független tanúsítványok formátumban különbségek vannak.

### <a name="ws-federation-endpoint-url"></a>WS-Federation endpoint URL
Az összevonási metaadatok tartalmazzák, amely az Azure AD egyszeri bejelentkezési és az egyszeri kijelentkezéshez a WS-Federation protokollt használt URL-CÍMÉT. Ez a végpont megjelenik a `PassiveRequestorEndpoint` elemet.

A következő metaadatokat mintáját szemlélteti `PassiveRequestorEndpoint` elem egy bérlő-specifikus végponton.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
A bérlő független végponton a WS-összevonás URL-cím jelenik meg a WS-Federation végpont a következő mintában látható módon.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>Az SAML protokoll végpont URL-címe
Az összevonási metaadatok tartalmazzák az Azure AD egyszeri bejelentkezési és az egyszeri kijelentkezéshez a SAML 2.0 protokollt használó URL-cím. Ezeket a végpontokat megjelennek a `IDPSSODescriptor` elemet.

A bejelentkezési és kijelentkezési URL-címek jelennek meg a `SingleSignOnService` és `SingleLogoutService` elemeket.

A következő metaadatokat mintáját szemlélteti `PassiveResistorEndpoint` egy bérlő-specifikus végponton.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Hasonlóképpen a végpontok a közös SAML 2.0 protokoll végpontok közzétett a bérlő független összevonási metaadatok között az alábbi mintában látható módon.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
