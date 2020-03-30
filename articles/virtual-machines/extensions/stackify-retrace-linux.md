---
title: Stackify Retrace Azure Linux Ügynök bővítmény
description: Telepítse a Stackify Retrace Linux-ügynököt egy Linux virtuális gépen.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: akjosh
ms.openlocfilehash: 5914947bd994ee405f253e34c3dd919dd6561898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253792"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify Retrace Linux ügynök bővítmény

## <a name="overview"></a>Áttekintés

A Stackify olyan termékeket biztosít, amelyek nyomon követik az alkalmazás részleteit, hogy gyorsan megtalálják és kijavítsák a problémákat. Fejlesztői csapatok számára a Retrace egy teljesen integrált, többkörnyezetes, alkalmazásteljesítmény-szuperteljesítmény. Számos eszközt egyesít, amelyre minden fejlesztőcsapatnak szüksége van.

A Retrace az egyetlen olyan eszköz, amely egyetlen platformon biztosítja az összes alábbi képességet az összes környezetben.

* Alkalmazásteljesítmény-kezelés (APM)
* Alkalmazás- és kiszolgálónaplózás
* Hibakövetés és figyelés
* Kiszolgálói, alkalmazás- és egyéni mutatók

**A Stackify Linux Ügynök bővítményről**

Ez a bővítmény telepítési elérési utat biztosít a Linux-ügynök számára a Retrace számára. 

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer 

A Retrace ügynök futtatható ezekkel a Linux disztribúciókkal

| Disztribúció | Verzió |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 és 17.04 |
| Debian | 7.9+ és 8.2+, 9 |
| Red Hat | 6.7+, 7.1+ |
| CentOS | 6.3+, 7.0+ |

### <a name="internet-connectivity"></a>Internetkapcsolat

A Stackify Agent bővítmény Linux megköveteli, hogy a cél virtuális gép csatlakozik az internethez. 

Előfordulhat, hogy módosítania kell a hálózati konfigurációt, https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewallhogy a kapcsolatok Stackify, lásd: . 


## <a name="extension-schema"></a>Bővítményséma

---

A következő JSON a Stackify Retrace Ügynök bővítmény sémáját jeleníti meg. A kiterjesztéshez `activationKey`a és a. `environment`

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Sablonalapú telepítés 

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők. Az előző szakaszban részletezett JSON-séma egy Azure Resource Manager-sablonban használható a Stackify Retrace Linux-ügynök bővítmény futtatásához az Azure Resource Manager-sablon üzembe helyezése során.  

A virtuálisgép-bővítmény JSON-ja beágyazható a virtuálisgép-erőforrásba, vagy elhelyezhető egy Erőforrás-kezelő JSON-sablon gyökér- vagy legfelső szintjén. A JSON elhelyezése hatással van az erőforrás nevének és típusának értékére. További információ: Név beállítása és beírás a gyermekerőforrásokhoz.

A következő példa feltételezi, hogy a Stackify Retrace Linux-bővítmény a virtuális gép erőforrásba van ágyazva. A bővítményerőforrás beágyazásakor a JSON a virtuális gép "erőforrások": [] objektuma.

A kiterjesztéshez `activationKey`a és a. `environment`

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

Amikor a JSON bővítményt helyezi a sablon gyökeréhez, az erőforrás neve tartalmaz egy hivatkozást a szülő virtuális gépre, és a típus a beágyazott konfigurációt tükrözi.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>PowerShell-telepítés

A `Set-AzVMExtension` parancs segítségével telepítheti a Stackify Retrace Linux Ügynök virtuálisgép-bővítményt egy meglévő virtuális gépre. A parancs futtatása előtt a nyilvános és privát konfigurációkat egy PowerShell-kivonattáblában kell tárolni.

A kiterjesztéshez `activationKey`a és a. `environment`

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Az Azure CLI üzembe helyezése 

Az Azure CLI eszköz használható a Stackify Retrace Linux-ügynök virtuálisgép-bővítmény üzembe helyezéséhez egy meglévő virtuális gépre.  

A kiterjesztéshez `activationKey`a és a. `environment`

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="error-codes"></a>Hibakódok

| Hibakód | Jelentés | Lehetséges művelet |
| :---: | --- | --- |
| 10 | Telepítési hiba | wget szükséges |
| 20 | Telepítési hiba | python van szükség |
| 30 | Telepítési hiba | sudo szükséges |
| 40 | Telepítési hiba | aktiválásKulcs szükséges |
| 51 | Telepítési hiba | Az operációs rendszer szétoszlása nem támogatott |
| 60 | Telepítési hiba | környezetre van szükség |
| 70 | Telepítési hiba | Ismeretlen |
| 80 | Hiba engedélyezése | A szolgáltatás beállítása nem sikerült |
| 90 | Hiba engedélyezése | A szolgáltatás indítása nem sikerült |
| 100 | Hiba letiltása | A szolgáltatás leállítása nem sikerült |
| 110 | Hiba letiltása | A szolgáltatás eltávolítása nem sikerült |
| 120 | Eltávolítási hiba | A szolgáltatás leállítása nem sikerült |

Ha további segítségre van szüksége, forduljon a Stackify ügyfélszolgálatához a. https://support.stackify.com