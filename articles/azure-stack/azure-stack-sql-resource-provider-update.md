---
title: A verem SQL Azure erőforrás-szolgáltató frissítése |} Microsoft Docs
description: Ismerje meg, hogy miként frissítheti az Azure verem SQL erőforrás-szolgáltató.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: ac5073d1abc32b7598a869750f9c5a801559e9e6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264077"
---
# <a name="update-the-sql-resource-provider"></a>Frissítés az SQL erőforrás-szolgáltató

*A következőkre vonatkozik: Azure verem integrált rendszerek.*

Új SQL erőforrás-szolgáltató lehet, hogy mikorra várható, Azure verem új buildverziót frissítésekor. Bár a meglévő adapter folytatja a működést, javasoljuk, minél hamarabb frissítése a legújabb buildjével.

>[!IMPORTANT]
>Ahhoz, azok által kiadott frissítéseket kell telepíteni. Verziók nem hagyható ki. Tekintse meg a verziók listájában [telepíteni az erőforrás-szolgáltató Előfeltételek](.\azure-stack-sql-resource-provider-deploy.md#prerequisites).

## <a name="overview"></a>Áttekintés

Az erőforrás-szolgáltató frissítéséhez használja a *UpdateSQLProvider.ps1* parancsfájl. Ezt a parancsfájlt az új SQL erőforrás-szolgáltató a letöltés részét képezi. A frissítési folyamat hasonlít a folyamat, amellyel [telepíteni az erőforrás-szolgáltató](.\azure-stack-sql-resource-provider-deploy.md). A frissítés parancsfájl ugyanazokkal az argumentumokkal használja, mint a DeploySqlProvider.ps1 parancsfájl, és a tanúsítvány információk megadására lesz szüksége.

### <a name="update-script-processes"></a>Frissítési parancsprogram folyamatok

A *UpdateSQLProvider.ps1* parancsfájlt hoz létre egy új virtuális gép (VM) az erőforrás-szolgáltató legújabb kódját.

>[!NOTE]
>Azt javasoljuk, hogy töltse le a legújabb Windows Server 2016 Core kép a piactér felügyelet alól. Ha egy frissítés telepítésére van szüksége, elhelyezhet egy **egyetlen** MSU csomagot a helyi függőségi elérési úton. A parancsfájl futtatása sikertelen lesz, ha egynél több MSU fájl ezen a helyen.

Miután a *UpdateSQLProvider.ps1* parancsfájlt hoz létre egy új virtuális Gépet, a parancsfájl a következő beállításokat áttelepíti a virtuális gép régi szolgáltató:

* Adatbázis-információ
* üzemeltetési kiszolgáló adatait
* szükséges DNS-rekord

### <a name="update-script-powershell-example"></a>PowerShell-mintaparancsfájl frissítése

Szerkesztheti, és futtassa a következő parancsfájl egy rendszergazda jogú PowerShell ISE. Ne felejtse el módosítani a fiók- és a saját környezetéhez szükséges módon.

> [!NOTE]
> A frissítési folyamat csak az integrált Azure verem rendszerekre vonatkozik.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `

 ```

## <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 parameters

A parancssorból a következő paramétereket is megadhat, a parancsfájl futtatásakor. Ha ezt elmulasztja, vagy bármely paraméter-ellenőrzés sikertelen, a program kéri, a szükséges paraméterek megadásához.

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A felhő rendszergazdájával, a kiemelt végpont eléréséhez szükséges hitelesítő adatait. | _Szükséges_ |
| **AzCredential** | A verem Azure szolgáltatás-rendszergazdai fiók hitelesítő adatait. Az Azure-verem telepítéséhez használt hitelesítő használja. | _Szükséges_ |
| **VMLocalCredential** | Az SQL erőforrás-szolgáltató VM a helyi rendszergazdai fiók hitelesítő adatait. | _Szükséges_ |
| **PrivilegedEndpoint** | Az IP-cím vagy a kiemelt végpont DNS-nevét. |  _Szükséges_ |
| **DependencyFilesLocalPath** | A tanúsítvány .pfx fájlját is ebben a könyvtárban kell helyezni. | _Egyetlen csomópont azonban kötelező több csomópontos a nem kötelező_ |
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava. | _Szükséges_ |
| **MaxRetryCount** | Ennyiszer azt szeretné, majd ismételje meg minden egyes művelet, ha hiba történik.| 2 |
| **RetryDuration** |Az időkorlát másodpercben az újrapróbálkozások között. | 120 |
| **Eltávolítás** | Eltávolítja az erőforrás-szolgáltató és minden kapcsolódó erőforrásokat. | Nem |
| **DebugMode** | Megakadályozza az automatikus tisztítás hiba esetén. | Nem |

## <a name="next-steps"></a>További lépések

[Az erőforrás-szolgáltató SQL karbantartása](azure-stack-sql-resource-provider-maintain.md)
