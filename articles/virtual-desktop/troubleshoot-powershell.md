---
title: Windows asztali virtuális PowerShell – Azure
description: Tudnivalók a PowerShell-lel hibák elhárításához, ha egy virtuális asztali Windows-bérlős környezet beállítása.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: ad32f7ff883812830dbcf2ed900c4034bd90abfc
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927507"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows virtuális asztali PowerShell

Ez a cikk segítségével hibáinak és problémáinak megoldása, a virtuális asztal Windows PowerShell használatakor. További információ a távoli asztali szolgáltatások PowerShell: [Windows virtuális asztal Powershell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Visszajelzés küldése

A Microsoft jelenleg nem tart támogatási esetek Windows virtuális asztal pedig előzetes verzióban érhető el. Látogasson el a [Windows virtuális asztal technikai Közösség](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) vitatni a virtuális asztali Windows-szolgáltatás a termékért felelős csoport és az aktív Közösség tagjai.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows virtuális asztalok telepítése során használt PowerShell-parancsok

Ez a rész felsorolja, amely jellemzően Windows virtuális asztal beállítása közben, és azok használata közben felmerülő problémák megoldásához különféle módot kínál a PowerShell-parancsokat.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Hiba: Adjon hozzá RdsAppGroupUser parancsot – a megadott UserPrincipalName már hozzá van rendelve a megadott gazdagép-készlet csoporthoz RemoteApp alkalmazás

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**OK:** Használt felhasználónév már lett hozzárendelve egy másik típusú alkalmazás csoporthoz. Felhasználók mindkét egy távoli asztal és a Távsegítség alkalmazás csoporthoz ugyanazon munkamenet gazdagép készlet alapján nem lehet hozzárendelni.

**Javítás:** Felhasználónak van szüksége, távoli alkalmazások és a távoli asztal, ha másik gazdagép-címkészletek létrehozása, vagy a távoli asztal, amelyek lehetővé teszik a minden alkalmazás használatát a munkamenet virtuális gép gazdagépen való felhasználói hozzáférést.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Hiba: Adjon hozzá RdsAppGroupUser parancsot – a megadott UserPrincipalName nem létezik a távoli asztal tenanthoz társított Azure Active Directoryban

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**OK:** A - UserPrincipalName által megadott felhasználó nem található az Azure Active Directory, a virtuális asztali Windows-bérlőhöz vannak kötve.

**Javítás:** Erősítse meg a következő listán szereplő elemeket.

- A felhasználó Azure Active Directoryval van szinkronizálva.
- Végfelhasználói kereskedelem (B2C) vagy-vállalatközi (B2B) commerce üzleti a felhasználó nem kötődik.
- A Windows virtuális asztal bérlő helyes-e az Azure Active Directory van kötve.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Hiba: Get-RdsDiagnosticActivities – Felhasználó nem jogosult a felügyeleti szolgáltatás lekérdezése

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**OK:** - TenantName paraméter

**Javítás:** Adja ki a Get-RdsDiagnosticActivities rendelkező - TenantName <TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: Get-RdsDiagnosticActivities – a felhasználó nem jogosult a felügyeleti szolgáltatás lekérdezése

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**OK:** Használatával – üzembe helyezési kapcsoló.

**Javítás:** -üzembe helyezési kapcsoló csak a központi telepítési rendszergazdái használható. Ezek a rendszergazdák általában a távoli asztali szolgáltatások/Windows virtuális asztal csapat tagjai. Cserélje le a – üzembe helyezés a kapcsolót a - TenantName <TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: Új RdsRoleAssignment – a felhasználó nem jogosult a felügyeleti szolgáltatás lekérdezése

**1. ok:** Az éppen használt fiók a bérlő nem rendelkezik a távoli asztali szolgáltatások tulajdonosi engedélyekkel.

**1 javítás:** Távoli asztali szolgáltatások tulajdonosi engedélyekkel rendelkező felhasználó van szüksége a szerepkör-hozzárendelés végrehajtásához.

**2. ok:** Az éppen használt fiók rendelkezik tulajdonosi engedélyekkel a távoli asztali szolgáltatások, de nem a bérlő Azure Active Directory része, vagy nem rendelkezik engedélyekkel, ahol a felhasználó megtalálható az Azure Active Directory lekérdezéséhez.

**2 Javítás:** Az Active Directory-engedélyek a felhasználónak kell hajtsa végre a szerepkör-hozzárendelést.

>[!Note]
>Új RdsRoleAssignment, amely nem létezik az Azure Active Directory (AD) a felhasználó nem lehet engedélyt.

## <a name="next-steps"></a>További lépések

- Hibaelhárítási Windows virtuális asztal és a kiterjesztés nyomon követi az áttekintést lásd: [hibaelhárítási áttekintése, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- Windows virtuális asztali környezetben egy bérlő és a gazdagép-készlet létrehozása során problémák hibaelhárítása: [bérlő és a gazdagép-készlet létrehozása](troubleshoot-set-up-issues.md).
- Egy virtuális gépet (VM) konfigurálása a Windows virtuális asztal során problémák hibaelhárítása: [munkamenetgazda virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- Windows virtuális asztali kapcsolatok problémáinak hibaelhárítása: [távoli asztali kapcsolatok](troubleshoot-client-connection.md).
- Az előzetes verziójú szolgáltatások kapcsolatos további információkért lásd: [Windows Desktop előzetes verziójú környezet](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Nyissa meg a hibaelhárítás az oktatóanyagot, tekintse meg [oktatóanyag: Resource Manager-sablon üzemelő példányok hibaelhárítása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Naplózási műveletek kapcsolatos további információkért lásd: [auditálási műveletek a Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Üzembe helyezés során a hibák megállapításához műveleteivel kapcsolatos tudnivalókért lásd: [üzembehelyezési műveletek megtekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).