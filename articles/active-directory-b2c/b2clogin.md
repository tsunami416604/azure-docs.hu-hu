---
title: A b2clogin.com használata |} A Microsoft Docs
description: Ismerje meg a b2clogin.com használata helyett login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c24582fce44006d9a3972d73078aa8cb0d212c11
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47053844"
---
# <a name="using-b2clogincom"></a>A B2Clogin.com használata

Továbbítja, hogy még arra ösztönzi használni minden ügyfél `<YourDirectoryName>.b2clogin.com` megszüntetjük, és `login.microsoftonline.com`. A B2Clogin.com biztosít további előnyöket és juttatásokat például:
* Már nem az azonos cookie-k megosztása más Microsoft-szolgáltatás.
* Minden hivatkozás, amelyben a Microsoft eltávolíthatja az URL-címben (lecserélheti `<YourDirectoryName>.onmicrosoft.com` a címtár-azonosító). Például: `https://<YourDirectoryName>.b2clogin.com/tfp/<YourDirectoryID>/<policyname>/v2.0/.well-known/openid-configuration`.

Íme, mit kell tennie a b2clogin.com-on keresztül áttelepítése

* Az átirányítási URI-k a közösségi szolgáltató alkalmazások módosítása
* Az alkalmazás helyett a B2Clogin.com használata szerkesztése `login.microsoftonline.com` házirend-referenciák és jogkivonat-végpont számára.
* Ha MSAL használ, be kell `ValidateAuthority=false`.  

##<a name="redirect-uris-for-social-identity-providers"></a>A közösségi identitásszolgáltatókat átirányítási URI-k

Ha közösségi fiók Identitásszolgáltatók állítsa be a könyvtárban van szüksége, módosításokat végezzen az alkalmazásaikba.  Nincs olyan paramétert az alkalmazást, a közösségi szolgáltató, amely megbízható URL-címének átirányítása az Azure AD B2C-vel listáját tartalmazza.  Jelenleg, valószínűleg nincs beállítva visszairányítja néhány `login.microsoftonline.com` helyen kell módosítani az URL-címet, hogy `YourDirectoryName.b2clogin.com` lesz egy engedéllyel rendelkező átirányítási URI-t.  El kell távolítania a `/te` is.  Kisebb-változatok vannak az URL-címet minden egyes identitásszolgáltató ezért ellenőrizze a megfelelő lapon, a pontos URL-Címének lekéréséhez.  

| Identitásszolgáltató |
|-------------------|
|[Microsoft-fiók](active-directory-b2c-setup-msa-app.md)|
|[Facebook](active-directory-b2c-setup-fb-app.md)|
|[Google](active-directory-b2c-setup-goog-app.md)|
|[Amazon](active-directory-b2c-setup-amzn-app.md)|
|[LinkedIn](active-directory-b2c-setup-li-app.md)|
|[Twitter](active-directory-b2c-setup-twitter-app.md)|
|[GitHubon](active-directory-b2c-setup-github-app.md)|
|[Weibo](active-directory-b2c-setup-weibo-app.md)|
|[QQ](active-directory-b2c-setup-qq-app.md)|
|[WeChat](active-directory-b2c-setup-wechat-app.md)|
|[Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)|
|[Egyéni OIDC](active-directory-b2c-setup-oidc-idp.md)|

##<a name="update-your-application-references"></a>Az alkalmazás hivatkozások frissítését

Az alkalmazás valószínűleg hivatkozik `login.microsoftonline.com` több helyen, például a házirend-hivatkozások és a jogkivonat-végpont.  Győződjön meg arról, hogy az engedélyezési végpont, a jogkivonat-végpont és a kiállító frissítve lett-e.  

##<a name="set-validateauthorityfalse-in-msal"></a>Állítsa be `ValidateAuthority=false` az MSAL

Ha MSAL használ, meg kell beállítani `ValidateAuthority=false`.  További információkért lásd: [ebben a dokumentációban](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase?view=azure-dotnet).