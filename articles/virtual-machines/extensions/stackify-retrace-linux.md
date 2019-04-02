---
title: Visszalépés az Azure Linux-ügynök bővítmény stackify értelmezi |} A Microsoft Docs
description: A stackify értelmezi végig Linux-ügynök Linux rendszerű virtuális gépre telepíteni.
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
ms.author: roiyz
ms.openlocfilehash: b9c035c1c9088957f59550bf6564cc02bc7972f4
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58792419"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify értelmezi a Visszalépés Linux-ügynök bővítmény

## <a name="overview"></a>Áttekintés

Stackify értelmezi, amelyek nyomon követik az alkalmazás segítségével megkeresheti és kijavíthatja az esetleges problémákat gyorsan részleteinek termékek biztosít. Fejlesztői csapatok a Visszalépés egy teljes körűen integrált, több környezet, az alkalmazás teljesítményének felettes power. Amely ötvözi az minden fejlesztési csapat által igényelt számos eszközt.

A csak eszköz, amely egyetlen platform minden környezetben keresztül biztosít minden, a következő funkciók végig.

* Alkalmazásteljesítmény-felügyeleti (APM)
* Alkalmazás- és naplózás
* Hiba nyomon követése és figyelése
* Kiszolgáló, az alkalmazás és az egyéni metrikák

**Körülbelül stackify értelmezi a Linux-ügynök bővítmény**

Ez a bővítmény a Linux-ügynökének végig egy telepítési útvonal biztosít. 

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer 

A Visszalépés az ügynök ezen Linux-disztribúció futtatható

| Disztribúció | Verzió |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 és 17.04 |
| Debian | 7.9 + és 8.2 +, 9 |
| Red Hat | 6.7+, 7.1+ |
| CentOS | 6.3+, 7.0+ |

### <a name="internet-connectivity"></a>Internetkapcsolat

A stackify értelmezi az ügynök-bővítmény linuxhoz megköveteli, hogy a céloldali virtuális gép csatlakozik az internethez. 

Szükség lehet módosítani a kapcsolat lehetővé tétele a Stackify, olvassa el a hálózati konfiguráció https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Bővítményséma

---

A következő JSON a stackify értelmezi az ügynök végig bővítmény sémáját jeleníti meg. A bővítmény telepítéséhez a `environment` és `activationKey`.

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

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Az előző szakaszban részletes JSON-sémája a stackify értelmezi végig Linux Agent bővítmény futtatása során egy Azure Resource Manager-sablon üzembe helyezése Azure Resource Manager-sablon is használható.  

A JSON-t egy virtuálisgép-bővítményt a virtuális gép típusú erőforrást belülre, vagy elhelyezve, a legfelső szintű vagy a legfelső szintű Resource Managerből származó JSON-sablon. A JSON-fájllal való elhelyezését hatással van az erőforrás nevét, és írja be az értékét. További információkért tekintse meg a csoport nevét és típusát gyermekerőforrásait.

Az alábbi példa azt feltételezi, hogy a stackify értelmezi végig Linux-bővítményt a virtuális gép típusú erőforrást van beágyazva. A bővítmény erőforrás beágyazása, ha a JSON kerül a "resources": [] objektumot a virtuális gép.

A bővítmény telepítéséhez a `environment` és `activationKey`.

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


## <a name="powershell-deployment"></a>PowerShell környezetben végzett telepítés

A `Set-AzVMExtension` parancs a stackify értelmezi végig Linux ügynök virtuálisgép-bővítmény egy meglévő virtuális gépek üzembe helyezéséhez használható. A parancs futtatása előtt a nyilvános és privát konfigurációk kell egy PowerShell kivonattábla kell tárolni.

A bővítmény telepítéséhez a `environment` és `activationKey`.

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

Az Azure parancssori eszköz segítségével végig Linux-ügynök stackify értelmezi virtuálisgép-bővítmény egy meglévő virtuális gépek üzembe helyezéséhez.  

A bővítmény telepítéséhez a `environment` és `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="error-codes"></a>Hibakódok

| Hibakód | Jelentés | A művelet lehetséges |
| :---: | --- | --- |
| 10 | Telepítési hiba | wget megadása kötelező |
| 20 | Telepítési hiba | Python megadása kötelező |
| 30 | Telepítési hiba | sudo megadása kötelező |
| 40 | Telepítési hiba | tárolófájlban megadása kötelező |
| 51 | Telepítési hiba | Nem támogatott operációsrendszer-disztribúció |
| 60 | Telepítési hiba | környezet megadása kötelező |
| 70 | Telepítési hiba | Ismeretlen |
| 80 | Hiba történt engedélyezése | Szolgáltatás beállítása nem sikerült |
| 90 | Hiba történt engedélyezése | Szolgáltatás indítása sikertelen |
| 100 | Letilthatja a hibajelentési | Szolgáltatás leállítása nem sikerült |
| 110 | Letilthatja a hibajelentési | Szolgáltatás eltávolítása sikertelen |
| 120 | Eltávolítási hiba | Szolgáltatás leállítása nem sikerült |

Ha további segítségre van szüksége, forduljon a támogatási Stackify https://support.stackify.com.