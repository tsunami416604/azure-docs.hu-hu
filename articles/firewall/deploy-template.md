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
ms.openlocfilehash: 1a732e22d72c36afe11030e42bae529baa35df1a
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991378"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Üzembe helyezése Azure tűzfal-sablon használatával

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Az Azure-tűzfal cikkekben szereplő példák feltételezik, hogy, hogy már engedélyezte az Azure-tűzfal nyilvános előzetes verziója. További információkért lásd: [az Azure-tűzfal nyilvános előzetes verziójának engedélyezése](public-preview.md).

Ez a sablon létrehoz egy tűzfal és a egy tesztelési hálózati környezet. A hálózat egy virtuális hálózatot három alhálózattal rendelkezik: *AzureFirewallSubnet*, *ServersSubnet*, és a egy *JumpboxSubnet*. A ServersSubnet és JumpboxSubnet is egy 2 magos Windows Server bennük.

A tűzfal a AzureFirewallSubnet van, és az alkalmazás-szabálygyűjtemény egyetlen szabállyal, amely lehetővé teszi a hozzáférést, www.microsoft.com van konfigurálva.

Egy felhasználó által definiált útvonal jön létre, amely a hálózati forgalom mutat a ServersSubnet a tűzfalon keresztül, az, ahol a tűzfalszabályok vannak alkalmazva.

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

- [Oktatóanyag: A figyelő Azure tűzfal-naplókon](./tutorial-diagnostics.md)

