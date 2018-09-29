---
title: Chef kiterjesztése az Azure virtuális gépek |} A Microsoft Docs
description: A Chef ügyfél központi telepítése egy virtuális gépet, a Chef Virtuálisgép-bővítmény használatával.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: roiyz
ms.openlocfilehash: 159ce1b565068e2cfdb3cb1cb2e5b5f72ff6848f
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451357"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>A Linux és Windows Chef Virtuálisgép-bővítmény

A Chef szoftver egy Linux és Windows rendszerhez készült DevOps automatizálási platformot biztosít, amely lehetővé teszi a fizikai és a virtuális kiszolgálók konfigurációjának kezelését. A Chef Virtuálisgép-bővítmény, amely lehetővé teszi a Chef a virtuális gépek bővítmény.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Chef Virtuálisgép-bővítményt az összes támogatott a [bővítmény támogatott operációs rendszer a](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) az Azure-ban.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Chef Virtuálisgép-bővítmény szükséges, hogy a céloldali virtuális gép csatlakozik az internethez annak érdekében, hogy a Chef ügyfél hasznos adatainak lekérése az content delivery network (CDN).  

## <a name="extension-schema"></a>Bővítményséma

A következő JSON-ra a sémát a Chef Virtuálisgép-bővítmény mutatja be. A bővítmény telepítéséhez legalább a Chef URL-címe, az érvényesítési ügyfél nevét és az érvényesítési kulcs a Chef kiszolgáló; Ezek az értékek található a `knife.rb` fájlt a letöltött telepítésekor starter-kit.zip [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) vagy egy önálló [Chef kiszolgáló](https://downloads.chef.io/chef-server). Az érvényesítési kulcs kényes adatként kell kezelni, mert azt kell konfigurálni a a **protectedSettings** elem, ami azt jelenti, hogy azt a rendszer csak visszafejti a cél virtuális gépen.

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

### <a name="core-property-values"></a>Core tulajdonságértékek

| Name (Név) | Érték és példa | Adattípus
| ---- | ---- | ---- | ----
| apiVersion | `2017-12-01` | karakterlánc (dátum) |
| publisher | `Chef.Bootstrap.WindowsAzure` | sztring |
| type | `LinuxChefClient` (Linux), `ChefClient` (Windows) | sztring |
| typeHandlerVersion | `1210.12` | karakterlánc (kétirányú) |

### <a name="settings"></a>Beállítások

| Name (Név) | Érték és példa | Adattípus | Kötelező?
| ---- | ---- | ---- | ----
| beállítások/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | karakterlánc (url) | I |
| beállítások/bootstrap_options/validation_client_name | `myorg-validator` | sztring | I |
| beállítások/runlist | `recipe[mycookbook::default]` | sztring | I |

### <a name="protected-settings"></a>Védett beállításai

| Name (Név) | Példa | Adattípus | Kötelező?
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

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Sablonok segítségével helyezhet üzembe egy vagy több virtuális gépeket, a Chef-ügyfél telepítése, csatlakozhat a Chef és a hajtsa végre a kiszolgálón a kezdeti konfiguráció által meghatározott a [Run-list](https://docs.chef.io/run_lists.html)

Egy mintául szolgáló Resource Manager-sablon, amely tartalmazza a Chef Virtuálisgép-bővítmény találhatók a [Azure gyors üzembe helyezési katalógus](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

Virtuálisgép-bővítmények JSON konfigurációjának a virtuális gép típusú erőforrást belülre, vagy elhelyezve, a legfelső szintű vagy a legfelső szintű Resource Managerből származó JSON-sablon. A JSON konfigurációs elhelyezését hatással van az erőforrás nevét, és írja be az értékét. További információkért lásd: [állítsa be a nevét és típusát gyermekerőforrásait](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az Azure CLI is használható a Chef Virtuálisgép-bővítmény telepítéséhez a meglévő virtuális géphez. Cserélje le a **validation_key** az érvényesítési kulcs tartalmát (a fájlt egy `.pem` kiterjesztés).  Cserélje le **validation_client_name**, **chef_server_url** és **run_list** e értékeit a `knife.rb` az Starter Kit fájlt.

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
| 51 | Ezt a bővítményt a virtuális gép operációs rendszeren nem támogatott | |

További hibaelhárítási információk megtalálhatók a [Chef Virtuálisgép-bővítmény fontos](https://github.com/chef-partners/azure-chef-extension).

## <a name="next-steps"></a>További lépések

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
