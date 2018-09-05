---
title: Az Azure Stack SQL erőforrás-szolgáltató frissítése |} A Microsoft Docs
description: Ismerje meg, hogyan frissítheti az Azure Stack SQL erőforrás-szolgáltató.
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
ms.openlocfilehash: 017ac3214046b812ce003ab7a190f2f2b3f4f2e2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697941"
---
# <a name="update-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató frissítése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek.*

Azure Stack egy új létrehozást való frissítésekor előfordulhat, hogy elérhető egy új SQL erőforrás-szolgáltató. Bár a meglévő adapter továbbra is működik, javasoljuk, hogy frissítse a legújabb buildre minél hamarabb.

>[!IMPORTANT]
>A már kiadott sorrendben frissítéseket telepíteni kell. Verziók nem hagyhatja ki. A verziók listáját lásd [üzembe helyezése az erőforrás-szolgáltatóra vonatkozó Előfeltételek](.\azure-stack-sql-resource-provider-deploy.md#prerequisites).

## <a name="overview"></a>Áttekintés

Az erőforrás-szolgáltató frissítéséhez használja a *UpdateSQLProvider.ps1* parancsfájlt. Ez a szkript az új SQL erőforrás-szolgáltató a letöltés részét képezi. A frissítési folyamat hasonlít a folyamat, amellyel [az erőforrás-szolgáltató üzembe helyezése](.\azure-stack-sql-resource-provider-deploy.md). A frissítési parancsfájl ugyanazon argumentumokat használja DeploySqlProvider.ps1 szkriptet, és meg kell adnia a tanúsítvány adatait.

### <a name="update-script-processes"></a>Frissítési parancsfájl folyamatok

A *UpdateSQLProvider.ps1* a szkript létrehoz egy új virtuális gépet (VM) a legújabb erőforrás-szolgáltató kóddal.

>[!NOTE]
>Azt javasoljuk, hogy le kell tölteni a legújabb Windows Server 2016 Core lemezképet a Marketplace-kezelés. Ha szeretne telepíteni egy frissítést, elhelyezhet egy **egyetlen** MSU-csomagot a helyi függőségi útvonalát. A parancsfájl futtatása sikertelen lesz, ha egynél több MSU-fájlt ezen a helyen.

Miután a *UpdateSQLProvider.ps1* a szkript létrehoz egy új virtuális Gépet, a szkript a következő beállításokat áttelepíti a virtuális gép régi szolgáltató:

* adatbázis-információ
* üzemeltető kiszolgáló adatai
* szükséges DNS-rekord

### <a name="update-script-powershell-example"></a>PowerShell példaszkript frissítése

Szerkesztheti, és futtassa a következő parancsfájl egy rendszergazda jogú PowerShell ISE-ben. Ne felejtse el módosítani a fiók adatait és a jelszavakat, mint a saját környezetéhez szükséges.

> [!NOTE]
> A frissítési folyamat csak az Azure Stack integrált rendszerek vonatkozik.

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

A parancssorból a következő paramétereket is megadhat, a parancsfájl futtatásakor. Ha nem, vagy ha minden paraméter ellenőrzése sikertelen, kéri, hogy adja meg a szükséges paramétereket.

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A felhő rendszergazdájához, a kiemelt végponthoz eléréséhez szükséges hitelesítő adatait. | _Szükséges_ |
| **AzCredential** | Az Azure Stack szolgáltatás-rendszergazdai fiók hitelesítő adatait. Használja az Azure Stack üzembe helyezéséhez használt hitelesítő adatokkal. | _Szükséges_ |
| **VMLocalCredential** | Az SQL-erőforrás-szolgáltató virtuális gép helyi rendszergazdai fiókjának hitelesítő adatait. | _Szükséges_ |
| **PrivilegedEndpoint** | Az IP-cím vagy a kiemelt végponthoz DNS-nevét. |  _Szükséges_ |
| **AzureEnvironment** | Az azure-környezethez az Azure Stack üzembe helyezéséhez használt szolgáltatás-rendszergazdai fiókot. Akkor kötelező, ha be nem ADFS. Támogatott környezeti nevek **AzureCloud**, **AzureUSGovernment**, vagy ha az China Azure Active Directoryval, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | A tanúsítvány .pfx fájlját is ebben a könyvtárban kell helyezni. | _Az egyetlen csomópont, de a kötelező a több csomópontos nem kötelező megadni_ |
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava. | _Szükséges_ |
| **MaxRetryCount** | Többször ismételje meg minden művelet, ha sikertelen egy kívánt száma.| 2 |
| **RetryDuration** |Az időkorlát között eltelő időt másodpercben mérve. | 120 |
| **Eltávolítás** | Eltávolítja az erőforrás-szolgáltató és az összes társított erőforrást. | Nem |
| **DebugMode** | Megakadályozza, hogy hiba esetén az automatikus tisztítás. | Nem |

## <a name="next-steps"></a>További lépések

[Az SQL erőforrás-szolgáltató kezelése](azure-stack-sql-resource-provider-maintain.md)
