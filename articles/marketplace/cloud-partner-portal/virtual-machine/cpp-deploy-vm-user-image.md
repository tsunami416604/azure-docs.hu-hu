---
title: Azure-beli virtuális gép központi telepítése felhasználói virtuális merevlemezről | Azure Piactér
description: Bemutatja, hogyan telepíthet egy felhasználói virtuális merevlemez-lemezképet egy Azure virtuálisgép-példány létrehozásához.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 79754b4ce7c3dfe2a5c549f4a39ef3160be423d8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273885"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Azure-beli virtuális gép üzembe helyezése felhasználói virtuális merevlemezről

> [!IMPORTANT]
> 2020. április 13-tól megkezdjük az Azure virtuálisgép-ajánlatok kezelését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [Azure VM-lemezkép-minősítés](https://aks.ms/CertifyVMimage) utasításait az áttelepített ajánlatok kezeléséhez.

Ez a cikk bemutatja, hogyan telepíthet egy általános virtuális merevlemez-lemezképet egy új Azure virtuálisgép-erőforrás létrehozásához a mellékelt Azure Resource Manager-sablon és az Azure PowerShell-parancsfájl használatával.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Virtuális merevlemez telepítési sablonja

Másolja a [virtuális merevlemez üzembe helyezéséhez](cpp-deploy-json-template.md) az `VHDtoImage.json`Azure Resource Manager sablont egy helyi fájlba, amelynek neve .  A fájl szerkesztésével adja meg a következő paraméterek értékeit. 

|  **Paraméter**             |   **Leírás**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Meglévő Azure-erőforráscsoport neve.  Általában ugyanazt az RG-t használja a key vaulthoz társított  |
| Sablonfájl               | A fájl teljes elérési útja`VHDtoImage.json`                                    |
| userStorageAccountName     | A tárfiók neve                                                    |
| sNameForPublicIP           | A nyilvános IP DNS-neve. Kisbetűsnek kell lennie                                  |
| subscriptionId             | Azure-előfizetés azonosítója                                                  |
| Hely                   | Az erőforráscsoport szabványos Azure-földrajzi helye                       |
| vmName                     | A virtuális gép neve                                                    |
| vaultName                  | A kulcstartó neve                                                          |
| vaultResourceGroup         | A kulcstartó erőforráscsoportja
| certificateUrl             | A tanúsítvány url-címe, beleértve a key vaultban tárolt verziót is, például:`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | A virtuális merevlemez URL-címe                                                   |
| vmSize                     | A virtuálisgép-példány mérete                                           |
| nyilvánosIPAddressName        | A nyilvános IP-cím neve                                                  |
| virtualNetworkName         | A virtuális hálózat neve                                                    |
| nicName (név)                    | A virtuális hálózat hálózati kártya neve                     |
| adminUserName              | A rendszergazdai fiók felhasználóneve                                          |
| adminPassword              | Rendszergazdai jelszó                                                          |
|  |  |


## <a name="powershell-script"></a>Powershell parancsfájl

Másolja és szerkesztse a következő `$storageaccount` parancsfájlt, hogy adja meg a változók értékeit. `$vhdUrl`  Hajtsa végre egy Azure virtuális gép erőforrás a meglévő általános virtuális merevlemez létrehozásához.

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

A virtuális gép üzembe helyezése után készen áll [a virtuális gép lemezképének hitelesítésére.](./cpp-certify-vm.md)
