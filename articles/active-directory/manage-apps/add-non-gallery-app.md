---
title: Nem Gallery-alkalmazás hozzáadása – Microsoft Identity platform | Microsoft Docs
description: Adjon hozzá egy nem katalógusbeli alkalmazást az Azure AD-bérlőhöz.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: db8d8d6df16c5df7e29d8bb870c5d5eda6d8a2d3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477264"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Lista nélküli (nem katalógusos) alkalmazás hozzáadása az Azure AD-szervezethez

Az [Azure ad Application Galleryben](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)választható lehetőségek mellett lehetősége van egy nem katalógusbeli **alkalmazás**hozzáadására is. Hozzáadhat bármely olyan alkalmazást, amely már létezik a szervezetében, vagy bármely harmadik féltől származó alkalmazás olyan szállítótól, amely még nem része az Azure AD-katalógusnak. A licencszerződéstől függően [](https://azure.microsoft.com/pricing/details/active-directory/)a következő lehetőségek érhetők el:

- Bármely olyan alkalmazás önkiszolgáló integrációja, amely támogatja az [Security Assertion Markup Language (SAML) 2,0-](https://wikipedia.org/wiki/SAML_2.0) es identitás-szolgáltatót (SP által kezdeményezett vagy identitásszolgáltató)
- A HTML-alapú bejelentkezési oldallal rendelkező webalkalmazások önkiszolgáló integrációja [jelszó-alapú egyszeri bejelentkezés](what-is-single-sign-on.md#password-based-sso) használatával
- Az olyan alkalmazások önkiszolgáló kapcsolata, amelyek a [rendszert a tartományok közötti Identitáskezelés-felügyeleti (scim) protokoll használatával használják a felhasználók üzembe](use-scim-to-provision-users-and-groups.md) helyezéséhez
- Az [Office 365 app Launcher](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) vagy az [Azure ad hozzáférési paneljén](what-is-single-sign-on.md#linked-sign-on) bármely alkalmazásra mutató hivatkozások hozzáadásának lehetősége

Ez a cikk azt ismerteti, hogyan adhat hozzá egy nem katalógusos alkalmazást a **vállalati alkalmazásokhoz** a Azure Portal kód írása nélkül. Ha ehelyett fejlesztői útmutatóra van szüksége az egyéni alkalmazások Azure AD-vel való integrálásához, tekintse meg az [Azure ad hitelesítési forgatókönyveit](../develop/authentication-scenarios.md). Ha olyan alkalmazást fejleszt ki, amely egy modern protokollt használ, például az [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) -t a felhasználók hitelesítéséhez, akkor a Azure Portal [Alkalmazásregisztrációk](../develop/quickstart-register-app.md) felületének használatával regisztrálhatja azt a Microsoft Identity platformon.

## <a name="add-a-non-gallery-application"></a>Nem Gallery-alkalmazás hozzáadása

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/) a Microsoft Identity platform rendszergazdai fiókjának használatával.
1. Válassza a **vállalati alkalmazások** > **új alkalmazás**lehetőséget.
2. (Nem kötelező, de ajánlott) A **Hozzáadás a** katalógusból mezőben adja meg az alkalmazás megjelenítendő nevét. Ha az alkalmazás megjelenik a keresési eredmények között, válassza ki, és hagyja ki az eljárás hátralévő részét.
3. Válassza a **nem Gallery-alkalmazás**lehetőséget. Megjelenik a **saját alkalmazás hozzáadása** lap.

   ![Alkalmazás hozzáadása](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Adja meg az új alkalmazás megjelenítendő nevét.
6. Válassza a **Hozzáadás** lehetőséget. Megnyílik az  alkalmazás áttekintő lapja.

## <a name="configure-user-sign-in-properties"></a>Felhasználó bejelentkezési tulajdonságainak konfigurálása

1. Válassza a **Tulajdonságok** lehetőséget a Tulajdonságok ablaktábla szerkesztéshez való megnyitásához.

    ![Tulajdonságok ablaktábla szerkesztése](media/add-application-portal/edit-properties.png)

1. A következő beállítások megadásával meghatározhatja, hogy az alkalmazáshoz hozzárendelt vagy hozzá nem rendelt felhasználók hogyan jelentkezhetnek be az alkalmazásba, és ha egy felhasználó láthatja az alkalmazást a hozzáférési panelen.

    - Az **Engedélyezett a felhasználók számára a bejelentkezés** meghatározza, hogy az alkalmazáshoz hozzárendelt felhasználók bejelentkezhetnek-e.
    - A **felhasználó-hozzárendelés kötelezően** meghatározza, hogy az alkalmazáshoz nem rendelt felhasználók bejelentkezhetnek-e.
    - A **Felhasználók számára látható** meghatározza, hogy az alkalmazáshoz hozzárendelt felhasználók láthatják-e az alkalmazást a hozzáférési panelen és az O365 indítójában.

      Működés **hozzárendelt** felhasználók esetében:

       | Alkalmazás tulajdonságainak beállításai | | | Felhasználói élmény – hozzárendelt felhasználók | |
       |---|---|---|---|---|
       | Engedélyezett a felhasználók számára a bejelentkezés? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Bejelentkezhetnek a hozzárendelt felhasználók? | Látható az alkalmazás a hozzárendelt felhasználók számára?* |
       | igen | igen | igen | igen | igen  |
       | igen | igen | nem  | igen | nem   |
       | igen | nem  | igen | igen | igen  |
       | igen | nem  | nem  | igen | nem   |
       | nem  | igen | igen | nem  | nem   |
       | nem  | igen | nem  | nem  | nem   |
       | nem  | nem  | igen | nem  | nem   |
       | nem  | nem  | nem  | nem  | nem   |

      Működés **nem hozzárendelt** felhasználók esetében:

       | Alkalmazás tulajdonságainak beállításai | | | Felhasználói élmény – nem hozzárendelt felhasználók | |
       |---|---|---|---|---|
       | Engedélyezve van a felhasználók számára a bejelentkezés? | Kell felhasználó-hozzárendelés? | Felhasználók számára látható? | Bejelentkezhetnek a nem hozzárendelt felhasználók? | Látható az alkalmazás a nem hozzárendelt felhasználók számára?* |
       | igen | igen | igen | nem  | nem   |
       | igen | igen | nem  | nem  | nem   |
       | igen | nem  | igen | igen | nem   |
       | igen | nem  | nem  | igen | nem   |
       | nem  | igen | igen | nem  | nem   |
       | nem  | igen | nem  | nem  | nem   |
       | nem  | nem  | igen | nem  | nem   |
       | nem  | nem  | nem  | nem  | nem   |

     *Látható az alkalmazás a felhasználó számára a hozzáférési panelen és az Office 365 alkalmazás indítójában?

1. Egyéni embléma használatához hozzon létre egy 215 és 215 képpont közötti emblémát, és mentse PNG formátumban. Ezután keresse meg az emblémát, és töltse fel.

    ![Embléma módosítása](media/add-application-portal/change-logo.png)

1. Ha elkészült, válassza a **Mentés**lehetőséget.

## <a name="next-steps"></a>További lépések

Most, hogy hozzáadta az alkalmazást az Azure AD-szervezethez, [válassza ki a használni kívánt egyszeri bejelentkezési módszert](what-is-single-sign-on.md#choosing-a-single-sign-on-method) , és tekintse meg az alábbi megfelelő cikket:

- [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md)
- [Jelszó egyszeri bejelentkezésének konfigurálása](configure-password-single-sign-on-non-gallery-applications.md)
- [Csatolt bejelentkezés konfigurálása](configure-linked-sign-on.md)
