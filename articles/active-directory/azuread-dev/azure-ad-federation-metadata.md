---
title: Azure AD összevonási metaadatok | Microsoft dokumentumok
description: Ez a cikk ismerteti az összevonási metaadat-dokumentumot, amelyet az Azure Active Directory közzétesz az Azure Active Directory-jogkivonatokat fogadó szolgáltatásokhoz.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: bcc44f61ccb7b4a19e7df39ab979669c5aa37da1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154899"
---
# <a name="federation-metadata"></a>Összevonási metaadatok

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Az Azure Active Directory (Azure AD) egy összevonási metaadat-dokumentumot tesz közzé az Azure AD által kiadott biztonsági jogkivonatok fogadására konfigurált szolgáltatásokhoz. Az összevonási metaadat-dokumentum formátumát a [Web Services Federation Language (WS-Federation) 1.2-es verziója](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)ismerteti, amely kiterjeszti az OASIS security assertion [markup language (SAML) 2.0 (SAML) verziójának metaadatait.](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf)

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Bérlő-specifikus és bérlő-független metaadat-végpontok
Az Azure AD bérlő-specifikus és bérlőfüggetlen végpontokat tesz közzé.

Bérlő-specifikus végpontok egy adott bérlőhöz tervezték. A bérlő-specifikus összevonási metaadatok információkat tartalmaz a bérlő, beleértve a bérlő-specifikus kibocsátó és a végpont adatait. Az egyetlen bérlőhöz való hozzáférést korlátozó alkalmazások bérlőspecifikus végpontokat használnak.

A bérlőfüggetlen végpontok az összes Azure AD-bérlő közös adatait biztosítják. Ez az információ a *login.microsoftonline.com* üzemeltetett bérlőkre vonatkozik, és a bérlők között van megosztva. Bérlő-független végpontok ajánlott több-bérlős alkalmazások, mivel azok nem társított egy adott bérlő.

## <a name="federation-metadata-endpoints"></a>Összevonási metaadat-végpontok
Az Azure AD közzéteszi az `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`összevonási metaadatokat a.-on.

**Bérlő-specifikus végpontok**esetén `TenantDomainName` a következő típusok egyike lehet:

* Egy Azure AD-bérlő regisztrált tartományneve, `contoso.onmicrosoft.com`például: .
* A tartomány nem módosítható bérlői azonosítója, `72f988bf-86f1-41af-91ab-2d7cd011db45`például .

**A bérlőtől független végpontok**esetén a `TenantDomainName` `common`. Ez a dokumentum csak az összevonási metaadat-elemeket sorolja fel, amelyek a login.microsoftonline.com üzemeltetett összes Azure AD-bérlőre közösek.

Például egy bérlő-specifikus végpont `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`lehet. A bérlőtől független [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml)végpont a . Az összevonási metaadat-dokumentum megtekintéséhez írja be ezt az URL-címet a böngészőbe.

## <a name="contents-of-federation-metadata"></a>Az összevonási metaadatok tartalma
A következő szakasz az Azure AD által kiadott jogkivonatokat használó szolgáltatások által szükséges információkat tartalmazza.

### <a name="entity-id"></a>Entitás azonosítója
Az `EntityDescriptor` elem `EntityID` attribútumot tartalmaz. Az attribútum `EntityID` értéke a kibocsátót, azaz a jogkivonatot kibocsátó biztonsági jogkivonat-szolgáltatást (STS) jelöli. Fontos, hogy ellenőrizze a kibocsátó, ha egy jogkivonatot kap.

A következő metaadatok egy minta bérlő-specifikus `EntityDescriptor` elemet mutatnak egy `EntityID` elemmel.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
A bérlő-független végpont bérlői azonosítóját lecserélheti a bérlői azonosítóra, hogy bérlő-specifikus `EntityID` értéket hozzon létre. Az eredményül kapott érték megegyezik a token kibocsátójával. A stratégia lehetővé teszi, hogy egy több-bérlős alkalmazás érvényesítse a kibocsátó egy adott bérlő.

