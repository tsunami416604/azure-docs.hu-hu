---
title: Azure-beli virtuális gép üzembe helyezése felhasználói VHD-ről | Azure piactér
description: A cikk azt ismerteti, hogyan helyezhet üzembe egy felhasználói VHD-rendszerképet egy Azure virtuálisgép-példány létrehozásához.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: e3bad2dc63f6a75f52c537aabfa6e85d1846ef15
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147935"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Azure-beli virtuális gép üzembe helyezése felhasználói VHD-ből

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuálisgép-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az [Azure VM-rendszerkép minősítésének](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) utasításait.

Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy általánosított VHD-rendszerképet új Azure-beli virtuálisgép-erőforrások létrehozásához a megadott Azure Resource Manager sablonnal és Azure PowerShell parancsfájl használatával.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>VHD központi telepítési sablon

Másolja a [VHD-telepítés](cpp-deploy-json-template.md) Azure Resource Manager sablonját egy nevű `VHDtoImage.json`helyi fájlba.  Szerkessze a fájlt a következő paraméterek értékeinek megadásához. 

|  **Paraméter**             |   **Leírás**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Meglévő Azure-erőforráscsoport neve.  Általában ugyanazt a RG-t használja, mint amelyet a kulcstartóhoz társít  |
| TemplateFile               | A fájl teljes elérési útja`VHDtoImage.json`                                    |
| userStorageAccountName     | A Storage-fiók neve                                                    |
| sNameForPublicIP           | A nyilvános IP-cím DNS-neve. Kisbetűsnek kell lennie                                  |
| subscriptionId             | Azure-előfizetés azonosítója                                                  |
| Hely                   | Az erőforráscsoport szabványos Azure-beli földrajzi helye                       |
| vmName                     | A virtuális gép neve                                                    |
| vaultName                  | A Key Vault neve                                                          |
| vaultResourceGroup         | A Key Vault erőforráscsoport
| certificateUrl             | A tanúsítvány URL-címe, beleértve a Key vaultban tárolt verziót, például:`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | A virtuális merevlemez URL-címe                                                   |
| vmSize                     | Virtuálisgép-példány mérete                                           |
| publicIPAddressName        | A nyilvános IP-cím neve                                                  |
| virtualNetworkName         | A virtuális hálózat neve                                                    |
| nicName                    | A virtuális hálózat hálózati kártyájának neve                     |
| adminUserName              | A rendszergazdai fiók felhasználóneve                                          |
| adminPassword              | Rendszergazdai jelszó                                                          |
|  |  |


## <a name="powershell-script"></a>PowerShell-parancsfájl

Másolja és szerkessze a következő parancsfájlt, hogy megadja `$storageaccount` a `$vhdUrl` és a változók értékeit.  Futtassa azt egy Azure-beli virtuálisgép-erőforrás létrehozásához a meglévő általánosított VHD-ről.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>További lépések

A virtuális gép üzembe helyezése után készen áll a virtuálisgép- [lemezkép tanúsítására](./cpp-certify-vm.md).
