---
title: Delegált hozzáférés a Windows rendszerű virtuális asztalon – Azure
description: Felügyeleti képességek delegálása Windows rendszerű virtuális asztali környezetben, például példákkal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 16b4fca475f91a8cb5b7f9a20ea5aa74b6b674a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82612860"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Delegált hozzáférés a Windows Virtual Desktopon

>[!IMPORTANT]
>Ez a tartalom a Spring 2020 frissítésre vonatkozik Azure Resource Manager Windows rendszerű virtuális asztali objektumokkal. Ha a Windows rendszerű virtuális 2019 asztalt Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).
>
> A Windows rendszerű virtuális asztali Spring 2020 frissítése jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. 
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Windows virtuális asztal olyan delegált hozzáférési modellel rendelkezik, amely lehetővé teszi, hogy egy adott felhasználó számára engedélyezett mennyiségű hozzáférést rendeljen hozzá egy szerepkörhöz. A szerepkör-hozzárendelés három összetevőből áll: rendszerbiztonsági tag, szerepkör-definíció és hatókör. A Windows rendszerű virtuális asztali delegált hozzáférési modell az Azure RBAC-modellen alapul. Ha többet szeretne megtudni az adott szerepkör-hozzárendelésekről és azok összetevőiről, tekintse meg [Az Azure szerepköralapú hozzáférés-vezérlés áttekintését](../role-based-access-control/built-in-roles.md).

A Windows virtuális asztal delegált hozzáférése a következő értékeket támogatja a szerepkör-hozzárendelés minden eleméhez:

* Rendszerbiztonsági tag
    * Felhasználók
    * Felhasználói csoportok
    * Szolgáltatásnevek
* Szerepkör-definíció
    * Beépített szerepkörök
    * Egyéni szerepkörök
* Hatókör
    * Gazdagépek készletei
    * Alkalmazáscsoportok
    * Munkaterületek

## <a name="powershell-cmdlets-for-role-assignments"></a>A szerepkör-hozzárendelések PowerShell-parancsmagjai

Mielőtt elkezdené, kövesse a [PowerShell-modul beállítása](powershell-module.md) a Windows rendszerű virtuális asztali PowerShell-modul beállítására vonatkozó útmutatást, ha még nem tette meg.

A Windows virtuális asztal Azure szerepköralapú hozzáférés-vezérlést (RBAC) használ az alkalmazások felhasználói vagy felhasználói csoportok számára való közzétételekor. Az asztali virtualizálási felhasználói szerepkör hozzá van rendelve a felhasználóhoz vagy a felhasználói csoporthoz, és a hatókör az alkalmazás csoportja. Ez a szerepkör a felhasználó számára speciális adatelérést biztosít az alkalmazás csoportjának.  

A következő parancsmag futtatásával Azure Active Directory felhasználókat adhat hozzá egy alkalmazás-csoporthoz:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'  
```

A következő parancsmag futtatásával Azure Active Directory felhasználói csoportot adhat hozzá egy alkalmazás-csoporthoz:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

## <a name="next-steps"></a>További lépések

Az egyes szerepkörök által használható PowerShell-parancsmagok teljes listájáért tekintse meg a [PowerShell-referenciát](/powershell/windows-virtual-desktop/overview).

Az Azure RBAC által támogatott szerepkörök teljes listájáért tekintse meg az [Azure beépített szerepkörei](../role-based-access-control/built-in-roles.md)című témakört.

A Windows rendszerű virtuális asztali környezet beállításával kapcsolatos útmutatásért lásd: [Windows rendszerű virtuális asztali környezet](environment-setup.md).
