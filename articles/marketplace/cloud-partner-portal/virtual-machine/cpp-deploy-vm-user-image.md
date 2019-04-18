---
title: Üzembe helyezése egy Azure virtuális gép VHD-t felhasználói |} A Microsoft Docs
description: Azt ismerteti, hogyan helyezhet üzembe egy Azure-beli Virtuálisgép-példány létrehozása egy felhasználó VHD-rendszerképet.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 0885b187c4b9e9636144a3ab2a94b941008bec20
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046735"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Egy Azure virtuális gép VHD-t felhasználói üzembe helyezése

Ez a cikk bemutatja, hogyan hozhat létre egy új Azure-beli Virtuálisgép-erőforrást, a megadott Azure Resource Manager-sablon és az Azure PowerShell-szkript használatával általánosított virtuális merevlemez-lemezképek telepíthetők.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Virtuális merevlemez központi telepítési sablon

Másolja ki az Azure Resource Manager-sablonja [VHD-t üzembe helyezési](cpp-deploy-json-template.md) nevű helyi fájlba `VHDtoImage.json`.  Módosítsa ezt a fájlt a következő paraméterek értékének megadására. 

|  **A paraméter**             |   **Leírás**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Meglévő Azure-erőforrás neve.  Általában az ugyanahhoz az Entitáshoz társított kulcstartó használata  |
| TemplateFile               | A fájl teljes elérési útja `VHDtoImage.json`                                    |
| userStorageAccountName     | A tárfiók neve                                                    |
| sNameForPublicIP           | A nyilvános IP-cím DNS-neve. Kisbetűnek kell lennie                                  |
| subscriptionId             | Az Azure előfizetés-azonosító                                                  |
| Földrajzi egység                   | Standard szintű Azure földrajzi helye az erőforráscsoport                       |
| vmName                     | A virtuális gép neve                                                    |
| VaultName                  | A kulcstároló nevét                                                          |
| vaultResourceGroup         | A kulcstároló erőforráscsoport
| certificateUrl             | A tanúsítvány, ideértve például a key vaultban tárolt verzió URL-címe:  `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | A virtuális merevlemez URL-címe                                                   |
| vmSize                     | A virtuálisgép-példány mérete                                           |
| publicIPAddressName        | A nyilvános IP-cím neve                                                  |
| virtualNetworkName         | A virtuális hálózat neve                                                    |
| nicName                    | A hálózati kártyát a virtuális hálózat neve                     |
| adminUserName              | A rendszergazdai fiók felhasználóneve                                          |
| adminPassword              | Rendszergazda jelszava                                                          |
|  |  |


## <a name="powershell-script"></a>PowerShell-parancsfájl

Másoljon és szerkesszen értéket ad meg a következő szkriptet a `$storageaccount` és `$vhdUrl` változókat.  Hajtsa végre, hogy a meglévő általánosított virtuális merevlemezből az Azure-beli Virtuálisgép-erőforrás létrehozásához.

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

A virtuális gép üzembe helyezését követően készen áll [igazolja, a Virtuálisgép-lemezkép](./cpp-certify-vm.md).
