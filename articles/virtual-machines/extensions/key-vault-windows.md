---
title: Tároló Virtuálisgép-bővítmény kulcsot a következőhöz Windows |} Microsoft Docs
description: Az ügynök végrehajtja a Key Vault titkos kulcsok automatikus frissítését a virtuálisgép-bővítmény használó virtuális gépek telepítéséhez.
services: virtual-machines-windows
documentationcenter: ''
author: dragav
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: dragosav
ms.openlocfilehash: 19e177f086ea9fa817a36e67ace77aed39ee89b5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>A Windows Key Vault virtuálisgép-bővítmény

## <a name="overview"></a>Áttekintés

A Key Vault Virtuálisgép-bővítmény biztosít az Azure key vaultban tárolt titkos kulcsok automatikus frissítését. Pontosabban, a bővítmény figyelők listája megfigyelt kulcstárolót, és tárolt, után a változás észlelésekor tanúsítványok lekéri és telepíti a megfelelő tanúsítványokat. A Key Vault Virtuálisgép-bővítmény közzétett és a Microsoft jelenleg a Windows virtuális gépeken támogatott. Ez a dokumentum a támogatott platformok, konfigurációk és a Windows a Key Vault Virtuálisgép-bővítmény telepítési lehetőségek részletes. 

## <a name="prerequisites"></a>Előfeltételek

A Key Vault Virtuálisgép-bővítmény a kezelt Service identitás Virtuálisgép-bővítmény, az ahhoz, hogy hitelesítse magát a Key Vault szolgáltatás függ. További részleteket a MSI Virtuálisgép-bővítmény dokumentációjában tájékozódhat.

### <a name="operating-system"></a>Operációs rendszer

A Key Vault Virtuálisgép-bővítmény jelenleg az összes Windows-verziókat támogatja.

| Disztribúció | Verzió |
|---|---|
| Windows | Mag |

### <a name="internet-connectivity"></a>Internetkapcsolat

A Windows kulcsot tároló Virtuálisgép-bővítmény szükséges, hogy a cél virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Key Vault Virtuálisgép-bővítmény séma jeleníti meg. A bővítmény védett beállításainak nincs szükség, mert a beállítások minősülnek adatokat biztonsági gyakorolt hatás nélkül. A bővítmény alkalmazásnak szüksége van a felügyelt titkos kulcsok, lekérdezési gyakoriság és a célkiszolgáló tanúsítványtárolójába. Konkrétan:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

### <a name="property-values"></a>A tulajdonság értékek

| Name (Név) | Érték / – példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2016-10-01 | dátum |
| publisher | Microsoft.Azure.KeyVault.Edp | karakterlánc |
| type | KeyVaultForWindows | karakterlánc |
| typeHandlerVersion | 0.0 | int |
| pollingIntervalInS | 3600 | int |
| Tanúsítványtároló_neve | A | karakterlánc |
| certificateStoreLocation  | LOCAL_MACHINE | karakterlánc |
| observedCertificates  | ["https://myvault.vault.azure.net:443/secrets/mycertificate"] | Karakterlánc-tömbben


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Sablonok ideális telepítése során egy vagy több feladás egy vagy több központi telepítési igénylő virtuális gépekben tanúsítványok frissítése. A bővítmény is telepíthető az egyes virtuális gépek vagy virtuális gép méretezési készlet. A séma- és konfigurációs megegyeznek az mindkét sablon típusát. 

A virtuálisgép-bővítmény a JSON-konfigurációs kell ágyazni a virtuálisgép-erőforrás töredéke a sablon kifejezetten `"resources": []` objektum a virtuális gép.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

## <a name="azure-powershell-deployment"></a>Az Azure PowerShell-telepítés

Az Azure PowerShell segítségével a Key Vault Virtuálisgép-bővítmény telepítése egy meglévő virtuális gép vagy virtuálisgép-méretezési készlet. 

* A bővítményt a virtuális gép központi telepítéséhez:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMName> + '/KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Set-AzureRmVmExtension -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* A Virtuálisgép-méretezési készlet, például a Service Fabric-fürt részeként a bővítmény telepítéséhez:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMSSName> + '_KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Add-AzureRmVmssExtension -VirtualMachineScaleSet <VMSS> -Name $extName -Publisher $extPublisher -Type $extType -Setting $settings
    
    ```

Vegye figyelembe a következő korlátozások vagy követelmények:
- A telepítés után kell elvégezni a déli középső Régiójában régió
- Key Vault korlátozások:
    - A központi telepítés alkalmával léteznie kell 
    - Ennek a központi telepítés erőforráscsoport és ugyanabban a régióban kell található
    - Engedélyezni kell a központi telepítés és sablon

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Példák a kulcsot tároló Virtuálisgép-bővítmény, az Azure parancssori felület telepítése hamarosan elérhető. 

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotára vonatkozó lehet adatokat beolvasni az Azure-portálon, és az Azure PowerShell használatával. A megadott virtuális gépek bővítmények központi telepítési állapotának megtekintéséhez a következő parancsot az Azure PowerShell használatával.

```powershell
Get-AzureRMVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

A következő fájl kerül a bővítmény végrehajtás kimenetének:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
