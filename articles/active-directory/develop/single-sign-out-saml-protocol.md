---
title: Az Azure egyszeri kijelentkezési SAML protokoll
description: Ez a cikk az Azure Active Directory egyszeri kijelentkezési SAML protokollját ismerteti
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881265"
---
# <a name="single-sign-out-saml-protocol"></a>Egyszeri kijelentkezési SAML protokoll

Az Azure Active Directory (Azure AD) támogatja az SAML 2.0 böngésző egyetlen kijelentkezési profilját. Egyszeri kijelentkezés megfelelő működéséhez az alkalmazás **LogoutURL-t** explicit módon regisztrálni kell az Azure AD-vel az alkalmazás regisztrációja során. Az Azure AD a LogoutURL használatával átirányítja a felhasználókat a kijelentkezésük után.

Az alábbi ábrán az Azure AD egyszeri kijelentkezési folyamat munkafolyamata látható.

![Az Azure AD egyszeri kijelentkezési munkafolyamata](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>Kijelentkezési kérelem
A felhőszolgáltatás `LogoutRequest` üzenetet küld az Azure AD-nek, jelezve, hogy egy munkamenet megszakadt. A következő részlet `LogoutRequest` egy mintaelemet mutat be.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>Kijelentkezési kérelem
Az `LogoutRequest` Azure AD-nek küldött elem hez a következő attribútumok szükségesek:

* `ID`- Ez azonosítja a kijelentkezési kérelmet. Az érték `ID` nem kezdődhet számmal. A tipikus gyakorlat az, hogy hozzáfűzi **id** a karakterlánc ábrázolása a GUID.
* `Version`- Állítsa az elem értékét **2.0-ra**. Kötelezően megadandó érték.
* `IssueInstant`- Ez `DateTime` egy string egy coordinate universal time (UTC) értékkel és [oda-vissza formátummal ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Az Azure AD ilyen típusú értéket vár, de nem kényszeríti ki.

### <a name="issuer"></a>Kiállító
Az `Issuer` elem `LogoutRequest` egy pontosan meg kell egyeznie az egyik **ServicePrincipalNames** a felhőszolgáltatás az Azure AD-ben. Ez általában az alkalmazásregisztráció során megadott **alkalmazásazonosító URI-ra** van beállítva.

### <a name="nameid"></a>Névazonosító
Az `NameID` elem értékének pontosan `NameID` meg kell egyeznie a kijelentkezett felhasználó értékével.

## <a name="logoutresponse"></a>Kijelentkezésválasz
Az Azure AD küld `LogoutResponse` `LogoutRequest` egy válaszként egy elemet. A következő részlet `LogoutResponse`egy mintát mutat be.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>Kijelentkezésválasz
Az Azure AD `Version` `IssueInstant` beállítja `LogoutResponse` a `ID`és az értékeket az elemben. Azt is `InResponseTo` beállítja az `ID` elemet, hogy `LogoutRequest` az érték az attribútum, hogy kiváltotta a választ.

### <a name="issuer"></a>Kiállító
Az Azure AD `https://login.microsoftonline.com/<TenantIdGUID>/` beállítja ezt az értéket, ahol \<TenantIdGUID> az Azure AD-bérlő bérlőazonosítója.

Az `Issuer` elem értékének kiértékeléséhez használja az alkalmazásregisztráció során megadott **alkalmazásazonosító-URI** értékét.

### <a name="status"></a>status
Az Azure AD `StatusCode` az `Status` elem az elem ben a kijelentkezés sikeressének vagy sikertelensének jelzésére használja. Ha a kijelentkezési `StatusCode` kísérlet sikertelen, az elem egyéni hibaüzeneteket is tartalmazhat.
