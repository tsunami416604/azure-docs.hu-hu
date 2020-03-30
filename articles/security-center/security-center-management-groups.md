---
title: Az Azure Security Center bérlői szintű láthatóságának erősítése | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan kezelheti a biztonsági testtartás nagy méretekben szabályzatok az Azure Active Directory-bérlőhöz kapcsolódó összes előfizetések.
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
ms.openlocfilehash: 734876380d22f5d4d6dae0dd375b238fd5f6ffed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74559346"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Az Azure Security Center bérlői szintű láthatósága
Ez a cikk bemutatja, hogyan kezelheti a szervezet biztonsági állapotát nagy méretekben az Azure Active Directory-bérlőhöz kapcsolódó összes Azure-előfizetésre vonatkozó biztonsági szabályzatok alkalmazásával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Felügyeleti csoportok
Az Azure felügyeleti csoportok lehetővé teszik a hozzáférés, a szabályzatok és az előfizetések csoportjairól szóló jelentések hatékony kezelését, valamint a teljes Azure-hagyaték hatékony kezelését a gyökérfelügyeleti csoporton végrehajtott műveletek végrehajtásával. Minden Azure AD-bérlő kap egy legfelső szintű felügyeleti csoportot, amelyet a legfelső szintű felügyeleti csoportnak neveznek. Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. Ez a csoport lehetővé teszi a globális házirendek és RBAC-hozzárendelések könyvtárszinten történő alkalmazását. 

