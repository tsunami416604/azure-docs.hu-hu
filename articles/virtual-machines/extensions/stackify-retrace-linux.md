---
title: Az Azure Linux-ügynök bővítményének Stackify újrarajzolása | Microsoft Docs
description: Telepítse a Stackify retrace Linux-ügynököt egy linuxos virtuális gépre.
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
ms.author: roiyz
ms.openlocfilehash: 6db152394a8e57689b34436b48dbcb4ecdc58b5a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084317"
---
# <a name="stackify-retrace-linux-agent-extension"></a>A Linux-ügynök bővítményének Stackify újranyomkövetése

## <a name="overview"></a>Áttekintés

A Stackify olyan termékeket biztosít, amelyek nyomon követhetik az alkalmazás részleteit, így gyorsan megtalálhatja és elháríthatja a problémákat. A fejlesztői csapatok esetében az újbóli nyomkövetés egy teljes körűen integrált, több környezetből álló, az alkalmazások teljesítményének növelésére használható. Számos eszközt egyesít, minden fejlesztési csapatnak szüksége van.

Az újbóli nyomkövetés az egyetlen eszköz, amely a következő képességeket biztosítja az összes környezetben egyetlen platformon.

* Alkalmazások teljesítményének kezelése (APM)
* Alkalmazás-és kiszolgáló-naplózás
* Hibák nyomon követése és figyelése
* Kiszolgáló, alkalmazás és egyéni metrikák

**Tudnivalók a Stackify Linux-ügynök bővítménnyel kapcsolatban**

Ez a bővítmény telepítési útvonalat biztosít a Linux-ügynök újranyomkövetéshez. 

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer 

Az újranyomkövetési ügynök futtatható a Linux-disztribúciók ellen

| Disztribúció | Version |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS, 16,10 és 17,04 |
| Debian | 7,9 + és 8.2 +, 9 |
| Red Hat | 6.7 +, 7.1 + |
| CentOS | 6.3 +, 7.0 + |

### <a name="internet-connectivity"></a>Internetkapcsolat

A Linux Stackify-ügynökének kiterjesztése megköveteli, hogy a célként megadott virtuális gép csatlakozik az internethez. 

Előfordulhat, hogy módosítania kell a hálózati konfigurációt, hogy engedélyezze a kapcsolódást a Stackify számára. lásd: https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Bővítményséma

---

A következő JSON a Stackify újranyomkövetési ügynök bővítményének sémáját mutatja be. A kiterjesztéshez a `environment` és `activationKey`a szükséges.

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

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Az előző szakaszban részletezett JSON-séma egy Azure Resource Manager sablonban használható a Stackify retrace Linux-ügynök bővítmény futtatásához Azure Resource Manager sablon központi telepítése során.  

A virtuálisgép-bővítmények JSON-je beágyazható a virtuális gép erőforrásaiba, vagy egy Resource Manager JSON-sablon legfelső szintű vagy legfelső szintjén helyezhető el. A JSON elhelyezése hatással van az erőforrás nevének és típusának értékére. További információ: a gyermek erőforrások nevének és típusának beállítása.

Az alábbi példa azt feltételezi, hogy a Stackify retrace Linux-bővítmény a virtuális gép erőforrásán belül van beágyazva. A bővítmény erőforrásának beágyazásakor a JSON a virtuális gép "erőforrások": [] objektumára kerül.

A kiterjesztéshez a `environment` és `activationKey`a szükséges.

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

Helyezi el a JSON-bővítmény a sablonban gyökérmappájában, amikor az erőforrás neve a szülő virtuális gép egy hivatkozást tartalmaz, és a típus a beágyazott konfigurációját tükrözi.

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

A `Set-AzVMExtension` parancs segítségével telepítheti a Stackify retrace Linux-ügynök virtuálisgép-bővítményét egy meglévő virtuális gépre. A parancs futtatása előtt a nyilvános és a privát konfigurációkat egy PowerShell-kivonatoló táblában kell tárolni.

A kiterjesztéshez a `environment` és `activationKey`a szükséges.

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

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés 

Az Azure CLI-eszköz használatával üzembe helyezheti a Stackify retrace Linux-ügynök virtuálisgép-bővítményét egy meglévő virtuális gépre.  

A kiterjesztéshez a `environment` és `activationKey`a szükséges.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="error-codes"></a>Hibakódok

| Hibakód | Jelentés | Lehetséges művelet |
| :---: | --- | --- |
| 10 | Telepítési hiba | a wget megadása kötelező |
| 20 | Telepítési hiba | Python szükséges |
| 30 | Telepítési hiba | a sudo megadása kötelező |
| 40 | Telepítési hiba | a activationKey megadása kötelező |
| 51 | Telepítési hiba | Az operációs rendszer disztribúciója nem támogatott |
| 60 | Telepítési hiba | a környezet megadása kötelező |
| 70 | Telepítési hiba | Ismeretlen |
| 80 | Hiba engedélyezése | A szolgáltatás telepítése nem sikerült |
| 90 | Hiba engedélyezése | A szolgáltatás indítása nem sikerült |
| 100 | Hiba letiltása | A szolgáltatás leállítása sikertelen |
| 110 | Hiba letiltása | A szolgáltatás eltávolítása nem sikerült |
| 120 | Eltávolítási hiba | A szolgáltatás leállítása sikertelen |

Ha további segítségre van szüksége, vegye fel a kapcsolatot https://support.stackify.com a Stackify támogatási szolgálatával.