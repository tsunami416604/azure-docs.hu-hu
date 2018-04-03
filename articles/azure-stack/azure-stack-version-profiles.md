---
title: Azure verem API verziója profilok használatával |} Microsoft Docs
description: További információk a API verziója profilok Azure-készletben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 68f4250c2a2a6bed1a1e21dc444e93cc87b6f59b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Azure verem API-verzió profilok kezelése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

API-verzió profilok teszik lehetővé az Azure és az Azure-verem közötti kezelése. Az API-verzió profilok olyan AzureRM PowerShell modult az adott API-verziók. Minden egyes felhő platform támogatott API-verzió profilok rendelkezik. Például a Azure verem használható egy adott dátummal Profilverzió például **2017-03-09-profil**, és Azure támogatja a **legújabb** API-verzió profil. A profil telepítése után, amikor a AzureRM PowerShell-modulok, amelyek megfelelnek a megadott profil telepítve vannak.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>API-verzió profilok használatához szükséges PowerShell moduljának telepítése

A **AzureRM.Bootstrapper** modult, amelyben a PowerShell-galériában keresztül elérhető API-verzió profilokkal működéséhez szükséges PowerShell-parancsmagokat kínál. AzureRM.Bootstrapper-modul telepítéséhez használja a következő parancsmagot:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
A AzureRM.Bootstrapper modul jelenleg előzetes verzióban érhető; Részletek és a funkciók van változhatnak. Töltse le és telepítse a legújabb verziót a modul a PowerShell-galériából, futtassa a következő parancsmagot:

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>Profil telepítése

Használja a **Install-AzureRmProfile** parancsmagot a **2017-03-09-profil** API-verzió profilt Azure verem szükséges AzureRM-modulok telepítése. Vegye figyelembe, hogy az Azure-verem operátor modulok nincsenek telepítve az API-verzió profillal, és akkor kell telepíteni, külön-külön meghatározott a 3. lépését a [verem Azure PowerShell telepítése](azure-stack-powershell-install.md) cikk.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Telepítse és egy modul importálása

Használja a **használata-AzureRmProfile** parancsmag telepítéséhez és az API-verzió profilhoz társított modulok importálásához. Csak egy API-verzió profil importálhatja egy PowerShell-munkamenetben. Különböző API-verzió profil importálása, egy új PowerShell-munkamenetet kell megnyitni. A használat-AzureRMProfile parancsmag futtatásakor a következő feladatokat:  
1. Ellenőrzi, hogy ha a megadott API-verzió profilhoz tartozó PowerShell-modul telepítve van az aktuális hatókörben.  
2. Letölti és telepíti a modult, ha még nincs telepítve.   
3. A modulok importálása a jelenlegi PowerShell-munkamenetben. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

Telepítéséhez, és kijelölt AzureRM modulok importálása az API-verzió profilt, futtassa a parancsmagot használja-AzureRMProfile a **modul** paraméter:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>A telepített profilok beolvasása

Használja a **Get-AzureRmProfile** parancsmag elérhető API-verzió profilok listájának lekérdezése: 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Frissítés profilok

Használja a **frissítés-AzureRmProfile** parancsmaggal frissítsen az API-verzió profilban modulok által biztosított a PSGallery modulok legújabb verzióját. Mindig futtatásához javasoljuk a **frissítés-AzureRmProfile** parancsmag egy új PowerShell-munkamenetben a modulok importálása során ütközések elkerülése érdekében. A frissítés-AzureRmProfile parancsmag futtatásakor a következő feladatokat:

1. Ellenőrzi, hogy ha a modulok legfrissebb verziója telepítve van az adott API verziója profil az aktuális hatókörben.  
2. Kéri, hogy a telepítés, ha még nincs telepítve.  
3. Telepíti, és a frissített modulok importálása a jelenlegi PowerShell-munkamenetben.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Az elérhető legújabb verzióra való frissítés előtt a modulok korábban telepített verzióinak eltávolításához használja a frissítés-AzureRmProfile parancsmag, valamint a **- RemovePreviousVersions** paraméter:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Ez a parancsmag futtatásakor a következő feladatokat:  

1. Ellenőrzi, hogy ha a modulok legfrissebb verziója telepítve van az adott API verziója profil az aktuális hatókörben.  
2. Eltávolítja a modulok régebbi verzióit, a jelenlegi API-verzió profil, valamint a jelenlegi PowerShell-munkamenetben.  
4. Telepítse a legújabb verziót kéri.  
5. Telepíti, és a frissített modulok importálása a jelenlegi PowerShell-munkamenetben.  
 
## <a name="uninstall-profiles"></a>Távolítsa el a profilok

Használja a **eltávolítás-AzureRmProfile** parancsmag segítségével távolítsa el a megadott API-verzió profilt.

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>További lépések
* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)
* [Az Azure-verem felhasználói PowerShell környezet konfigurálása](user/azure-stack-powershell-configure-user.md)  
