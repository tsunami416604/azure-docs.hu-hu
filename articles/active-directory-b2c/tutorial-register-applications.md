---
title: Útmutató – alkalmazás engedélyezése a regisztrációt, és jelentkezzen be Azure Active Directory B2C használatával regisztrálása |} Microsoft Docs
description: Az Azure AD B2C bérlő létrehozása és egy alkalmazás regisztrálása az Azure-portál használatával.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: patricka
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/08/2018
ms.author: davidmu
ms.openlocfilehash: 81ab3288d7a365c2665b3b38ca220a3e7cb648c7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-register-an-application-to-enable-sign-up-and-sign-in-using-azure-active-directory-b2c"></a>Oktatóanyag: Egy alkalmazás-előfizetési, és jelentkezzen be Azure Active Directory B2C használatával regisztrálása

Ez az oktatóanyag segítséget nyújt a Microsoft Azure Active Directory (Azure AD) B2C-bérlő létrehozása és egy alkalmazás regisztrálása vele csak néhány perc múlva.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * A bérlő csatolása az előfizetéshez
> * Alkalmazás regisztrálása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása

B2C funkciók nem engedélyezhetők a a meglévő bérlők számára. Azure AD B2C-bérlő létrehozásához szükséges.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Gratulálunk, az Azure Active Directory B2C-bérlő hozott létre. A bérlő globális rendszergazdája áll. Szükség szerint más globális rendszergazdákat is hozzáadhat. Az új bérlőjéhez tartozik, kattintson a *kezelése az új bérlő hivatkozás*.

![Az új bérlő hivatkozás kezelése](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Ha azt tervezi, hogy a B2C-bérlő éles alkalmazások használja, olvassa el a cikk a [éles méretű és a B2C előzetes bérlők](active-directory-b2c-reference-tenant-type.md). Vannak ismert problémák egy meglévő B2C-bérlő törölje és hozza létre újból az azonos nevű tartomány. Egy másik tartomány nevét a B2C-bérlő létrehozásához szükséges.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>A bérlő csatolása az előfizetéshez

Az Azure AD B2C-bérlő csatolása az Azure-előfizetés engedélyezése a B2C-funkciókat és használati díjat kell fizetnie kell. További tudnivalókért olvassa el a [Ez a cikk](active-directory-b2c-how-to-enable-billing.md). Ha az Azure AD B2C-bérlő nem kapcsolódik az Azure-előfizetéssel, néhány funkció le van tiltva, és megjelenik egy figyelmeztető üzenet ("Nincs előfizetés csatolva a B2C-bérlő vagy az előfizetés kell a figyelmet.") a B2C beállítások. Fontos, hogy ezzel a lépéssel előtt az alkalmazások éles környezetben.


[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Emellett a blade megadásával `Azure AD B2C` a **keresési erőforrások** a portál felső. Az eredmények listájában válassza **az Azure AD B2C** a B2C beállítások panel eléréséhez.

## <a name="register-your-application"></a>Alkalmazás regisztrálása

A felhasználói regisztrációt és bejelentkezést elfogadó alkalmazás létrehozásához először regisztrálnia kell az alkalmazást egy Azure Active Directory B2C-bérlővel. Hozzon létre egy saját bérlőt az [Azure AD B2C bérlő létrehozása](active-directory-b2c-get-started.md) részben ismertetett lépések segítségével.

Az Azure Portalon létrehozott alkalmazásokat ugyanarról a helyről kell kezelnie. Ha az Azure AD B2C-alkalmazásokat a PowerShell vagy egy másik portál használatával szerkeszti, a rendszer nem támogatja többé az alkalmazásokat, és nem fognak működni az Azure AD B2C-vel. További részletek a [hibás alkalmazások](#faulted-apps) szakaszban. 

A jelen cikkben bemutatott példák segítségével megismerkedhet a minták használatával. A következő cikkekből többet is megtudhat ezekről a mintákról.

### <a name="navigate-to-b2c-settings"></a>Navigálás a B2C beállításaihoz

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a B2C-bérlő globális rendszergazdájaként. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

### <a name="choose-next-steps-based-on-your-application-type"></a>Válassza ki a következő lépéseket az alkalmazás típusától függően

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

* **Egyedi**: Az átirányítási URI sémájának minden alkalmazás esetén egyedinek kell lennie. A példában (com.onmicrosoft.contoso.appname://redirect/path) a com.onmicrosoft.contoso.appname a séma. Javasoljuk, hogy ezt a mintát kövesse. Ha két alkalmazás ugyanazt a sémát használja, akkor a felhasználó egy „Alkalmazás kiválasztása” párbeszédpanelt fog látni. Ha a felhasználó helytelenül választ, a bejelentkezés meghiúsul.
* **Teljes**: Az átirányítási URI-nak sémával és elérési útvonallal kell rendelkeznie. Az útvonalnak a tartomány után legalább egy perjelet kell tartalmaznia (például a //contoso/ helyes, de a //contoso nem).

Ügyeljen rá, hogy az átirányítási uri ne tartalmazzon különleges karaktereket, például aláhúzásjeleket.

### <a name="faulted-apps"></a>Hibás alkalmazások

A B2C-alkalmazások NEM szerkeszthetők:

* Egyéb alkalmazáskezelési portálokon, például az [Alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/).
* Graph API vagy PowerShell használata

Ha a leírtak alapján szerkeszti az Azure AD B2C-alkalmazást, és ezután megpróbálja újra szerkeszteni az Azure Portal Azure AD B2C-funkciókat az Azure Portalon, az alkalmazás hibás alkalmazássá válik, és többé nem lehet majd használni az Azure AD B2C-vel. Az alkalmazást ekkor törölnie kell, és újra létre kell hoznia.

Az alkalmazás törléséhez lépjen az [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/), ahol törölheti az alkalmazást. Ahhoz, hogy láthatóvá lehessen tenni az alkalmazást, Önnek kell lennie az alkalmazás tulajdonosának (és nem csak a bérlő rendszergazdájának).

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * A bérlő csatolása az előfizetéshez
> * Alkalmazás regisztrálása

> [!div class="nextstepaction"]
> [Egy webes alkalmazás való hitelesítéshez szükséges fiókok engedélyezése](active-directory-b2c-tutorials-web-app.md)