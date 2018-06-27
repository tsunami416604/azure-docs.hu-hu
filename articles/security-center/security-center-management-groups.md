---
title: Hogy bérlői szintű lássák az Azure Security Centerben |} Microsoft Docs
description: További tudnivalók az Azure Security Center bérlői szintű láthatóságot eszközöket.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: terrylan
ms.openlocfilehash: 2c95b06ce34b850d1bfaf60e47d6e5fede148a38
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025815"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Hogy bérlői szintű lássák az Azure Security Centerben
Ez a cikk segít első lépések végrehajtásával néhány művelet, amelyet a következő előnyöket biztosítja az Azure Security Center maximalizálása érdekében. A műveletek végrehajtása lehetővé teszi a számára, hogy az összes Azure-előfizetések az Azure Active Directory-bérlő és hatékonyan kapcsolt kezelése lássák a szervezet biztonsági előírások léptékű több alkalmazza a biztonsági házirendek előfizetések aggregative módon.

## <a name="management-groups"></a>Felügyeleti csoportok
Az Azure felügyeleti csoportok lehetővé teszik a hatékonyan tudja kezelni a hozzáférést, a házirendek és a csoportok előfizetések jelentéskészítést, valamint a teljes Azure-erőforrások hatékony kezeléséhez műveletek végrehajtása a legfelső szintű felügyeleti csoportra vonatkozóan. Minden Azure AD-bérlő kap a legfelső szintű felügyeleti csoport egyetlen legfelső szintű felügyeleti csoportjában. A gyökérszintű felügyeleti csoport összeállítása a hierarchiába, hogy az összes felügyeleti csoportot, és előfizetések részekre bontani hozzá. Ez a csoport lehetővé teszi a globális házirendek és a könyvtár szinten alkalmazható RBAC-hozzárendelések. 

