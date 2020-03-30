---
title: Virtuális gép üzembe helyezése az Azure Piactérről
description: Bemutatja, hogyan telepíthet egy virtuális gépet egy előre konfigurált virtuális gépről az Azure Marketplace-ről.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 7d5269cf8865faeb65356bc8fd3eea087cb7653c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277973"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Virtuális gép üzembe helyezése az Azure Piactérről

Ez a cikk bemutatja, hogyan telepíthet egy előre konfigurált virtuális gépet (VM) egy Azure Piactérről a megadott Azure PowerShell-parancsfájl használatával.  Ez a parancsfájl a virtuális gép WinRM HTTP- és HTTPS-végpontjait is elérhetővé teszi.  A parancsfájl megköveteli, hogy már rendelkezik egy tanúsítványt feltölteni az Azure Key Vault, ahogy az Azure Key Vault létrehozása című dokumentumban [leírtak](./cpp-create-key-vault-cert.md)szerint. 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Virtuális gép telepítési sablonja

A rövid útmutató Az Azure VM központi telepítési sablon érhető el az online fájl [azuredeploy.json.](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json)  A következő paramétereket tartalmazza:

|  **Paraméter**        |   **Leírás**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | A tárfiók neve                       |
| dnsNameForPublicIP    | A nyilvános IP DNS-neve. Kisbetűsnek kell lennie.    |
| adminUserName         | Rendszergazda felhasználóneve                          |
| adminPassword         | Rendszergazda ipassword                          |
| imagePublisher        | Kép közzétevője                                   |
| imageOffer            | Képajánlat                                       |
| imageSKU              | Kép Termékváltozat                                         |
| vmSize                | A virtuális gép mérete                                    |
| vmName                | A virtuális gép neve                                    |
| vaultName             | A kulcstartó neve                             |
| vaultResourceGroup    | A kulcstartó erőforráscsoportja                   |
| certificateUrl        | A tanúsítvány URL-címe, beleértve a KeyVault verzióját is, például`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Üzembehelyezési szkript

A következő Azure PowerShell-parancsfájl szerkesztése és végrehajtása a megadott Azure Marketplace virtuális gép üzembe helyezéséhez.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>További lépések

Miután telepítettegy előre konfigurált virtuális gép, konfigurálhatja és elérheti a benne található megoldásokat és szolgáltatásokat, vagy használhatja a további fejlesztéshez. 
