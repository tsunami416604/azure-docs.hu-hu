---
title: Chef-bővítmény Azure-beli virtuális gépekhez | Microsoft Docs
description: Telepítse a Chef-ügyfelet egy virtuális gépre a Chef VM bővítmény használatával.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/21/2018
ms.author: akjosh
ms.openlocfilehash: e82a5fefcc7f582df65d945735d9840fc3e49829
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169150"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Chef VM-bővítmény Linux és Windows rendszerekhez

A Chef szoftver egy Linux és Windows rendszerhez készült DevOps automatizálási platformot biztosít, amely lehetővé teszi a fizikai és a virtuális kiszolgálók konfigurációjának kezelését. A Chef VM bővítmény egy bővítmény, amely lehetővé teszi a Chef használatát a virtuális gépeken.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Chef virtuálisgép-bővítmény támogatott az Azure-ban [támogatott operációs rendszerek összes bővítményében](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) .

### <a name="internet-connectivity"></a>Internetkapcsolat

A Chef virtuálisgép-bővítmény megköveteli, hogy a célként megadott virtuális gép csatlakoztatva legyen az internethez, hogy lekérje a Chef-ügyfél adattartalmát a Content Delivery Network (CDN) szolgáltatásból.  

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Chef virtuálisgép-bővítmény sémáját mutatja be. A kiterjesztéshez legalább a Chef-kiszolgáló URL-címére, az érvényesítési ügyfél nevére és a Chef-kiszolgáló érvényesítési kulcsára van szükség; Ezek az értékek a `knife.rb` [Chef automatizálása](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) vagy egy önálló Chef- [kiszolgáló](https://downloads.chef.io/chef-server)telepítésekor letöltött Starter-Kit. zip fájlban találhatók. Mivel az ellenőrző kulcsot bizalmas adatokként kell kezelni, azt a **protectedsettingsfromkeyvault** elem alatt kell konfigurálni, ami azt jelenti, hogy csak a célként megadott virtuális gépen lesz visszafejtve.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.12",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Alapvető tulajdonságok értékei

| Name (Név) | Érték és példa | Adattípus
| ---- | ---- | ----
| apiVersion | `2017-12-01` | string (date) |
| publisher | `Chef.Bootstrap.WindowsAzure` | Karakterlánc |
| type | `LinuxChefClient`(Linux), `ChefClient` (Windows) | Karakterlánc |
| typeHandlerVersion | `1210.12` | string (double) |

### <a name="settings"></a>Beállítások

| Name (Név) | Érték és példa | Adattípus | Kötelező?
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | string (url) | I |
| settings/bootstrap_options/validation_client_name | `myorg-validator` | Karakterlánc | I |
| settings/runlist | `recipe[mycookbook::default]` | Karakterlánc | I |

### <a name="protected-settings"></a>Védett beállítások

| Name (Név) | Példa | Adattípus | Kötelező?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | Karakterlánc | I |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. A sablonokat egy vagy több virtuális gép üzembe helyezésére, a Chef-ügyfél telepítésére, a Chef-kiszolgálóhoz való kapcsolódásra, valamint a [futtatási lista](https://docs.chef.io/run_lists.html) által meghatározott kezdeti konfiguráció végrehajtására használhatja.

A Chef VM-bővítményt tartalmazó példa Resource Manager-sablon az [Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm)rövid útmutatójában található.

Virtuálisgép-bővítmények JSON konfigurációjának a virtuális gép típusú erőforrást belülre, vagy elhelyezve, a legfelső szintű vagy a legfelső szintű Resource Managerből származó JSON-sablon. A JSON konfigurációs elhelyezését hatással van az erőforrás nevét, és írja be az értékét. További információkért lásd: [állítsa be a nevét és típusát gyermekerőforrásait](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az Azure CLI használatával üzembe helyezheti a Chef virtuálisgép-bővítményt egy meglévő virtuális gépre. Cserélje le a **validation_key** az érvényesítési kulcs tartalmára (ez a fájl `.pem` kiterjesztésként).  Cserélje le a **validation_client_name**, a **chef_server_url** és a **run_list** értéket `knife.rb` az alapszintű csomagban található fájl értékeire.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.12 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Hibaelhárítás és támogatás

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure parancssori felület használatával. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő fájlt:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és azok jelentését

| Hibakód | Jelentés | A művelet lehetséges |
| :---: | --- | --- |
| 51 | Ez a bővítmény nem támogatott a virtuális gép operációs rendszerében | |

További hibaelhárítási információk a [Chef VM-bővítmény readme](https://github.com/chef-partners/azure-chef-extension)-ban találhatók.

## <a name="next-steps"></a>További lépések

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
