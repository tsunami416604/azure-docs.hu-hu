---
title: Üzembe helyezése Azure tűzfal-sablon használatával
description: Üzembe helyezése Azure tűzfal-sablon használatával
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 12/01/2018
ms.author: victorh
ms.openlocfilehash: e0fbec8b22993345114d8d6642e42095191d0b37
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046274"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Üzembe helyezése Azure tűzfal-sablon használatával

A [létrehozása AzureFirewall védőfal telepítő sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox) a tesztelési hálózati környezet hoz létre tűzfalat. A hálózat egy virtuális hálózat (VNet) három alhálózattal rendelkezik: *AzureFirewallSubnet*, *ServersSubnet*, és *JumpboxSubnet*. A *ServersSubnet* és *JumpboxSubnet* minden egyes alhálózatnak rendelkeznie egy egyetlen, két mag a Windows Server virtuális gépet.

A tűzfal van a *AzureFirewallSubnet* alhálózathoz, és rendelkezik az alkalmazás-szabálygyűjtemény egy szabályt, amely lehetővé teszi a hozzáférést a *www.microsoft.com*.

Egy felhasználó által megadott útvonalat mutat érkező hálózati forgalmat a *ServersSubnet* alhálózat a tűzfalon, ahol a tűzfalszabályok vannak alkalmazva.

Az Azure-tűzfallal kapcsolatos további információkért lásd: [központi telepítése és konfigurálása az Azure portal segítségével Azure tűzfal](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>A sablon segítségével üzembe helyezése az Azure-tűzfal

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

**Telepítése és üzembe helyezése az Azure-tűzfal a sablon használatával:**

1. A sablon eléréséhez [ https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox).
   
1. Olvassa el a bevezető, és ha készen áll a központi telepítése, válassza ki **üzembe helyezés az Azure**.
   
1. Ha szükséges, jelentkezzen be az Azure Portalon. 

1. A portálon a a **hozzon létre egy tesztkörnyezet beállítása AzureFirewall** lapon adja meg a következő értékeket:
   
   - **Erőforráscsoport**: Válassza ki **új létrehozása**, írja be az erőforráscsoport nevét, és válassza ki **OK**. 
   - **Virtuális hálózat neve**: Adja meg az új virtuális hálózat nevét. 
   - **Rendszergazdai felhasználónév**: Írjon be egy rendszergazdai felhasználói fiókhoz tartozó felhasználónév.
   - **Rendszergazdai jelszó**: Írjon be egy rendszergazdai jelszót. 
   
1. Olvassa el a feltételeket és kikötéseket, és válassza ki **elfogadom a feltételeket és a fenti feltételeket**.
   
1. Válassza a **Beszerzés** lehetőséget.
   
   Az erőforrások létrehozása néhány percet vesz igénybe. 
   
1. Fedezze fel az erőforrásokat, a tűzfal-ekkel hozta létre. 

A JSON-szintaxist és a egy sablonban tűzfalak tulajdonságaival kapcsolatos tudnivalókért lásd: [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, eltávolíthatja az erőforráscsoportot, a tűzfal és az összes kapcsolódó erőforrást futtatásával a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) PowerShell-parancsot. Eltávolítható egy nevű erőforráscsoport *MyResourceGroup*futtassa: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Ne távolítsa el az erőforráscsoportot és a tűzfal, ha azt tervezi, hogy a tűzfal oktatóanyag figyelési folytassa. 

## <a name="next-steps"></a>További lépések

Ezután az Azure tűzfalnaplók figyelheti:

> [!div class="nextstepaction"]
> [Oktatóanyag: A figyelő Azure tűzfal-naplókon](./tutorial-diagnostics.md)
