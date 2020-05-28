---
title: Key Vault integrálása klasszikus Azure-SQL Server VM
description: Megtudhatja, hogyan automatizálhatja SQL Server titkosítás konfigurációját Azure Key Vault használatával. Ez a témakör azt ismerteti, hogyan használható a Azure Key Vault integráció a SQL Server Virtual Machines szolgáltatással a klasszikus üzemi modellben.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6c84be9a23713080f348daf8dddf0ad6b0390ded
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014697"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Azure Key Vault integráció konfigurálása SQL Server Azure-beli Virtual Machines (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/azure-key-vault-integration-configure.md)
> * [Klasszikus](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Áttekintés
Több SQL Server titkosítási funkció, például [transzparens adattitkosítás (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), az [oszlop szintű titkosítás (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)és a [biztonsági másolatok titkosítása](https://msdn.microsoft.com/library/dn449489.aspx). Ezek a titkosítási formák a titkosításhoz használt titkosítási kulcsok felügyeletét és tárolását igénylik. A Azure Key Vault (AKV) szolgáltatás úgy lett kialakítva, hogy javítsa a kulcsok biztonságát és felügyeletét egy biztonságos és magasan elérhető helyen. A [SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) lehetővé teszi, hogy a SQL Server ezeket a kulcsokat a Azure Key Vault használatával használhassa.

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/management/deployment-models.md). Ez a cikk a klasszikus üzembe helyezési modell használatát ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ha a SQL Servert a helyszíni gépekkel futtatja, akkor a helyszíni [SQL Server számítógépéről elérhető lépéseket követve elérheti a Azure Key Vault](https://msdn.microsoft.com/library/dn198405.aspx). Az Azure-beli virtuális gépek SQL Server azonban időt takaríthat meg a *Azure Key Vault integrációs* funkciójával. Néhány Azure PowerShell parancsmaggal engedélyezheti ezt a funkciót, automatizálhatja az SQL-alapú virtuális gépekhez szükséges konfigurációt a kulcstartó eléréséhez.

Ha ez a szolgáltatás engedélyezve van, a automatikusan telepíti a SQL Server Connector, konfigurálja a EKM-szolgáltatót a Azure Key Vault eléréséhez, és létrehozza a hitelesítő adatokat, amely lehetővé teszi a tár elérését. Ha a korábban említett helyszíni dokumentáció lépéseit kereste, láthatja, hogy ez a funkció automatizálja a 2. és a 3. lépést. A Key Vault és a kulcsok létrehozásához az egyetlen dolog, amit manuálisan kell elvégezni. Innentől kezdve az SQL-alapú virtuális gép teljes telepítése automatizálható. Miután a funkció befejezte a telepítést, a T-SQL-utasítások végrehajtásával megkezdheti az adatbázisok vagy biztonsági másolatok titkosítását, ahogy azt a szokásos módon tenné.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>AKV-integráció konfigurálása
Azure Key Vault integráció konfigurálásához használja a PowerShellt. A következő szakaszokban áttekintheti a szükséges paramétereket, majd egy minta PowerShell-parancsfájlt.

### <a name="install-the-sql-server-iaas-extension"></a>A SQL Server IaaS bővítmény telepítése
Először [telepítse a SQL Server IaaS bővítményt](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>A bemeneti paraméterek ismertetése
A következő táblázat a PowerShell-parancsfájl következő szakaszban való futtatásához szükséges paramétereket sorolja fel.

| Paraméter | Leírás | Példa |
| --- | --- | --- |
| **$akvURL** |**A Key Vault URL-címe** |"https: \/ /contosokeyvault.Vault.Azure.net/" |
| **$spName** |**Egyszerű szolgáltatásnév** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Egyszerű szolgáltatás titka** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =" |
| **$credName** |**Hitelesítő adat neve**: Az AKV-integráció létrehoz egy hitelesítő adatot az SQL Serverben, amely hozzáférést biztosít a virtuális gépnek a Key Vaulthoz. Válasszon egy nevet ennek a hitelesítő adatnak. |"mycred1" |
| **$vmName** |**Virtuális gép neve**: egy korábban létrehozott SQL virtuális gép neve. |"myvmname" |
| **$serviceName** |**Szolgáltatás neve**: az SQL virtuális géphez társított felhőalapú szolgáltatás neve. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>AKV-integráció engedélyezése a PowerShell-lel
A **New-AzureVMSqlServerKeyVaultCredentialConfig** parancsmag egy konfigurációs objektumot hoz létre a Azure Key Vault integrációs szolgáltatáshoz. A **set-AzureVMSqlServerExtension** ezt az integrációt a **KeyVaultCredentialSettings** paraméterrel konfigurálja. A következő lépések bemutatják, hogyan használhatja ezeket a parancsokat.

1. A Azure PowerShellban először konfigurálja a bemeneti paramétereket az adott értékekkel a témakör előző részeiben leírtak szerint. A következő szkript egy példa.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Ezután használja a következő szkriptet a AKV-integráció konfigurálásához és engedélyezéséhez.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Az SQL IaaS-ügynök bővítménye frissíteni fogja az SQL virtuális gépet ezzel az új konfigurációval.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

