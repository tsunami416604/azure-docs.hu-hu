---
title: Azure-beli virtuális gép üzembe helyezése felhasználói VHD-ről | Azure piactér
description: A cikk azt ismerteti, hogyan helyezhet üzembe egy felhasználói VHD-rendszerképet egy Azure virtuálisgép-példány létrehozásához.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 8421e9b7b7e2b7d13054e977da83be044b4e6af7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816645"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Azure-beli virtuális gép üzembe helyezése felhasználói VHD-ből

Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy általánosított VHD-rendszerképet új Azure-beli virtuálisgép-erőforrások létrehozásához a megadott Azure Resource Manager sablonnal és Azure PowerShell parancsfájl használatával.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>VHD központi telepítési sablon

Másolja a [VHD-telepítés](cpp-deploy-json-template.md) Azure Resource Manager sablonját egy `VHDtoImage.json`nevű helyi fájlba.  Szerkessze a fájlt a következő paraméterek értékeinek megadásához. 

|  **Paraméter**             |   **Leírás**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Meglévő Azure-erőforráscsoport neve.  Általában ugyanazt a RG-t használja, mint amelyet a kulcstartóhoz társít  |
| TemplateFile               | A fájl teljes elérési útja `VHDtoImage.json`                                    |
| userStorageAccountName     | A Storage-fiók neve                                                    |
| sNameForPublicIP           | A nyilvános IP-cím DNS-neve. Kisbetűsnek kell lennie                                  |
| subscriptionId             | Azure-előfizetés azonosítója                                                  |
| Hely                   | Az erőforráscsoport szabványos Azure-beli földrajzi helye                       |
| vmName                     | A virtuális gép neve                                                    |
| vaultName                  | A Key Vault neve                                                          |
| vaultResourceGroup         | A Key Vault erőforráscsoport
| certificateUrl             | A tanúsítvány URL-címe, beleértve a Key vaultban tárolt verziót, például: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | A virtuális merevlemez URL-címe                                                   |
| vmSize                     | Virtuálisgép-példány mérete                                           |
| publicIPAddressName        | A nyilvános IP-cím neve                                                  |
| virtualNetworkName         | A virtuális hálózat neve                                                    |
| nicName                    | A virtuális hálózat hálózati kártyájának neve                     |
| adminUserName              | A rendszergazdai fiók felhasználóneve                                          |
| adminPassword              | Rendszergazdai jelszó                                                          |
|  |  |


## <a name="powershell-script"></a>PowerShell-parancsfájl

Másolja és szerkessze a következő parancsfájlt, hogy megadja a `$storageaccount` és a `$vhdUrl` változók értékeit.  Futtassa azt egy Azure-beli virtuálisgép-erőforrás létrehozásához a meglévő általánosított VHD-ről.

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
