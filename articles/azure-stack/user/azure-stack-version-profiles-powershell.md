---
title: API-verzióprofilok használata a PowerShell-lel az Azure Stackben |} A Microsoft Docs
description: Ismerje meg az API-verzióprofilok használata a PowerShell-lel az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 8da02641cc54f9308f8e0bbb8d2b28da9a930aa2
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054897"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Az Azure Stack PowerShell API-verzióprofilok használata

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

API-verzióprofilok kezelése az Azure és az Azure Stack közötti terveztek. Egy API-verzióprofil egy adott API-verziók az AzureRM PowerShell-modulok. Minden felhőalapú platform a támogatott API-verzióprofilok készletével rendelkezik. Például Azure Stack is támogatja a felülírás Profilverzió például **2017-03-09-profile**, és az Azure támogatja a **legújabb** API-verzióprofil. Amikor telepít egy profilt, az AzureRM PowerShell-modulok, amelyek megfelelnek a megadott profil vannak telepítve.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Az API-verzióprofilok használatához szükséges PowerShell-modul telepítése

A **AzureRM.Bootstrapper** modul, amely a PowerShell-galériából érhető el az API-verzióprofilok működéséhez szükséges PowerShell-parancsmagokat kínál. AzureRM.Bootstrapper moduljának telepítéséhez használja a következő parancsmagot:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="install-a-profile"></a>Telepítse a profilt

Használja a **Install-azurermprofile új** parancsmagot a **2017-03-09-profile** API-verzióprofil az Azure Stack által igényelt AzureRM-modulok telepítéséhez. Az Azure Stack operátori modulok nincsenek telepítve a profil API verziója. Akkor kell telepíteni külön meghatározott a 3. lépését a [Azure Stack PowerShell telepítése](azure-stack-powershell-install.md) cikk.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Telepítse, és a egy profil-modulok importálása

Használja a **használata-azurermprofile új** parancsmag telepítése és a egy API-verzióprofil társított modulok importálásához. Csak egy API-verzióprofil importálhatja a PowerShell-munkamenetben. Egy másik API-verzió profil importálása, egy új PowerShell-munkamenetet kell megnyitni. A használat-AzureRMProfile parancsmag futtatásakor a következő feladatokat:  
1. Ellenőrzi a PowerShell-modulok a megadott API-verzió profilhoz társított telepítve vannak-e az aktuális hatókörben.  
2. Letölti és telepíti a modulokat, ha azok nem települnek.   
3. Importálja a modulokat az aktuális PowerShell-munkamenetbe. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

Telepítette, és a egy API-verzióprofil kiválasztott AzureRM-modulok importálása, futtassa a használat-AzureRMProfile parancsmag a **modul** paramétert:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>A telepített profilok beolvasása

Használja a **Get-azurermprofile új** parancsmag elérhető API-verzióprofilok listájának beolvasása: 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Profil frissítése

Használja a **Update-azurermprofile új** parancsmagot, hogy a modulok egy API-verzió profil frissítése a legújabb verzióra, amelyek a PSGallery érhetők el. Azt javasoljuk, hogy mindig fusson a **Update-azurermprofile új** parancsmag egy új PowerShell-munkamenetben a modulok importálása során az ütközések elkerülése érdekében. A frissítés-azurermprofile új parancsmag futtatásakor a következő feladatokat:

1. Ellenőrzi, hogy ha a megadott API verzió profilt az aktuális hatókör telepített modulok a legújabb verzióra.  
2. Kéri, hogy a telepítés, ha azok nem települnek.  
3. Telepíti, és importálja a frissített modulok az aktuális PowerShell-munkamenetbe.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

A korábban telepített verzióinak a modulok a legújabb elérhető verzióra való frissítése előtt eltávolításához használja az Update-AzureRmProfile parancsmag az a **- RemovePreviousVersions** paramétert:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Ez a parancsmag futtatása a következő feladatokat:  

1. Ellenőrzi, hogy ha a megadott API verzió profilt az aktuális hatókör telepített modulok a legújabb verzióra.  
2. Eltávolítja a modulok korábbi verziói, a jelenlegi API-verzióprofil, valamint a jelenlegi PowerShell-munkamenetben.  
4. Telepítse a legújabb verziót kéri.  
5. Telepíti, és importálja a frissített modulok az aktuális PowerShell-munkamenetbe.  
 
## <a name="uninstall-profiles"></a>Távolítsa el a profilok

Használja a **Uninstall-azurermprofile új** parancsmag segítségével távolítsa el a megadott API-verzió profilt.

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>További lépések
* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)
* [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md)  
