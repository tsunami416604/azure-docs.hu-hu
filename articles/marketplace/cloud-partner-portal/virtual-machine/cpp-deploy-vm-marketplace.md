---
title: Virtuális gép üzembe helyezése az Azure Marketplace-ről
description: A cikk azt ismerteti, hogyan lehet virtuális gépet üzembe helyezni egy Azure piactéren előre konfigurált virtuális gépről.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 2888d7643fd4f624634dc2ec520bec6e753382f1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816826"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Virtuális gép üzembe helyezése az Azure Marketplace-en

Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy előre konfigurált virtuális gépet (VM) az Azure Marketplace-en a megadott Azure PowerShell parancsfájl használatával.  Ez a szkript a WinRM HTTP-és HTTPS-végpontokat is elérhetővé teszi a virtuális gépen.  A parancsfájlhoz az szükséges, hogy a tanúsítványoknak a [Azure Key Vaulthoz való létrehozása](./cpp-create-key-vault-cert.md)című témakörben leírtak szerint már Azure Key Vault feltöltött tanúsítványt. 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Virtuálisgép-telepítési sablon

Az Azure-beli virtuális gép üzembe helyezési sablonja a [azuredeploy. JSON](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json)online fájlként érhető el.  A következő paramétereket tartalmazza:

|  **Paraméter**        |   **Leírás**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | A Storage-fiók neve                       |
| dnsNameForPublicIP    | A nyilvános IP-cím DNS-neve. Kisbetűsnek kell lennie.    |
| adminUserName         | Rendszergazda felhasználóneve                          |
| adminPassword         | Rendszergazdai jelszó                          |
| imagePublisher        | Rendszerkép kiadója                                   |
| imageOffer            | Rendszerkép-ajánlat                                       |
| imageSKU              | Rendszerkép SKU                                         |
| vmSize                | A virtuális gép mérete                                    |
| vmName                | A virtuális gép neve                                    |
| vaultName             | A Key Vault neve                             |
| vaultResourceGroup    | A Key Vault erőforráscsoport                   |
| certificateUrl        | A tanúsítvány URL-címe, beleértve a kulcstartóban lévő verziót, például `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Üzembe helyezési parancsfájl

Szerkessze a következő Azure PowerShell szkriptet, és hajtsa végre a megadott Azure Marketplace-beli virtuális gép üzembe helyezéséhez.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>További lépések

Az előre konfigurált virtuális gépek üzembe helyezését követően konfigurálhatja és elérheti a benne foglalt megoldásokat és szolgáltatásokat, vagy felhasználhatja azt a további fejlesztéshez. 
