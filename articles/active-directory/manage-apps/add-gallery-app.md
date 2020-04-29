---
title: Gallery-alkalmazás hozzáadása – Azure Active Directory | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá egy alkalmazást az Azure AD Galleryből az Azure Enterprise-alkalmazásaihoz.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0a826c499790c86a20ea7061075a6c3e66b3a4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73062565"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Gallery-alkalmazás hozzáadása az Azure AD-szervezethez

Azure Active Directory (Azure AD) gyűjteménye több ezer előre integrált alkalmazást tartalmaz, amelyek engedélyezve vannak a vállalati egyszeri bejelentkezéssel. Ez a cikk az alkalmazások katalógusból az Azure AD-szervezetbe való hozzáadásának általános lépéseit ismerteti.

> [!IMPORTANT]
> Először keresse meg az alkalmazást az [oktatóanyagok listájában arról, hogyan integrálható az SaaS-alkalmazások Azure Active Directory használatával](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). A hozzáadni kívánt Gallery-alkalmazás hozzáadásával és konfigurálásával kapcsolatban valószínűleg részletes útmutatást talál.

## <a name="add-a-gallery-application"></a>Gyűjteménybeli alkalmazás hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure AD-bérlő globális rendszergazdájaként, felhőalkalmazás- vagy alkalmazás-rendszergazdaként.

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

1. A **Azure Active Directory** ablaktáblán válassza a **vállalati alkalmazások**lehetőséget.

    ![Vállalati alkalmazások megnyitása](media/add-gallery-app/open-enterprise-apps.png)


3. Ha szeretne hozzáadni egy Gallery-alkalmazást a bérlőhöz, válassza az **új alkalmazás**lehetőséget.

    ![Válassza az új alkalmazás lehetőséget, ha egy katalógus-alkalmazást szeretne hozzáadni a bérlőhöz](media/add-gallery-app/new-application.png)

 4. Váltson az új katalógus előzetes verziójára: az **alkalmazás hozzáadása lap**tetején található szalagcímen válassza ki azt a hivatkozást, amely az **új és továbbfejlesztett alkalmazás-katalógus kipróbálásához kattintson ide**.

5. Megnyílik az **Azure ad Gallery tallózása** panel, és megjeleníti a Felhőbeli platformok, a helyszíni alkalmazások és a Kiemelt alkalmazások csempéit. Vegye figyelembe, hogy a **Kiemelt alkalmazások** szakaszban felsorolt alkalmazások rendelkeznek ikonokkal, amelyek azt jelzik, hogy támogatják-e az összevont egyszeri bejelentkezést (SSO) és az üzembe helyezést.

    ![Alkalmazás keresése név vagy kategória szerint](media/add-gallery-app/browse-gallery.png)

6. Tallózással keresse meg a hozzáadni kívánt alkalmazás katalógusát, vagy keresse meg az alkalmazást úgy, hogy beírja a nevét a keresőmezőbe. Ezután válassza ki az alkalmazást az eredmények közül. Választható Az űrlapon szerkesztheti az alkalmazás nevét, hogy az megfeleljen a szervezet igényeinek.

    ![Bemutatja, hogyan adhat hozzá egy alkalmazást a katalógusból](media/add-gallery-app/create-application.png)

7. Kattintson a **Létrehozás** gombra. Megjelenik az első lépések lap, amelyen a beállításokkal konfigurálhatja az alkalmazást a vállalat számára.

## <a name="configure-user-sign-in-properties"></a>Felhasználó bejelentkezési tulajdonságainak konfigurálása

1. Válassza a **Tulajdonságok** lehetőséget a Tulajdonságok ablaktábla szerkesztéshez való megnyitásához.

    ![Tulajdonságok ablaktábla szerkesztése](media/add-gallery-app/edit-properties.png)

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
       | Engedélyezve van a felhasználók számára a bejelentkezés? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Bejelentkezhetnek a nem hozzárendelt felhasználók? | Látható az alkalmazás a nem hozzárendelt felhasználók számára?* |
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

    ![Embléma módosítása](media/add-gallery-app/change-logo.png)

1. Ha elkészült, válassza a **Mentés**lehetőséget.

## <a name="next-steps"></a>További lépések

Most, hogy hozzáadta az alkalmazást az Azure AD-szervezethez, [válassza ki a használni kívánt egyszeri bejelentkezési módszert](what-is-single-sign-on.md#choosing-a-single-sign-on-method) , és tekintse meg az alábbi megfelelő cikket:

- [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md)
- [Jelszó egyszeri bejelentkezésének konfigurálása](configure-password-single-sign-on-non-gallery-applications.md)
- [Csatolt bejelentkezés konfigurálása](configure-linked-sign-on.md)

