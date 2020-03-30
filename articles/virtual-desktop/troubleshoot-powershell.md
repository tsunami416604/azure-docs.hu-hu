---
title: Windows virtuális asztali PowerShell – Azure
description: A PowerShell használatával kapcsolatos problémák elhárítása windowsos virtuális asztali bérlői környezet beállításakor.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fb5436c2b5c30c5336385792d0597bdcea2b538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127466"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop – PowerShell

Ebben a cikkben a PowerShell Windows virtual desktop szolgáltatással való használatasorán felmerülő hibák és problémák elhárításához. A Távoli asztali szolgáltatások PowerShell szolgáltatásáról a [Windows Virtual Desktop Powershell](/powershell/module/windowsvirtualdesktop/)című témakörben talál további információt.

## <a name="provide-feedback"></a>Visszajelzés küldése

Látogasson el a [Windows Virtual Desktop Tech Közösség,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) hogy megvitassák a Windows Virtual Desktop szolgáltatás a termék csapat és az aktív közösség tagjai.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>A Windows Virtual Desktop telepítése során használt PowerShell-parancsok

Ez a szakasz azokat a PowerShell-parancsokat sorolja fel, amelyeket általában a Windows virtuális asztal beállításasorán használnak, és lehetőséget nyújt a használatuk során felmerülő problémák megoldására.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Hiba: Add-RdsAppGroupUser parancs -- A megadott UserPrincipalName már hozzá van rendelve egy RemoteApp-alkalmazáscsoporthoz a megadott állomáskészletben

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Ok:** A használt felhasználónév már hozzá van rendelve egy másik típusú alkalmazáscsoporthoz. A felhasználók nem rendelhetők hozzá mind a távoli asztalhoz, sem a távoli alkalmazáscsoporthoz ugyanazon munkamenetgazda-készlet alatt.

**Javítás:** Ha a felhasználónak szüksége van mind a távoli alkalmazások és a távoli asztal, hozzon létre különböző gazdagépkészletek, vagy adjon felhasználói hozzáférést a távoli asztalhoz, amely lehetővé teszi a munkamenet gazdagép bármely alkalmazás használatát.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Hiba: Add-RdsAppGroupUser parancs - A megadott UserPrincipalName nem létezik a távoli asztali bérlőhöz társított Azure Active Directoryban

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Ok:** A -UserPrincipalName által megadott felhasználó nem található a Windows virtuális asztal bérlőhöz kötött Azure Active Directoryban.

**Javítás:** Erősítse meg az alábbi lista elemeit.

- A felhasználó szinkronizálva van az Azure Active Directoryval.
- A felhasználó nem kötődik a fogyasztói (B2C) vagy a vállalkozások (B2B) kereskedelemhez.
- A Windows virtuális asztal bérlője az Azure Active Directory javításához van kötve.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Hiba: Get-RdsDiagnosticActivities - A felhasználó nem jogosult lekérdezni a felügyeleti szolgáltatást

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Ok:** -TenantName paraméter

**Javítás:** Kérdés Get-RdsDiagnosticActivities a \<-TenantName TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: Get-RdsDiagnosticActivities – a felhasználó nem jogosult lekérdezni a felügyeleti szolgáltatást

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Ok:** A -Deployment kapcsoló használata.

**Javítás:** -A központi telepítési kapcsolót csak a központi telepítés rendszergazdái használhatják. Ezek a rendszergazdák általában a Távoli asztali szolgáltatások/Windows virtuális asztal csapatának tagjai. Cserélje le a -Deployment \<kapcsolót a -TenantName TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: New-RdsRoleAssignment - a felhasználó nincs jogosult lekérdezni a felügyeleti szolgáltatást

**1. ok:** A használt fiók nem rendelkezik a távoli asztali szolgáltatások tulajdonosi engedélyeivel a bérlőn.

**1. javítás:** A Távoli asztali szolgáltatások tulajdonosi engedélyekkel rendelkező felhasználónak végre kell hajtania a szerepkör-hozzárendelést.

**2. ok:** A használt fiók a Távoli asztali szolgáltatások tulajdonosi engedélyekkel rendelkezik, de nem része a bérlő Azure Active Directory, vagy nem rendelkezik engedélyekkel az Azure Active Directory lekérdezéséhez, ahol a felhasználó található.

**2. javítás:** Az Active Directory-engedélyekkel rendelkező felhasználóknak végre kell hajtaniuk a szerepkör-hozzárendelést.

>[!Note]
>Az új-RdsRoleAssignment nem adhat engedélyt olyan felhasználónak, aki nem létezik az Azure Active Directoryban (AD).

## <a name="next-steps"></a>További lépések

- A Windows virtuális asztal hibáinak és az eszkalációs pályáknak a [hibaelhárítás – áttekintés– áttekintés– visszajelzés és támogatás – áttekintést.](troubleshoot-set-up-overview.md)
- A bérlői és gazdagépkészlet Windows virtuális asztali környezetben való létrehozása során felmerülő problémák elhárításához olvassa el a [Bérlő és a gazdakészlet létrehozása című témakört.](troubleshoot-set-up-issues.md)
- A Windows virtuális asztal virtuális gépének konfigurálása során felmerülő problémák elhárításához olvassa el a [Munkamenetgazda virtuálisgép-konfiguráció című témakört.](troubleshoot-vm-configuration.md)
- A Windows Virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításáról a Windows Virtuális asztal szolgáltatás kapcsolatai című [témakörben található.](troubleshoot-service-connection.md)
- A Távoli asztali ügyfelekkel kapcsolatos problémák elhárításáról [a Távoli asztali ügyfél – Problémaeltetés – problémamegoldás](troubleshoot-client.md)
- A szolgáltatásról a [Windows virtuális asztali környezet című témakörben](environment-setup.md)olvashat bővebben.
- Ha hibaelhárítási oktatóanyagon szeretne átmenni, olvassa el [az Oktatóanyag: Erőforrás-kezelő sablontelepítésekkel kapcsolatos hibaelhárítása című témakört.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
- A naplózási műveletekről a [Naplózási műveletek az Erőforrás-kezelővel (Audit operations with Resource Manager) témakörben](../azure-resource-manager/management/view-activity-logs.md)olvashat.
- A központi telepítés során fellépő hibák meghatározására irányuló műveletekről a [Telepítési műveletek megtekintése című témakörben](../azure-resource-manager/templates/deployment-history.md)olvashat.
