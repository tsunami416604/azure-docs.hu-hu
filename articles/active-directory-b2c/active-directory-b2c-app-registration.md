---
title: "Azure Active Directory B2C: Alkalmazás regisztrációja | Microsoft Docs"
description: "Hogyan regisztrálhatja alkalmazását Azure Active Directory B2C-vel?"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: PatAltimore
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.openlocfilehash: 6232459326599d244c6d3f055bf890e78ecf631e
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: Az alkalmazás regisztrációja

Ez a gyors útmutató pár percben összefoglalja egy alkalmazás regisztrálásának folyamatát egy Microsoft Azure Active Directory (Azure AD) B2C-bérlőben. Az útmutató befejeztével az alkalmazás regisztrálva lesz és használatra készen áll az Azure AD B2C-bérlőben.

## <a name="prerequisites"></a>Előfeltételek

A felhasználói regisztrációt és bejelentkezést elfogadó alkalmazás létrehozásához először regisztrálnia kell az alkalmazást egy Azure Active Directory B2C-bérlővel. Hozzon létre egy saját bérlőt az [Azure AD B2C bérlő létrehozása](active-directory-b2c-get-started.md) részben ismertetett lépések segítségével.

Az Azure Portal Azure AD B2C paneljén létrehozott alkalmazásokat ugyanarról a helyről kell kezelnie. Ha a B2C-alkalmazásokat a PowerShell vagy egy másik portál használatával szerkeszti, a rendszer nem támogatja többé az alkalmazásokat, és nem fognak működni az Azure AD B2C-vel. További részletek a [hibás alkalmazások](#faulted-apps) szakaszban. 

A jelen cikkben bemutatott példák segítségével megismerkedhet a minták használatával. A következő cikkekből többet is megtudhat ezekről a mintákról.

## <a name="navigate-to-b2c-settings"></a>Navigálás a B2C beállításaihoz

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a B2C-bérlő globális rendszergazdájaként. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

## <a name="choose-next-steps-based-on-your-application-type"></a>Válassza ki a következő lépéseket az alkalmazás típusától függően

* [Webalkalmazás regisztrációja](#register-a-web-app)
* [Webes API regisztrálása](#register-a-web-api)
* [Mobil vagy natív alkalmazás regisztrálása](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Webalkalmazás regisztrációja

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>Webalkalmazás titkos ügyfélkódjának létrehozása

Ha webalkalmazása az Azure AD B2C által védett webes API-t hív meg, kövesse a következő lépéseket:
   1. Hozzon létre egy alkalmazáskulcsot a **Kulcsok** panel **Kulcs létrehozása** gombjára kattintva. Jegyezze fel az **Alkalmazáskulcs** értékét. Ezt az értéket fogja használni az alkalmazás kódjában alkalmazáskulcsként.
   2. Kattintson az **API-hozzáférés**, majd a **Hozzáadás**, lehetőségre, és válassza ki webes API-ját és a hatóköröket (engedélyeket).

> [!NOTE]
> Az **Application Secret** (Alkalmazástitok) fontos biztonsági hitelesítő adat, amelynek megfelelő biztonságáról gondoskodni kell.
> 

[Ugrás a **következő lépésekre**](#next-steps)

### <a name="register-a-web-api"></a>Webes API regisztrálása

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Szükség szerint több hatókört is felvehet a **Közzétett hatókörök** lehetőségre kattintva. Alapértelmezés szerint a „user_impersonation” hatókör van meghatározva. A user_impersonation hatókör lehetővé teszi az egyéb alkalmazások számára, hogy a bejelentkezett felhasználó nevében érjék el az API-t. Ha szeretné, eltávolíthatja a user_impersonation hatókört.

[Ugrás a **következő lépésekre**](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>Mobil vagy natív alkalmazás regisztrálása

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[Ugrás a **következő lépésekre**](#next-steps)

## <a name="limitations"></a>Korlátozások

### <a name="choosing-a-web-app-or-api-reply-url"></a>Webalkalmazás vagy API-válasz URL-címének kiválasztása

Az Azure AD B2C-vel regisztrált alkalmazások jelenleg csak meghatározott válasz URL-címértékekre korlátozódnak. A webalkalmazások és -szolgáltatások válasz URL-címének a `https`-sémával kell kezdődnie, és valamennyi válasz URL-címértéknek egyetlen DNS-tartományba kell tartoznia. Például nem regisztrálhat olyan webalkalmazást, amely az alábbi URL-címek egyikével rendelkezik:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

A regisztrációs rendszer összehasonlítja a meglévő válasz URL-cím teljes DNS-nevét a hozzáadni kívánt válasz URL-cím DNS-nevével. A DNS-név hozzáadására vonatkozó kérést nem lehet teljesíteni, ha az alábbi körülmények valamelyike fennáll:

* Az új válasz URL-cím teljes DNS-neve nem egyezik meg a meglévő válasz URL-cím DNS-nevével.
* Az új válasz URL-cím teljes DNS-neve nem tartozik a meglévő válasz URL-cím altartományába.

Ha például az alkalmazás válasz URL-címe a következő:

`https://login.contoso.com`

A következő módon adhatja hozzá:

`https://login.contoso.com/new`

Ebben az esetben a DNS-név pontosan egyezik. Esetleg a következőt teheti meg:

`https://new.login.contoso.com`

Ebben az esetben a login.contoso.com egyik DNS-altartományára hivatkozik. Ha azt szeretné, hogy az alkalmazás a login-east.contoso.com vagy a login-west.contoso.com válasz URL-címmel rendelkezzen, ezeket a válasz URL-címeket az alábbi sorrendben kell hozzáadnia:

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Hozzáadhatja az utóbbi kettőt, mivel az első válasz URL-cím, a contoso.com altartományába tartoznak.

### <a name="choosing-a-native-app-redirect-uri"></a>Natív alkalmazás átirányítási URI-jának kiválasztása

A mobil-/natív alkalmazások átirányítási URI-jának kiválasztásakor két fontos szempontot kell megfontolnia:

* **Egyedi**: Az átirányítási URI sémájának minden alkalmazás esetén egyedinek kell lennie. A példánkban (com.onmicrosoft.contoso.appname://redirect/path) a com.onmicrosoft.contoso.appnév a séma. Javasoljuk, hogy ezt a mintát kövesse. Ha két alkalmazás ugyanazt a sémát használja, akkor a felhasználó egy „Alkalmazás kiválasztása” párbeszédpanelt fog látni. Ha a felhasználó helytelenül választ, a bejelentkezés meghiúsul.
* **Teljes**: Az átirányítási URI-nak sémával és elérési útvonallal kell rendelkeznie. Az útvonalnak a tartomány után legalább egy perjelet kell tartalmaznia (például a //contoso/ helyes, de a //contoso nem).

Ügyeljen rá, hogy az átirányítási uri ne tartalmazzon különleges karaktereket, például aláhúzásjeleket.

### <a name="faulted-apps"></a>Hibás alkalmazások

A B2C-alkalmazások NEM szerkeszthetők:

* Egyéb alkalmazáskezelési portálokon, például a [klasszikus Azure portálon](https://manage.windowsazure.com/) vagy az [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/).
* Graph API vagy PowerShell használata

Ha a fent leírtak alapján szerkeszti a B2C-alkalmazást, és ezután megpróbálja újra szerkeszteni az Azure Portal Azure AD B2C-funkciókat tartalmazó paneljén, az alkalmazás hibás alkalmazássá válik, és többé nem lehet majd használni az Azure AD B2C-vel. Az alkalmazást ekkor törölnie kell, és újra létre kell hoznia.

Az alkalmazás törléséhez lépjen az [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/), ahol törölheti az alkalmazást. Ahhoz, hogy láthatóvá lehessen tenni az alkalmazást, Önnek kell lennie az alkalmazás tulajdonosának (és nem csak a bérlő rendszergazdájának).

## <a name="next-steps"></a>Következő lépések

Miután az Azure AD B2C-vel regisztrált egy alkalmazást, [gyors üzembe helyezési oktatóanyagunk](active-directory-b2c-overview.md#get-started) segítségével elsajátíthatja a használatát.

> [!div class="nextstepaction"]
> [ASP.NET-webalkalmazás létrehozása regisztrációval, bejelentkezéssel és új jelszó kérésével](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
