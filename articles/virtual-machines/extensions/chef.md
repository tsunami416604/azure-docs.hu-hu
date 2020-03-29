---
title: Chef-bővítmény az Azure-beli virtuális gépekhez
description: Telepítse a Chef-ügyfelet egy virtuális gépre a Chef virtuálisgép-bővítmény használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76544718"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Chef virtuálisgép-bővítmény Linuxra és Windowsra

A Chef szoftver egy Linux és Windows rendszerhez készült DevOps automatizálási platformot biztosít, amely lehetővé teszi a fizikai és a virtuális kiszolgálók konfigurációjának kezelését. A Chef virtuálisgép-bővítmény egy bővítmény, amely lehetővé teszi a Chef virtuális gépeken.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Chef virtuálisgép-bővítmény támogatja az összes [bővítmény támogatott operációs rendszer](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) az Azure-ban.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Chef virtuálisgép-bővítmény megköveteli, hogy a cél virtuális gép csatlakozik az internethez annak érdekében, hogy lekérje a Chef ügyfél hasznos adat a tartalomkézbesítési hálózat (CDN).  

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Chef virtuálisgép-bővítmény sémáját mutatja. A bővítményhez legalább a Chef-kiszolgáló URL-címére, az érvényesítési ügyfélnévre és a chef-kiszolgáló érvényesítési kulcsára van szükség; ezek az értékek a `knife.rb` [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) vagy egy önálló [Chef Server](https://downloads.chef.io/chef-server)telepítésekor letöltött starter-kit.zip fájlban találhatók. Mivel az érvényesítési kulcsot bizalmas adatként kell kezelni, a **protectedSettings** elem alatt kell konfigurálni, ami azt jelenti, hogy csak a cél virtuális gépen lesz visszafejtve.

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

### <a name="core-property-values"></a>Alapvető tulajdonságértékek

| Név | Érték / Példa | Adattípus
| ---- | ---- | ----
| apiVersion | `2017-12-01` | karakterlánc (dátum) |
| közzétevő | `Chef.Bootstrap.WindowsAzure` | sztring |
| type | `LinuxChefClient`(Linux), `ChefClient` (Windows) | sztring |
| typeHandlerVersion | `1210.13` | string (dupla) |

### <a name="settings"></a>Beállítások

| Név | Érték / Példa | Adattípus | Kötelező?
| ---- | ---- | ---- | ----
| beállítások/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | karakterlánc (url) | I |
| beállítások/bootstrap_options/validation_client_name | `myorg-validator` | sztring | I |
| beállítások/futtatási lista | `recipe[mycookbook::default]` | sztring | I |

### <a name="protected-settings"></a>Védett beállítások

| Név | Példa | Adattípus | Kötelező?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | sztring | I |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők. A sablonok segítségével egy vagy több virtuális gépet telepíthet, telepítheti a Chef ügyfelet, csatlakozhat a Chef kiszolgálóhoz, és végrehajthatja a kezdeti konfigurációt a kiszolgálón a [Run-list (Futtatás lista)](https://docs.chef.io/run_lists.html) meghatározása szerint.

A Chef virtuálisgép-bővítményt tartalmazó mintaerőforrás-kezelősablon az [Azure gyorsútmutató-gyűjteményében](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm)található.

A virtuálisgép-bővítmény JSON-konfigurációja beágyazható a virtuálisgép-erőforrásba, vagy elhelyezhető egy Erőforrás-kezelő JSON-sablon gyökér- vagy legfelső szintjén. A JSON-konfiguráció elhelyezése hatással van az erőforrás nevének és típusának értékére. További információt a [Név és a gyermekerőforrások típusának beállítása](../../azure-resource-manager/resource-manager-template-child-resource.md)című témakörben talál.

## <a name="azure-cli-deployment"></a>Az Azure CLI üzembe helyezése

Az Azure CLI használható a Chef virtuálisgép-bővítmény üzembe helyezéséhez egy meglévő virtuális gépre. Cserélje le a **validation_key** az érvényesítési kulcs `.pem` tartalmára (ez a fájl kiterjesztésként).  Cserélje le **a validation_client_name,** **chef_server_url** `knife.rb` és **run_list** ezekkel az értékekkel a Starter Kit fájljában.

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

A bővítmény-üzembe helyezések állapotára vonatkozó adatok az Azure Portalról és az Azure CLI használatával is lekérdezhetők. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

A bővítmény-végrehajtási kimenet a következő fájlba kerül:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és jelentésük

| Hibakód | Jelentés | Lehetséges művelet |
| :---: | --- | --- |
| 51 | Ez a bővítmény nem támogatott a virtuális gép operációs rendszerében | |

További hibaelhárítási információk a [Chef vm bővítményében található readme](https://github.com/chef-partners/azure-chef-extension).

> [!NOTE]
> A Chef-hez közvetlenül kapcsolódó minden más, a [Chef támogatási szolgálatához fordulhat.](https://www.chef.io/support/)

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.
