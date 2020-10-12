---
title: Az alkalmazás listázása az Azure AD Application Galleryben | Microsoft Docs
description: Útmutató az egyszeri bejelentkezést támogató alkalmazások listázásához a Azure Active Directory app Galleryben
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/20/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 40d299049a0a1bbff81c412a5beb84f28e64ff46
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706302"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Ez a cikk bemutatja, hogyan listázhat alkalmazást az Azure Active Directory (Azure AD) alkalmazás-katalógusban, hogyan implementálhat egyszeri bejelentkezést (SSO), és hogyan kezelheti az adatokat.

## <a name="what-is-the-azure-ad-application-gallery"></a>Mi az Azure AD Application Gallery?

- Az ügyfelek megtalálják a lehető legjobb egyszeri bejelentkezési élményt.
- Az alkalmazás konfigurálása egyszerű és minimális.
- A gyors keresés megkeresi az alkalmazást a galériában.
- Az ingyenes, alapszintű és prémium szintű Azure AD-ügyfelek használhatják ezt az integrációt.
- A kölcsönös ügyfelek részletes konfigurációs oktatóanyagot kapnak.
- Azok a felhasználók, akik a rendszert használják a tartományok közötti Identitáskezelés ([scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) számára, használhatják a kiépítés használatát ugyanahhoz az alkalmazáshoz.

## <a name="prerequisites"></a>Előfeltételek

- Összevont alkalmazások esetében (Open ID és SAML/WS-Fed) az alkalmazásnak támogatnia kell az Azure AD-katalógusban való ismerkedéshez szükséges szoftveres (SaaS) modellt. A nagyvállalati katalógus alkalmazásainak több ügyfél-konfigurációt kell támogatnia, és nem adott ügyfelet.
- A nyitott AZONOSÍTÓk összekapcsolásához az alkalmazásnak több-Bérlőnek kell lennie, és az [Azure ad-beli engedélyezési keretrendszert](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) megfelelően kell megvalósítani az alkalmazáshoz. A felhasználó elküldheti a bejelentkezési kérést egy közös végpontnak, így bármely ügyfél beleegyezik az alkalmazásba. A felhasználó hozzáférését a bérlő azonosítója és a tokenben kapott felhasználó egyszerű felhasználóneve alapján szabályozhatja.
- Az SAML 2.0/WS-fed esetében az alkalmazásnak képesnek kell lennie az SAML/WS-fed SSO-integráció SP-vagy IDENTITÁSSZOLGÁLTATÓ módban történő elvégzésére. Győződjön meg arról, hogy ez a funkció megfelelően működik a kérelem elküldése előtt.
- Az egyszeri jelszavas bejelentkezéshez győződjön meg arról, hogy az alkalmazás támogatja az űrlapos hitelesítést, hogy a jelszó-tárolót úgy lehessen elvégezni, hogy az egyszeri bejelentkezés a várt módon működjön.
- Állandó fiókra van szükség legalább két regisztrált felhasználó teszteléséhez.

**Hogyan szerezhet be Azure AD-t a fejlesztőknek?**

A prémium szintű Azure AD-funkciókkal ingyenes tesztelési fiókot vehet igénybe – a 90 napos ingyenes és a bővíthető, ha a fejlesztői munkát végzik: https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>A kérelem elküldése a portálon

Miután tesztelte, hogy az alkalmazás-integráció az Azure AD-vel működik, küldje el kérelmét a [Microsoft Application Network portálon](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

Ha a bejelentkezés után a következő oldal jelenik meg, lépjen kapcsolatba az [Azure ad SSO integrációs csapatával](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Adja meg a kérelem elküldéséhez használni kívánt e-mail-fiókot. Egy üzleti e-mail-cím, például [name@yourbusiness.com](mailto:name@yourbusiness.com) előnyben részesített. Az Azure AD csapata felveszi a fiókot a Microsoft Application Network portálon.

![Hozzáférési kérelemre vonatkozó üzenet a SharePoint-portálon](./media/howto-app-gallery-listing/errorimage.png)

A fiók hozzáadása után bejelentkezhet a Microsoft Application Network portálra.

Ha a bejelentkezés után a következő oldal jelenik meg, adjon meg egy üzleti indoklást a szövegmezőben való hozzáféréshez. Ezután válassza a **hozzáférés kérése**lehetőséget.

  ![Üzleti indoklási mező a SharePoint-portálon](./media/howto-app-gallery-listing/accessrequest.png)

Csapatunk áttekinti a részleteket, és ennek megfelelően hozzáférést biztosít Önnek. A kérelem jóváhagyását követően bejelentkezhet a portálra, és elküldheti a kérést, ha a kezdőlapon a **küldési kérelem (ISV)** csempét választja.

![A beküldési kérelem (ISV) csempéje a kezdőlapon](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>A portálra való bejelentkezéssel kapcsolatos problémák

Ha ezt a hibát látja a bejelentkezés során, akkor itt látható a probléma részletei, és így javítható.

* Ha a bejelentkezés le lett tiltva, ahogy az alábbi ábrán is látható:

  ![az alkalmazás katalógusban való feloldásával kapcsolatos problémák](./media/howto-app-gallery-listing/blocked.png)

**mi történik:**

A vendég felhasználó összevonása egy otthoni Bérlővel, amely egyben Azure AD is. A vendég felhasználó magas kockázatú. A Microsoft nem teszi lehetővé, hogy a magas kockázatú felhasználók hozzáférjenek erőforrásaihoz. Az összes magas kockázatú felhasználó (alkalmazottak vagy vendégek/szállítók) a Microsoft erőforrásaihoz való hozzáférésre vonatkozó kockázatot kell kijavítania/lezárva. A vendég felhasználók számára ez a felhasználói kockázat a Kezdőlap bérlőtől származik, és a szabályzat az erőforrás-bérlőtől származik (ebben az esetben a Microsoft).
 
**Biztonságos megoldások:**

* Az MFA által regisztrált vendég felhasználók a saját felhasználói kockázatot szervizelik. Ezt megteheti a vendég felhasználó a biztonságos jelszó módosításával vagy alaphelyzetbe állításával (a saját https://aka.ms/sspr) bérlőn (ez az MFA és a SSPR szükséges a hazai bérlőn). A biztonságos jelszó módosítását vagy alaphelyzetbe állítását az Azure AD-ben kell elindítani, nem pedig a helyi gépen.

* A vendég felhasználók rendelkeznek a saját adminisztrátorokkal. Ebben az esetben a rendszergazda jelszó-visszaállítást (ideiglenes jelszó létrehozása) hajt végre. Ehhez nincs szükség az identitások védelmére. A vendég felhasználó rendszergazdája a https://aka.ms/RiskyUsers "jelszó alaphelyzetbe állítása" lehetőségre kattintva léphet ki.

* A vendég felhasználók a rendszergazdájuk bezárják vagy elmulasztják a kockázatokat. Ez megint nem szükséges az identitások védelméhez. A rendszergazda a https://aka.ms/RiskyUsers "felhasználói kockázat elvetése" gombra kattintva léphet ki. Azonban a rendszergazdának el kell végeznie az átvilágítás elvégzését annak érdekében, hogy ez hamis pozitív kockázatértékelés legyen a felhasználói kockázat bezárása előtt. Ellenkező esetben a kockázat kiértékelését vizsgálat nélkül letiltva veszélyeztetik a Microsoft erőforrásait.

> [!NOTE]
> Ha bármilyen probléma merül fel a hozzáféréssel kapcsolatban, forduljon az [Azure ad SSO Integration csapatához](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Egyszeri bejelentkezés implementálása az összevonási protokoll használatával

Egy alkalmazás az Azure AD-katalógusban való listázásához először meg kell valósítania az Azure AD által támogatott alábbi összevonási protokollok egyikét. Emellett el kell fogadnia az Azure AD Application Gallery használati feltételeit is. Olvassa el [ezen a webhelyen](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)az Azure ad Application Gallery használati feltételeit.

- **OpenID Connect**: az alkalmazás Azure ad-val való integrálásához az Open ID Connect protokoll használatával kövesse a [fejlesztők utasításait](v1-authentication-scenarios.md).

    ![OpenID Connect-alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/openid.png)

    * Ha az OpenID Connect használatával szeretné felvenni az alkalmazást a katalógusban szereplő listára, válassza az **OpenID connect & OAuth 2,0** lehetőséget a képen látható módon.
    * Ha bármilyen probléma merül fel a hozzáféréssel kapcsolatban, forduljon az [Azure ad SSO Integration csapatához](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **Saml 2,0** vagy **ws-fed**: Ha az alkalmazás támogatja az SAML 2,0-et, akkor közvetlenül is integrálhatja azt egy Azure ad-Bérlővel az [egyéni alkalmazások hozzáadásához szükséges utasítások](../manage-apps/view-applications-portal.md)követésével.

  ![SAML 2,0-vagy WS-Fed-alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/saml.png)

  * Ha az **saml 2,0** vagy a **ws-fed**használatával szeretné felvenni az alkalmazást a katalógusba, válassza az **SAML 2.0/ws-fed** lehetőséget, amint az látható.

  * Ha bármilyen probléma merül fel a hozzáféréssel kapcsolatban, forduljon az [Azure ad SSO Integration csapatához](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Egyszeri bejelentkezés implementálása a jelszó-SSO használatával

Hozzon létre egy webalkalmazást, amely tartalmaz egy HTML-bejelentkezési oldalt a [jelszó alapú egyszeri bejelentkezés](../manage-apps/what-is-single-sign-on.md)konfigurálásához. A jelszó-alapú egyszeri bejelentkezés (más néven a jelszó-tároló) lehetővé teszi a felhasználók hozzáférésének és jelszavának kezelését az identitás-összevonást nem támogató webalkalmazásokhoz. Olyan helyzetekben is hasznos, amikor több felhasználónak egyetlen fiókot kell megosztania, például a szervezete közösségi Media app-fiókjaival.

![Jelszó-SSO-alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/passwordsso.png)

* Ha hozzá szeretné adni az alkalmazást a katalógusban a jelszó SSO használatával, válassza a **jelszó egyszeri bejelentkezés (UserName & Password)** lehetőséget az ábrán látható módon.
* Ha bármilyen probléma merül fel a hozzáféréssel kapcsolatban, forduljon az [Azure ad SSO Integration csapatához](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>A felhasználó kiépítési kérelme

A felhasználó kiépítés igényléséhez kövesse az alábbi képen látható eljárást.

   ![A felhasználó kiépítési kérelme](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Meglévő lista frissítése vagy eltávolítása

Egy meglévő alkalmazás Azure AD-katalógusban való frissítéséhez vagy eltávolításához először el kell küldenie a kérést az [Application Network portálon](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Ha munkahelyi vagy iskolai fiókkal rendelkezik, használja a következőt a portálra való bejelentkezéshez. Ha nem, használja a Microsoft-fiókt, például az Outlookot vagy a Hotmailt a bejelentkezéshez.

- Válassza ki a megfelelő beállítást, ahogy az az alábbi képen is látható.

    ![SAML-alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/updateorremove.png)

    * Egy meglévő alkalmazás frissítéséhez válassza ki a megfelelő beállítást a követelménynek megfelelően.
    * Ha el szeretne távolítani egy meglévő alkalmazást az Azure AD-alkalmazás- **katalógusból, válassza az alkalmazás listájának eltávolítása lehetőséget a gyűjteményből**.
    * Ha bármilyen probléma merül fel a hozzáféréssel kapcsolatban, forduljon az [Azure ad SSO Integration csapatához](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Ügyfelek által küldött kérések listázása

Az ügyfelek az alkalmazások **igénylésére vonatkozó**kérelmet küldhetnek az  >  **új kérés beküldéséhez**.

![Az ügyfél által kért alkalmazások csempe megjelenítése](./media/howto-app-gallery-listing/customer-submit-request.png)

Az ügyfél által kért alkalmazások folyamata.

![Az ügyfél által kért alkalmazások folyamatát jeleníti meg](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>Idősorok

Az SAML 2,0 vagy WS-Fed alkalmazás a katalógusban való listázásának ütemterve 7 – 10 munkanap.

  ![Az SAML-alkalmazások katalógusban való listázásának ütemterve](./media/howto-app-gallery-listing/timeline.png)

A katalógusban található OpenID Connect-alkalmazás listázási folyamatának ütemterve 2 – 5 munkanap.

  ![Az OpenID Connect alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Azok következményeinek

Minden eszkaláció esetében küldjön e-mailt az [Azure ad SSO integrációs csapatának](mailto:SaaSApplicationIntegrations@service.microsoft.com) a címen SaaSApplicationIntegrations@service.microsoft.com , és a lehető leghamarabb válaszolunk.