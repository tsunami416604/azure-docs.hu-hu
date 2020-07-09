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
ms.openlocfilehash: 82a3310f6fc2169a515b4b13d81c88d187bd0f9c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956152"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Gyors útmutató: alkalmazás tulajdonságainak konfigurálása a Azure Active Directory (Azure AD) bérlőben

Az előző rövid útmutatóban egy alkalmazást adott hozzá az Azure AD-bérlőhöz. Ha hozzáad egy alkalmazást, akkor az Azure AD-bérlőnek tudnia kell, hogy az alkalmazás identitás-szolgáltatója. Most konfigurálja az alkalmazás egyes tulajdonságait.
 
## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-bérlőben található alkalmazások tulajdonságainak konfigurálásához a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A következő szerepkörök egyike: globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás rendszergazdája vagy az egyszerű szolgáltatásnév tulajdonosa.
- (Nem kötelező: [az alkalmazások megtekintésének](view-applications-portal.md)befejezése).
- (Nem kötelező: [alkalmazás hozzáadása](add-application-portal.md)).

>[!IMPORTANT]
>Az ebben a rövid útmutatóban ismertetett lépések teszteléséhez nem éles környezet használatát javasoljuk.

## <a name="configure-app-properties"></a>Alkalmazás tulajdonságainak konfigurálása

Amikor befejezi az alkalmazás hozzáadását az Azure AD-bérlőhöz, azonnal megjelenik az áttekintő oldal. Ha olyan alkalmazást konfigurál, amely már hozzá lett adva, tekintse meg az első rövid útmutatót, amely végigvezeti a bérlőhöz hozzáadott alkalmazások megtekintésén. 

Az alkalmazás tulajdonságainak szerkesztése:

1. Az Azure AD-portálon válassza a **vállalati alkalmazások** elemet, majd keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
2. A kezelés szakaszban válassza a **Tulajdonságok** lehetőséget a Tulajdonságok ablaktábla szerkesztéshez való megnyitásához.
    ![A tulajdonságok képernyő és a szerkeszthető alkalmazás tulajdonságainak megjelenítése](media/add-application-portal/edit-properties.png)
3. Szánjon egy kis időt a konfiguráláshoz elérhető beállítások megismerésére.
    - **Engedélyezve van a felhasználók számára a bejelentkezés?** meghatározza, hogy be tud-e jelentkezni az alkalmazáshoz hozzárendelt felhasználók.
    - **Felhasználói hozzárendelés szükséges?** meghatározza, hogy be tud-e jelentkezni az alkalmazáshoz nem rendelt felhasználók.
    - **Látható a felhasználók számára?** meghatározza, hogy az alkalmazáshoz rendelt felhasználók láthatják-e a hozzáférési panelen ( https://myapps.microsoft.com) és az O365 (az Office 365 vagy Microsoft 365 webhely bal felső részén található Waffle menüből).
4. Az alábbi táblázatok segítségével kiválaszthatja az igényeinek legmegfelelőbb beállításokat.

   - Működés **hozzárendelt** felhasználók esetében:

       | Alkalmazás tulajdonsága | Alkalmazás tulajdonsága | Alkalmazás tulajdonsága | Felhasználói élmény – hozzárendelt felhasználók | Felhasználói élmény – hozzárendelt felhasználók |
       |---|---|---|---|---|
       | Engedélyezve van a felhasználók számára a bejelentkezés? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Bejelentkezhetnek a hozzárendelt felhasználók? | Látható az alkalmazás a hozzárendelt felhasználók számára?* |
       | igen | igen | igen | igen | igen  |
       | igen | igen | nem  | igen | nem   |
       | igen | nem  | igen | igen | igen  |
       | igen | nem  | nem  | igen | nem   |
       | nem  | igen | igen | nem  | nem   |
       | nem  | igen | nem  | nem  | nem   |
       | nem  | nem  | igen | nem  | nem   |
       | nem  | nem  | nem  | nem  | nem   |

   - Működés **nem hozzárendelt** felhasználók esetében:

       | Alkalmazás tulajdonsága | Alkalmazás tulajdonsága | Alkalmazás tulajdonsága | Felhasználói élmény – nem hozzárendelt felhasználók | Felhasználói élmény – nem hozzárendelt felhasználók |
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

## <a name="use-a-custom-logo"></a>Egyéni embléma használata

Egyéni embléma használata:

1. Hozzon létre egy emblémát, amelynek mérete 215 × 215 képpont, és mentse PNG formátumban.
2. Az Azure AD-portálon válassza a **vállalati alkalmazások** elemet, majd keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
3. A kezelés szakaszban válassza a **Tulajdonságok** lehetőséget a Tulajdonságok ablaktábla szerkesztéshez való megnyitásához. 
4. Válassza ki az ikont az embléma feltöltéséhez.
5. Ha elkészült, válassza a **Mentés**lehetőséget. 
    ![Bemutatja, hogyan módosítható az embléma az alkalmazás Tulajdonságok lapján](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > A **Tulajdonságok** ablaktáblán megjelenő miniatűr nem azonnal frissül. A frissített ikon megjelenítéséhez a tulajdonságok bezárásával és újbóli megnyitásával megjelenítheti a tulajdonságokat.

## <a name="next-steps"></a>További lépések

Most, hogy konfigurálta egy alkalmazás tulajdonságait, a továbbiakban is beállíthatja az egyszeri bejelentkezést.

- [Egyszeri bejelentkezés beállítása](add-application-portal-setup-sso.md)
- [Alkalmazás törlése](delete-application-portal.md)