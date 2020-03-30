---
title: Integrálja a Key Vaultot egy klasszikus Azure SQL Server virtuális géptel
description: Megtudhatja, hogyan automatizálhatja az SQL Server titkosításkonfigurációját az Azure Key Vault használatával. Ez a témakör bemutatja, hogyan használhatja az Azure Key Vault-integrációt az SQL Server virtuális gépekkel létrehozott sql server-rendszerrel a klasszikus központi telepítési modellben.
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
ms.openlocfilehash: f878c6f7a59328e2f68ffbaee066bba4a5b6c898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978125"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Az Azure Key Vault-integráció konfigurálása az SQL Server hez az Azure virtuális gépeken (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Klasszikus](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Áttekintés
Az SQL Server titkosítási szolgáltatásai, például [az átlátszó adattitkosítás (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [az oszlopszintű titkosítás (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)és [a biztonsági másolat titkosítása](https://msdn.microsoft.com/library/dn449489.aspx)több. A titkosítás ezen formái megkövetelik a titkosításhoz használt titkosítási kulcsok kezelését és tárolását. Az Azure Key Vault (AKV) szolgáltatás célja, hogy javítsa a biztonságot és a kulcsok felügyeletét egy biztonságos és magas rendelkezésre állású helyen. Az [SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) lehetővé teszi, hogy az SQL Server használja ezeket a kulcsokat az Azure Key Vaultból.

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellt rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Erőforrás-kezelő és a Klasszikus.](../../../azure-resource-manager/management/deployment-models.md) Ez a cikk a klasszikus üzembe helyezési modell használatával ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ha az SQL Servert helyszíni gépekkel futtatja, a [helyszíni SQL Server-gépről az Azure Key Vault eléréséhez számos lépést követhet.](https://msdn.microsoft.com/library/dn198405.aspx) Az Azure-beli virtuális gépeken lévő SQL Server számára azonban időt takaríthat meg az *Azure Key Vault-integráció funkció* használatával. Néhány Azure PowerShell-parancsmaggal a funkció engedélyezéséhez automatizálhatja az SQL virtuális gép számára a key vault eléréséhez szükséges konfigurációt.

Ha ez a funkció engedélyezve van, automatikusan telepíti az SQL Server-összekötőt, konfigurálja az EKM-szolgáltatót az Azure Key Vault eléréséhez, és létrehozza a hitelesítő adatokat, amelyek lehetővé teszik a tároló elérését. Ha a korábban említett helyszíni dokumentációban ismertetett lépéseket is megtekintette, láthatja, hogy ez a funkció automatizálja a 2. Az egyetlen dolog, amit továbbra is manuálisan kell tennie, hogy létrehozza a key vault ot és a kulcsokat. Innen az SQL virtuális gép teljes beállítása automatikus. Miután ez a szolgáltatás befejezte ezt a beállítást, a T-SQL utasítások végrehajtásával megkezdheti az adatbázisok vagy biztonsági mentések titkosítását a szokásos módon.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>AKV-integráció konfigurálása
A PowerShell segítségével konfigurálhatja az Azure Key Vault-integrációt. A következő szakaszok áttekintést nyújtanak a szükséges paramétereket, majd egy minta PowerShell-parancsfájlt.

### <a name="install-the-sql-server-iaas-extension"></a>Az SQL Server IaaS-bővítmény telepítése
Először [telepítse az SQL Server IaaS bővítményt](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>A bemeneti paraméterek megismerése
Az alábbi táblázat felsorolja a PowerShell-parancsfájl futtatásához szükséges paramétereket a következő szakaszban.

| Paraméter | Leírás | Példa |
| --- | --- | --- |
| **$akvURL** |**A kulcstartó URL-címe** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Egyszerű szolgáltatás neve** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Szolgáltatás fő titka** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Hitelesítő adat neve**: Az AKV-integráció létrehoz egy hitelesítő adatot az SQL Serverben, amely hozzáférést biztosít a virtuális gépnek a Key Vaulthoz. Válasszon egy nevet ennek a hitelesítő adatnak. |"mycred1" |
| **$vmName** |**Virtuálisgép neve**: Egy korábban létrehozott SQL virtuális gép neve. |"myvmname" |
| **$serviceName** |**Szolgáltatás neve**: Az SQL virtuális géphez társított felhőszolgáltatás-név. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>AKV-integráció engedélyezése a PowerShelllel
A **New-AzureVMSqlServerKeyVaultCredentialConfig** parancsmag létrehoz egy konfigurációs objektumot az Azure Key Vault-integrációszolgáltatáshoz. A **Set-AzureVMSqlServerExtension** konfigurálja ezt az integrációt a **KeyVaultCredentialSettings** paraméterrel. Az alábbi lépések bemutatják, hogyan kell használni ezeket a parancsokat.

1. Az Azure PowerShellben először konfigurálja a bemeneti paramétereket a témakör előző szakaszaiban ismertetett értékekhez. A következő parancsfájl egy példa.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Ezután a következő parancsfájl segítségével konfigurálja és engedélyezze az AKV-integrációt.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Az SQL IaaS Agent Extension ezzel az új konfigurációval frissíti az SQL virtuális gép.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

