---
title: A Key Vault integrálható az SQL Server a Windows virtuális gépek (klasszikus) Azure-ban |} A Microsoft Docs
description: Megtudhatja, hogyan automatizálhatja a konfigurációját az Azure Key Vault használata az SQL Server titkosítását. Ez a témakör ismerteti az Azure Key Vault-integráció használata az SQL Server virtuális gépek létrehozása a klasszikus üzemi modellben.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
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
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e20e2a094e1fd88dfc2a25b586dc6c894f92b418
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312444"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Az SQL Server az Azure Key Vault-integráció konfigurálása Azure-beli virtuális gépeken (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Klasszikus](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Áttekintés
Nincsenek több SQL Server titkosítási funkciók, például [transzparens adattitkosítás (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [oszlop a blokkszintű titkosítás (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), és [biztonsági mentés titkosítása](https://msdn.microsoft.com/library/dn449489.aspx). Ezek az űrlapok titkosítási kezelését és a titkosításhoz használt kriptográfiai kulcsok tárolásához szükséges. Az Azure Key Vault (AKV) szolgáltatás célja a biztonsági és a egy biztonságos és magas rendelkezésre állású helyen ezek a kulcsok kezelését. A [SQL Server-összekötő](https://www.microsoft.com/download/details.aspx?id=45344) lehetővé teszi, hogy ezek a kulcsok Azure Key vault használata az SQL Server.

> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

A helyszíni gépek az SQL Server futtatja, van-e [lépések a helyszíni SQL Server-gép az Azure Key Vault elérése](https://msdn.microsoft.com/library/dn198405.aspx). Az SQL Server Azure virtuális gépeken, időt takaríthat használatával, de a *Azure Key Vault-integráció* funkció. Néhány Azure PowerShell-parancsmagokkal a funkció engedélyezéséhez automatizálhatja a konfiguráció szükséges az SQL virtuális gép hozzáférjen a kulcstartóhoz.

Ez a funkció engedélyezve van, amikor szolgáltatás automatikusan telepíti az SQL Server-összekötő, konfigurálja az EKM-szolgáltató Azure Key Vault elérése érdekében, és létrehozza a hitelesítő adatokat, ezáltal lehetővé teszi a hozzáférést a tárolóhoz. A korábban említett helyszíni dokumentáció lépéseit nézett, láthatja, hogy ez a funkció automatizálja a 2. és 3. Továbbra is kell manuálisan egyedül, ha a key vaulttal és a kulcsokat. Itt az SQL virtuális gép teljes beállítása automatizált. Ez a funkció a telepítés befejezését követően megkezdheti az adatbázisok vagy a biztonsági mentések titkosításához, ahogy azt szokásosan tenné a T-SQL utasításokkal hajthat végre.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>AKV-integráció konfigurálása
Az Azure Key Vault-integráció konfigurálása a PowerShell használatával. A következő szakaszok a szükséges paramétereket, majd egy PowerShell-példaszkript áttekintése.

### <a name="install-the-sql-server-iaas-extension"></a>Az SQL Server IaaS-bővítmény telepítése
Először [telepítse az SQL Server IaaS-bővítményt](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>A bemeneti paraméterek ismertetése
Az alábbi táblázat a következő szakaszban a PowerShell-parancsfájl futtatásához szükséges paramétereket.

| Paraméter | Leírás | Példa |
| --- | --- | --- |
| **$akvURL** |**A kulcstároló URL-címe** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Egyszerű szolgáltatásnév** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Egyszerű szolgáltatás titkos kulcs** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Hitelesítő adat neve**: AKV-integráció az SQL Serverrel, így a virtuális Géphez a key vault eléréséhez hitelesítő adatot hoz létre. Válasszon egy nevet ennek a hitelesítő adatnak. |"mycred1" |
| **$vmName** |**A virtuális gép neve**: Egy korábban létrehozott SQL virtuális gép nevét. |"myvmname" |
| **$serviceName** |**Szolgáltatásnév**: A Felhőszolgáltatás neve, amely az SQL virtuális gép társítva van. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>A PowerShell használatával az AKV-integráció engedélyezése
A **New-AzureVMSqlServerKeyVaultCredentialConfig** parancsmag létrehoz egy konfigurációs objektumot az Azure Key Vault-integráció funkció. A **Set-AzureVMSqlServerExtension** konfigurálja az integrációt a **KeyVaultCredentialSettings** paraméter. A következő lépések bemutatják, hogyan a parancsok használatához.

1. Az Azure PowerShell, először a bemeneti paramétereinek konfigurálása az adott értékek a jelen témakör korábbi szakaszokban ismertetett módon. A következő parancsfájl csak példaként szolgál.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Majd használja a következő parancsfájl konfigurálása és az AKV-integráció engedélyezése.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Az SQL IaaS-ügynök bővítményt a rendszer ekkor frissíti az SQL virtuális gép új konfigurációját.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

