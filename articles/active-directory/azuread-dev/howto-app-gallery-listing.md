---
title: Az alkalmazás listázása az Azure AD alkalmazásgyűjteményében | Microsoft dokumentumok
description: Megtudhatja, hogy miként sorolhat fel egy egyszeri bejelentkezést támogató alkalmazást az Azure Active Directory alkalmazásgalériájában
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/06/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 969193b2c0843c4eb217d2bdb9f9ad9a40ccf9af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154967"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Ez a cikk bemutatja, hogyan sorolhatja fel az alkalmazást az Azure Active Directory (Azure AD) alkalmazásgalériában, valósíthatja meg az egyszeri bejelentkezést (SSO), és kezelheti a listát.

## <a name="what-is-the-azure-ad-application-gallery"></a>Mi az Azure AD alkalmazáskatalógus?

- Az ügyfelek megtalálják a lehető legjobb egyszeri bejelentkezési élményt.
- Az alkalmazás konfigurációja egyszerű és minimális.
- Egy gyors keresés megtalálja az alkalmazást a galériában.
- Az ingyenes, alapszintű és prémium szintű Azure AD-ügyfelek mind használhatják ezt az integrációt.
- A kölcsönös ügyfelek lépésről lépésre konfigurálási oktatóanyagot kapnak.
- A tartományok közötti identitáskezelés rendszerének[(SCIM)](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)használó felhasználói használhatják ugyanahhoz az alkalmazáshoz a kiépítést.

## <a name="prerequisites"></a>Előfeltételek

- Összevont alkalmazások (Open ID és SAML/WS-Fed) esetében az alkalmazásnak támogatnia kell a szoftver-szolgáltatásként (SaaS) modellt az Azure AD alkalmazásgalériában való listázáshoz. A vállalati katalógusalkalmazásoknak több ügyfélkonfigurációt kell támogatniuk, nem pedig egy adott ügyfelet.
- Az Open ID Connect esetén az alkalmazásnak több-bérlősnek kell lennie, és az [Azure AD jóváhagyási keretrendszert](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) megfelelően kell megvalósítani az alkalmazáshoz. A felhasználó elküldheti a bejelentkezési kérelmet egy közös végpontra, hogy bármely ügyfél beleegyezését adja az alkalmazáshoz. A felhasználói hozzáférés a bérlői azonosító és a felhasználó upn kapott a jogkivonatalapján.
- SamL 2.0/WS-Fed esetén az alkalmazásnak képesnek kell lennie az SAML/WS-Fed SSO-integráció sp- vagy IDP módban történő lehetővé. A kérelem elküldése előtt győződjön meg arról, hogy ez a funkció megfelelően működik.
- A jelszó egyszeri bejelentkezés, győződjön meg arról, hogy az alkalmazás támogatja az űrlaphitelesítést, hogy a jelszó-tároló lehet tenni, hogy egyszeri bejelentkezés a várt módon működik.
- Legalább két regisztrált felhasználóval végzett teszteléshez állandó fiókra van szükség.

**Hogyan szerezheti be az Azure AD-t fejlesztőknek?**

Ingyenes tesztfiókot kaphat az Összes prémium szintű Azure AD-funkcióval – 90 nap ingyenes, és kiterjeszthető mindaddig, amíg dev-munkát végez vele:https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>A kérelem elküldése a portálon

