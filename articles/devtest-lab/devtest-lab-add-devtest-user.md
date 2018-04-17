---
title: Adja hozzá a tulajdonosok és a felhasználók a Azure DevTest Labs szolgáltatásban |} Microsoft Docs
description: Adja hozzá a tulajdonosok és a felhasználók a Azure DevTest Labs szolgáltatásban az Azure-portálon vagy a PowerShell használatával
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: ''
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-craic
ms.openlocfilehash: d0fdd8441143e2d2eb49399a10dc12d38fbf6823
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Adja hozzá a tulajdonosok és a felhasználók a Azure DevTest Labs szolgáltatásban
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Hozzáférés az Azure DevTest Labs vezérli [átruházásához hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md). Szerepalapú használ, akkor is elkülönítse azon belül a csapat a *szerepkörök* ahol engedélyezheti, hogy csak olyan mértékű hozzáférést a felhasználóknak a feladataik elvégzéséhez szükséges. Közül három ezeket az RBAC-szerepkörök *tulajdonos*, *DevTest Labs felhasználói*, és *közreműködő*. Ebből a cikkből megismerheti milyen műveletek is elvégezhetők a három fő RBAC-szerepkörök. Ott megismerheti a felhasználók felvétele egy tesztlabor - mind a portálon keresztül egy PowerShell-parancsfájlt, és felhasználók hozzáadása az előfizetés szintjén.

## <a name="actions-that-can-be-performed-in-each-role"></a>Az egyes szerepkörökhöz végrehajtható műveletek
Az a felhasználó hozzárendelheti három fő szerepkörök állnak rendelkezésre:

* Tulajdonos
* DevTest Labs-felhasználó
* Közreműködő

A következő táblázat bemutatja, ezek a szerepkörök a felhasználók által végrehajtható műveleteket:

| **A szerepet betöltő felhasználók műveleteket hajthat végre.** | **DevTest Labs felhasználó** | **Tulajdonos** | **Közreműködő** |
| --- | --- | --- | --- |
| **Labor feladatok** | | | |
| Felhasználók hozzáadása egy laborhoz |Nem |Igen |Nem |
| Költség-beállítások frissítése |Nem |Igen |Igen |
| **Alapszintű VM-feladatok** | | | |
| Hozzáadhat és eltávolíthat egyéni lemezképek |Nem |Igen |Igen |
| Hozzáadása, módosítása és törlése képletek |Igen |Igen |Igen |
| Engedélyezett Azure piactéren elérhető rendszerkép |Nem |Igen |Igen |
| **VM-feladatok** | | | |
| Virtuális gépek létrehozása |Igen |Igen |Igen |
| Indítás, Leállítás, és törölje a virtuális gépek |Csak virtuális gépek a felhasználó által létrehozott |Igen |Igen |
| Virtuális gép házirendek frissítése |Nem |Igen |Igen |
| Az adatlemezek és a virtuális gépek hozzáadása |Csak virtuális gépek a felhasználó által létrehozott |Igen |Igen |
| **Összetevő-feladatok** | | | |
| Hozzáadhat és eltávolíthat összetevő adattárak |Nem |Igen |Igen |
| Az összetevők alkalmazása |Igen |Igen |Igen |

