---
title: A Azure Security Center teljes körű láthatósága a bérlők számára | Microsoft Docs
description: Ismerje meg, hogyan nyerheti meg a bérlői szintű láthatóságot a Azure Security Centerban.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 730ccd7c64ac9ca87fb6da5add130feb3b6ce502
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201950"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>A Azure Security Center teljes bérlőre kiterjedő láthatóságának megszerzése
Ebből a cikkből megtudhatja, hogyan kezdheti el számos olyan műveletet, amely maximalizálja a Azure Security Center nyújtotta előnyöket. Ezeknek a műveleteknek a végrehajtása lehetővé teszi, hogy megismerje a Azure Active Directory bérlőhöz kapcsolódó összes Azure-előfizetést, és hatékonyan kezelje a szervezete biztonsági helyzetét a skálán, ha biztonsági szabályzatokat alkalmaz többek között az előfizetések aggregált módon jelennek meg.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Felügyeleti csoport
Az Azure felügyeleti csoportjai hatékonyan kezelhetik a hozzáférést, a szabályzatokat és a jelentéskészítést az előfizetések csoportjaira vonatkozóan, valamint hatékonyan kezelhetik a teljes Azure-birtokot a gyökérszintű felügyeleti csoport műveleteinek végrehajtásával. Minden egyes Azure AD-bérlő egyetlen legfelső szintű felügyeleti csoportot kap, amelynek neve a gyökérszintű felügyeleti csoport. Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. Ez a csoport lehetővé teszi, hogy a globális házirendek és a RBAC-hozzárendelések a címtár szintjén legyenek alkalmazva. 

