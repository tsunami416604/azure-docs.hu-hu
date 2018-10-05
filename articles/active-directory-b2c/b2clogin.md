---
title: Set átirányítási URL-címek a b2clogin.com-on Azure Active Directory B2C |} A Microsoft Docs
description: További információ a b2clogin.com használata az átirányítási URL-címek az Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8e06cf1a443d4fd158e29ef4b53206a83800dfe9
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803052"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Set átirányítási URL-címek a b2clogin.com-on Azure Active Directory B2C-vel

Az identitásszolgáltató-előfizetés, és jelentkezzen be az Azure Active Directory (Azure AD) B2C alkalmazásban beállításakor szüksége egy átirányítási URL-címet. A múltban login.microsoftonline.com használták, most kell a b2clogin.com használata.

A b2clogin.com használata lehetővé teszi további előnyökkel, például:

- A cookie-k már nem a más Microsoft-szolgáltatásokba vannak megosztva.
- Az URL-címek már nem a Microsoft hivatkozást tartalmaz. Például: `https://your-tenant-name.b2clogin.com/tfp/your-tenant-ID/policyname/v2.0/.well-known/openid-configuration`.

A b2clogin.com használata esetén állítsa be az átirányítási URL-címek az identity provider alkalmazásokban a b2clogin.com használata. Is meg az Azure AD B2C alkalmazás a b2clogin.com használata a házirend-referenciák és jogkivonat-végpont. Az MSAL használatakor be kell a **ValidateAuthority** tulajdonságot `false`.

## <a name="change-redirect-urls"></a>Változás átirányítási URL-címek

Szeretne használni a b2clogin.com, az identity provider alkalmazás beállításait, keresse meg és megbízható URL-címének átirányítása az Azure AD B2C-vel listája.  Jelenleg, valószínűleg nincs beállítva néhány login.microsoftonline.com hely visszairányítja. 

Módosítsa az átirányítási URL-címet kell, hogy `your-tenant-name.b2clogin.com` jogosult. Cserélje le `your-tenant-name` neve az Azure AD B2C-bérlőben, és távolítsa el `/te` Ha az URL-cím létezik. Kisebb-változatok vannak az URL-címet minden egyes identitásszolgáltató ezért ellenőrizze a megfelelő lapon, a pontos URL-Címének lekéréséhez.

Identitás-szolgáltatóktól telepítési adatait a következő cikkekben találja meg:

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

Az Azure AD B2C-alkalmazást valószínűleg hivatkozik `login.microsoftonline.com` több helyen, például a házirend-hivatkozások és a jogkivonat-végpont.  Győződjön meg arról, hogy az engedélyezési végpont, a jogkivonat-végpont és a kiállító használatára frissítve lett-e `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>A ValidateAuthority tulajdonsága

Ha MSAL használ, állítsa be a **ValidateAuthority** való `false`. Az alábbi példa bemutatja, hogyan lehet, hogy a tulajdonság értéke:

```
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

 További információkért lásd: [ClientApplicationBase osztály ](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase?view=azure-dotnet).