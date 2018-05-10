---
title: Visszalépés Azure Linux ügynök-bővítmény stackify |} Microsoft Docs
description: A Linux virtuális gépeken Stackify végig Linux-ügynök telepítéséhez.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: danis
ms.openlocfilehash: 376c5a087f74fbe087db9fa2df38b2ba4e6cf1ff
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="stackify-retrace-linux-agent-extension"></a>Linux-ügynök bővítmény végig stackify

## <a name="overview"></a>Áttekintés
Stackify biztosít a termékeket, amik nyomon követéséhez keresse meg és problémák gyors megoldása érdekében az alkalmazás részleteit. A fejlesztői csapat végig egy teljesen integrált, több környezet alkalmazás teljesítmény felettes power. Egyesít minden fejlesztési csoport eszközöket.

Visszalépés a csak eszköz, amely biztosítja a következő funkciók egy egységes platform összes környezetek között.

* Alkalmazáskezelés teljesítmény (APM)
* Alkalmazások és a kiszolgáló naplózása
* Hiba nyomon követési és figyelése
* Kiszolgáló, az alkalmazás és az egyéni metrikák

**Linux-ügynök bővítmény készül Stackify**

A bővítmény egy telepítési útvonalat biztosít a Linux-ügynökének visszavonásához. 

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer 
A Visszalépés ügynök is futtathatók a Linux terjesztéseket

| Disztribúció | Verzió |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 és 17.04 |
| Debian | 7.9 + és 8.2 +, 9 |
| RedHat | 6.7 +, 7.1 + |
| CentOS | 6.3 +, 7.0 + |

### <a name="internet-connectivity"></a>Internetkapcsolat
A Linux ügynök Stackify kiterjesztése van szükség, hogy a cél virtuális gép csatlakozik az internethez. 

Meg kell Stackify kapcsolatok engedélyezése, olvassa el a hálózati konfiguráció https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Bővítményséma
---

A következő JSON a Visszalépés ügynök Stackify bővítmény séma jeleníti meg. A bővítménynek szüksége a `environment` és `activationKey`.

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

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Az előző szakaszban ismertetett JSON-séma segítségével az Azure Resource Manager-sablonok a Visszalépés Linux-ügynök Stackify bővítmény futtatása az Azure Resource Manager sablon üzembe helyezése során.  

A virtuálisgép-bővítmény JSON ágyazott a virtuálisgép-erőforrást, vagy elhelyezve, a gyökér vagy a legfelső szintű erőforrás-kezelő JSON-sablon. A JSON elhelyezésének befolyásolja az erőforrás neve és típusa értékét. További információkért tekintse meg a készlet neve és az alsóbb szintű erőforrásai típusát.

Az alábbi példa azt feltételezi, hogy a Visszalépés Linux Stackify bővítményt a virtuálisgép-erőforrás van beágyazva. A bővítmény erőforrás beágyazási, amikor a JSON helyezkedik el, a "források": a virtuális gép [] objektum.

A bővítménynek szüksége a `environment` és `activationKey`.

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

A bővítmény JSON elhelyezésekor a sablon gyökerében, az erőforrás nevét a szülő virtuális gép egy hivatkozást tartalmaz, és a típus beágyazott konfigurációját tükrözi.

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


## <a name="powershell-deployment"></a>PowerShell telepítése

A `Set-AzureRmVMExtension` parancs segítségével végig Linux-ügynök Stackify virtuálisgép-bővítmény telepítése egy meglévő virtuális gépre. A parancs futtatása előtt a nyilvános és titkos konfigurációk kell egy kivonattáblát a PowerShell tárolódnak.

A bővítménynek szüksége a `environment` és `activationKey`.

```
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzureRmVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
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

Az Azure parancssori eszköz segítségével végig Linux-ügynök Stackify virtuálisgép-bővítmény telepítése egy meglévő virtuális gépre.  

A bővítménynek szüksége a `environment` és `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="error-codes"></a>Hibakódok

| Hibakód | Jelentés | Lehetséges művelet |
| :---: | --- | --- |
| 10 | Telepítési hiba | wget szükség |
| 20 | Telepítési hiba | Python szükség |
| 30 | Telepítési hiba | sudo szükség |
| 40 | Telepítési hiba | activationKey szükség |
| 51 | Telepítési hiba | Nem támogatott az operációs rendszer distro |
| 60 | Telepítési hiba | környezetre szükség |
| 70 | Telepítési hiba | Ismeretlen |
| 80 | Hibajelentés engedélyezése | A telepítő nem tudta szolgáltatás |
| 90 | Hibajelentés engedélyezése | Szolgáltatás indítása sikertelen |
| 100 | Tiltsa le a hiba | Szolgáltatás leállítása nem sikerült |
| 110 | Tiltsa le a hiba | Nem sikerült eltávolítani szolgáltatás |
| 120 | Eltávolítási hiba | Szolgáltatás leállítása nem sikerült |

Ha további segítségre van szüksége, forduljon a Stackify támogatását https://support.stackify.com.