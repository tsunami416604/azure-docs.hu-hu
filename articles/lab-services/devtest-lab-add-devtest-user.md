---
title: Tulajdonosok és felhasználók hozzáadása az Azure DevTest Labsben| Microsoft dokumentumok
description: Tulajdonosok és felhasználók hozzáadása az Azure DevTest Labs-ben az Azure Portalon vagy a PowerShellen keresztül
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: spelluru
ms.openlocfilehash: a9426c20ae23fd3dad4cdba25590ff2eac271896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284277"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Tulajdonosok és felhasználók hozzáadása az Azure DevTest Labs-ben
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Az Azure DevTest Labs elérését az [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md)szabályozza. Az RBAC használatával a csapaton belüli feladatokat *szerepkörökre* különítheti el, ahol csak a feladatok elvégzéséhez szükséges hozzáférés a felhasználók számára biztosítható. Az RBAC-szerepkörök közül három a *Tulajdonos*, *a DevTest Labs felhasználója*és *a Közreműködő.* Ebben a cikkben megtudhatja, hogy milyen műveletekhajthatók el a három fő RBAC-szerepkör mindegyikében. Innen megtudhatja, hogyan adhat hozzá felhasználókat egy laborba – a portálon keresztül és egy PowerShell-parancsfájlon keresztül, és hogyan adhat hozzá felhasználókat az előfizetés szintjén.

## <a name="actions-that-can-be-performed-in-each-role"></a>Az egyes szerepkörekben végrehajtható műveletek
A felhasználóhoz három fő szerepkörrendelhető:

* Tulajdonos
* DevTest Labs felhasználó
* Közreműködő

Az alábbi táblázat azokat a műveleteket mutatja be, amelyeket a felhasználók az egyes szerepkörökben végrehajthatnak:

| **Az ebben a szerepkörben lévő felhasználók által végrehajtott műveletek** | **DevTest Labs felhasználó** | **Tulajdonos** | **Közreműködő** |
| --- | --- | --- | --- |
| **Laborfeladatok** | | | |
| Felhasználók hozzáadása laborhoz |Nem |Igen |Nem |
| Költségbeállítások frissítése |Nem |Igen |Igen |
| **Virtuális gép alapfeladatai** | | | |
| Egyéni képek hozzáadása és eltávolítása |Nem |Igen |Igen |
| Képletek hozzáadása, frissítése és törlése |Igen |Igen |Igen |
| Engedélyezési lista Az Azure Piactér lemezképei |Nem |Igen |Igen |
| **Virtuálisgép-feladatok** | | | |
| Virtuális gépek létrehozása |Igen |Igen |Igen |
| Virtuális gépek indítása, leállítása és törlése |Csak a felhasználó által létrehozott virtuális gépek |Igen |Igen |
| Virtuálisgép-házirendek frissítése |Nem |Igen |Igen |
| Adatlemezek hozzáadása/eltávolítása virtuális gépekhez/gépekből |Csak a felhasználó által létrehozott virtuális gépek |Igen |Igen |
| **Műtárgy-feladatok** | | | |
| Műtermék-tárolók hozzáadása és eltávolítása |Nem |Igen |Igen |
| Összetevők alkalmazása |Igen |Igen |Igen |

