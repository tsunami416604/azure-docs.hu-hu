---
title: Kulcstároló integrálható az SQL Server rendszerhez a Windows-alapú virtuális gépek (klasszikus) Azure-ban |} Microsoft Docs
description: Megtudhatja, hogyan használható az Azure Key Vault SQL Server titkosítási konfigurálásának automatizálásához. Ez a témakör ismerteti az Azure Key Vault-integráció használata az SQL Server virtuális gépek létrehozása a klasszikus üzembe helyezési modellben.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5fd0fb1f8ac9bb0132c64c195d4cc9c86ef8edd0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399729"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>SQL Server az Azure Key Vault-integráció konfigurálása az Azure virtuális gépeken (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Klasszikus](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Áttekintés
Nincsenek több SQL Server titkosítási szolgáltatások, például a [átlátható adattitkosítás (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [oszlop a blokkszintű titkosítás (törlése)](https://msdn.microsoft.com/library/ms173744.aspx), és [a biztonsági másolat titkosításához](https://msdn.microsoft.com/library/dn449489.aspx). Titkosítási űrlapok kell kezelése és a titkosításhoz használt kriptográfiai kulcsok tárolásához. Az Azure Key Vault (AKV) szolgáltatás szolgáltatást javítására készült, a biztonsági és kezelésére, ezek a kulcsok egy magas rendelkezésre állású és biztonságos helyen. A [SQL Server-összekötő](http://www.microsoft.com/download/details.aspx?id=45344) lehetővé teszi, hogy ezek a kulcsok Azure Key Vault a használni kívánt SQL Server.

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

SQL Server helyszíni gépeken futtatja, hogy van-e [lépések követésével hozzáférni a helyszíni SQL Server-számítógépen az Azure Key Vault](https://msdn.microsoft.com/library/dn198405.aspx). Az SQL Server Azure virtuális gépeken, időt takaríthat meg használatával, de a *Azure Key Vault-integráció* szolgáltatás. A néhány Azure PowerShell-parancsmagokkal a funkció engedélyezéséhez az SQL virtuális gép a kulcstároló eléréséhez szükséges konfigurációs automatizálható.

Ha ez a funkció engedélyezve van, automatikusan telepíti az SQL Server-összekötő, konfigurálja az Azure Key Vault eléréséhez EKM-szolgáltatóval, és lehetővé teszi a tároló eléréséhez a hitelesítő adatot hoz létre. A korábban említett helyszíni ismertetettek nézett, láthatja, hogy ez a szolgáltatás automatizálja a 2. és 3. Még mindig manuálisan kell egyedül, ha a kulcstartó és a kulcsokat. Ott a teljes telepítése az SQL virtuális gép automatikus. Ez a funkció a telepítés befejezése után hajthat végre T-SQL utasítást, hogy az adatbázis vagy a biztonsági mentések titkosításához, a szokásos módon megkezdéséhez.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>AKV-integráció konfigurálása
A PowerShell szolgáltatás használatával az Azure Key Vault-integráció konfigurálása. Az alábbi szakaszok a szükséges paramétereket, majd egy PowerShell-parancsfájlpélda áttekintés biztosítása.

### <a name="install-the-sql-server-iaas-extension"></a>Az SQL Server IaaS-bővítményének telepítése
Első, [az SQL Server IaaS-bővítményének telepítése](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>A bemeneti paraméterek ismertetése
A következő táblázat a következő szakaszban a PowerShell parancsfájl futtatásához szükséges paraméterek.

| Paraméter | Leírás | Példa |
| --- | --- | --- |
| **$akvURL** |**A kulcstároló URL-címe** |"https://contosokeyvault.vault.azure.net/" |
| **$spName** |**Egyszerű szolgáltatásnév** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Egyszerű titok** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Hitelesítő adat neve**: Az AKV-integráció létrehoz egy hitelesítő adatot az SQL Serverben, amely hozzáférést biztosít a virtuális gépnek a Key Vaulthoz. Válasszon egy nevet ennek a hitelesítő adatnak. |"mycred1" |
| **$vmName** |**Virtuális gép neve**: egy korábban létrehozott SQL virtuális gép nevét. |"myvmname" |
| **$serviceName** |**Szolgáltatásnév**: SQL virtuális gép társított a Felhőszolgáltatás neve. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>A PowerShell-lel AKV-integráció engedélyezése
A **New-AzureVMSqlServerKeyVaultCredentialConfig** parancsmag létrehoz egy konfigurációs objektumot az Azure Key Vault-integráció szolgáltatás számára. A **Set-AzureVMSqlServerExtension** konfigurálja az integrációt a **KeyVaultCredentialSettings** paraméter. A következő lépések bemutatják, hogyan ezen parancsok használatával.

1. Azure PowerShell először konfigurálja a bemeneti paraméterek az adott értékek a jelen témakör korábbi szakaszokban ismertetett módon. A következő parancsfájl csak példaként szolgál.
   
        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. A következő parancsfájl segítségével konfigurálhatja és engedélyezheti az AKV-integráció.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Az SQL-infrastruktúra-szolgáltatási ügynök bővítmény frissíti az SQL virtuális gép ezen új konfigurációjával.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

