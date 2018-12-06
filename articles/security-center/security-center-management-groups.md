---
title: Összes bérlőre vonatkozó információk megjelenítése az Azure Security Centerben |} A Microsoft Docs
description: Ismerje meg a videókban rejlő információk a bérlői szintű láthatóság az Azure Security Centerben.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2018
ms.author: rkarlin
ms.openlocfilehash: cb70c99d56cb1d09e561a44a90fd6c007ea9b59f
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52964117"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Összes bérlőre vonatkozó információk megjelenítése az Azure Security Centerben
Ez a cikk használatának megkezdésébe vezeti be, amely az Azure Security Center biztosít előnyök maximalizálása és több művelet végrehajtásával. Ezek a műveletek végrehajtása lehetővé teszi, hogy nagyobb láthatóságot érhet el az összes Azure-előfizetések vannak kapcsolva, az Azure Active Directory-bérlőhöz, és hatékonyan kezelheti a szervezet biztonsági állapotáról, ipari méretekben több biztonsági házirendek alkalmazásával előfizetések aggregative módon.

## <a name="management-groups"></a>Felügyeleti csoportok
Az Azure felügyeleti csoportok lehetővé teszi, hogy hatékonyan kezelheti a hozzáférést, a házirendek és a csoportok előfizetések-jelentések, valamint hatékonyan kezelheti a teljes Azure hagyatéki műveletek végrehajtásával a legfelső szintű felügyeleti csoporton. Minden egyes Azure AD-bérlőt kap egy legfelső szintű felügyeleti csoport gyökérszintű felügyeleti csoport neve. Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. Ez a csoport lehetővé teszi a globális szabályzatok és az RBAC-hozzárendeléseket a könyvtár szintjén alkalmazható. 