> [!NOTE]
> Amikor egy felhasználó létrehoz egy virtuális gép, hogy a felhasználó automatikusan hozzá van rendelve a **tulajdonos** szerepkör a létrehozott virtuális gép.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Tulajdonos vagy felhasználó hozzáadása a labor szintjén
A tulajdonosok és a felhasználók az Azure Portalon keresztül adhatók hozzá a labor szintjén. A felhasználó lehet érvényes [Microsoft-fiókkal (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account)rendelkező külső felhasználó.
Az alábbi lépések végigvezetik a tulajdonos vagy felhasználó azure DevTest Labs laborhoz való hozzáadásának folyamatán:

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
2. Válassza a **Minden szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
3. A laborok listájából válassza ki a kívánt labort.
4. A tesztkörnyezet paneljén válassza a **Konfiguráció és házirendek**lehetőséget. 
5. A **Konfiguráció és házirendek** lapon válassza a bal oldali menü **Hozzáférés-vezérlés (IAM)** lehetőséget. 
6. Ha felhasználót szeretne hozzáadni egy szerepkörhöz, jelölje be a **Szerepkör-hozzárendelés hozzáadása** jelölőnégyzetet az eszköztáron.
1. Az **Engedélyek hozzáadása** ablakban végezze el a következő műveleteket: 
    1. Válasszon ki egy szerepkört (például: DevTest Labs felhasználó). Az [egyes szerepkörökben végrehajtható műveletek](#actions-that-can-be-performed-in-each-role) szakasz felsorolja a tulajdonosi, a devteszt-felhasználó és a közreműködői szerepkör ben a felhasználók által végrehajtható különböző műveleteket.
    2. Válassza ki a szerepkörbe hozzáadni kívánt felhasználót. 
    3. Kattintson a **Mentés** gombra. 
11. Amikor visszatér a **Felhasználók** panelhez, a felhasználó hozzá lett adva.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Külső felhasználó hozzáadása laborhoz a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A felhasználók hozzáadása az Azure Portalon, hozzáadhat egy külső felhasználót a laborhoz egy PowerShell-parancsfájl használatával. A következő példában módosítsa a paraméterértékeket az Értékek csoportban a megjegyzés **módosításához.**
A , `subscriptionId` `labResourceGroup`és `labName` az értékek et az Azure Portalon a laborpanelről kérheti le.

> [!NOTE]
> A mintaparancsfájl feltételezi, hogy a megadott felhasználó vendégként lett hozzáadva az Active Directoryhoz, és sikertelen lesz, ha nem ez a helyzet. Ha nem az Active Directoryban nem szereplő felhasználót szeretne hozzáadni egy tesztkörnyezethez, az Azure Portal segítségével rendelje hozzá a felhasználót egy szerepkörhöz, amint azt a szakaszban, tulajdonos vagy felhasználó hozzáadása a labor szintjén című szakaszban [láthatómódon](#add-an-owner-or-user-at-the-lab-level)rendelheti hozzá.   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Connect-AzAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Tulajdonos vagy felhasználó hozzáadása az előfizetés szintjén
Az Azure-engedélyek a szülőhatókörből az Azure-ban lévő gyermekhatókörbe kerülnek propagálásra. Ezért a laborokat tartalmazó Azure-előfizetés tulajdonosai automatikusan tulajdonosai ezeknek a laboroknak. Ők is a virtuális gépek és a labor felhasználói által létrehozott egyéb erőforrások, valamint az Azure DevTest Labs szolgáltatás. 

További tulajdonosokat adhat hozzá egy laborhoz a labor paneljén keresztül az [Azure Portalon.](https://go.microsoft.com/fwlink/p/?LinkID=525040) A hozzáadott tulajdonos felügyeleti hatóköre azonban szűkebb, mint az előfizetés tulajdonosának hatóköre. Például a hozzáadott tulajdonosok nem rendelkeznek teljes hozzáféréssel a DevTest Labs szolgáltatás által az előfizetésben létrehozott erőforrások egy részéhez. 

Ha tulajdonost szeretne hozzáadni egy Azure-előfizetéshez, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
2. Válassza a **Minden szolgáltatás**lehetőséget, majd a listából válassza az **Előfizetések** elemet.
3. Válassza ki a kívánt előfizetést.
4. Válassza **az Access** ikont. 
   
    ![Felhasználók elérése](./media/devtest-lab-add-devtest-user/access-users.png)
5. A **Felhasználók** panelen válassza a **Hozzáadás**lehetőséget.
   
    ![Felhasználó hozzáadása](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. A **Szerepkör kiválasztása** panelen válassza a **Tulajdonos**lehetőséget.
7. A **Felhasználók hozzáadása** panelen adja meg a tulajdonosként hozzáadni kívánt felhasználó e-mail címét vagy nevét. Ha a felhasználó nem található, hibaüzenet jelenik meg a probléma magyarázatakor. Ha a felhasználó megtalálható, a felhasználó a **Felhasználó** szövegmezőben jelenik meg.
8. Válassza ki a kívánt felhasználónevet.
9. Válassza a **Kiválasztás** lehetőséget.
10. A **Hozzáférés hozzáadása** panel bezárásához válassza az **OK gombot.**
11. Amikor visszatér a **Felhasználók** panelhez, a felhasználó tulajdonosként lett hozzáadva. Ez a felhasználó most antól az előfizetés alatt létrehozott bármely labor tulajdonosa, és így képes a tulajdonosi feladatok végrehajtására. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

