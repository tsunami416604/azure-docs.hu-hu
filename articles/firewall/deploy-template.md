---
title: Azure Firewall üzembe helyezése sablon használatával
description: Telepítse az Azure Firewall sablon használatával. A létrehozott hálózat rendelkezik egy virtuális hálózattal három alhálózattal. Két, kétmagos Windows Server virtuális gépek vannak telepítve.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169200"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Azure Firewall üzembe helyezése sablon használatával

Az [AzureFirewall sandbox telepítősablonja](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) tűzfallal hoz létre teszthálózati környezetet. A hálózat egy virtuális hálózattal (VNet) rendelkezik három alhálózattal: *AzureFirewallSubnet*, *ServersSubnet*és *JumpboxSubnet*. A *ServersSubnet* és *a JumpboxSubnet* alhálózat egyetlen, kétmagos Windows Server virtuális géppel rendelkezik.

A tűzfal az *AzureFirewallSubnet* alhálózatban található, és egyetlen szabállyal `www.microsoft.com`rendelkezik egy alkalmazásszabály-gyűjteménynel, amely lehetővé teszi a hozzáférést.

A felhasználó által definiált útvonalpontok hálózati forgalmat a *ServersSubnet* alhálózat a tűzfalon keresztül, ahol a tűzfal szabályok vonatkoznak.

Az Azure Firewall szolgáltatásról az [Azure Firewall telepítése és konfigurálása az Azure Portalhasználatával című](tutorial-firewall-deploy-portal.md)témakörben talál további információt.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Az Azure Tűzfal üzembe helyezéséhez használja a sablont

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

**Az Azure Firewall telepítése és telepítése a sablon használatával:**

1. A sablon [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)elérése a(z) programban.
   
1. Olvassa el a bevezetést, és amikor készen áll az üzembe helyezésre, válassza **a Telepítés az Azure-ba**lehetőséget.
   
1. Ha szükséges, jelentkezzen be az Azure Portalra. 

1. A portálon az **AzureFirewall sandbox beállítása lapon** írja be vagy válassza ki a következő értékeket:
   
   - **Erőforráscsoport**: Válassza **az Új létrehozása**lehetőséget, írja be az erőforráscsoport nevét, és válassza az **OK**gombot. 
   - **Virtuális hálózat neve**: Írja be az új virtuális hálózat nevét. 
   - **Rendszergazdai felhasználónév**: Írja be a rendszergazdai felhasználói fiók felhasználónevét.
   - **Rendszergazdai jelszó**: Írjon be rendszergazdai jelszót. 
   
1. Olvassa el a feltételeket, majd válassza **az Elfogadom a fent meghatározott feltételeket**.
   
1. Válassza a **Beszerzés** lehetőséget.
   
   Az erőforrások létrehozása néhány percet vesz igénybe. 
   
1. Fedezze fel a tűzfallal létrehozott erőforrásokat. 

A sablonban lévő tűzfal JSON-szintaxisáról és tulajdonságairól a [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)című témakörben olvashat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szüksége, eltávolíthatja az erőforráscsoportot, a tűzfalat és az összes kapcsolódó erőforrást az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) PowerShell parancs futtatásával. *A MyResourceGroup*nevű erőforráscsoport eltávolításához futtassa a következőt: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Még ne távolítsa el az erőforráscsoportot és a tűzfalat, ha azt tervezi, hogy folytatja a tűzfalfigyelési oktatóanyagot. 

## <a name="next-steps"></a>További lépések

Ezután figyelheti az Azure tűzfal naplóit:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./tutorial-diagnostics.md)
