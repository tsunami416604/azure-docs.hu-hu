---
title: Az Azure Stack MySQL erőforrás-szolgáltató frissítése |} A Microsoft Docs
description: Ismerje meg, hogyan frissítheti az Azure Stack MySQL erőforrás-szolgáltató.
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
ms.date: 09/04/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: fb9f022f0af821d81e5b61b99ecb52b7f7151b5f
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391555"
---
# <a name="update-the-mysql-resource-provider"></a>A MySQL erőforrás-szolgáltató frissítése 

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek.*

Azure Stack-buildek frissítésekor előfordulhat, hogy elérhető egy új SQL erőforrás-szolgáltató adapter. A meglévő adapter továbbra is működik, javasoljuk, hogy frissítse a legújabb buildre minél hamarabb. 

>[!IMPORTANT]
>A már kiadott sorrendben frissítéseket telepíteni kell. Verziók nem hagyhatja ki. A verziók listáját lásd [üzembe helyezése az erőforrás-szolgáltatóra vonatkozó Előfeltételek](.\azure-stack-mysql-resource-provider-deploy.md#prerequisites).

## <a name="update-the-mysql-resource-provider-adapter-integrated-systems-only"></a>Frissítés a MySQL erőforrás-szolgáltató adapter (csak az integrált rendszerek)

Azure Stack-buildek frissítésekor előfordulhat, hogy elérhető egy új SQL erőforrás-szolgáltató adapter. A meglévő adapter továbbra is működik, javasoljuk, hogy frissítse a legújabb buildre minél hamarabb.  
 
Frissíteni az erőforrás-szolgáltatót használ, a **UpdateMySQLProvider.ps1** parancsfájlt. A folyamat egy erőforrás-szolgáltató telepítéséhez használt leírtak szerint a folyamat hasonlít a [az erőforrás-szolgáltató üzembe helyezése](#deploy-the-resource-provider) című szakaszát. A parancsfájl az erőforrás-szolgáltató a letöltés részét képezi. 

A **UpdateMySQLProvider.ps1** parancsfájl egy új virtuális Gépet hoz létre az erőforrás-szolgáltató legújabb kódját, és a beállításokat áttelepíti a régi virtuális gépről az új virtuális gépre. A beállításokat, amelyeket át közé tartozik az adatbázis és az üzemeltetési kiszolgáló adatait, és a szükséges DNS-bejegyzést. 

>[!NOTE]
>Azt javasoljuk, hogy le kell tölteni a legújabb Windows Server 2016 Core lemezképet a Marketplace-kezelés. Ha szeretne telepíteni egy frissítést, elhelyezhet egy **egyetlen** MSU-csomagot a helyi függőségi útvonalát. A parancsfájl futtatása sikertelen lesz, ha egynél több MSU-fájlt ezen a helyen.

>[!NOTE]  
> 

A szkript a DeployMySqlProvider.ps1 parancsfájl ismertetett argumentumokkal használatát igényli. A tanúsítvány itt is biztosítanak.  

Az alábbiakban egy példát a a *UpdateMySQLProvider.ps1* parancsfájlt, amely a PowerShell-parancssorban futtathatja. Ügyeljen arra, hogy a fiók- és igény szerint módosíthatja:  

> [!NOTE] 
> A frissítési folyamat csak integrált rendszerek vonatkozik. 

```powershell 
# Install the AzureRM.Bootstrapper module and set the profile. 
Install-Module -Name AzureRm.BootStrapper -Force 
Use-AzureRmProfile -Profile 2017-03-09-profile 

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM. 
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access. 
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate. 
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
 
# Change directory to the folder where you extracted the installation files. 
# Then adjust the endpoints. 
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
``` 
 
### <a name="updatemysqlproviderps1-parameters"></a>UpdateMySQLProvider.ps1 paraméterek 
Ezeket a paramétereket is megadhat a parancssorban. Ha nem, vagy ha minden paraméter ellenőrzése sikertelen, a rendszer kéri, adja meg a szükséges paramétereket. 

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték | 
| --- | --- | --- | 
| **CloudAdminCredential** | A felhő rendszergazdájához, a kiemelt végponthoz eléréséhez szükséges hitelesítő adatait. | _Szükséges_ | 
| **AzCredential** | Az Azure Stack szolgáltatás-rendszergazdai fiók hitelesítő adatait. Használja ugyanazokat a hitelesítő adatokat az Azure Stack üzembe helyezéséhez használt. | _Szükséges_ | 
| **VMLocalCredential** |Az SQL-erőforrás-szolgáltató virtuális gép helyi rendszergazdai fiókjának hitelesítő adatait. | _Szükséges_ | 
| **PrivilegedEndpoint** | Az IP-cím vagy a kiemelt végponthoz DNS-nevét. |  _Szükséges_ | 
| **AzureEnvironment** | Az azure-környezethez az Azure Stack üzembe helyezéséhez használt szolgáltatás-rendszergazdai fiókot. Akkor kötelező, ha be nem ADFS. Támogatott környezeti nevek **AzureCloud**, **AzureUSGovernment**, vagy ha az China Azure Active Directoryval, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | A tanúsítvány .pfx fájlját a könyvtárban kell elhelyezni. | _Nem kötelező_ (_kötelező_ több csomópont) | 
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava. | _Szükséges_ | 
| **MaxRetryCount** | Többször ismételje meg minden művelet, ha sikertelen egy kívánt száma.| 2 | 
| **RetryDuration** | Az időkorlát között eltelő időt másodpercben mérve. | 120 | 
| **Eltávolítás** | Távolítsa el az erőforrás-szolgáltató és az összes társított erőforrást (lásd az alábbi megjegyzések). | Nem | 
| **DebugMode** | Megakadályozza, hogy hiba esetén az automatikus tisztítás. | Nem | 
| **AcceptLicense** | A rendszer kéri, fogadja el a GPL-licenc kihagyja.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 
 

## <a name="next-steps"></a>További lépések
[MySQL típusú erőforrás-szolgáltató kezelése](azure-stack-mysql-resource-provider-maintain.md)