A gyökérfelügyeleti csoport automatikusan létrejön, ha az alábbi műveletek bármelyikét végrehajtja: 
1. Az Azure felügyeleti csoportok használatát az Azure [Portalfelügyeleti](https://portal.azure.com) **csoportokra** való navigálással engedélyezheti.
2. Hozzon létre egy felügyeleti csoportot egy API-híváson keresztül.
3. Hozzon létre egy felügyeleti csoportot a PowerShell használatával.

A felügyeleti csoportok részletes áttekintését az [Erőforrások rendszerezése az Azure felügyeleti csoportokhasználatával](../azure-resource-manager/management-groups-overview.md) című cikkben találja.

## <a name="create-a-management-group-in-the-azure-portal"></a>Felügyeleti csoport létrehozása az Azure Portalon
Az előfizetéseket felügyeleti csoportokba rendezheti, és alkalmazhatja a cégirányítási szabályzatokat a felügyeleti csoportokra. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott szabályzatokat. Bár a felügyeleti csoportok nem szükséges a Security Center fedélzeti, erősen ajánlott, hogy hozzon létre legalább egy felügyeleti csoportot, így a legfelső szintű felügyeleti csoport jön létre. A csoport létrehozása után az Azure AD-bérlő n y-bérlő összes előfizetése hozzá lesz kapcsolva. A PowerShell-re vonatkozó útmutatásért és további információkért olvassa [el a Felügyeleti csoportok létrehozása erőforrás- és szervezetkezeléshez című témakört.](../azure-resource-manager/management-groups-create.md)

 
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza az **Összes szolgáltatásfelügyeleti** > **csoport lehetőséget.**
3. A főlapon válassza az **Új kezelés csoport lehetőséget.** 

    ![Fő csoport](./media/security-center-management-groups/main.png) 
4.  Töltse ki a felügyeleti csoport azonosító mezőjét. 
    - A **felügyeleti csoport azonosítója** az a könyvtáregyedi azonosító, amely a felügyeleti csoport parancsainak elküldésére szolgál. Ez az azonosító létrehozása után nem szerkeszthető, mivel az Azure-rendszerben használják a csoport azonosítására. 
    - A megjelenítendő név mező az Azure Portalon megjelenő név. A külön megjelenítendő név a felügyeleti csoport létrehozásakor nem kötelező mező, amely bármikor módosítható.  

      ![Létrehozás](./media/security-center-management-groups/create_context_menu.png)  
5.  **Mentés** kiválasztása

### <a name="view-management-groups-in-the-azure-portal"></a>Felügyeleti csoportok megtekintése az Azure Portalon
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Felügyeleti csoportok megtekintéséhez válassza az Azure **főmenüjében** minden szolgáltatás.
3. Az **Általános csoportban**válassza a **Felügyeleti csoportok lehetőséget.**

    ![Felügyeleti csoport létrehozása](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Bérlői szintű láthatóság és házirendek hozzárendelésének lehetősége

Az Azure AD-bérlőben regisztrált összes előfizetés biztonsági állapotának láthatósága érdekében a gyökérfelügyeleti csoporthoz megfelelő olvasási engedélyekkel rendelkező RBAC-szerepkörszükséges.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Hozzáférés magasabb szintű ítése az Azure Active Directoryban
Az Azure Active Directory-bérlői rendszergazda nem rendelkezik közvetlen hozzáféréssel az Azure-előfizetésekhez. Címtár-rendszergazdaként azonban joguk van olyan szerepkörre emelni magukat, amely nem rendelkezik hozzáféréssel. Az Azure AD-bérlői rendszergazdának a gyökérfelügyeleti csoport szintjén kell a felhasználói hozzáférés rendszergazdájának emelkednie, hogy RBAC-szerepkörökhöz rendelhessenek. A PowerShell-utasításokért és további információkért olvassa el [a Hozzáférés elévülése globális rendszergazda számára az Azure Active Directoryban című témakört.](../role-based-access-control/elevate-access-global-admin.md) 


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) vagy az [Azure Active Directory felügyeleti központjába.](https://aad.portal.azure.com)

2. A navigációs listában kattintson az **Azure Active Directory** elemre, majd a **Tulajdonságok**gombra.

   ![Az Azure AD tulajdonságai – képernyőkép](./media/security-center-management-groups/aad-properties.png)

3. Az **Azure-erőforrások Hozzáférés-kezelése csoportban**állítsa a kapcsolót **Igen**beállításra.

   ![A globális rendszergazda kezelheti az Azure-előfizetéseket és felügyeleti csoportokat – képernyőkép](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Ha a kapcsolót Igen értékre állítja, a felhasználói hozzáférés-rendszergazdai szerepkör az Azure RBAC-ban van hozzárendelve a gyökérhatókörben (/). Ez engedélyt ad szerepkörök hozzárendelésére az Azure AD-címtárhoz társított összes Azure-előfizetésben és felügyeleti csoportban. Ez a kapcsoló csak azok nak érhető el, akik az Azure AD globális rendszergazdai szerepkörrel vannak elrendelve.

   - Ha a kapcsolót Nem, a felhasználói hozzáférés rendszergazda szerepkör az Azure RBAC törlődik a felhasználói fiókból. Az Azure AD-címtárhoz társított összes Azure-előfizetésben és felügyeleti csoportban már nem rendelhet hozzá szerepköröket. Csak azokat az Azure-előfizetéseket és felügyeleti csoportokat tekintheti meg és kezelheti, amelyekhez hozzáférést kapott.

4. A beállítás mentéséhez kattintson a **Mentés** gombra.

    - Ez a beállítás nem globális tulajdonság, és csak az aktuálisan bejelentkezett felhasználóra vonatkozik.

5. Végezze el a szükséges feladatokat a emelt szintű hozzáféréssel. Ha elkészült, állítsa vissza a kapcsolót **Nem**beállításra.


### <a name="assign-rbac-roles-to-users"></a>RBAC-szerepkörök hozzárendelése a felhasználókhoz
Ahhoz, hogy minden előfizetés láthatóvá, a bérlői rendszergazdáknak hozzá kell rendelniük a megfelelő RBAC szerepkört minden olyan felhasználóhoz, aki bérlői szintű láthatóságot szeretne biztosítani, beleértve magukat is, a gyökérfelügyeleti csoport szintjén. A hozzárendelés ajánlott szerepkörök a **Biztonsági rendszergazda** vagy a **Biztonsági olvasó**. Általában a biztonsági rendszergazdai szerepkör szükséges a házirendek a gyökérszinten, míg a Biztonsági olvasó elegendő a bérlői szintű láthatóság biztosításához. A szerepkörök által megadott engedélyekről a [Biztonsági rendszergazda beépített szerepkör-leírásában](../role-based-access-control/built-in-roles.md#security-admin) vagy a Biztonsági olvasó beépített [szerepkör-leírásában](../role-based-access-control/built-in-roles.md#security-reader)talál további információt.


#### <a name="assign-rbac-roles-to-users-through-the-azure-portal"></a>RBAC-szerepkörök hozzárendelése a felhasználókhoz az Azure Portalon keresztül: 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 
1. A felügyeleti csoportok megtekintéséhez válassza az Azure **főmenüjében** a Minden szolgáltatás lehetőséget, majd válassza a **Felügyeleti csoportok lehetőséget.**
1.  Jelöljön ki egy felügyeleti csoportot, és kattintson a **részletekre.**

    ![Felügyeleti csoportok részletei képernyőkép](./media/security-center-management-groups/management-group-details.PNG)
 
1. Kattintson **a Hozzáférés-vezérlés (IAM)** elemre, majd **a szerepkör-hozzárendelések parancsra.**

1. Kattintson **a Szerepkör-hozzárendelés hozzáadása gombra.**

1. Jelölje ki a hozzárendelni kívánt szerepkört és a felhasználót, majd kattintson a **Mentés gombra.**  
   
   ![Biztonsági olvasó szerepkör hozzáadása képernyőkép](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-rbac-roles-to-users-with-powershell"></a>RBAC-szerepkörök hozzárendelése a Felhasználókhoz a PowerShell használatával: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Telepítse [az Azure PowerShellt.](/powershell/azure/install-az-ps)
2. Futtassa az alábbi parancsot: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Amikor a rendszer kéri, jelentkezzen be globális rendszergazdai hitelesítő adatokkal. 

    ![Bejelentkezési kérdés képernyőképe](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Az olvasói szerepkör-engedélyek megadása a következő parancs futtatásával:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. A szerepkör eltávolításához használja a következő parancsot: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Biztonsági központ megnyitása vagy frissítése
Miután emelt szintű hozzáféréssel rendelkezik, nyissa meg vagy frissítse az Azure Security Centert, hogy ellenőrizze, hogy az Azure AD-bérlő alatt minden előfizetést láthatja-e. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 
2. Győződjön meg arról, hogy az összes előfizetést kikell választania az előfizetésválasztóban, amelyet meg szeretne tekinteni a Security Centerben.

    ![Előfizetés-választó képernyőképe](./media/security-center-management-groups/subscription-selector.png)

1. Válassza az Azure főmenüjében az **Összes szolgáltatás** lehetőséget, majd válassza a **Security Center**lehetőséget.
2. Az **Áttekintés alkalmazásban**van egy előfizetési lefedettségi diagram.

    ![Előfizetési lefedettségi diagram képernyőképe](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Kattintson a **Lefedettség** gombra az érintett előfizetések listájának megtekintéséhez. 

    ![Előfizetési lefedettségi lista képernyőképe](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása 
Miután az RBAC-szerepkörök hozzá lettek rendelve a felhasználókhoz, a bérlői rendszergazdának el kell távolítania magát a felhasználói hozzáférés rendszergazdai szerepköréből.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) vagy az [Azure Active Directory felügyeleti központjába.](https://aad.portal.azure.com)

2. A navigációs listában kattintson az **Azure Active Directory** elemre, majd a **Tulajdonságok**gombra.

3. A Globális rendszergazda csoportban kezelheti az **Azure-előfizetéseket és felügyeleti csoportokat,** állítsa a kapcsolót **Nem**beállításra.

4. A beállítás mentéséhez kattintson a **Mentés** gombra.



## <a name="adding-subscriptions-to-a-management-group"></a>Előfizetések hozzáadása felügyeleti csoporthoz
Előfizetések a létrehozott felügyeleti csoporthoz. Ezek a lépések nem kötelezőak a bérlői szintű láthatóság és a globális házirend- és hozzáférés-kezelés megszerzéséhez.

1. A **Felügyeleti csoportok csoportban**válassza ki azt a felügyeleti csoportot, amelyhez hozzá szeretné adni az előfizetését.

    ![Válassza ki azt a felügyeleti csoportot, amelyhez előfizetést szeretne hozzáadni](./media/security-center-management-groups/management-group-subscriptions.png)

2. Válassza **a Meglévő hozzáadása lehetőséget.**

    ![Meglévő hozzáadása](./media/security-center-management-groups/add-existing.png)

3. Írja be az előfizetést **a Meglévő erőforrás hozzáadása csoportba,** és kattintson a Mentés **gombra.**

4. Ismételje meg az 1–3.

   > [!NOTE]
   > A felügyeleti csoportok előfizetéseket és gyermekfelügyeleti csoportokat is tartalmazhatnak. Amikor rbac-szerepkört rendel egy felhasználóhoz a fölérendelt felügyeleti csoporthoz, a hozzáférést a gyermekfelügyeleti csoport előfizetései öröklik. A szülőfelügyeleti csoportban beállított házirendeket a gyermekek is öröklik. 

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan szerezhet bérlői szintű láthatóságot az Azure Security Center számára. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

> [!div class="nextstepaction"]
> [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)

