---
title: Galériaalkalmazás hozzáadása – Azure Active Directory | Microsoft dokumentumok
description: Megtudhatja, hogyan adhat hozzá egy alkalmazást az Azure AD-galériából az Azure vállalati alkalmazásaihoz.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73062565"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Galériaalkalmazás hozzáadása az Azure AD-szervezethez

Az Azure Active Directory (Azure AD) rendelkezik egy katalógus, amely több ezer előre integrált alkalmazások, amelyek engedélyezve vannak a vállalati egyszeri bejelentkezés. Ez a cikk ismerteti az általános lépéseket egy alkalmazás hozzáadása a katalógusból az Azure AD-szervezet.

> [!IMPORTANT]
> Először ellenőrizze az alkalmazást az [útmutatók listájában arról, hogyan integrálhatja az SaaS-alkalmazásokat az Azure Active Directoryval.](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) Valószínűleg részletes útmutatást talál a hozzáadni kívánt katalógusalkalmazás hozzáadásához és konfigurálásához.

## <a name="add-a-gallery-application"></a>Galériaalkalmazás hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure AD-bérlő globális rendszergazdájaként, felhőalkalmazás- vagy alkalmazás-rendszergazdaként.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

1. Az **Azure Active Directory** ablaktáblában válassza a **Vállalati alkalmazások**lehetőséget.

    ![Vállalati alkalmazások megnyitása](media/add-gallery-app/open-enterprise-apps.png)


3. Ha katalógusalkalmazást szeretne hozzáadni a bérlőhöz, válassza az **Új alkalmazás**lehetőséget.

    ![Válassza az Új alkalmazás lehetőséget, ha katalógusalkalmazást szeretne hozzáadni a bérlőhöz](media/add-gallery-app/new-application.png)

 4. Váltás az új galéria előnézeti felület: A banner tetején a **Add a application oldal**, válassza ki a linket, hogy **kattintson ide, hogy próbálja ki az új és továbbfejlesztett app galéria**.

5. Az **Azure AD-gyűjtemény tallózása** ablaktábla megnyílik, és megjeleníti a felhőalapú platformok, a helyszíni alkalmazások és a kiemelt alkalmazások csempéit. Vegye figyelembe, hogy a **Kiemelt alkalmazások** szakaszban felsorolt alkalmazások ikonok jelzik, hogy támogatják-e az összevont egyszeri bejelentkezést (SSO) és a kiépítést.

    ![Alkalmazás keresése név vagy kategória szerint](media/add-gallery-app/browse-gallery.png)

6. Böngésszen a gyűjteményben a hozzáadni kívánt alkalmazás között, vagy keresse meg az alkalmazást a keresőmezőbe a nevének megadásával. Ezután válassza ki az alkalmazást az eredmények közül. (Nem kötelező) Az űrlapon módosíthatja az alkalmazás nevét, hogy megfeleljen a szervezet igényeinek.

    ![Alkalmazás hozzáadása a gyűjteményből](media/add-gallery-app/create-application.png)

7. Kattintson a **Létrehozás** gombra. Megjelenik az első lépések lap, amelyen a beállításokkal konfigurálhatja az alkalmazást a vállalat számára.

## <a name="configure-user-sign-in-properties"></a>Felhasználó bejelentkezési tulajdonságainak konfigurálása

1. A **Tulajdonságok** lehetőséget választva nyissa meg a tulajdonságok ablaktáblát szerkesztésre.

    ![Tulajdonságok szerkesztése ablaktábla](media/add-gallery-app/edit-properties.png)

1. Adja meg az alábbi beállításokat annak meghatározásához, hogy az alkalmazáshoz hozzárendelt vagy hozzá nem rendelt felhasználók hogyan jelentkezhetnek be az alkalmazásba, és hogy a felhasználó láthatja-e az alkalmazást a hozzáférési panelen.

    - Az **Engedélyezett a felhasználók számára a bejelentkezés** meghatározza, hogy az alkalmazáshoz hozzárendelt felhasználók bejelentkezhetnek-e.
    - **A szükséges felhasználóhozzárendelés** határozza meg, hogy az alkalmazáshoz nem rendelt felhasználók bejelentkezhetnek-e.
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
       | Engedélyezve van a felhasználók számára a bejelentkezéshez? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Bejelentkezhetnek a nem hozzárendelt felhasználók? | Látható az alkalmazás a nem hozzárendelt felhasználók számára?* |
       | igen | igen | igen | nem  | nem   |
       | igen | igen | nem  | nem  | nem   |
       | igen | nem  | igen | igen | nem   |
       | igen | nem  | nem  | igen | nem   |
       | nem  | igen | igen | nem  | nem   |
       | nem  | igen | nem  | nem  | nem   |
       | nem  | nem  | igen | nem  | nem   |
       | nem  | nem  | nem  | nem  | nem   |

     *Látható az alkalmazás a felhasználó számára a hozzáférési panelen és az Office 365 alkalmazás indítójában?

1. Egyéni embléma használatához hozzon létre egy 215 x 215 képpont méretű emblémát, és mentse PNG formátumban. Ezután keresse meg az emblémát, és töltse fel.

    ![Embléma módosítása](media/add-gallery-app/change-logo.png)

1. Ha végzett, válassza a **Mentés gombot.**

## <a name="next-steps"></a>További lépések

Most, hogy hozzáadta az alkalmazást az Azure AD-szervezethez, [válasszon ki egy bejelentkezési módszert,](what-is-single-sign-on.md#choosing-a-single-sign-on-method) amelyet használni szeretne, és olvassa el az alábbi megfelelő cikket:

- [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md)
- [Jelszó egyszeri bejelentkezéskonfigurálása](configure-password-single-sign-on-non-gallery-applications.md)
- [Csatolt bejelentkezés konfigurálása](configure-linked-sign-on.md)

