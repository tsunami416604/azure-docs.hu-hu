---
title: Azure Firewall üzembe helyezése sablon használatával
description: Azure Firewall üzembe helyezése sablon használatával
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/9/2018
ms.author: victorh
ms.openlocfilehash: c0a6cda54a58e3cc03ba31e221fb57fc725dd779
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839389"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Azure Firewall üzembe helyezése sablon használatával

A [AzureFirewall létrehozása – a homokozó telepítési sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) egy teszt hálózati környezetet hoz létre tűzfallal. A hálózat egy virtuális hálózattal (VNet) rendelkezik, amely három alhálózattal rendelkezik: *AzureFirewallSubnet*, *ServersSubnet*és *JumpboxSubnet*. A *ServersSubnet* és a *JumpboxSubnet* alhálózatnak egyetlen, kétmagos Windows Server rendszerű virtuális géppel kell rendelkeznie.

A tűzfal a *AzureFirewallSubnet* alhálózatban található, és egy olyan szabály-gyűjteményt tartalmaz, amely lehetővé teszi a `www.microsoft.com`elérését.

A felhasználó által megadott útvonal hálózati forgalmat végez a *ServersSubnet* alhálózatról a tűzfalon keresztül, ahol a tűzfalszabályok érvényesek.

További információ a Azure Firewallről: [Azure Firewall központi telepítése és konfigurálása a Azure Portal használatával](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Azure Firewall üzembe helyezése sablon használatával

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

**Azure Firewall telepítése és telepítése a sablon használatával:**

1. A sablon elérése a következő helyen: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox).
   
1. Olvassa el a bevezetést, és ha készen áll a telepítésre, válassza a **telepítés az Azure**-ba lehetőséget.
   
1. Ha szükséges, jelentkezzen be a Azure Portalba. 

1. A portálon, a **AzureFirewall-példány létrehozása** lapon írja be vagy válassza ki a következő értékeket:
   
   - **Erőforráscsoport**: válassza az **új létrehozása**elemet, írja be az erőforráscsoport nevét, majd kattintson **az OK gombra**. 
   - **Virtual Network neve**: írja be az új VNet nevét. 
   - Rendszergazdai **Felhasználónév**: adjon meg egy felhasználónevet a rendszergazda felhasználói fiókhoz.
   - Rendszergazdai **jelszó**: adjon meg egy rendszergazdai jelszót. 
   
1. Olvassa el a használati feltételeket, majd válassza az Elfogadom **a fenti feltételeket és**kikötéseket lehetőséget.
   
1. Válassza a **Beszerzés** lehetőséget.
   
   Az erőforrások létrehozása több percet is igénybe vehet. 
   
1. Ismerkedjen meg a tűzfallal létrehozott erőforrásokkal. 

A sablonban található tűzfal JSON-szintaxisáról és tulajdonságairól a [Microsoft. Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)című témakörben olvashat bővebben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, eltávolíthatja az erőforráscsoportot, a tűzfalat és az összes kapcsolódó erőforrást a [Remove-AzResourceGroup PowerShell-](/powershell/module/az.resources/remove-azresourcegroup) parancs futtatásával. A *MyResourceGroup*nevű erőforráscsoport eltávolításához futtassa a következőt: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Ne távolítsa el az erőforráscsoportot és a tűzfalat, ha azt tervezi, hogy folytatja a tűzfal-figyelési oktatóanyagot. 

## <a name="next-steps"></a>További lépések

Ezután nyomon követheti a Azure Firewall naplókat:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./tutorial-diagnostics.md)
