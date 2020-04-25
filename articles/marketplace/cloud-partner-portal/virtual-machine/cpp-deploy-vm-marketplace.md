---
title: Virtuális gép üzembe helyezése az Azure Marketplace-ről
description: A cikk azt ismerteti, hogyan lehet virtuális gépet üzembe helyezni egy Azure piactéren előre konfigurált virtuális gépről.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 2fb04e2e953e2bcbe479f7685b8042fdc7ea1767
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146992"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Virtuális gép üzembe helyezése az Azure Marketplace-en

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuálisgép-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az [Azure VM-rendszerkép minősítésének](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) utasításait.

Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy előre konfigurált virtuális gépet (VM) az Azure Marketplace-en a megadott Azure PowerShell parancsfájl használatával.  Ez a szkript a WinRM HTTP-és HTTPS-végpontokat is elérhetővé teszi a virtuális gépen.  A parancsfájlhoz az szükséges, hogy a tanúsítványoknak a [Azure Key Vaulthoz való létrehozása](./cpp-create-key-vault-cert.md)című témakörben leírtak szerint már Azure Key Vault feltöltött tanúsítványt. 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Virtuálisgép-telepítési sablon

Az Azure-beli virtuális gép üzembe helyezési sablonja a [azuredeploy. JSON](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json)online fájlként érhető el.  A következő paramétereket tartalmazza:

|  **Paraméter**        |   **Leírás**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName    | A Storage-fiók neve                       |
| dnsNameForPublicIP    | A nyilvános IP-cím DNS-neve. Kisbetűsnek kell lennie.    |
| adminUserName            | Rendszergazda felhasználóneve                          |
| adminPassword            | Rendszergazdai jelszó                          |
| imagePublisher        | Rendszerkép kiadója                                   |
| imageOffer            | Rendszerkép-ajánlat                                       |
| imageSKU                | Rendszerkép SKU                                         |
| vmSize                | A virtuális gép mérete                                    |
| vmName                | A virtuális gép neve                                    |
| vaultName                | A Key Vault neve                             |
| vaultResourceGroup    | A Key Vault erőforráscsoport                   |
| certificateUrl        | A tanúsítvány URL-címe, beleértve a kulcstartó verzióját is, például:`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Üzembehelyezési szkript

Szerkessze a következő Azure PowerShell szkriptet, és hajtsa végre a megadott Azure Marketplace-beli virtuális gép üzembe helyezéséhez.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>További lépések

Az előre konfigurált virtuális gépek üzembe helyezését követően konfigurálhatja és elérheti a benne foglalt megoldásokat és szolgáltatásokat, vagy felhasználhatja azt a további fejlesztéshez. 