A gyökérszintű felügyeleti csoport automatikusan létrejön, amikor ezt teszi, hogy az alábbi műveletek egyikét: 
1. Az Azure felügyeleti csoportok használata **felügyeleti csoportok** a a [az Azure portal](https://portal.azure.com).
2. Hozzon létre egy API-hívás egy felügyeleti csoporthoz.
3. A felügyeleti csoport létrehozása a PowerShell használatával.

Felügyeleti csoportok részletes ismertetőt talál a [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](../azure-resource-manager/management-groups-overview.md) cikk.

## <a name="create-a-management-group-in-the-azure-portal"></a>A felügyeleti csoport létrehozása az Azure Portalon
Előfizetések felügyeleti csoportokba való rendszerezése és a alkalmazni a cégirányítási szabályzatokat a felügyeleti csoportok. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott szabályzatokat. Míg a felügyeleti csoportok nem szükséges felvétele a Security centerhez, azt javasoljuk, hogy legalább egy felügyeleti csoportot hoz létre, így a gyökérszintű felügyeleti csoport jön létre. A csoport létrehozása után a rendszer hozzá kell kapcsolni az Azure AD-bérlőhöz tartozó összes előfizetést. Útmutatás a PowerShell és a további információk: [-erőforrás és a szervezet felügyeleti csoportok létrehozása](../azure-resource-manager/management-groups-create.md).

 
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.
3. A fő oldalon válassza ki a **új felügyeleti csoportban.** 

    ![Fő csoport](./media/security-center-management-groups/main.png) 
4.  Adja meg a felügyeleti csoport azonosító mező. 
    - A **felügyeleti csoport azonosítója** directory egyedi azonosítója, amellyel küldje el a felügyeleti csoportra parancsokat. Ez az azonosító nem szerkeszthető a létrehozása után, mivel a Azure rendszerben ez a csoport azonosításához használt. 
    - A megjelenített név mezőjének Ez a név jelenik meg az Azure Portalon. Egy külön megjelenített név mező kitöltése nem kötelező, ha a felügyeleti csoportot, és bármikor módosítható.  

      ![Létrehozás](./media/security-center-management-groups/create_context_menu.png)  
5.  Válassza ki **mentése**

### <a name="view-management-groups-in-the-azure-portal"></a>Felügyeleti csoportok megtekintése az Azure Portalon
1. Jelentkezzen be a [az Azure portal](http://portal.azure.com).
2. Felügyeleti csoportok megtekintéséhez jelölje ki **minden szolgáltatás** az Azure főmenüjében.
3. A **általános**válassza **felügyeleti csoportok**.

    ![A felügyeleti csoport létrehozása](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Adja meg a bérlői szintű láthatóság, és lehetővé teszi szabályzatok hozzárendelése

Betekintést nyerhet az Azure AD-bérlő regisztrálva az összes előfizetés biztonsági állapotát, hogy az RBAC szerepkör megfelelő olvasási engedélyekkel rendelkező szükség a legfelső szintű felügyeleti csoport hozzárendelni.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Az Azure Active Directoryban egy globális rendszergazda hozzáférési szintjének emelése
Az Azure Active Directory-Bérlői rendszergazda nem rendelkezik Azure-előfizetések közvetlen hozzáférést. Azonban directory-rendszergazdák jogosultak megszereznie magukat egy szerepkör, amely hozzáfér. Azure AD-Bérlői rendszergazda kell megszereznie magát a legfelső szintű felügyeleti csoport szintjén a felhasználói hozzáférés rendszergazdája így rendeljen RBAC-szerepkörök. PowerShell-utasítások és a további tudnivalókat lásd: [az Azure Active Directoryban egy globális rendszergazda hozzáférési szintjének emelése](../role-based-access-control/elevate-access-global-admin.md). 


1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) vagy a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com).

2. Kattintson a navigációs lista **Azure Active Directory** majd **tulajdonságok**.

   ![Az Azure AD tulajdonságok – képernyőkép](./media/security-center-management-groups/aad-properties.png)

3. A **globális rendszergazda kezelheti az Azure-előfizetéseket és a felügyeleti csoportok**, a kapcsoló beállítása **Igen**.

   ![Globális rendszergazda kezelheti az Azure-előfizetéseket és a felügyeleti csoport – képernyőkép](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Ha beállította a kapcsoló **Igen**, a globális rendszergazdai fiókjával (jelenleg bejelentkezett felhasználó) kerül a felhasználói hozzáférés rendszergazdájának szerepköre az Azure RBAC a gyökérszintű hatókörben (`/`), mely engedélyezi a nézet és a jelentés eléréséhez az összes Azure-előfizetések az Azure AD-bérlőhöz társított.

   - Ha beállította a kapcsoló **nem**, a globális rendszergazdai fiókjával (jelenleg bejelentkezett felhasználó) eltávolítják az Azure RBAC-ben a felhasználói hozzáférés rendszergazdája szerepkör. Az összes Azure-előfizetések az Azure AD-bérlőhöz társított nem fogja látni, és megtekintheti, és csak az Azure-előfizetések kezelése, amelyhez rendelkezik hozzáféréssel.

4. Kattintson a **mentése** a beállítás mentéséhez.

    - Ez a beállítás egy globális tulajdonság nem, és csak a bejelentkezett felhasználó vonatkozik.

5. A végre kell hajtania az emelt szintű hozzáférési feladatok elvégzéséhez. Ha elkészült, állítsa a kapcsolót vissza **nem**.


### <a name="assign-rbac-roles-to-users"></a>RBAC-szerepkörök hozzárendelése felhasználókhoz
Az információk megjelenítése az összes előfizetés, a bérlői rendszergazdák kell a megfelelő RBAC-szerepkörök hozzárendelése egy felhasználóhoz sem kívánnak biztosítanak bérlői szintű láthatóságot, beleértve a maguk a legfelső szintű felügyeleti csoport szintjén. A javasolt szerepkör hozzárendelése a vagy **biztonsági rendszergazda** vagy **biztonsági olvasó**. Általában a biztonsági rendszergazdai szerepkör szükség a gyökérszintű, a szabályzatok alkalmazása, miközben a biztonsági olvasó adja meg a bérlői szintű látható-e elegendő. A szerepkörökhöz rendelt engedélyeket kapcsolatos további információkért lásd: a [biztonsági rendszergazdai szerepkört leírás](../role-based-access-control/built-in-roles.md#security-admin) vagy a [biztonsági olvasó beépített szerepkör leírása](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-rbac-roles-to-users-through-the-azure-portal"></a>Rendeljen RBAC-szerepköröket a felhasználók számára az Azure Portalon: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Felügyeleti csoportok megtekintéséhez jelölje ki **minden szolgáltatás** az Azure főmenüjében válassza **felügyeleti csoportok**.
1.  Válasszon ki egy felügyeleti csoportot, és kattintson a **részletek**.

    ![Felügyeleti csoportok részletek képernyőképe](./media/security-center-management-groups/management-group-details.PNG)
 
1. Kattintson a **hozzáférés-vezérlés (IAM)** majd **szerepkör-hozzárendelések**.

1. Kattintson a **szerepkör-hozzárendelés hozzáadása**.

1. Válassza ki a szerepkör hozzárendelése és a felhasználót, majd kattintson a **mentése**.  
   
   ![Biztonsági olvasó szerepkör képernyőfelvétel hozzáadása](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-rbac-roles-to-users-with-powershell"></a>Rendeljen RBAC-szerepköröket a felhasználók számára a PowerShell-lel: 
1. Telepítse az [Azure PowerShellt](/powershell/azure/install-azurerm-ps).
2. Futtassa az alábbi parancsot: 

    ```azurepowershell
    # Install Management Groups Powershell module
    Install-Module AzureRM.Resources
    
    # Login to Azure as a Global Administrator user
    Login-AzureRmAccount
    ```

3. Amikor a rendszer kéri, jelentkezzen be globális rendszergazdai hitelesítő adataival. 

    ![Jelentkezzen be a parancssorba képernyőképe](./media/security-center-management-groups/azurerm-sign-in.PNG)

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

### <a name="open-or-refresh-security-center"></a>Nyissa meg, vagy frissítse a Security Centerben
Ha emelt hozzáférési szintű, nyissa meg, vagy frissítse az Azure Security Center használatával ellenőrizze, hogy az összes előfizetés betekintést az Azure AD-bérlőhöz tartozik. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Ellenőrizze, hogy az előfizetés-választó, amely megtekintése a Security Centerben szeretné előfizetések lehetőséget választja.
    ![Előfizetés-választó képernyőképe](./media/security-center-management-groups/subscription-selector.png)
1. Válassza ki **minden szolgáltatás** az Azure főmenüjében válassza **a Security Center**.
2. Az a **áttekintése**, van egy előfizetési lefedettség diagramot. 
    ![Előfizetési lefedettség diagram képernyőképe](./media/security-center-management-groups/security-center-subscription-coverage.png)
3. Kattintson a **lefedettség** kezelt előfizetések listájának megtekintéséhez. 
    ![Előfizetési lefedettség lista képernyőképe](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása 
Az RBAC-szerepkörök vannak rendelve a felhasználókat, miután a bérlői rendszergazda távolítsa el magát az a felhasználói hozzáférés rendszergazdájának szerepköre.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) vagy a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com).

2. Kattintson a navigációs lista **Azure Active Directory** majd **tulajdonságok**.

3. A **globális rendszergazda kezelheti az Azure-előfizetéseket és a felügyeleti csoportok**, a kapcsoló beállítása **nem**.

4. Kattintson a **mentése** a beállítás mentéséhez.



## <a name="adding-subscriptions-to-a-management-groups"></a>Előfizetések hozzáadása a felügyeleti csoportokhoz
A felügyeleti csoport létrehozott előfizetések adhat hozzá. Ezeket a lépéseket nem kötelező a bérlői szintű láthatóság és a globális szabályzat és a hozzáférés-kezelés.

1. A **felügyeleti csoportok**, válassza ki az előfizetés felvételéhez a felügyeleti csoport.

    ![Válassza ki az előfizetés felvételéhez a felügyeleti csoport](./media/security-center-management-groups/management-group-subscriptions.png)

2. Válassza ki **hozzáadása meglévő**.

    ![Meglévő hozzáadása](./media/security-center-management-groups/add-existing.png)

3. Adja meg az előfizetés alatt **hozzáadása meglévő erőforrás** kattintson **mentése**.

4. Ismételje 1 – 3 hozzáadott előfizetések hatókörében.

 > [!NOTE]
 > Felügyeleti csoportok előfizetések és az alárendelt felügyeleti csoportok is tartalmazhatnak. Amikor hozzárendel egy felhasználó egy RBAC szerepkör a fölérendelt felügyeleti csoport, a hozzáférés az alárendelt felügyeleti csoportot az előfizetések örökli. A gyermekek is örökli szülő felügyeleti csoportok beállított szabályzatoknak. 

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan figyelheti az Azure Security Center a bérlői szintű. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

> [!div class="nextstepaction"]
> [Biztonsági állapot monitorozása az Azure Security Centerben](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)

