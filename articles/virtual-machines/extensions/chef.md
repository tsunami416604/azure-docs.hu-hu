---
title: Chef-bővítmény Azure-beli virtuális gépekhez
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
ms.openlocfilehash: a21b8f2fea7433e9f65fd790321a28ea47a38c79
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76544718"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Chef VM-bővítmény Linux és Windows rendszerekhez

A Chef szoftver egy Linux és Windows rendszerhez készült DevOps automatizálási platformot biztosít, amely lehetővé teszi a fizikai és a virtuális kiszolgálók konfigurációjának kezelését. A Chef VM bővítmény egy bővítmény, amely lehetővé teszi a Chef használatát a virtuális gépeken.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Chef virtuálisgép-bővítmény támogatott az Azure-ban [támogatott operációs rendszerek összes bővítményében](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) .

### <a name="internet-connectivity"></a>Internetkapcsolat

A Chef virtuálisgép-bővítmény megköveteli, hogy a célként megadott virtuális gép csatlakoztatva legyen az internethez, hogy lekérje a Chef-ügyfél adattartalmát a Content Delivery Network (CDN) szolgáltatásból.  

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Chef virtuálisgép-bővítmény sémáját mutatja be. A kiterjesztéshez legalább a Chef-kiszolgáló URL-címére, az érvényesítési ügyfél nevére és a Chef-kiszolgáló érvényesítési kulcsára van szükség; Ezek az értékek a [Chef automatizálása](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) vagy egy önálló [Chef-kiszolgáló](https://downloads.chef.io/chef-server)telepítésekor letöltött Starter-Kit. zip `knife.rb` fájlban találhatók. Mivel az ellenőrző kulcsot bizalmas adatokként kell kezelni, azt a **protectedsettingsfromkeyvault** elem alatt kell konfigurálni, ami azt jelenti, hogy csak a célként megadott virtuális gépen lesz visszafejtve.

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
    "typeHandlerVersion": "1210.13",
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

| Name (Név) | Érték/példa | Adattípus
| ---- | ---- | ----
| apiVersion | `2017-12-01` | karakterlánc (dátum) |
| közzétevő | `Chef.Bootstrap.WindowsAzure` | sztring |
| type | `LinuxChefClient`(Linux), `ChefClient` (Windows) | sztring |
| typeHandlerVersion | `1210.13` | karakterlánc (dupla) |

### <a name="settings"></a>Beállítások

| Name (Név) | Érték/példa | Adattípus | Kötelező?
| ---- | ---- | ---- | ----
| beállítások/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | karakterlánc (URL) | I |
| beállítások/bootstrap_options/validation_client_name | `myorg-validator` | sztring | I |
| beállítások/Runlist | `recipe[mycookbook::default]` | sztring | I |

### <a name="protected-settings"></a>Védett beállítások

| Name (Név) | Példa | Adattípus | Kötelező?
| ---- | ---- | ---- | ---- |
| Protectedsettingsfromkeyvault/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | sztring | I |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. A sablonokat egy vagy több virtuális gép üzembe helyezésére, a Chef-ügyfél telepítésére, a Chef-kiszolgálóhoz való kapcsolódásra, valamint a [futtatási lista](https://docs.chef.io/run_lists.html) által meghatározott kezdeti konfiguráció végrehajtására használhatja.

A Chef VM-bővítményt tartalmazó példa Resource Manager-sablon az [Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm)rövid útmutatójában található.

A virtuálisgép-bővítmény JSON-konfigurációja beágyazható a virtuális gép erőforrásaiba, vagy egy Resource Manager JSON-sablon legfelső szintű vagy legfelső szintjén helyezhető el. A JSON-konfiguráció elhelyezése hatással van az erőforrás nevének és típusának értékére. További információ: [a gyermek erőforrások nevének és típusának beállítása](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése

Az Azure CLI használatával üzembe helyezheti a Chef virtuálisgép-bővítményt egy meglévő virtuális gépre. Cserélje le a **validation_keyt** az érvényesítési kulcs tartalmára (ez a fájl `.pem` kiterjesztésként).  Cserélje le a **validation_client_name**, a **chef_server_url** és a **Run_list** értékeit az `knife.rb` alapszintű csomagban lévő fájlból.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.13 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Hibaelhárítás és támogatás

A bővítmények állapotával kapcsolatos adatok a Azure Portalból és az Azure CLI használatával kérhetők le. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

A bővítmény végrehajtásának kimenete a következő fájlba van naplózva:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és jelentéseik

| Hibakód | Jelentés | Lehetséges művelet |
| :---: | --- | --- |
| 51 | Ez a bővítmény nem támogatott a virtuális gép operációs rendszerében | |

További hibaelhárítási információk a [Chef VM-bővítmény readme](https://github.com/chef-partners/azure-chef-extension)-ban találhatók.

> [!NOTE]
> A Chefhez közvetlenül kapcsolódó bármilyen más esetben forduljon a [Chef ügyfélszolgálatához](https://www.chef.io/support/).

## <a name="next-steps"></a>További lépések

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
