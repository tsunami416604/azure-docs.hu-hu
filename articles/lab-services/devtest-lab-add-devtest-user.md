---
title: Tulajdonosok és felhasználók hozzáadása az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Tulajdonosok és felhasználók hozzáadása az Azure DevTest Labs szolgáltatásban az Azure Portalon vagy a PowerShell használatával
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
ms.openlocfilehash: 558df3fa70989aaf9ba182df3a918994c7dc9db6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243709"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Tulajdonosok és felhasználók hozzáadása az Azure DevTest Labs szolgáltatásban
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Az Azure DevTest Labs szolgáltatásban hozzáférést vezérlik [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md). Az RBAC használatával, akkor is elkülönítse azokat a csapaton belül *szerepkörök* , ahol megadta csak olyan mértékű hozzáférést a felhasználók számára a munkájuk elvégzéséhez szükséges. Közül három ezek RBAC-szerepkörök *tulajdonosa*, *DevTest Labs-felhasználó*, és *közreműködői*. Ebből a cikkből megtudhatja, milyen műveletek végezhetők az egyes a három fő RBAC-szerepkört. Itt megismerheti a felhasználók hozzáadása egy laborhoz – mind a portálon keresztül, és a egy PowerShell-szkripttel nevezhetnek és felhasználók hozzáadása az előfizetés szintjén.

## <a name="actions-that-can-be-performed-in-each-role"></a>Minden szerepkör végrehajtott műveletek
Az egy felhasználó hozzárendelheti három fő szerepkörök állnak rendelkezésre:

* Tulajdonos
* DevTest Labs-felhasználó
* Közreműködő

Az alábbi táblázat mutatja be, hogy ezek a szerepkörök minden egyes felhasználók által végrehajtható műveleteket:

| **A szerepkör felhasználói műveleteket hajthat végre.** | **DevTest Labs-felhasználó** | **Tulajdonos** | **Közreműködő** |
| --- | --- | --- | --- |
| **Tesztlabor-feladatok** | | | |
| Felhasználók hozzáadása egy laborhoz |Nem |Igen |Nem |
| Költség-beállítások frissítése |Nem |Igen |Igen |
| **Alap VM-feladatok** | | | |
| Hozzáadhat és eltávolíthat egyéni rendszerképek |Nem |Igen |Igen |
| Hozzáadása, frissítése és törlése a képletek |Igen |Igen |Igen |
| Engedélyezett Azure Marketplace-rendszerképek |Nem |Igen |Igen |
| **Virtuális gépek feladatainak** | | | |
| Virtuális gépek létrehozása |Igen |Igen |Igen |
| Indítása, leállítása és virtuális gépek törlése |Csak a felhasználó által létrehozott virtuális |Igen |Igen |
| Frissítse a virtuális gépek házirendjei |Nem |Igen |Igen |
| Az adatlemezeket és a virtuális gépek hozzáadása/eltávolítása |Csak a felhasználó által létrehozott virtuális |Igen |Igen |
| **Összetevő-feladatok** | | | |
| Hozzáadhat és eltávolíthat összetevő adattárak |Nem |Igen |Igen |
| Összetevők alkalmazása |Igen |Igen |Igen |

