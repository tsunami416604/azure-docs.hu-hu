---
title: A Linux rendszerhez készült virtuálisgép-bővítmény Azure Key Vault
description: Telepítsen egy ügynököt, amely a virtuális gépek Key Vault tanúsítványainak automatikus frissítését végzi a virtuálisgép-bővítmény használatával.
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: e653adfd7a148cea7bfb1ecfdbbf386eff0c3e86
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723323"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>A Linux rendszerhez készült virtuálisgép-bővítmény Key Vault

A Key Vault virtuálisgép-bővítmény az Azure Key vaultban tárolt tanúsítványok automatikus frissítését teszi lehetővé. Pontosabban a bővítmény figyeli a Key vaultban tárolt megfigyelt tanúsítványok listáját.  A módosítás észlelése után a bővítmény lekéri és telepíti a megfelelő tanúsítványokat. A Key Vault VM-bővítményt a Microsoft közzétette és támogatja, jelenleg Linux rendszerű virtuális gépeken. Ez a dokumentum részletesen ismerteti a Linux rendszerhez készült Key Vault virtuálisgép-bővítmény támogatott platformokat, konfigurációkat és telepítési lehetőségeit. 

### <a name="operating-system"></a>Operációs rendszer

A Key Vault virtuálisgép-bővítmény támogatja ezeket a Linux-disztribúciókat:

- Ubuntu – 1604
- Ubuntu – 1804
- Debian – 9
- SUSE – 15 

### <a name="supported-certificate-content-types"></a>Támogatott tanúsítvány-tartalomtípusok

- PKCS #12
- PEM

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Key Vault virtuálisgép-bővítmény sémáját jeleníti meg. A kiterjesztéshez nincs szükség védett beállításokra – az összes beállítás biztonsági hatás nélkül tekinthető információnak. A bővítményhez meg kell adni a figyelt titkok listáját, a lekérdezés gyakoriságát és a célhely tanúsítványtárolóját. Ezek a következők:  
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
> A megfigyelt tanúsítványok URL-címeinek űrlapnak kell lenniük `https://myVaultName.vault.azure.net/secrets/myCertName` .
> 
> Ennek az az oka, hogy az `/secrets` elérési út a teljes tanúsítványt adja vissza, beleértve a titkos kulcsot is, míg az `/certificates` elérési út nem. A tanúsítványokkal kapcsolatos további információkért tekintse meg a következőt: [Key Vault tanúsítványok](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)


### <a name="property-values"></a>Tulajdonságértékek

| Name | Érték/példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| közzétevő | Microsoft.Azure.KeyVault | sztring |
| típus | KeyVaultForLinux | sztring |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | sztring |
| certificateStoreName | MY | sztring |
| linkOnRenewal | hamis | logikai |
| certificateStoreLocation  | LocalMachine | sztring |
| requiredInitialSync | igaz | logikai |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | karakterlánc-tömb


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. A sablonok ideálisak egy vagy több olyan virtuális gép üzembe helyezéséhez, amelyek a tanúsítványok telepítés utáni frissítését igénylik. A bővítmény az egyes virtuális gépekre vagy virtuálisgép-méretezési csoportokra is telepíthető. A séma és a konfiguráció a sablonok típusainál is gyakori. 

A virtuálisgép-bővítmények JSON-konfigurációját a sablon virtuálisgép-erőforrás szilánkján belül kell beágyazni, kifejezetten `"resources": []` a virtuálisgép-sablonhoz, és az objektum alatt található virtuálisgép-méretezési csoport esetében `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` .

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


## <a name="azure-powershell-deployment"></a>Azure PowerShell üzemelő példány

A Azure PowerShell használatával telepítheti a Key Vault virtuálisgép-bővítményt egy meglévő virtuális gépre vagy virtuálisgép-méretezési csoportba. 

* A bővítmény üzembe helyezése egy virtuális gépen:
    
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

## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése

Az Azure CLI használatával telepítheti a Key Vault virtuálisgép-bővítményt egy meglévő virtuális gépre vagy virtuálisgép-méretezési csoportba. 
 
* A bővítmény üzembe helyezése egy virtuális gépen:
    
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

Vegye figyelembe a következő korlátozásokat/követelményeket:
- Key Vault korlátozások:
  - A telepítés időpontjában léteznie kell 
  - A Key Vault hozzáférési szabályzat a virtuális gép/VMSS identitásának mustbe van beállítva felügyelt identitás használatával. Lásd: [Key Vault hitelesítés megadása felügyelt identitással](../../key-vault/managed-identity.md)


## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok beolvashatók a Azure Portalból és a Azure PowerShell használatával. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot a Azure PowerShell használatával.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