A felügyeleti csoport automatikusan létrejön, ha az alábbi műveletek bármelyikét végrehajtja: 
1. Az Azure felügyeleti csoportok használatának engedélyezése a [Azure Portal](https://portal.azure.com) **Management groups** való navigálással.
2. Hozzon létre egy felügyeleti csoportot egy API-hívás használatával.
3. Hozzon létre egy felügyeleti csoportot a PowerShell használatával.

A felügyeleti csoportok részletes áttekintését az [erőforrások rendszerezése az Azure felügyeleti csoportokkal](../azure-resource-manager/management-groups-overview.md) című cikkben találja.

## <a name="create-a-management-group-in-the-azure-portal"></a>Felügyeleti csoport létrehozása a Azure Portalban
Az előfizetéseket felügyeleti csoportokba rendezheti, és az irányítási szabályzatokat alkalmazhatja a felügyeleti csoportokra. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott szabályzatokat. Amíg a felügyeleti csoportok nem szükségesek a Security Center bevezetéséhez, erősen ajánlott legalább egy felügyeleti csoportot létrehozni, hogy a rendszer létrehozza a gyökérszintű felügyeleti csoportot. A csoport létrehozása után az Azure AD-bérlő alá tartozó összes előfizetés hozzá lesz kapcsolva. A PowerShellre és további információkra vonatkozó utasításokért lásd: [felügyeleti csoportok létrehozása az erőforrás-és szervezeti felügyelethez](../azure-resource-manager/management-groups-create.md).

 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza **a minden szolgáltatás** > **felügyeleti csoportok**lehetőséget.
3. A főoldalon válassza az **új felügyeleti csoport elemet.** 

    ![Fő csoport](./media/security-center-management-groups/main.png) 
4.  Töltse ki a felügyeleti csoport azonosítója mezőt. 
    - A **felügyeleti csoport azonosítója** a címtár egyedi azonosítója, amely a parancsok ezen a felügyeleti csoporton való elküldésére szolgál. Ez az azonosító nem szerkeszthető a létrehozás után, mivel az egész Azure-rendszeren a csoport azonosítására szolgál. 
    - A megjelenítendő név mező a Azure Portal belül megjelenő név. A felügyeleti csoport létrehozásakor egy külön megjelenítendő név nem választható mező, és bármikor módosítható.  

      ![Hozzon létre](./media/security-center-management-groups/create_context_menu.png)  
5.  Válassza ki **mentése**

### <a name="view-management-groups-in-the-azure-portal"></a>Felügyeleti csoportok megtekintése a Azure Portal
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felügyeleti csoportok megtekintéséhez válassza a **minden szolgáltatás** lehetőséget az Azure főmenüjében.
3. Az **általános**területen válassza a **Management groups**lehetőséget.

    ![Felügyeleti csoport létrehozása](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>A bérlői szintű láthatóság és a házirendek hozzárendelési képességének biztosítása

Ha szeretné megtekinteni az Azure AD-bérlőben regisztrált összes előfizetés biztonsági állapotát, a legfelső szintű felügyeleti csoportban hozzá kell rendelnie egy megfelelő olvasási engedéllyel rendelkező RBAC-szerepkört.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Globális rendszergazda hozzáférésének megemelése Azure Active Directory
Egy Azure Active Directory bérlői rendszergazdának nincs közvetlen hozzáférése az Azure-előfizetésekhez. Címtár-rendszergazdaként azonban joguk van arra, hogy egy hozzáféréssel rendelkező szerepkörhöz emeljenek. Az Azure AD-bérlői rendszergazdának a felügyeleti csoport szintjén kell megadnia magát a felhasználói hozzáférési rendszergazdának, hogy RBAC szerepköröket rendeljen hozzá. PowerShell-utasítások és további információk: a [globális rendszergazda hozzáférésének Megemelése Azure Active Directoryban](../role-based-access-control/elevate-access-global-admin.md). 


1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) vagy a [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com).

2. A navigációs listában kattintson a **Azure Active Directory** elemre, majd a **Tulajdonságok**elemre.

   ![Azure AD-tulajdonságok – képernyőfelvétel](./media/security-center-management-groups/aad-properties.png)

3. Az **Azure-erőforrások hozzáférés-kezelés**területén állítsa a kapcsolót **Igen**értékre.

   ![A globális rendszergazda felügyelheti az Azure-előfizetéseket és Management Groups – képernyőfelvétel](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Ha az Igen értékre állítja a kapcsolót, a rendszer az Azure RBAC felhasználói hozzáférés rendszergazdai szerepkörét rendeli hozzá a gyökérszintű hatókörben (/). Ez lehetővé teszi, hogy az ehhez az Azure AD-címtárhoz társított összes Azure-előfizetéshez és felügyeleti csoporthoz rendeljen szerepköröket. Ez a kapcsoló csak olyan felhasználók számára érhető el, akik a globális rendszergazdai szerepkörhöz vannak rendelve az Azure AD-ben.

   - Ha a nem értékre állítja a kapcsolót, a rendszer eltávolítja az Azure RBAC felhasználói hozzáférés rendszergazdai szerepkörét a felhasználói fiókjából. Az ehhez az Azure AD-címtárhoz társított összes Azure-előfizetéshez és felügyeleti csoporthoz már nem lehet szerepköröket hozzárendelni. Csak azokat az Azure-előfizetéseket és felügyeleti csoportokat tekintheti meg és kezelheti, amelyekhez hozzáférést kapott.

4. A beállítás mentéséhez kattintson a **Save (Mentés** ) gombra.

    - Ez a beállítás nem globális tulajdonság, és csak az aktuálisan bejelentkezett felhasználóra vonatkozik.

5. Hajtsa végre a emelt szintű hozzáféréshez szükséges feladatokat. Ha elkészült, állítsa vissza a kapcsolót a **nem**értékre.


### <a name="assign-rbac-roles-to-users"></a>RBAC-szerepkörök kiosztása a felhasználók számára
Az összes előfizetés láthatósága érdekében a bérlői rendszergazdáknak hozzá kell rendelniük a megfelelő RBAC-szerepkört minden olyan felhasználóhoz, aki a legfelső szintű felügyeleti csoport szintjén kívánja biztosítani a bérlői szintű láthatóságot. A hozzárendelni kívánt szerepkörök vagy **biztonsági rendszergazda** vagy biztonsági **olvasó**. Általában a biztonsági rendszergazdai szerepkör szükséges ahhoz, hogy a legfelső szintű házirendeket alkalmazzuk, míg a biztonsági olvasó elegendő lesz a bérlői szintű láthatóság biztosításához. A szerepkörök által biztosított engedélyekkel kapcsolatos további információkért tekintse meg a [biztonsági rendszergazda beépített szerepkörének leírását](../role-based-access-control/built-in-roles.md#security-admin) vagy a [biztonsági olvasó beépített szerepkörének leírását](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-rbac-roles-to-users-through-the-azure-portal"></a>RBAC szerepköröket rendelhet a felhasználókhoz a Azure Portalon keresztül: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. A felügyeleti csoportok megtekintéséhez válassza a **minden szolgáltatás** lehetőséget az Azure főmenüjében, majd válassza a **Management groups**lehetőséget.
1.  Válasszon ki egy felügyeleti csoportot, és kattintson a **részletek**elemre.

    ![Management Groups részletek képernyőképe](./media/security-center-management-groups/management-group-details.PNG)
 
1. Kattintson a **hozzáférés-vezérlés (iam)** , majd a **szerepkör-hozzárendelések**elemre.

1. Kattintson a **szerepkör-hozzárendelés hozzáadása**.

1. Válassza ki a hozzárendelni kívánt szerepkört és a felhasználót, majd kattintson a **Mentés**gombra.  
   
   ![Biztonsági olvasó szerepkör hozzáadása képernyőkép](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-rbac-roles-to-users-with-powershell"></a>RBAC-szerepkörök társítása a felhasználókhoz a PowerShell használatával: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Telepítse az [Azure PowerShellt](/powershell/azure/install-az-ps).
2. Futtassa az alábbi parancsot: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Ha a rendszer kéri, jelentkezzen be a globális rendszergazdai hitelesítő adatokkal. 

    ![Bejelentkezés a képernyőn](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. A következő parancs futtatásával adja meg az olvasói szerepkör engedélyeit:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. A szerepkör eltávolításához használja a következő parancsot: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Security Center megnyitása vagy frissítése
Ha emelt szintű hozzáférést, nyissa meg vagy frissítse Azure Security Center annak ellenőrzéséhez, hogy látható-e az Azure AD-bérlő összes előfizetése. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Győződjön meg arról, hogy az előfizetéshez tartozó összes előfizetést kijelöli, amelyet Security Center szeretne megtekinteni.

    ![Előfizetés-választó képernyőképe](./media/security-center-management-groups/subscription-selector.png)

1. Válassza a **minden szolgáltatás** lehetőséget az Azure főmenüjében, majd válassza a **Security Center**lehetőséget.
2. Az **áttekintésben**van egy előfizetési lefedettségi diagram.

    ![Előfizetés-lefedettségi diagram képernyőképe](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Az előfizetések listájának megtekintéséhez kattintson a **lefedettség** elemre. 

    ![Előfizetés-lefedettségi lista képernyőképe](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása 
Miután hozzárendelte a RBAC szerepköröket a felhasználókhoz, a bérlői rendszergazdának el kell távolítania magát a felhasználói hozzáférés rendszergazdai szerepkörből.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) vagy a [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com).

2. A navigációs listában kattintson a **Azure Active Directory** elemre, majd a **Tulajdonságok**elemre.

3. A **globális rendszergazda területen kezelheti az Azure-előfizetéseket és a Management groups**, a **nem**értékre állíthatja a kapcsolót.

4. A beállítás mentéséhez kattintson a **Save (Mentés** ) gombra.



## <a name="adding-subscriptions-to-a-management-groups"></a>Előfizetés hozzáadása egy felügyeleti csoporthoz
Előfizetéseket adhat hozzá a létrehozott felügyeleti csoporthoz. Ezek a lépések nem kötelezőek a bérlői szintű láthatóság és a globális házirend-és hozzáférés-kezelés beszerzéséhez.

1. Az **Management groups**területen válassza ki azt a felügyeleti csoportot, amelyhez hozzá szeretné adni az előfizetést.

    ![Válasszon ki egy felügyeleti csoportot az előfizetés hozzáadásához](./media/security-center-management-groups/management-group-subscriptions.png)

2. Válassza a **meglévő hozzáadása**elemet.

    ![Meglévő hozzáadása](./media/security-center-management-groups/add-existing.png)

3. Adja meg az előfizetést a **meglévő erőforrás hozzáadása** területen, majd kattintson a **Mentés**gombra.

4. Ismételje meg az 1 – 3. lépést, amíg hozzá nem adta az összes előfizetést a hatókörben.

   > [!NOTE]
   > A felügyeleti csoportok előfizetéseket és alárendelt felügyeleti csoportokat is tartalmazhatnak. Amikor RBAC-szerepkört rendel a fölérendelt felügyeleti csoporthoz, a gyermek-felügyeleti csoport előfizetései öröklik a hozzáférést. A szülő felügyeleti csoportban beállított házirendeket a gyermekek is öröklik. 

## <a name="next-steps"></a>További lépések
Ebből a cikkből megtudhatta, hogyan szerezhet Azure Security Center bérlői szintű láthatóságot. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

> [!div class="nextstepaction"]
> [Biztonsági állapot monitorozása az Azure Security Centerben](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Biztonsági riasztások kezelése és reagálás Azure Security Center](security-center-managing-and-responding-alerts.md)