> [!NOTE]
> Amikor egy felhasználó létrehoz egy virtuális Gépet, a felhasználó automatikusan hozzá lesz rendelve a **tulajdonos** a létrehozott virtuális gép szerepkör.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>A lab szintjén egy tulajdonos vagy a felhasználó hozzáadása
Tulajdonosok és a felhasználók az Azure Portalon a labor szintjén adhatók meg. A felhasználó lehet egy külső felhasználót érvényes [Microsoft-fiókkal (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
A következő lépések végigvezetik a tulajdonosnak vagy felhasználó hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet folyamatán:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.
4. A labor paneljén válassza ki a **Konfigurace a zásady**. 
5. Az a **Konfigurace a zásady** lapon jelölje be **hozzáférés-vezérlés (IAM)** a bal oldali menüből. 
6. Válassza ki **Hozzáadás** hozzáadása egy felhasználó egy szerepkörhöz az eszköztáron.

    ![Felhasználó hozzáadása](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
1. Az a **engedélyek hozzáadása** ablakban hajtsa végre a következőket: 
    1. Válasszon egy szerepkört (például: DevTest Labs-felhasználó). A szakasz [minden szerepkörben végrehajtható műveletek](#actions-that-can-be-performed-in-each-role) felsorolja azokat a különböző műveleteket, hogy a felhasználók a tulajdonos, DevTest-felhasználó, és közreműködő szerepkörrel végezhet el.
    2. Válassza ki a felhasználót a szerepkörhöz kell hozzáadni. 
    3. Kattintson a **Mentés** gombra. 

        ![Adja hozzá a felhasználót a szerepkörhöz](./media/devtest-lab-add-devtest-user/add-user.png) 
11. Amikor visszatér a **felhasználók** panelen, a felhasználó hozzá lett adva.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Külső felhasználó hozzáadása egy laborhoz PowerShell-lel
Felhasználók hozzáadása az Azure Portalon, valamint egy külső felhasználót adhat hozzá a labor egy PowerShell-parancsfájl használatával. A paraméter értékét módosítsa a következő példában a **értékek módosítása** megjegyzést.
Lekérheti a `subscriptionId`, `labResourceGroup`, és `labName` értékeket a labor panelről az Azure Portalon.

> [!NOTE]
> A minta parancsfájl feltételezi, hogy a megadott felhasználó az Active Directory vendégként bővült, és sikertelen lesz, ha, amely nem áll fenn. Felhasználó hozzáadása egy laborhoz nem található az Active Directory, az Azure portal segítségével rendelje hozzá a felhasználót egy szerepkörhöz, amint az a szakasz az [tulajdonosa vagy felhasználó hozzáadása a labor szintjén](#add-an-owner-or-user-at-the-lab-level).   
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
    Connect-AzureRmAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzureRmADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Adjon hozzá egy tulajdonosi vagy a felhasználó az előfizetés szintjén
Azure-engedélyeket, az Azure-ban a gyermek hatókörben vannak propagálni szülőhatókörben. Ezért az Azure-előfizetéssel, amely tartalmazza a labs tulajdonosai a program automatikusan ezeket labs tulajdonosai. Emellett a virtuális gépek és egyéb erőforrások a labor létrehozása felhasználók és az Azure DevTest Labs szolgáltatás által létrehozott saját. 

A labor létrehozása panelen keresztül labor is hozzáadhat további tulajdonosok a [az Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040). Azonban az új tulajdonos felügyeleti hatóköre sokkal szűkebb, mint az előfizetés tulajdonosa hatókör. Például a hozzáadva tulajdonosok nem rendelkezik teljes hozzáféréssel néhány, az előfizetésben a DevTest Labs szolgáltatás által létrehozott erőforrást. 

Tulajdonos hozzáadása egy Azure-előfizetést, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **előfizetések** a listából.
3. Válassza ki a kívánt előfizetés azonosítóértékét.
4. Válassza ki **hozzáférés** ikonra. 
   
    ![Hozzáféréssel rendelkező felhasználók](./media/devtest-lab-add-devtest-user/access-users.png)
5. Az a **felhasználók** panelen válassza ki **Hozzáadás**.
   
    ![Felhasználó hozzáadása](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. Az a **Szerepkörválasztás** panelen válasszon ki **tulajdonosa**.
7. Az a **felhasználók hozzáadása** panelen adja meg a tulajdonosként hozzáadni kívánt felhasználó nevét vagy e-mail cím. Ha a felhasználó nem található, megjelenik egy hibaüzenet, amely elmagyarázza a hibát. Ha a felhasználó megtalálható, területen látható, hogy a felhasználó a **felhasználói** szövegmezőben.
8. Válassza ki a található felhasználónév.
9. Válassza ki **kiválasztása**.
10. Válassza ki **OK** gombra kattintva zárja be a **hozzáférés hozzáadása** panelen.
11. Amikor visszatér a **felhasználók** panelen, a felhasználó hozzá van adva tulajdonosai. Ez a felhasználó már bármilyen ebben az előfizetésben létrehozott labs tulajdonosa, és így tulajdonos feladatok végrehajtásához. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

