---
title: Quickstart - Add an app to your Azure Active Directory tenant
description: Ebben a rövid útmutatóban egy katalógusbeli alkalmazást adhat hozzá az Azure Portalon az Azure Active Directory- (Azure AD-) bérlőhöz.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 650a4f643f170fc64dd0e0643d645a7dcbffdfbc
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420463"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Rövid útmutató: Alkalmazás hozzáadása az Azure Active Directory-bérlőhöz

Az Azure Active Directory (Azure AD) rendelkezik egy katalógussal, amely több ezer előre integrált alkalmazást tartalmaz. A vállalat által használt alkalmazások egy része valószínűleg megtalálható a katalógusban. Ebben a rövid útmutatóban egy katalógusbeli alkalmazást adhat hozzá az Azure Portalon az Azure Active Directory- (Azure AD-) bérlőhöz.

Miután hozzáadta az alkalmazást az Azure AD-bérlőhöz, a következőket teheti:

- Manage user access to the application with a Conditional Access policy.
- Felhasználók konfigurálása az alkalmazásba való egyszeri bejelentkezésre az Azure AD-fiókkal.

## <a name="before-you-begin"></a>Előzetes teendők

Ahhoz, hogy hozzáadhasson egy alkalmazást a bérlőhöz, a következőkre van szüksége:

- Azure AD-előfizetés
- Az egyszeri bejelentkezés használatára képes előfizetés az alkalmazáshoz

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure AD-bérlő globális rendszergazdájaként, felhőalkalmazás- vagy alkalmazás-rendszergazdaként.

Javasoljuk, hogy az oktatóanyag lépéseinek teszteléséhez ne éles környezetet használjon. Ha nem rendelkezik nem éles Azure AD-környezettel, szerezze be az [egy hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Alkalmazás hozzáadása az Azure AD-bérlőhöz

Katalógusbeli alkalmazás hozzáadása az Azure AD-bérlőhöz:

1. In the [Azure portal](https://portal.azure.com), on the left navigation panel, select **Azure Active Directory**.

2. In the **Azure Active Directory** pane, select **Enterprise applications**. The **All applications** pane opens and displays a random sample of the applications in your Azure AD tenant.

3. To add a gallery app to your tenant, select **New application**. 

    ![Select New application to add a gallery app to your tenant](media/add-application-portal/new-application.png)

 4. Switch to the new gallery preview experience: In the banner at the top of the **Add an application page**, select the link that says **Click here to try out the new and improved app gallery**.

5. The **Browse Azure AD Gallery (Preview)** pane opens and displays tiles for cloud platforms, on-premises applications, and featured applications. Note that the applications listed in the **Featured applications** section have icons indicating whether they support federated single sign-on (SSO) and provisioning.

    ![Search for an app by name or category](media/add-application-portal/browse-gallery.png)

6. You can browse the gallery for the application you want to add, or search for the application by entering its name in the search box. Then select the application from the results. In the form, you can edit the name of the application to match the needs of your organization. In this example we've changed the name to **GitHub-test**.

    ![Shows how to add an application from the gallery](media/add-application-portal/create-application.png)

7. Kattintson a **Létrehozás** gombra. Megjelenik az első lépések lap, amelyen a beállításokkal konfigurálhatja az alkalmazást a vállalat számára.

You've finished adding your application. A következő szakaszokban megtudhatja, hogyan módosíthatja az alkalmazás emblémáját és szerkesztheti az alkalmazás egyéb tulajdonságait.

## <a name="find-your-azure-ad-tenant-application"></a>Azure AD-bérlői alkalmazás megkeresése

Tegyük fel, hogy időközben valamiért el kellett mennie, és most folytatja az alkalmazás konfigurálását. The first thing to do is find your application.

1. In the **[Azure portal](https://portal.azure.com)** , on the left navigation panel, select **Azure Active Directory**.
1. In the **Azure Active Directory** pane, select **Enterprise applications**.
1. From the **Application Type** drop-down menu, select **All Applications**, and then select **Apply**. A megtekintési lehetőségekről további információkat a [bérlői alkalmazások megtekintését](view-applications-portal.md) ismertető cikkben olvashat.
1. Most az Azure AD-bérlőn található összes alkalmazás listáját látja. A lista egy véletlenszerű minta. To see more applications, select **Show more** one or more times.
1. To quickly find an application in your tenant, enter the application name in the search box and select **Apply**. This example finds the GitHub-test application added previously.

    ![Shows how to find an application using the search box](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Felhasználó bejelentkezési tulajdonságainak konfigurálása

Now that you've found the application, you can open it and configure application properties.

To edit the application properties:

1. Select the application to open it.
2. Select **Properties** to open the properties pane for editing.

    ![Shows the Properties screen and editable app properties](media/add-application-portal/edit-properties.png)

3. Szánjon egy kis időt a bejelentkezési beállítások megismerésére. The options determine how users who are assigned or unassigned to the application can sign into the application. And, the options also determine if a user can see the application in the access panel.

    - **Enabled for users to sign-in?** determines whether users assigned to the application can sign in.
    - **User assignment required?** determines whether users who aren't assigned to the application can sign in.
    - **Visible to users?** determines whether users assigned to an app can see it in the access panel and O365 launcher.

4. Use the following tables to help you choose the best options for your needs.

   - Működés **hozzárendelt** felhasználók esetében:

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

   - Működés **nem hozzárendelt** felhasználók esetében:

       | Alkalmazás tulajdonságainak beállításai | | | Felhasználói élmény – nem hozzárendelt felhasználók | |
       |---|---|---|---|---|
       | Enabled for users to sign in? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Bejelentkezhetnek a nem hozzárendelt felhasználók? | Látható az alkalmazás a nem hozzárendelt felhasználók számára?* |
       | igen | igen | igen | nem  | nem   |
       | igen | igen | nem  | nem  | nem   |
       | igen | nem  | igen | igen | nem   |
       | igen | nem  | nem  | igen | nem   |
       | nem  | igen | igen | nem  | nem   |
       | nem  | igen | nem  | nem  | nem   |
       | nem  | nem  | igen | nem  | nem   |
       | nem  | nem  | nem  | nem  | nem   |

     *Látható az alkalmazás a felhasználó számára a hozzáférési panelen és az Office 365 alkalmazás indítójában?

## <a name="use-a-custom-logo"></a>Egyéni embléma használata

Egyéni embléma használata:

1. Hozzon létre egy emblémát, amelynek mérete 215 × 215 képpont, és mentse PNG formátumban.
1. Since you've already found your application, select the application.
1. In the left pane, select **Properties**.
1. Töltse fel az emblémát.
1. When you're finished, select **Save**. 

    ![Shows how to change the logo from the app's Properties page](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > The thumbnail displayed on this **Properties** pane doesn't update right away. You can close and reopen the properties to see the updated icon.

## <a name="next-steps"></a>Következő lépések

Now that you've added the application to your Azure AD organization, [choose a single sign-on method](what-is-single-sign-on.md#choosing-a-single-sign-on-method) you want to use and refer to the appropriate article below:

- [Configure SAML-based single sign-on](configure-single-sign-on-non-gallery-applications.md)
- [Configure password single sign-on](configure-password-single-sign-on-non-gallery-applications.md)
- [Configure linked sign-on](configure-linked-sign-on.md)
