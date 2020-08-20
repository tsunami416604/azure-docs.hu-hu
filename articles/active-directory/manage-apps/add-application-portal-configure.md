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
ms.openlocfilehash: 1873cd14883e8b1a5174b8761e4a0bc9ba1ef872
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641857"
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
2. A **kezelés** szakaszban válassza a **Tulajdonságok** lehetőséget a **Tulajdonságok** ablaktábla szerkesztéshez való megnyitásához.

    ![Képernyőfelvétel a tulajdonságok képernyőről, amely a szerkeszthető alkalmazás tulajdonságait jeleníti meg.](media/add-application-portal/edit-properties.png)

3. Szánjon egy kis időt a konfiguráláshoz elérhető beállítások megismerésére:
    - **Engedélyezve van a felhasználók számára a bejelentkezés?** meghatározza, hogy be tud-e jelentkezni az alkalmazáshoz hozzárendelt felhasználók.
    - **Felhasználói hozzárendelés szükséges?** meghatározza, hogy be tud-e jelentkezni az alkalmazáshoz nem rendelt felhasználók.
    - **Látható a felhasználók számára?** meghatározza, hogy az alkalmazáshoz rendelt felhasználók láthatják-e a [saját alkalmazások](https://myapps.microsoft.com) és az Office 365 app Launcher szolgáltatásban. (Lásd az Office 365 vagy Microsoft 365 webhely bal felső sarkában található Waffle menüt.)
    
    > [!TIP]
    > A felhasználók kiosztása a navigáció **felhasználók és csoportok** szakaszában történik.

    A három lehetőség egymástól függetlenül válthatók, és az eredményül kapott viselkedés nem mindig nyilvánvaló. Itt látható egy táblázat, amely segítségére lehet:
    
    | Engedélyezve van a felhasználók számára a bejelentkezés? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Az alkalmazáshoz rendelt vagy nem használt felhasználók viselkedése. |
    |---|---|---|---|
    | Igen | Igen | Igen | A hozzárendelt felhasználók láthatják az alkalmazást, és bejelentkezhetnek.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be. |
    | Igen | Igen | Nem  | A hozzárendelt felhasználások nem látják az alkalmazást, de be tudnak jelentkezni.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be. |
    | Igen | Nem  | Igen | A hozzárendelt felhasználók láthatják az alkalmazást, és bejelentkezhetnek.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, de jelentkezhetnek be. |
    | Igen | Nem  | Nem  | A hozzárendelt felhasználók nem látják az alkalmazást, de be tudnak jelentkezni.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, de jelentkezhetnek be. |
    | Nem  | Igen | Igen | A hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be. |
    | Nem  | Igen | Nem  | A hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be. |
    | Nem  | Nem  | Igen | A hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be. |
    | Nem  | Nem  | Nem  | A hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be.<br>A nem hozzárendelt felhasználók nem látják az alkalmazást, és nem jelentkezhetnek be. |

4. Ha elkészült, válassza a **Mentés**lehetőséget.

## <a name="use-a-custom-logo"></a>Egyéni embléma használata

Egyéni embléma használata:

1. Hozzon létre egy 215-es, 215 képpont-os emblémát, és mentse. png formátumban.
2. Az Azure AD-portálon válassza a **vállalati alkalmazások**lehetőséget. Ezután keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
3. A **kezelés** szakaszban válassza a **Tulajdonságok** lehetőséget a **Tulajdonságok** ablaktábla szerkesztéshez való megnyitásához. 
4. Válassza ki az ikont az embléma feltöltéséhez.
5. Ha elkészült, válassza a **Mentés**lehetőséget.

    ![Képernyőkép a tulajdonságok képernyőről, amely bemutatja, hogyan módosíthatja az emblémát.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > A **Tulajdonságok** ablaktáblán megjelenő miniatűr nem azonnal frissül. A frissített ikon megjelenítéséhez kattintson a **Tulajdonságok** panel bezárásához és újbóli megnyitásához.


> [!TIP]
> Az Graph API segítségével automatizálhatja az alkalmazások felügyeletét: az [alkalmazások kezelésének automatizálása Microsoft Graph API-val](https://docs.microsoft.com/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a gyors üzembe helyezési sorozatot, akkor érdemes lehet törölni az alkalmazást a tesztelési bérlő törléséhez. Az alkalmazás törlését a sorozat utolsó rövid útmutatója tartalmazza. [alkalmazás törlése](delete-application-portal.md).

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan állíthatja be az egyszeri bejelentkezést az alkalmazásokhoz.
> [!div class="nextstepaction"]
> [Egyszeri bejelentkezés beállítása](add-application-portal-setup-sso.md)