A következő metaadatok egy bérlőtől független mintaelemet `EntityID` mutatnak. Kérjük, vegye `{tenant}` figyelembe, hogy a szó szerinti, nem pedig helyőrző.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Jogkivonat-aláíró tanúsítványok
Amikor egy szolgáltatás kap egy jogkivonatot, amely egy Azure AD-bérlő által kiadott, a jogkivonat aláírását kell érvényesíteni egy aláíró kulcs, amely az összevonási metaadat-dokumentumban közzétett. Az összevonási metaadatok tartalmazzák a tanúsítványok nyilvános részét, amelyet a bérlők a jogkivonat-aláíráshoz használnak. A tanúsítvány nyers bájtjai `KeyDescriptor` megjelennek az elemben. A jogkivonat-aláíró tanúsítvány csak akkor érvényes `use` az `signing`aláíráshoz, ha az attribútum értéke .

Az Azure AD által közzétett összevonási metaadat-dokumentum több aláíró kulccsal is rendelkezhet, például amikor az Azure AD az aláíró tanúsítvány frissítésére készül. Ha egy összevonási metaadat-dokumentum egynél több tanúsítványt tartalmaz, a jogkivonatokat érvényesítő szolgáltatásnak támogatnia kell a dokumentum összes tanúsítványát.

A következő metaadatok `KeyDescriptor` egy aláíró kulccsal rendelkező mintaelemet mutatnak.

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

Az `KeyDescriptor` elem két helyen jelenik meg az összevonási metaadat-dokumentumban; a WS-Federation-specifikus és az SAML-specifikus szakaszban. A két szakaszban közzétett tanúsítványok azonosak lesznek.

A WS-Federation-specifikus szakaszban a WS-Federation metaadat-olvasó `RoleDescriptor` a `SecurityTokenServiceType` tanúsítványokat egy ilyen típusú elemből olvassa be.

A következő metaadatok `RoleDescriptor` egy mintaelemet mutatnak.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

Az SAML-specifikus szakaszban a WS-Federation metaadat-olvasó `IDPSSODescriptor` egy elemből olvassa be a tanúsítványokat.

A következő metaadatok `IDPSSODescriptor` egy mintaelemet mutatnak.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
A bérlőspecifikus és a bérlőtől független tanúsítványok formátuma nem.

### <a name="ws-federation-endpoint-url"></a>WS-összevonási végpont URL-címe
Az összevonási metaadatok tartalmazzák az Azure AD által a WS-Federation protokollegyszeri bejelentkezéshez és egyszeri kijelentkezéshez használt URL-címet. Ez a végpont `PassiveRequestorEndpoint` jelenik meg az elemben.

A következő metaadatok `PassiveRequestorEndpoint` egy bérlő-specifikus végpont mintaelemét jelenítik meg.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
A bérlő-független végpont esetében a WS-összevonási URL-cím megjelenik a WS-Federation végpontban, ahogy az a következő mintában látható.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>SAML protokollvégpont URL-címe
Az összevonási metaadatok tartalmazzák az Azure AD által az egyszeri bejelentkezéshez és az egyszeri kijelentkezéshez az SAML 2.0 protokollban használt URL-címet. Ezek a végpontok `IDPSSODescriptor` jelennek meg az elemben.

A bejelentkezési és kijelentkezési URL-címek jelennek meg a `SingleSignOnService` és `SingleLogoutService` az elemek.

A következő metaadatok `PassiveResistorEndpoint` egy bérlő-specifikus végpont mintáját jelenítimeg.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Hasonlóképpen a közös SAML 2.0 protokoll végpontjainak végpontjai a bérlőfüggetlen összevonási metaadatokban kerülnek közzétételre, ahogy az a következő mintában látható.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
