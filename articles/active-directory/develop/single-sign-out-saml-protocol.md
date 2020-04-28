---
title: Azure egyszeri kijelentkezési SAML protokoll
description: Ez a cikk az egyszeri kijelentkezési SAML protokollt ismerteti Azure Active Directory
services: active-directory
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
ms.openlocfilehash: dbe21d020d5d01f24913b95587721403fa218cc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80881265"
---
# <a name="single-sign-out-saml-protocol"></a>Egyszeri kijelentkezési SAML protokoll

Azure Active Directory (Azure AD) támogatja az SAML 2,0 webböngésző egyszeri kijelentkezési profilját. Az egyszeri bejelentkezés megfelelő működéséhez az alkalmazás **LogoutURL** explicit módon regisztrálni kell az Azure ad-ben az alkalmazás regisztrálása során. Az Azure AD a LogoutURL használatával irányítja át a felhasználókat, miután kijelentkezett.

Az alábbi ábra az Azure AD egyszeri kijelentkezési folyamatának munkafolyamatát mutatja be.

![Azure AD egyszeri kijelentkezési munkafolyamat](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
A Cloud Service egy `LogoutRequest` üzenetet küld az Azure ad-nek, amely jelzi, hogy a munkamenet le lett szakítva. Az alábbi részlet egy minta `LogoutRequest` elemet mutat be.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Az `LogoutRequest` Azure ad-nak eljuttatott elem a következő attribútumokat igényli:

* `ID`-Ez azonosítja a kijelentkezési kérést. A értéke `ID` nem kezdődhet számmal. A tipikus eljárás egy GUID **azonosítójának** hozzáfűzése a GUID karakterlánc-ábrázolásához.
* `Version`– Az elem értékét állítsa **2,0**-ra. Kötelezően megadandó érték.
* `IssueInstant`– Ez egy `DateTime` , az egyezményes világidő (UTC) és az [oda-és visszaút formátuma ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx)karakterlánc. Az Azure AD egy ilyen típusú értéket vár, de nem kényszeríti ki.

### <a name="issuer"></a>Kiállító
Az `Issuer` a `LogoutRequest` elemnek pontosan egyeznie kell az Azure ad felhőalapú szolgáltatásának egyik **ServicePrincipalNames** . Ez általában az alkalmazás regisztrációja során megadott **alkalmazás-azonosító URI-ra** van beállítva.

### <a name="nameid"></a>NameID
Az `NameID` elem értékének pontosan egyeznie `NameID` kell a kijelentkezett felhasználóval.

## <a name="logoutresponse"></a>LogoutResponse
Az Azure AD egy `LogoutResponse` adott `LogoutRequest` elemre adott választ küld. Az alábbi részlet egy mintát `LogoutResponse`mutat be.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Az Azure ad beállítja `ID`az `Version` elemet `IssueInstant` és az értékeket `LogoutResponse` a elemben. Azt is megadja, `InResponseTo` hogy az elem a válasz kiváltotta `ID` attribútumának `LogoutRequest` értéke.

### <a name="issuer"></a>Kiállító
Az Azure AD ezt az értéket `https://login.microsoftonline.com/<TenantIdGUID>/` állítja \<be, hogy a TENANTIDGUID> az Azure ad-bérlő bérlői azonosítóját adja meg.

Az `Issuer` elem értékének kiértékeléséhez használja az alkalmazás regisztrációja során megadott **alkalmazás-azonosító URI** azonosító értékét.

### <a name="status"></a>status
Az Azure AD az `StatusCode` `Status` elem eleme alapján jelzi a kijelentkezés sikerességét vagy sikertelenségét. Ha a kijelentkezési kísérlet sikertelen, akkor `StatusCode` az elem egyéni hibaüzeneteket is tartalmazhat.
