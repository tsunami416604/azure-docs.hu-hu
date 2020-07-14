---
title: 'Gyors útmutató: alkalmazás tulajdonságainak konfigurálása a Azure Active Directory (Azure AD) bérlőben'
description: Ez a rövid útmutató a Azure Portal használatával konfigurálja az Azure Active Directory (Azure AD) Bérlővel regisztrált alkalmazást.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8418a45c9eba596339999c2bddf937a516d3e2d9
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223346"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Gyors útmutató: alkalmazás tulajdonságainak konfigurálása a Azure Active Directory (Azure AD) bérlőben

Az előző rövid útmutatóban egy alkalmazást adott hozzá a Azure Active Directory (Azure AD) bérlőhöz. Ha hozzáad egy alkalmazást, az Azure AD-bérlőnek tudnia kell, hogy az alkalmazás identitás-szolgáltatója. Most konfigurálja az alkalmazás egyes tulajdonságait.
 
## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-bérlőben található alkalmazások tulajdonságainak konfigurálásához a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A következő szerepkörök egyike: globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás rendszergazdája vagy az egyszerű szolgáltatásnév tulajdonosa.
- Nem kötelező: az [alkalmazások megtekintésének](view-applications-portal.md)befejezése.
- Nem kötelező: az [alkalmazás hozzáadásának](add-application-portal.md)befejezése.

>[!IMPORTANT]
>Az ebben a rövid útmutatóban ismertetett lépések teszteléséhez használjon nem termékrendszer-környezetet.

## <a name="configure-app-properties"></a>Alkalmazás tulajdonságainak konfigurálása

Amikor befejezi az alkalmazás hozzáadását az Azure AD-bérlőhöz, megjelenik az Áttekintés oldal. Ha már hozzáadott alkalmazást konfigurál, tekintse meg az első rövid útmutatót. Végigvezeti Önt a bérlőhöz hozzáadott alkalmazások megtekintésén. 

Az alkalmazás tulajdonságainak szerkesztése:

1. Az Azure AD-portálon válassza a **vállalati alkalmazások**lehetőséget. Ezután keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
1. A **kezelés** szakaszban válassza a **Tulajdonságok** lehetőséget a **Tulajdonságok** ablaktábla szerkesztéshez való megnyitásához.

    ![Képernyőfelvétel a tulajdonságok képernyőről, amely a szerkeszthető alkalmazás tulajdonságait jeleníti meg.](media/add-application-portal/edit-properties.png)

1. Szánjon egy kis időt a konfiguráláshoz elérhető beállítások megismerésére:
    - **Engedélyezve van a felhasználók számára a bejelentkezés?** meghatározza, hogy be tud-e jelentkezni az alkalmazáshoz hozzárendelt felhasználók.
    - **Felhasználói hozzárendelés szükséges?** meghatározza, hogy be tud-e jelentkezni az alkalmazáshoz nem rendelt felhasználók.
    - **Látható a felhasználók számára?** meghatározza, hogy az alkalmazáshoz rendelt felhasználók láthatják-e a [hozzáférési panelen](https://myapps.microsoft.com) és az Office 365 app launcherben. (Lásd az Office 365 vagy Microsoft 365 webhely bal felső sarkában található Waffle menüt.)
1. Az alábbi táblázatok segítségével kiválaszthatja az igényeinek legmegfelelőbb beállításokat.

   - Működés *hozzárendelt* felhasználók esetében:

       | Alkalmazás tulajdonsága | Alkalmazás tulajdonsága | Alkalmazás tulajdonsága | Felhasználói élmény – hozzárendelt felhasználók | Felhasználói élmény – hozzárendelt felhasználók |
       |---|---|---|---|---|
       | Engedélyezve van a felhasználók számára a bejelentkezés? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Bejelentkezhetnek a hozzárendelt felhasználók? | Látható az alkalmazás a hozzárendelt felhasználók számára?* |
       | Igen | Igen | Igen | Igen | Igen  |
       | Igen | Igen | Nem  | Igen | Nem   |
       | Igen | Nem  | Igen | Igen | Igen  |
       | Igen | Nem  | Nem  | Igen | Nem   |
       | Nem  | Igen | Igen | Nem  | Nem   |
       | Nem  | Igen | Nem  | Nem  | Nem   |
       | Nem  | Nem  | Igen | Nem  | Nem   |
       | Nem  | Nem  | Nem  | Nem  | Nem   |

   - Működés *nem hozzárendelt* felhasználók esetében:

       | Alkalmazás tulajdonsága | Alkalmazás tulajdonsága | Alkalmazás tulajdonsága | Felhasználói élmény – nem hozzárendelt felhasználók | Felhasználói élmény – nem hozzárendelt felhasználók |
       |---|---|---|---|---|
       | Engedélyezve van a felhasználók számára a bejelentkezés? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Bejelentkezhetnek a nem hozzárendelt felhasználók? | Látható az alkalmazás a nem hozzárendelt felhasználók számára?* |
       | Igen | Igen | Igen | Nem  | Nem   |
       | Igen | Igen | Nem  | Nem  | Nem   |
       | Igen | Nem  | Igen | Igen | Nem   |
       | Igen | Nem  | Nem  | Igen | Nem   |
       | Nem  | Igen | Igen | Nem  | Nem   |
       | Nem  | Igen | Nem  | Nem  | Nem   |
       | Nem  | Nem  | Igen | Nem  | Nem   |
       | Nem  | Nem  | Nem  | Nem  | Nem   |

     *Látható az alkalmazás a felhasználó számára a hozzáférési panelen és az Office 365 alkalmazás indítójában?

## <a name="use-a-custom-logo"></a>Egyéni embléma használata

Egyéni embléma használata:

1. Hozzon létre egy 215-es, 215 képpont-os emblémát, és mentse. png formátumban.
1. Az Azure AD-portálon válassza a **vállalati alkalmazások**lehetőséget. Ezután keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
1. A **kezelés** szakaszban válassza a **Tulajdonságok** lehetőséget a **Tulajdonságok** ablaktábla szerkesztéshez való megnyitásához. 
1. Válassza ki az ikont az embléma feltöltéséhez.
1. Ha elkészült, válassza a **Mentés**lehetőséget.

    ![Képernyőkép a tulajdonságok képernyőről, amely bemutatja, hogyan módosíthatja az emblémát.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > A **Tulajdonságok** ablaktáblán megjelenő miniatűr nem azonnal frissül. A frissített ikon megjelenítéséhez kattintson a **Tulajdonságok** panel bezárásához és újbóli megnyitásához.

## <a name="next-steps"></a>További lépések

Most, hogy konfigurálta egy alkalmazás tulajdonságait, folytathatja az egyszeri bejelentkezés beállítását.

- [Egyszeri bejelentkezés beállítása](add-application-portal-setup-sso.md)
- [Alkalmazás törlése](delete-application-portal.md)