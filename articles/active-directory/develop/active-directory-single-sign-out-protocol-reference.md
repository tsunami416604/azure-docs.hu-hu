---
title: "SAML protokoll Azure egyetlen kijelentkezés |} Microsoft Docs"
description: Ez a cikk ismerteti az Azure Active Directoryban egyetlen Sign-Out SAML protokoll
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: 45e4705f53d80b5fe852c484b5e64d18a8e24f09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="single-sign-out-saml-protocol"></a>Egyetlen kijelentkezési SAML protokoll
Az Azure Active Directory (Azure AD) támogatja a SAML 2.0-s webes böngésző egyetlen kijelentkezési profil. Egyetlen kijelentkezés, megfelelő működéséhez a **LogoutURL** a az alkalmazás explicit módon regisztrálni kell az Azure AD-alkalmazás regisztrációja során. Az Azure AD felhasználók átirányítása után a rendszer kijelentkezteti a LogoutURL használja.

A munkafolyamat az Azure AD egyetlen kijelentkezési folyamat látható.

![Munkafolyamat egyetlen kijelentkezés](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
A felhőalapú szolgáltatás küld egy `LogoutRequest` az Azure AD-üzenet jelzi, hogy a munkamenet meg lett szakítva. A következő cikkből megmutatja, `LogoutRequest` elemet.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
A `LogoutRequest` az Azure AD küldött elem szükséges a következő attribútumokat:

* `ID`: Ez azonosítja a kijelentkezési kérelmet. Értékének `ID` kell nem kezdődhet számmal. Az általános gyakorlat az, hogy hozzáfűzése **azonosító** a GUID karakterláncos ábrázolása.
* `Version`: Ez az elem értékének beállítása **2.0**. Kötelezően megadandó érték.
* `IssueInstant`: Ez egy `DateTime` koordinálják világidő (UTC) értékű karakterlánc és [körbejárási formátumban ("no")](https://msdn.microsoft.com/library/az4se3k1.aspx). Az Azure AD egy ilyen típusú értéket vár, de nem érvényesíti.

### <a name="issuer"></a>Kiállító
A `Issuer` eleme egy `LogoutRequest` pontosan egyeznie kell a **ServicePrincipalNames** az Azure AD-ben a felhőalapú szolgáltatáshoz. Általában értékre van állítva a **App ID URI** regisztrációja során meghatározott.

### <a name="nameid"></a>NameID
Értékét a `NameID` elem pontosan meg kell egyeznie a `NameID` a felhasználó ki aláírással.

## <a name="logoutresponse"></a>LogoutResponse
Az Azure AD küld egy `LogoutResponse` válaszul egy `LogoutRequest` elemet. A következő cikkből megmutatja, `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Az Azure AD-készletek a `ID`, `Version` és `IssueInstant` értékei a `LogoutResponse` elemet. Azt állítja be a `InResponseTo` elem értékének a `ID` attribútuma a `LogoutRequest` , amely a válasz megállapított.

### <a name="issuer"></a>Kiállító
Az Azure AD állítja ezt az értéket `https://login.microsoftonline.com/<TenantIdGUID>/` ahol <TenantIdGUID> a bérlő azonosítója az Azure AD-bérlő.

Értékének kiértékelése a `Issuer` elem, az értéket a **App ID URI** regisztrációja során.

### <a name="status"></a>status
Az Azure AD által használt a `StatusCode` eleme a `Status` elem a sikerességét vagy sikertelenségét kijelentkezési jelzi. A kijelentkezési kísérlet meghiúsul, ha a `StatusCode` elem is tartalmazhat egyéni hibaüzenetek.