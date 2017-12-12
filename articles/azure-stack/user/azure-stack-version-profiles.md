---
title: "Azure verem API-verzió profilok kezelése |} Microsoft Docs"
description: "További tudnivalók az Azure verem API verziója profilok"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6B749785-DCF5-4AD8-B808-982E7C6BBA0E
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: mabrigg
ms.openlocfilehash: d86a54ea9e165269131eb961df7f74703f0ec6ff
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Azure verem API-verzió profilok kezelése

Az API szolgáltatását, az Azure App Service verzió profilok kezelése az Azure és az Azure-verem közötti lehetőséget biztosít. Az API-verzió profilok olyan AzureRM PowerShell modult az adott API-verziók. Minden egyes felhő platform támogatott API-verzió profilok rendelkezik. Például a Azure verem használható egy adott, dátummal Profilverzió, például a **2017-03-09-profil**, és Azure támogatja a *legújabb* API-verzió profil. A profil telepítése után, amikor a AzureRM PowerShell-modulok, amelyek megfelelnek a megadott profil telepítve vannak.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>API-verzió profilok használatához szükséges PowerShell moduljának telepítése

A **AzureRM.Bootstrapper** modult, amelyben a PowerShell-galériában keresztül elérhető API-verzió profilokkal működéséhez szükséges PowerShell-parancsmagokat kínál. A következő parancsmag segítségével telepítse a **AzureRM.Bootstrapper** modul:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
A **AzureRM.Bootstrapper** modul az előnézet, úgy, hogy a részletek és a funkciók változhatnak. Töltse le és telepítse a legújabb verziót a modul a PowerShell-galériából, futtassa a következő parancsmagot:

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>Profil telepítése

Használja a **Install-AzureRmProfile** parancsmagot a **2017-03-09-profil** API-verzió profilt Azure verem szükséges AzureRM-modulok telepítése. 

>[!NOTE]
>Az Azure-verem felhő rendszergazda modulok nincsenek telepítve az API-verzió profillal. A rendszergazda modulok telepítse külön-külön meghatározott 3. lépésében a [verem Azure PowerShell telepítése](azure-stack-powershell-install.md) cikk.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Telepítse és egy modul importálása

Használja a **használata-AzureRmProfile** parancsmag telepítéséhez és az API-verzió profilhoz társított modulok importálásához. Csak egy API-verzió profil importálhatja egy PowerShell-munkamenetben. Különböző API-verzió profil importálása, egy új PowerShell-munkamenetet kell megnyitni. A **használata-AzureRMProfile** parancsmag futtatásakor a következő feladatokat:  
1. Ellenőrzi, hogy ha a megadott API-verzió profilhoz tartozó PowerShell-modul telepítve van az aktuális hatókörben.  
2. Letölti és telepíti a modult, ha még nincs telepítve.   
3. A modulok importálása a jelenlegi PowerShell-munkamenetben. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

Telepítse, és kijelölt AzureRM modulok importálása az API-verzió profilt, futtassa a **használata-AzureRMProfile** parancsmagot a *modul* paraméter:

```PowerShell
# Installs and imports the Compute, Storage, and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>A telepített profilok beolvasása

Használja a **Get-AzureRmProfile** parancsmag elérhető API-verzió profilok listájának lekérdezése: 

```PowerShell
# Lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# Lists the API version profiles that are installed on your machine.
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Frissítés profilok

Használja a **frissítés-AzureRmProfile** parancsmaggal frissítsen az API-verzió profilban modulok modulok, a PowerShell-galériában rendelkezésre álló legújabb verzióját. Azt javasoljuk, hogy a **frissítés-AzureRmProfile** parancsmag egy új PowerShell-munkamenetben modulok importálásakor ütközések elkerülése érdekében. A **frissítés-AzureRmProfile** parancsmag futtatásakor a következő feladatokat:

1. Ellenőrzi, hogy ha a modulok a legfrissebb verziója telepítve van az adott API verziója profil az aktuális hatókörben.  
2. Kéri, hogy telepítse a szükséges modulokat, ha még nincs telepítve.  
3. Telepíti, és a frissített modulok importálása a jelenlegi PowerShell-munkamenetben.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Az elérhető legújabb verzióra való frissítéséhez a modulok korábban telepített verzióinak eltávolításához használja a **frissítés-AzureRmProfile** parancsmaggal együtt a *- RemovePreviousVersions* a paraméter:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Ez a parancsmag futtatásakor a következő feladatokat:  

1. Ellenőrzi, hogy ha a modulok a legfrissebb verziója telepítve van az adott API verziója profil az aktuális hatókörben.  
2. Eltávolítja a modulok régebbi verzióit, a jelenlegi API-verzió profil, valamint a jelenlegi PowerShell-munkamenetben.  
3. Telepítse a legújabb verzióját a modulok kéri.  
4. Telepíti, és a frissített modulok importálása a jelenlegi PowerShell-munkamenetben.  
 
## <a name="uninstall-profiles"></a>Távolítsa el a profilok

Használja a **eltávolítás-AzureRmProfile** parancsmag segítségével távolítsa el a megadott API-verzió profil:

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>Következő lépések
* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)
* [Az Azure-verem felhasználói PowerShell környezet konfigurálása](azure-stack-powershell-configure-user.md)  
