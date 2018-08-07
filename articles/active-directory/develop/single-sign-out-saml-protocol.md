---
title: Az Azure egyszeri bejelentkezési SAML-protokoll |} A Microsoft Docs
description: Ez a cikk ismerteti az egyszeri kijelentkezéses SAML-protokoll az Azure Active Directoryban
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: b1c0dddec93e913e543558893979b95c8f53ad47
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581594"
---
# <a name="single-sign-out-saml-protocol"></a>Egyszeri kijelentkezés SAML-protokoll

Az Azure Active Directory (Azure AD) támogatja az SAML 2.0-s webes egyszeri kijelentkezési böngészőprofil. Az egyszeri kijelentkezéshez működéséhez az **LogoutURL** esetében az alkalmazás explicit módon regisztrálni kell az Azure AD-alkalmazás regisztrációja során. Az Azure AD felhasználók átirányítása után azok van jelentkezve a LogoutURL használ.

Az alábbi ábrán látható, a munkafolyamat az Azure AD egyszeri kijelentkezési folyamat.

![A munkafolyamat az Azure AD egyszeri kijelentkezés](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
A cloud service küld egy `LogoutRequest` az Azure AD-üzenet jelzi, hogy a munkamenet meg lett szakítva. A következő cikkből szerint jeleníti meg egy minta `LogoutRequest` elemet.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
A `LogoutRequest` elem küldeni az Azure AD szükséges a következő attribútumokat:

* `ID` – Ez azonosítja a kijelentkezési kérés. Az érték `ID` kell nem kezdődhet számmal. A szokásos gyakorlat az, ha hozzáfűzése **azonosító** a karakterlánc-ábrázolásra egy GUID azonosító.
* `Version` – Ez az elem értékének beállítása **2.0**. Kötelezően megadandó érték.
* `IssueInstant` – Ez egy `DateTime` koordinálása világidő (UTC) érték karakterlánc és [oda-vissza formátumot ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure ad-ben az ilyen típusú értéket vár, de nem kényszerítése.

### <a name="issuer"></a>Kiállító
A `Issuer` eleme egy `LogoutRequest` pontosan egyeznie kell a **ServicePrincipalNames** a felhőszolgáltatás az Azure ad-ben. Általában ez értékre van állítva a **Alkalmazásazonosító URI-t** alkalmazás regisztrációja során megadott.

### <a name="nameid"></a>NameID
Értékét a `NameID` elemben pontosan egyeznie kell a `NameID` ki regisztrált felhasználó.

## <a name="logoutresponse"></a>LogoutResponse
Az Azure AD elküldi egy `LogoutResponse` válaszul egy `LogoutRequest` elemet. A következő cikkből szerint jeleníti meg egy minta `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Az Azure AD-csoportok a `ID`, `Version` és `IssueInstant` lévő értékeknek a `LogoutResponse` elemet. Azt állítja be a `InResponseTo` elem értékét a `ID` attribútuma a `LogoutRequest` , amely a válasz megállapított.

### <a name="issuer"></a>Kiállító
Az Azure AD állítja ezt az értéket `https://login.microsoftonline.com/<TenantIdGUID>/` ahol <TenantIdGUID> a bérlő az Azure AD-bérlő azonosítója.

Értékét kiértékelheti, hogy a `Issuer` elem, az értéket a **Alkalmazásazonosító URI-t** alkalmazás regisztrációja során megadott.

### <a name="status"></a>status
Azure AD az a `StatusCode` eleme a `Status` elem jelzi a sikeres vagy sikertelen kijelentkezés. A kijelentkezési kísérlet sikertelen, amikor a `StatusCode` elem is tartalmazhat egyéni hibaüzenetek.
