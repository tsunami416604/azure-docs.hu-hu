---
title: Azure Key Vault virtuálisgép-bővítmény Linuxra
description: Telepítsen egy ügynököt a Key Vault-tanúsítványok automatikus frissítését a virtuális gépbővítményhasználatával működő virtuális gépeken.
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: add2d515e4f8e8c56a98a7292e137e601332d10c
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410867"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Key Vault virtuális gép kiterjesztése Linuxhoz

A Key Vault virtuális gép bővítmény az Azure key vaultban tárolt tanúsítványok automatikus frissítését biztosítja. Pontosabban a bővítmény a key vaultokban tárolt megfigyelt tanúsítványok listáját figyeli.  A módosítás észlelésekor a bővítmény lekéri és telepíti a megfelelő tanúsítványokat. A Key Vault virtuális gép bővítményét a Microsoft teszi közzé és támogatja, jelenleg Linux-alapú virtuális gépeken. Ez a dokumentum részletezi a támogatott platformok, konfigurációk és üzembe helyezési lehetőségek a Key Vault virtuális gép bővítmény Linux. 

### <a name="operating-system"></a>Operációs rendszer

A Key Vault virtuális gép bővítménye a következő Linux-disztribúciákat támogatja:

- Ubuntu-1604
- Ubuntu-1804
- Debian-9
- Suse-15 között 

### <a name="supported-certificate-content-types"></a>Támogatott tanúsítványtartalom-típusok

- PKCS #12
- PEM

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Key Vault virtuálisgép-bővítmény sémáját mutatja be. A bővítmény nem igényel védett beállításokat - minden beállítás biztonsági hatás nélkül információnak minősül. A bővítmény megköveteli a figyelt titkos kulcsok listáját, a lekérdezés gyakoriságát és a céltanúsítvány-tárolót. Ezek a következők:  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

> [!NOTE]
> A megfigyelt tanúsítványok URL-címeinek `https://myVaultName.vault.azure.net/secrets/myCertName`a képernyőn kell lennie.
> 
> Ennek az `/secrets` az oka, hogy az elérési út `/certificates` a teljes tanúsítványt adja vissza, beleértve a személyes kulcsot is, míg az elérési út nem. A tanúsítványokról további információt itt talál: [Key Vault-tanúsítványok](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)


### <a name="property-values"></a>Tulajdonság értékek

| Név | Érték / Példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | dátum |
| közzétevő | Microsoft.Azure.KeyVault | sztring |
| type | KeyVaultForLinux | sztring |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | sztring |
| certificateStoreName | MY | sztring |
| linkOnRenewal | hamis | logikai |
| certificateStoreLocation  | LocalMachine (Helyi gépgép) | sztring |
| requiredInitialSync | igaz | logikai |
| observedCertificates (megfigyelt tanúsítványok)  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | karakterlánc-tömb


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők. A sablonok ideálisak egy vagy több olyan virtuális gép telepítésekor, amelyek a tanúsítványok üzembe helyezés utáni frissítését igénylik. A bővítmény telepíthető az egyes virtuális gépek vagy a virtuális gép méretezési csoportok. A séma és a konfiguráció mindkét sablontípusban közös. 

A virtuálisgép-bővítmény JSON-konfigurációját a sablon virtuálisgép-erőforrás-töredékébe kell ágyazni, különösen `"resources": []` a virtuálisgép-sablon objektumába, és az objektum alatt `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` beállított virtuálisgép-méretezés esetén.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Az Azure PowerShell telepítése

Az Azure PowerShell segítségével üzembe helyezheti a Key Vault virtuális gép bővítményegy meglévő virtuális gép vagy virtuális gép méretezési csoport. 

* A bővítmény üzembe helyezése virtuális gépen:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* A bővítmény üzembe helyezése virtuálisgép-méretezési csoporton:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Az Azure CLI üzembe helyezése

Az Azure CLI használható a Key Vault virtuális gép bővítményének üzembe helyezéséhez egy meglévő virtuális gépre vagy virtuálisgép-méretezési csoportra. 
 
* A bővítmény üzembe helyezése virtuális gépen:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* A bővítmény üzembe helyezése virtuálisgép-méretezési csoporton:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Kérjük, vegye figyelembe a következő korlátozásokat/követelményeket:
- A Key Vault korlátozásai:
  - A telepítés időpontjában léteznie kell. 
  - A Key Vault hozzáférési házirendje az MSI használatával virtuális gép/VMSS identitáshoz van beállítva


## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmény-üzembe helyezések állapotára vonatkozó adatok az Azure Portalról és az Azure PowerShell használatával is lekérdezhetők. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure PowerShell használatával.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.
