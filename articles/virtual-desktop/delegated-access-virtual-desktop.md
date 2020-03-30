---
title: Delegált hozzáférés a Windows virtuális asztalon – Azure
description: Felügyeleti lehetőségek delegálása a Windows virtuális asztal központi telepítésén, példákkal együtt.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3e27550ecc5b42c2bf0d947690da09e13d88ea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128028"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Delegált hozzáférés a Windows Virtual Desktopon

A Windows virtuális asztal delegált hozzáférési modellel rendelkezik, amely lehetővé teszi, hogy egy szerepkör hozzárendelésével meghatározza, hogy egy adott felhasználó milyen mennyiségű hozzáféréssel rendelkezhet. A szerepkör-hozzárendelés három összetevőből áll: rendszerbiztonsági tag, szerepkör-definíció és hatókör. A Windows virtual desktop delegált hozzáférési modell az Azure RBAC modell alapul. Ha többet szeretne megtudni az adott szerepkör-hozzárendelésekről és azok összetevőiről, olvassa el [az Azure szerepköralapú hozzáférés-vezérlés áttekintése című témakört.](../role-based-access-control/built-in-roles.md)

A Windows virtuális asztal delegált hozzáférése a szerepkör-hozzárendelés minden eleméhez a következő értékeket támogatja:

* Rendszerbiztonsági tag
    * Felhasználók
    * Szolgáltatásnevek
* Szerepkör-definíció
    * Beépített szerepkörök
* Hatókör
    * Bérlői csoportok
    * Bérlők
    * Gazdamedencék
    * Alkalmazáscsoportok

## <a name="built-in-roles"></a>Beépített szerepkörök

A Windows virtuális asztal delegált hozzáférése számos beépített szerepkör-definícióval rendelkezik, amelyeket a felhasználókhoz és a szolgáltatástagokhoz rendelhet.

* Az RDS-tulajdonos mindent kezelhet, beleértve az erőforrásokhoz való hozzáférést is.
* Az RDS-közreműködő az erőforrásokhoz való hozzáférésen kivételül mindent kezelhet.
* Az RDS-olvasó mindent megtekinthet, de nem tud módosításokat végrehajtani.
* A Távoli asztali szolgáltatások felelőse megtekintheti a diagnosztikai tevékenységeket.

## <a name="powershell-cmdlets-for-role-assignments"></a>PowerShell-parancsmagok szerepkör-hozzárendelésekhez

A szerepkör-hozzárendelések létrehozásához, megtekintéséhez és eltávolításához a következő parancsmagokat futtathatja:

* **A Get-RdsRoleAssignment** megjeleníti a szerepkör-hozzárendelések listáját.
* **Az új-RdsRoleassignment** új szerepkör-hozzárendelést hoz létre.
* **Az Remove-RdsRoleAssignment** törli a szerepkör-hozzárendeléseket.

### <a name="accepted-parameters"></a>Elfogadott paraméterek

Az alaphárom parancsmag a következő paraméterekkel módosítható:

* **AadTenantId**: megadja az Azure Active Directory-bérlői azonosítót, amelyből az egyszerű szolgáltatás tagja.
* **AppGroupName**: a Távoli asztal alkalmazáscsoport neve.
* **Diagnosztika**: a diagnosztikai hatókört jelzi. (Az **infrastruktúra** vagy a **bérlő** paraméterekkel kell párosítani.)
* **HostPoolName**: a Távoli asztal állomáskészletének neve.
* **Infrastruktúra**: az infrastruktúra hatókörét jelzi.
* **RoleDefinitionName**: a felhasználóhoz, csoporthoz vagy alkalmazáshoz rendelt Távoli asztali szolgáltatások szerepköralapú hozzáférés-vezérlési szerepkör neve. (Például a Távoli asztali szolgáltatások tulajdonosa, a Távoli asztali szolgáltatások olvasója és így tovább.)
* **ServerPrincipleName**: az Azure Active Directory alkalmazás neve.
* **SignInName**: a felhasználó e-mail címe vagy egyszerű felhasználóneve.
* **TenantName**: a távoli asztal bérlőjének neve.

## <a name="next-steps"></a>További lépések

Az egyes szerepkörek által használható PowerShell-parancsmagok teljesebb listáját a [PowerShell-hivatkozás ban kaphatja meg.](/powershell/windows-virtual-desktop/overview)

A Windows virtuális asztali környezet beállítására vonatkozó útmutatást a [Windows virtuális asztal környezetben](environment-setup.md)találhatók.
