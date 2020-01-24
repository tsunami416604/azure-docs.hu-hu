---
title: Azure egyszeri kijelentkezéses SAML protokoll | Microsoft Docs
description: Ez a cikk az egyszeri kijelentkezési SAML protokollt ismerteti Azure Active Directory
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
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
ms.openlocfilehash: 95d3deff73ce357f012b15a7fc1cfa3decdb4bda
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701365"
---
# <a name="single-sign-out-saml-protocol"></a>Egyszeri kijelentkezési SAML protokoll

Azure Active Directory (Azure AD) támogatja az SAML 2,0 webböngésző egyszeri kijelentkezési profilját. Az egyszeri bejelentkezés megfelelő működéséhez az alkalmazás **LogoutURL** explicit módon regisztrálni kell az Azure ad-ben az alkalmazás regisztrálása során. Az Azure AD a LogoutURL használatával irányítja át a felhasználókat, miután kijelentkezett.

Az alábbi ábra az Azure AD egyszeri kijelentkezési folyamatának munkafolyamatát mutatja be.

![Azure AD egyszeri kijelentkezési munkafolyamat](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
A Cloud Service egy `LogoutRequest` üzenetet küld az Azure AD-nek, amely jelzi, hogy a munkamenet le lett szakítva. Az alábbi részlet egy minta `LogoutRequest` elemet mutat be.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Az Azure AD-ba eljuttatott `LogoutRequest` elemnek a következő attribútumokra van szüksége:

* `ID` – a kijelentkezési kérést azonosítja. `ID` értéke nem kezdődhet számmal. A tipikus eljárás egy GUID **azonosítójának** hozzáfűzése a GUID karakterlánc-ábrázolásához.
* `Version` – az elem értékét állítsa **2,0**-ra. Kötelezően megadandó érték.
* `IssueInstant` – ez egy `DateTime` sztring, amely az egyezményes világidő (UTC) értékét és az [oda-és visszaúti formátumot ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Az Azure AD egy ilyen típusú értéket vár, de nem kényszeríti ki.

### <a name="issuer"></a>Kiállító
Egy `LogoutRequest` `Issuer` elemének pontosan egyeznie kell az Azure AD felhőalapú szolgáltatásának egyik **ServicePrincipalNames** . Ez általában az alkalmazás regisztrációja során megadott **alkalmazás-azonosító URI-ra** van beállítva.

### <a name="nameid"></a>NameID
A `NameID` elem értékének pontosan egyeznie kell a bejelentkezett felhasználó `NameID`ával.

## <a name="logoutresponse"></a>LogoutResponse
Az Azure AD egy `LogoutRequest` elemre adott válaszként egy `LogoutResponse` küld. Az alábbi részlet egy minta `LogoutResponse`t mutat be.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Az Azure AD a `LogoutResponse` elemben lévő `ID`, `Version` és `IssueInstant` értékeket állítja be. Emellett a `InResponseTo` elemet a választ kiváltó `LogoutRequest` `ID` attribútumának értékére állítja be.

### <a name="issuer"></a>Kiállító
Az Azure AD ezt az értéket úgy állítja be, hogy `https://login.microsoftonline.com/<TenantIdGUID>/`, ahol \<TenantIdGUID > Az Azure AD-bérlő bérlői azonosítója.

A `Issuer` elem értékének kiértékeléséhez használja az alkalmazás regisztrációja során megadott **alkalmazás-azonosító URI** azonosító értékét.

### <a name="status"></a>Állapot
Az Azure AD a `Status` elem `StatusCode` elemével jelzi a kijelentkezés sikerességét vagy sikertelenségét. Ha a kijelentkezési kísérlet sikertelen, akkor a `StatusCode` elem egyéni hibaüzeneteket is tartalmazhat.
