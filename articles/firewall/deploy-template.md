---
title: Üzembe helyezése Azure tűzfal-sablon használatával
description: Üzembe helyezése Azure tűzfal-sablon használatával
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: d32e6e29c287d140c28206743e36dc025b26158b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991334"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Üzembe helyezése Azure tűzfal-sablon használatával

Ez a sablon létrehoz egy tűzfal és a egy tesztelési hálózati környezet. A hálózat egy virtuális hálózatot három alhálózattal rendelkezik: *AzureFirewallSubnet*, *ServersSubnet*, és a egy *JumpboxSubnet*. A ServersSubnet és a JumpboxSubnet tartalmaz egy-egy 2 magos Windows Servert.

A tűzfal az AzureFirewallSubnet alhálózaton található, és egy egyetlen szabályt tartalmazó alkalmazásszabály-gyűjteménnyel van konfigurálva, amely a www.microsoft.com webhelyhez való hozzáférést engedélyezi.

A felhasználó által létrehozott útvonal a ServersSubnet alhálózatról érkező hálózati forgalmat átirányítja a tűzfalon, amelyen a tűzfalszabályok alkalmazva vannak.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="template-location"></a>Sablon helye

A sablon a következő helyen található:

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox)

Olvassa el a bevezetés, és ha készen áll a központi telepítése, kattintson az **üzembe helyezés az Azure**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Először források a létrehozott a tűzfallal és majd amikor már nincs rá szükség, használhatja a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolítható az erőforráscsoport, a tűzfal és az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="next-steps"></a>További lépések

Ezután az Azure tűzfalnaplók figyelheti:

- [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./tutorial-diagnostics.md)