A gyökérszintű felügyeleti csoport automatikusan létrejön, ha így tesz, az alábbi műveletek egyikét: 
1. Navigáljon az Azure felügyeleti csoportok használata részt **felügyeleti csoportok** a a [Azure-portálon](https://portal.azure.com).
2. Hozzon létre egy API-hívás a felügyeleti csoporthoz.
3. A felügyeleti csoport létrehozása a PowerShell használatával.

A felügyeleti csoportok részletes áttekintése, tekintse meg a [rendezheti az erőforrásokat az Azure felügyeleti csoportok](../azure-resource-manager/management-groups-overview.md) cikk.

## <a name="create-a-management-group-in-the-azure-portal"></a>Hozzon létre egy felügyeleti csoportot az Azure-portálon
Előfizetések felügyeleti csoportokba rendezheti és a felügyeleti csoportok a cégirányítási házirendeket alkalmazhat. A felügyeleti csoporton belüli összes előfizetést a felügyeleti csoportra alkalmazott házirendek automatikusan öröklik. Amíg a felügyeleti csoportok nem kell bevezetni a Security Center, ajánlott legalább egy felügyeleti csoportot hoz létre, a legfelső szintű felügyeleti csoport jön létre. A csoport létrehozása után alatt az Azure AD-bérlő minden előfizetés csatolva lesz azt. Útmutatás a PowerShell és a további információk: [erőforrás és a szervezet felügyeleti felügyeleti csoportok létrehozása a](../azure-resource-manager/management-groups-create.md).

 
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.
3. Fő lapján, válassza ki a **új felügyeleti csoportban.** 

    ![Fő csoport](./media/security-center-management-groups/main.png) 
4.  Töltse ki a felügyeleti csoport azonosítója mezőt. 
    - A **felügyeleti csoport azonosítója** directory egyedi azonosítója, amely a felügyeleti csoportra parancsok továbbítására szolgál. Ez az azonosító nem szerkeszthető létrehozása után, mivel a Azure rendszerben ez a csoport azonosítására használt. 
    - A megjelenítési név mezője az Azure portálon megjelenő nevét. Egy külön megjelenített név mező kitöltése nem kötelező történő létrehozásakor a felügyeleti csoportban, és bármikor módosíthatók.  

      ![Létrehozás](./media/security-center-management-groups/create_context_menu.png)  
5.  Válassza ki **mentése**

### <a name="view-management-groups-in-the-azure-portal"></a>Felügyeleti csoportok megtekintése az Azure-portálon
1. Jelentkezzen be a [Azure-portálon](http://portal.azure.com).
2. Válassza ki, ha felügyeleti csoportok **minden szolgáltatás** az Azure fő menüjében.
3. A **általános**, jelölje be **felügyeleti csoportok**.

    ![Hozzon létre egy felügyeleti csoportot](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Elérhető bérlő szintű láthatóságot és tud rendelni házirendek

Láthatósági feltölti az Azure AD-bérlő regisztrált összes előfizetést a biztonsági állapotát, RBAC szerepet elegendő olvasási engedély is szükséges rendelhetők hozzá a legfelső szintű felügyeleti csoportra vonatkozóan.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Egy globális rendszergazda az Azure Active Directoryban jogosultságszintjének emelése
Egy Azure Active Directory Bérlői rendszergazda nem rendelkezik Azure-előfizetések közvetlen hozzáféréssel. Azonban directory rendszergazdaként jogosultak megszerzését maguk hozzáféréssel rendelkező szerepkörhöz. Egy Azure AD bérlői rendszergazdának kell jogosultságszint-emelés magát a felhasználói hozzáférés adminisztrátora a legfelső szintű felügyeleti csoportok szintjén, rendeljen RBAC-szerepkörök. PowerShell-utasításokat és további információkat lásd: [jogosultságszintjének emelése egy globális rendszergazda az Azure Active Directoryban](../role-based-access-control/elevate-access-global-admin.md). 


1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) vagy a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com).

2. Kattintson a navigációs lista **Azure Active Directory** majd **tulajdonságok**.

   ![Az Azure AD tulajdonságai – képernyőkép](./media/security-center-management-groups/aad-properties.png)

3. A **globális rendszergazdák által kezelhető, Azure-előfizetések és a felügyeleti csoportok**, a kapcsoló beállítása **Igen**.

   ![Globális rendszergazdák által kezelhető, Azure-előfizetések és a felügyeleti csoport – képernyőkép](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Ha beállította a kapcsoló **Igen**, a globális rendszergazdai fiókjával (jelenleg bejelentkezett felhasználó) kerül a felhasználói hozzáférés adminisztrátora szerepkört az Azure RBAC a gyökérszintű hatókörben (`/`), mely biztosít hozzáférést nézet és a jelentés a az összes Azure-előfizetések az Azure AD bérlőhöz társított.

   - Ha beállította a kapcsoló **nem**, a globális rendszergazdai fiókjával (jelenleg bejelentkezett felhasználó) a felhasználói hozzáférés adminisztrátora szerepkört az Azure RBAC törlődik. Az Azure AD-bérlő társított összes Azure előfizetések nem jelennek meg, és megtekintheti, és csak az Azure-előfizetések kezeléséhez, amelyhez Ön hozzáférést kapott.

4. Kattintson a **mentése** a beállítás mentéséhez.

    - Ez a beállítás nem globális tulajdonság, ezért csak a bejelentkezett felhasználó vonatkozik.

5. Az emelt szintű hozzáférés szükséges feladatokat végezhet. Amikor elkészült, a kapcsoló állítsa vissza a **nem**.

### <a name="assign-rbac-roles-to-users"></a>A felhasználók számára az RBAC szerepkörök hozzárendelése
A bérlői rendszergazda emelt szintű hozzáférés, amennyiben azok RBAC szerepet a felügyeleti csoport gyökérszintű vonatkozó felhasználóit is felruházhatja. Az ajánlott szerepkör hozzárendelése [ **olvasó**](../role-based-access-control/built-in-roles.md#reader). Ez a szerepkör szükséges a bérlői szintű láthatóságot biztosít. A hozzárendelt szerepkör automatikusan propagálódik az összes felügyeleti csoportok és előfizetések a legfelső szintű felügyeleti csoportban. Az RBAC-szerepkörök kapcsolatos további információkért lásd: [elérhető szerepkörök](../active-directory/active-directory-assign-admin-roles-azure-portal.md#available-roles).

1. Telepítse az [Azure PowerShellt](/powershell/azure/install-azurerm-ps).
2. Futtassa az alábbi parancsot: 

    ```azurepowershell
    # Install Management Groups Powershell module
    Install-Module AzureRM.Resources
    
    # Login to Azure as a Global Administrator user
    Login-AzureRmAccount
    ```

3. Amikor a rendszer kéri, jelentkezzen be globális rendszergazda hitelesítő adataival. 

    ![Jelentkezzen be Rákérdezés képernyőképe](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Engedélyek olvasó szerepkört a következő parancs futtatásával:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. A szerepkör eltávolításához használja a következő parancsot: 

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

<!-- Currently, PowerShell method only 6/26/18

1. Sign in to the [Azure portal](https://portal.azure.com). 
2. To view management groups, select **All services** under the Azure main menu then select **Management Groups**.
3.  Select a management group and click **details**.

    ![Management Groups details screenshot](./media/security-center-management-groups/management-group-details.PNG)
 
4. Click **Access control (IAM)** then **Add**.
5. Select the role to assign and the user, then click **Save**.  
   
   ![Add Security Reader role screenshot](./media/security-center-management-groups/asc-security-reader.png)
-->

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés 
Miután az RBAC-szerepkörök a felhasználók vannak rendelve, a bérlői rendszergazda távolítsa el magát a felhasználói hozzáférés rendszergazda szerepkörből.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) vagy a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com).

2. Kattintson a navigációs lista **Azure Active Directory** majd **tulajdonságok**.

3. A **globális rendszergazdák által kezelhető, Azure-előfizetések és a felügyeleti csoportok**, a kapcsoló beállítása **nem**.

4. Kattintson a **mentése** a beállítás mentéséhez.

### <a name="open-or-refresh-security-center"></a>Nyissa meg, vagy frissítse a biztonsági központ
Miután hozzárendelte a RBAC-szerepkörök, nyissa meg, vagy frissítse az Azure Security Center alatt az Azure AD-bérlő rendelkezik-e minden előfizetés láthatósága. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Válassza ki **minden szolgáltatás** az Azure fő menüjében válassza **Security Center**.
3. Az a **áttekintése**, előfizetés érvényességének diagram. 
    ![Előfizetés érvényességének diagram képernyőképe](./media/security-center-management-groups/security-center-subscription-coverage.png)
4. Kattintson a **érvényességének** hogy tartozó előfizetések listájából. 
    ![Előfizetés érvényességének lista képernyőképe](./media/security-center-management-groups/security-center-coverage.png)

## <a name="adding-subscriptions-to-a-management-groups"></a>Előfizetések hozzáadása egy felügyeleti csoporthoz
A létrehozott felügyeleti csoportot előfizetések adhat hozzá. Ezeket a lépéseket nem kötelező való bérlői szintű láthatóságot és globális házirend- és hozzáférés-kezelés.

1. A **felügyeleti csoportok**, válassza ki az előfizetés felvételéhez a felügyeleti csoportot.

    ![Válassza ki az előfizetés felvételéhez a felügyeleti csoport](./media/security-center-management-groups/management-group-subscriptions.png)

2. Válassza ki **hozzáadása a meglévő**.

    ![Meglévő hozzáadása](./media/security-center-management-groups/add-existing.png)

3. Adja meg azt az előfizetést **hozzáadása a meglévő erőforrás** kattintson **mentése**.

4. Ismételje 1 – 3 felvett összes előfizetést a hatókörben.

 > [!NOTE]
 > Felügyeleti csoportok tartalmazhatnak előfizetések és a felügyeleti csoportokat. Rendel egy felhasználói RBAC szerepet a fölérendelt felügyeleti csoporthoz, a gyermek felügyeleti csoport előfizetések örökli a hozzáférést. A gyermekek is örökli a szülő felügyeleti csoport beállított házirendek. 

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan számára, hogy az Azure Security Center bérlői szintű lássák. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

> [!div class="nextstepaction"]
> [Biztonsági állapot monitorozása az Azure Security Centerben](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)

