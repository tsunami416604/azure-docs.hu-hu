---
title: Virtuális gép üzembe helyezése az Azure Marketplace-ről
description: Azt ismerteti, hogyan helyezhet üzembe egy virtuális gépet az Azure Marketplace-en előre konfigurált virtuális gépből.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 482840f5e611bc2d8092add2822a0ed5b2c8f883
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938696"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Virtuális gép üzembe helyezése az Azure Marketplace-ről

Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy előre konfigurált virtuális gép (VM) az Azure piactéren, a megadott Azure PowerShell-parancsfájl használatával.  Ez a szkript is elérhetővé teszi a Rendszerfelügyeleti webszolgáltatások HTTP vagy HTTPS-végpontokat a virtuális gépen.  A parancsfájl megköveteli, hogy Ön már az Azure Key Vaultba feltöltött tanúsítvány leírtak szerint [tanúsítványok létrehozása az Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Virtuális gép központi telepítési sablon

A rövid útmutató Azure virtuális gép központi telepítési sablon érhető el az online fájlként [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Az alábbi paramétereket tartalmazza:

|  **A paraméter**        |   **Leírás**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | A tárfiók neve                       |
| dnsNameForPublicIP    | A nyilvános IP-cím DNS-neve. Kisbetűnek kell lennie.    |
| adminUserName         | Rendszergazdai felhasználónév                          |
| adminPassword         | Rendszergazdai jelszó                          |
| imagePublisher        | Image publisher                                   |
| imageOffer            | Lemezkép-ajánlat                                       |
| imageSKU              | Lemezkép Termékváltozata                                         |
| vmSize                | A virtuális gép mérete                                    |
| vmName                | A virtuális gép neve                                    |
| VaultName             | A kulcstároló nevét                             |
| vaultResourceGroup    | A kulcstároló erőforráscsoport                   |
| certificateUrl        | A tanúsítvány, beleértve a KeyVault, például verzió URL-címe  `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Üzembe helyezési parancsfájl

Szerkessze a következő Azure PowerShell-parancsfájlt, és hajtsa végre, hogy a megadott Azure Marketplace virtuális gép üzembe helyezése.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>További lépések

Miután telepített egy előre konfigurált virtuális Gépet, konfigurálása és a megoldások és a benne található szolgáltatások, vagy további fejlesztésére használja. 
