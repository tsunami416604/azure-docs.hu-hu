---
title: SQL-adatbázisok használata Azure veremben |} Microsoft Docs
description: Ismerje meg, hogyan telepítheti az SQL-adatbázisok Azure verem és a Gyorsműveletek központi telepítése az SQL Server erőforrás-szolgáltató adapter szolgáltatásként.
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
ms.date: 05/23/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: fd1c2241fe22dc35ceb09e0ba3650fa0000a77b1
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603616"
---
# <a name="update-the-sql-resource-provider-adapter"></a>Az SQL-erőforrás-szolgáltató adapter frissítése
Új SQL-erőforrás szolgáltató adapter előfordulhat, hogy mikorra várható, Azure verem buildek frissítésekor. A meglévő adapter továbbra is működik, de javasolt frissítésére a legújabb buildjével a lehető leghamarabb. Ahhoz telepíteni kell a frissítéseket: verziók nem hagyható ki (a verziók listáját lásd: [telepíteni az erőforrás-szolgáltató Előfeltételek](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)).

Az erőforrás-szolgáltató használata frissíteni a *UpdateSQLProvider.ps1* parancsfájl. A folyamat hasonlít a folyamat egy erőforrás-szolgáltató telepítéséhez használt leírtak szerint a [telepíteni az erőforrás-szolgáltató](.\azure-stack-sql-resource-provider-deploy.md) cikk. A parancsfájl az erőforrás-szolgáltató a letöltés részét képezi.

A *UpdateSQLProvider.ps1* parancsfájlt hoz létre egy új virtuális Gépet a legújabb erőforrás-szolgáltató kódot, és a beállítások áttelepítése a régi virtuális gépről az új virtuális Gépet. Telepítse át a beállításokat adatbázis és a helyet adó kiszolgáló adatait, és a szükséges DNS-rekordja.

A parancsfájl a DeploySqlProvider.ps1 parancsfájl ugyanazokkal az argumentumokkal ismertetett használatát igényli. Adja meg itt tanúsítványt is. 

Azt javasoljuk, hogy töltse le a legújabb Windows Server 2016 Core kép a piactér felügyelet alól. Ha egy frissítés telepítésére van szüksége, elhelyezhet egy. A helyi függőségi elérési MSU-csomagot. Ha egynél több. MSU fájl található, a parancsfájl futtatása sikertelen lesz.

Az alábbiakban látható egy példa a *UpdateSQLProvider.ps1* parancsfájlt, amely a PowerShell-parancssorból futtatható. Győződjön meg arról, a fiók- és igény szerint módosíthatja: 

> [!NOTE]
> A frissítési folyamat csak az integrált rendszerekre vonatkozik.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
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
  -DependencyFilesLocalPath $tempDir\cert
 ```

## <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 parameters
Ezeket a paramétereket is megadhat a parancssorban. Ha nem, vagy bármely paraméter érvényesítése sikertelen, a szükséges paraméterek megadását kéri.

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A felhő rendszergazdájával, a kiemelt végpont eléréséhez szükséges hitelesítő adatait. | _Szükséges_ |
| **AzCredential** | Az Azure-verem szolgáltatás rendszergazdai fiók hitelesítő adatait. Az Azure-verem telepítéséhez használt hitelesítő használja. | _Szükséges_ |
| **VMLocalCredential** | Az SQL erőforrás-szolgáltató VM a helyi rendszergazdai fiók hitelesítő adatait. | _Szükséges_ |
| **PrivilegedEndpoint** | Az IP-cím vagy a kiemelt végpont DNS-nevét. |  _Szükséges_ |
| **DependencyFilesLocalPath** | A .pfx fájl a könyvtárban kell elhelyezni. | _Nem kötelező_ (_kötelező_ több csomópont) |
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava. | _Szükséges_ |
| **MaxRetryCount** | Ennyiszer azt szeretné, majd ismételje meg minden egyes művelet, ha hiba történik.| 2 |
| **RetryDuration** |Az időkorlát másodpercben az újrapróbálkozások között. | 120 |
| **Eltávolítás** | Eltávolítja az erőforrás-szolgáltató és minden kapcsolódó erőforrások (lásd az alábbi megjegyzések). | Nem |
| **DebugMode** | Megakadályozza az automatikus tisztítás hiba esetén. | Nem |


## <a name="next-steps"></a>További lépések

[Az erőforrás-szolgáltató SQL karbantartása](azure-stack-sql-resource-provider-maintain.md)