> [!NOTE]
> Amikor egy felhasználó létrehoz egy virtuális Gépet, a rendszer automatikusan hozzárendeli, hogy a felhasználó a **tulajdonos** a létrehozott virtuális gép szerepkör.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>A tesztkörnyezet szinten egy tulajdonos vagy a felhasználó hozzáadása
Tulajdonosa és a felhasználók az Azure-portálon a labor szintjén adhatók meg. Ez magában foglalja a külső felhasználók érvényes [Microsoft-fiók (msa-t)](devtest-lab-faq.md#what-is-a-microsoft-account).
A következő lépések végigvezetik egy tulajdonos vagy a felhasználó hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban:

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Válassza ki a kívánt labor labs listájának megtekintéséhez.
4. A labor paneljén válassza **konfigurációs**. 
5. Az a **konfigurációs** panelen válassza **felhasználók**.
6. Az a **felhasználók** panelen válassza **+ Hozzáadás**.
   
    ![Felhasználó hozzáadása](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
7. Az a **Szerepkörválasztás** panelen válassza ki a kívánt szerepkört. A szakasz [szerepkörönként végrehajtott műveletekből](#actions-that-can-be-performed-in-each-role) felsorolja azokat a különböző, a tulajdonosa, a DevTest felhasználói és a közreműködői szerepkör a felhasználók által végrehajtható műveleteket.
8. Az a **felhasználók hozzáadása az** panelen adja meg az e-mail cím vagy a felhasználó a szerepkörben megadott hozzáadni kívánt nevét. Ha a felhasználó nem található, egy hibaüzenet ismerteti a problémát. Ha a felhasználó megtalálható, hogy a felhasználó a felsorolt és kiválasztott. 
9. Válassza ki **válasszon**.
10. Válassza ki **OK** bezárásához a **hozzáférés hozzáadása** panelen.
11. Amikor visszatér a **felhasználók** panelen, a felhasználó hozzá lett adva.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Külső felhasználó hozzáadása egy laborhoz PowerShell használatával
Mellett felhasználók hozzáadása az Azure portálon, a külső felhasználó adhat hozzá a labor egy PowerShell-parancsfájl használatával. A következő példában a paraméterértékeket a módosításához egyszerűen a **értékek módosítása** megjegyzés.
Kérheti le a `subscriptionId`, `labResourceGroup`, és `labName` értékeit a labor panel az Azure portálon.

> [!NOTE]
> A parancsfájlpéldát feltételezi, hogy a megadott felhasználó az Active Directory vendégként hozzáadva, és sikertelen lesz, ha a feltételek nem teljesülnek. A felhasználó nem szerepel az Active Directory hozzáadása egy tesztkörnyezetet, az Azure-portál használatával rendelje hozzá a felhasználót a szerepkörhöz a szakaszban ismertetett módon [egy tulajdonos vagy a felhasználó hozzáadása a labor szinten](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/en-us/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Login-AzureRmAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzureRmADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Az előfizetés szintjén egy tulajdonos vagy a felhasználó hozzáadása
Az Azure engedélyek rendszer nem propagál származik gyermek hatókörben az Azure-ban. Ezért az Azure-előfizetéssel, amely tartalmazza a labs tulajdonosai a program automatikusan e labs tulajdonosai. A virtuális gépek és egyéb erőforrások a labor felhasználók és az Azure DevTest Labs szolgáltatás által létrehozott is saját. 

A labor panel az keresztül labor adhat hozzá további tulajdonosai a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040). Azonban a hozzáadott tulajdonos felügyeleti hatóköre sokkal szűkebb, mint az előfizetés tulajdonosa hatókör. A hozzáadott tulajdonosai például nem rendelkezik teljes körű hozzáférés az egyes erőforrásokhoz az előfizetést a DevTest Labs szolgáltatás által létrehozott. 

Tulajdonosa hozzá Azure-előfizetéssel, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **előfizetések** a listából.
3. Válassza ki a kívánt előfizetést.
4. Válassza ki **hozzáférés** ikonra. 
   
    ![Használó felhasználók](./media/devtest-lab-add-devtest-user/access-users.png)
5. Az a **felhasználók** panelen válassza **Hozzáadás**.
   
    ![Felhasználó hozzáadása](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. Az a **Szerepkörválasztás** panelen válasszon ki **tulajdonos**.
7. Az a **felhasználók hozzáadása az** panelen adja meg az e-mail cím vagy a tulajdonos hozzáadni kívánt felhasználó nevét. Ha a felhasználó nem található, kap olyan hibaüzenetet, amely ismerteti, hogy a problémát. Ha a felhasználó megtalálható, a felhasználó megtalálható-e a **felhasználói** szövegmezőben.
8. Válassza ki a található felhasználónév.
9. Válassza ki **válasszon**.
10. Válassza ki **OK** bezárásához a **hozzáférés hozzáadása** panelen.
11. Amikor visszatér a **felhasználók** panelen, a felhasználó tulajdonos hozzáadva. Ez a felhasználó már létrehozott az előfizetéshez tartozó bármely labs tulajdonosa, és így lesz tulajdonos feladatok elvégzéséhez. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

