---
title: Átirányítási URL-címek beállítása b2clogin.com – Azure Active Directory B2C | Microsoft Docs
description: Ismerje meg, hogyan használhatja a b2clogin.com-t a Azure Active Directory B2C átirányítási URL-címeiben.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 080c1933f88d9e824969a42212de2eacd0f62e14
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927283"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Átirányítási URL-címek beállítása b2clogin.com Azure Active Directory B2C

Ha a Azure Active Directory-(Azure AD-) B2C-alkalmazásban regisztrál és bejelentkezik egy identitás-szolgáltatót, meg kell adnia egy átirányítási URL-címet. A múltban a login.microsoftonline.com használta, most pedig az b2clogin.com-t kell használnia.

> [!NOTE]
> A b2clogin.com-ben használhat JavaScript ügyféloldali kódot (jelenleg előzetes verzióban). Ha a login.microsoftonline.com-t használja, a JavaScript-kód törlődik az egyéni lapról. A rendszer további biztonsági korlátozásokat is alkalmaz a login.microsoftonline.com, például a HTML-űrlapok elemeinek eltávolítását az egyéni lapról. 

A b2clogin.com használata további előnyöket biztosít, például a következőket:

- A Microsoft-szolgáltatások cookie-fejlécében felhasznált lemezterület csökken.
- Az URL-címek már nem tartalmaznak hivatkozást a Microsoftnak. Például: `https://your-tenant-name.b2clogin.com/tenant-id/oauth2/authresp`.

> [!NOTE]
> A bérlő nevét és a bérlői GUID-t a következőképpen is használhatja:
> * `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`(amely továbbra is `onmicrosoft.com`a következőre hivatkozik)
> * `https://your-tenant-name.b2clogin.com/your-tenant-guid`(ebben az esetben a Microsoft egyáltalán nem hivatkozik rá)
>
> Azonban nem használhat _Egyéni tartományt_ a Azure Active Directory B2C bérlőhöz, például `https://your-tenant-name.b2clogin.com/your-custom-domain-name` _nem_ fog működni.

Ezeket a beállításokat érdemes lehet módosítani a b2clogin.com használatakor:

- Állítsa be az átirányítási URL-címeket az Identity Provider alkalmazásaiban a b2clogin.com használatára. 
- Állítsa be Azure AD B2C alkalmazást a b2clogin.com használatára a felhasználói folyamatok hivatkozásai és a jogkivonat-végpontok számára. 
- Ha a MSAL-t használja, be kell állítania a **ValidateAuthority** tulajdonságot `false`a következőre:.
- Győződjön meg arról, hogy a [felhasználói felület testreszabásához](active-directory-b2c-ui-customization-custom-dynamic.md)tartozó CORS-beállításokban meghatározott összes **engedélyezett eredetet** módosítja.  

## <a name="change-redirect-urls"></a>Átirányítási URL-címek módosítása

A b2clogin.com használatához az Identity Provider alkalmazás beállításainál keresse meg és módosítsa a megbízható URL-címek listáját, amelyeket vissza szeretne irányítani Azure AD B2Cre.  Jelenleg valószínűleg úgy állította be, hogy visszairányítson egy bizonyos login.microsoftonline.com-webhelyre. 

Módosítania kell az átirányítási URL-címet, `your-tenant-name.b2clogin.com` hogy az engedélyezve legyen. Ügyeljen arra, hogy `your-tenant-name` a helyére írja a Azure ad B2C bérlő nevét, és `/te` távolítsa el, ha az szerepel az URL-címben. Az URL-címek kis mértékben eltérnek az egyes identitás-szolgáltatóknál, ezért a megfelelő oldalon tekintse meg a pontos URL-címet.

A következő cikkekben talál információt az identitás-szolgáltatók számára:

- [Microsoft-fiók](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHubon](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [Egyéni OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Az alkalmazás frissítése

A Azure ad B2C alkalmazás valószínűleg több helyen `login.microsoftonline.com` hivatkozik, például a felhasználói folyamatok hivatkozásaira és a jogkivonat-végpontokra.  Győződjön meg arról, hogy az engedélyezési végpont, a jogkivonat-végpont és a kiállító frissítve lett `your-tenant-name.b2clogin.com`a használatra.  

## <a name="set-the-validateauthority-property"></a>A ValidateAuthority tulajdonság beállítása

Ha a MSAL-t `false`használja, állítsa a **ValidateAuthority** tulajdonságot a következőre:. Ha a `false`ValidateAuthority értékre van állítva, a rendszer átirányítja a b2clogin.com. 

Az alábbi példa bemutatja, hogyan állíthatja be a tulajdonságot:

A [.net-MSAL esetében](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet):

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

És a [MSAL for JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js):

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