Miután tesztelte, hogy az alkalmazásintegráció együttműködik-e az Azure AD-vel, küldje el hozzáférési kérelmét az [Application Network portálon.](https://microsoft.sharepoint.com/teams/apponboarding/Apps) Ha Office 365-fiókkal rendelkezik, ezzel jelentkezz be ebbe a portálba. Ha nem, jelentkezzen be Microsoft-fiókjával, például az Outlookkal vagy a Hotmaillel.

Ha a bejelentkezés után a következő lap jelenik meg, forduljon az [Azure AD SSO-integrációs csapatához.](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) Adja meg a kérelem elküldéséhez használni kívánt e-mail fiókot. Az Azure AD csapata hozzáadja a fiókot a Microsoft Application Network portálon.

![Access-kérelem üzenet a SharePoint portálon](./media/howto-app-gallery-listing/errorimage.png)

A fiók hozzáadása után bejelentkezhet a Microsoft Application Network portálra.

Ha a bejelentkezés után a következő oldal jelenik meg, adja meg a szövegmezőben való hozzáférés szükségességét. Ezután válassza **a Hozzáférés kérése**lehetőséget.

  ![Üzleti indoklás mező a SharePoint portálon](./media/howto-app-gallery-listing/accessrequest.png)

Csapatunk áttekinti a részleteket, és ennek megfelelően hozzáférést biztosít. A kérelem jóváhagyása után jelentkezzen be a portálra, és küldje el a kérelmet a **kérelem elküldése (ISV)** csempéjének kiválasztásával a kezdőlapon.

![Kérelem (ISV) küldése csempe a kezdőlapon](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>Problémák a portálra való bejelentkezéssel kapcsolatban

Ha ezt a hibát látja a bejelentkezés során, akkor itt vannak a probléma részletei, és így javíthatja ki.

* Ha a bejelentkezés az alábbi módon lett letiltva:

  ![alkalmazás megoldásával kapcsolatos problémák a galériában](./media/howto-app-gallery-listing/blocked.png)

**mi történik:**

A vendégfelhasználó egy otthoni bérlő, amely szintén egy Azure AD. A vendégfelhasználó nagy kockázatnak van kitéve. A Microsoft nem engedélyezi a magas kockázatú felhasználók számára az erőforrások elérését. Minden magas kockázatú felhasználónak (alkalmazottaknak vagy vendégeknek/ szállítóknak) orvosolnia kell / meg kell szüntetnie a Microsoft erőforrásaihoz való hozzáférés kockázatát. A vendégfelhasználók számára ez a felhasználói kockázat az otthoni bérlőtől származik, és a házirend az erőforrás-bérlőtől származik (ebben az esetben a Microsoft).
 
**Biztonságos megoldások:**

* Az MFA regisztrált vendégfelhasználói saját felhasználói kockázataikat orvosolják. Ezt a vendégfelhasználó teheti meg, aki biztonságoshttps://aka.ms/sspr) jelszómódosítást vagy alaphelyzetbe állítást hajt végre (az otthoni bérlőnél (ehhez az MFA-nak és az SSPR-nek kell az otthoni bérlőnél). A biztonságos jelszómódosítása vagy alaphelyzetbe állítását az Azure AD-n kell kezdeményezni, és nem a prem.The secured password change or reset must be initiated on Azure AD and not on-prem.

* A vendégfelhasználók adminisztrátorai orvosolják a kockázatot. Ebben az esetben a rendszergazda jelszó-visszaállítást hajt végre (ideiglenes jelszógenerálás). Ehhez nincs szükség identitásvédelemre. A vendégfelhasználó adminisztrátora a https://aka.ms/RiskyUsers "Jelszó visszaállítása" gombra kattinthat.

* Vendég felhasználók adminisztrátorok közel / utasítsa el a kockázatot. Ismét, ez nem kell identitásvédelem. Az admin mehet, https://aka.ms/RiskyUsers és kattintson a "Utasítsa el a felhasználói kockázatot". Azonban az admin kell tennie a kellő gondossággal annak biztosítása érdekében, ez volt a hamis pozitív kockázatértékelés lezárása előtt a felhasználói kockázat. Ellenkező esetben kockázatnak teszik ki a Microsoft és a Microsoft erőforrásait azáltal, hogy vizsgálat nélkül elnyomják a kockázatértékeléseket.

> [!NOTE]
> Ha bármilyen probléma van a hozzáféréssel kapcsolatban, forduljon az [Azure AD SSO integrációs csapatához.](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)

## <a name="implement-sso-by-using-the-federation-protocol"></a>Az SSO megvalósítása az összevonási protokoll használatával

Egy alkalmazás listázásához az Azure AD-alkalmazáskatalógusban először az Azure AD által támogatott összevonási protokollok egyikét kell megvalósítania. Azt is el kell fogadnia, hogy az Azure AD-alkalmazásgaléria általános szerződési feltételeit. Olvassa el az Azure AD alkalmazásgalériájának feltételeit ezen a [webhelyen.](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)

- **OpenID Connect**: Ha integrálni szeretné az alkalmazást az Azure AD-vel az Open ID Connect protokoll használatával, kövesse a [fejlesztők utasításait.](v1-authentication-scenarios.md)

    ![OpenID Connect alkalmazás listázása a galériában](./media/howto-app-gallery-listing/openid.png)

    * Ha az OpenID Connect segítségével szeretné hozzáadni az alkalmazást a katalógus listájához, válassza **az OpenID Connect & OAuth 2.0** lehetőséget az ábrán látható módon.
    * Ha bármilyen probléma van a hozzáféréssel kapcsolatban, forduljon az [Azure AD SSO integrációs csapatához.](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)

- **SAML 2.0** vagy **WS-Fed**: Ha az alkalmazás támogatja az SAML 2.0-s, integrálhatja azt közvetlenül egy Azure AD-bérlővel az [egyéni alkalmazás hozzáadására vonatkozó utasításokat](../active-directory-saas-custom-apps.md)követve.

  ![SamL 2.0 vagy WS-Fed alkalmazás listázása a galériában](./media/howto-app-gallery-listing/saml.png)

  * Ha az **SAML 2.0** vagy a **WS-Fed**használatával szeretné hozzáadni az alkalmazást a katalóguslistájához, válassza az **SAML 2.0/WS-Fed** lehetőséget az ábrán látható módon.

  * Ha bármilyen probléma van a hozzáféréssel kapcsolatban, forduljon az [Azure AD SSO integrációs csapatához.](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)

## <a name="implement-sso-by-using-the-password-sso"></a>Az SSO megvalósítása az SSO jelszó használatával

Hozzon létre egy HTML bejelentkezési lapot tartalmazó webalkalmazást a [jelszóalapú egyszeri bejelentkezés](../manage-apps/what-is-single-sign-on.md)konfigurálásához. Jelszóalapú egyszeri bejelentkezés, más néven jelszó-tároló, lehetővé teszi a felhasználói hozzáférés és jelszavak kezelését olyan webalkalmazásokhoz, amelyek nem támogatják az identitás-összevonást. Ez olyan esetekben is hasznos, amikor több felhasználónak kell megosztania egyetlen fiókot, például a szervezet közösségi média alkalmazásfiókjaival.

![Jelszó-sso alkalmazás listázása a galériában](./media/howto-app-gallery-listing/passwordsso.png)

* Ha azt szeretné, hogy az alkalmazás a listában a listában jelszó sso használatával, válassza **a Jelszó SSO** a hogy látható.
* Ha bármilyen probléma van a hozzáféréssel kapcsolatban, forduljon az [Azure AD SSO integrációs csapatához.](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)

## <a name="request-for-user-provisioning"></a>Felhasználói kiépítésre irányuló kérelem

Kövesse az alábbi képen látható folyamatot a felhasználói kiépítés kéréséhez.

   ![Felhasználói kiépítésre irányuló kérelem](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Meglévő listaelem frissítése vagy eltávolítása

Egy meglévő alkalmazás frissítéséhez vagy eltávolításához az Azure AD alkalmazásgyűjteményben először be kell küldenie a kérelmet az [Application Network portálon.](https://microsoft.sharepoint.com/teams/apponboarding/Apps) Ha Office 365-fiókkal rendelkezik, ezzel jelentkezz be ebbe a portálba. Ha nem, jelentkezzen be Microsoft-fiókjával, például az Outlookkal vagy a Hotmaillel.

- Válassza ki a megfelelő beállítást az alábbi képen látható módon.

    ![Saml alkalmazás listázása a galériában](./media/howto-app-gallery-listing/updateorremove.png)

    * Meglévő alkalmazás frissítéséhez válassza ki a megfelelő beállítást a követelménynek megfelelően.
    * Ha el szeretne távolítani egy meglévő alkalmazást az Azure AD alkalmazásgyűjteményből, válassza **az Alkalmazáslista eltávolítása a gyűjteményből**lehetőséget.
    * Ha bármilyen probléma van a hozzáféréssel kapcsolatban, forduljon az [Azure AD SSO integrációs csapatához.](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)

## <a name="list-requests-by-customers"></a>Ügyfelek kéréseinek listázása

Az ügyfelek kérelmet nyújthatnak be egy alkalmazás listázására az ügyfelek > új kérelmek**elküldése** **alkalmazáskérelmeinek**kiválasztásával.

![Az ügyfél által kért alkalmazások csempéjének megjelenítése](./media/howto-app-gallery-listing/customer-submit-request.png)

Itt van az ügyfél által kért alkalmazások áramlása.

![Az ügyfél által kért alkalmazások folyamatának megjelenítése](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>Idősorok

Az SAML 2.0 vagy WS-Fed alkalmazás galériában való listázásának idővonala 7-10 munkanap.

  ![Saml alkalmazás galériában való listázásának ütemterve](./media/howto-app-gallery-listing/timeline.png)

Az OpenID Connect alkalmazás galériában való listázásának idővonala 2-5 munkanap.

  ![Az OpenID Connect alkalmazás galériában való listázásának ütemterve](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Torkolljon

Az esetleges eszkalációk esetén küldjön e-mailt az SaaSApplicationIntegrations@service.microsoft.com [Azure AD SSO-integrációs csapatának](mailto:SaaSApplicationIntegrations@service.microsoft.com) a rendszerben, és a lehető leghamarabb válaszolunk.
