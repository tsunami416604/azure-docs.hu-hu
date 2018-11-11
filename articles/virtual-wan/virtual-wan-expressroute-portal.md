---
title: ExpressRoute-kapcsolatok létrehozása az Azure-hoz vagy helyszíni környezetekhez az Azure Virtual WAN használatával | Microsoft Docs
description: Az oktatóanyag azt ismerteti, hogyan hozhatók létre az Azure-ba vagy helyszíni környezetekbe irányuló ExpressRoute-kapcsolatok az Azure Virtual WAN használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/5/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporoate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: c02020ba8d49b123cf8914214d52ac40896a3c20
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248180"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan-preview"></a>Oktatóanyag: ExpressRoute-társítás létrehozása az Azure Virtual WAN (előzetes verzió) használatával

Az oktatóanyag bemutatja, hogyan kapcsolódhat a Virtual WAN használatával az Azure-ban lévő erőforrásaihoz egy ExpressRoute-kapcsolatcsoport és társítás segítségével. A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális WAN létrehozása
> * Elosztó létrehozása
> * Egy kapcsolatcsoport keresése és társítása a központhoz
> * A kapcsolatcsoport társítása a központ(ok)hoz
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * A virtuális WAN megtekintése
> * Erőforrás állapotának megtekintése
> * Kapcsolatok monitorozása

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>A funkció regisztrálása

A Virtual WAN konfigurálása előtt regisztrálnia kell az előfizetését az előzetes verzióban. Máskülönben nem használhatja a Virtual WAN-t a portálon. Ehhez küldjön egy e-mailt a következő címre az előfizetés azonosítójával: **azurevirtualwan@microsoft.com**. Az előfizetés regisztrációja után egy e-mailt fog kapni.

**Előzetes verzióval kapcsolatos szempontok:**

* Régiónkénti rendelkezésre állás: USA nyugati középső régiója
* Olyan országban, amely támogatja az [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#where-is-expressroute-global-reach-supported) használatát, engedélyezni kell az ExpressRoute-kapcsolatcsoportot.

## <a name="vnet"></a>1. Virtuális hálózat létrehozása

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Virtuális WAN létrehozása

Egy böngészőből lépjen az [Azure Portalra (előzetes verzió)](https://aka.ms/azurevirtualwanpreviewfeatures), majd jelentkezzen be Azure-fiókjával.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

### <a name="getting-started-page"></a>Első lépések oldal

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-gettingstarted-include.md)]

## <a name="hub"></a>3. Elosztó létrehozása

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="hub"></a>4. Egy kapcsolatcsoport keresése és társítása a központhoz

1. Válassza ki a virtuális WAN-t, majd a **Virtuális WAN-architektúra** területen válassza az **ExpressRoute-kapcsolatcsoportok** lehetőséget.
2. Ha az ExpressRoute-kapcsolatcsoport ugyanabban az előfizetésben van, mint a virtuális WAN, kattintson az **ExpressRoute-kapcsolatcsoport kiválasztása** elemre az előfizetés(ek)ből. 
3. A legördülő menüből válassza ki a központhoz társítani kívánt ExpressRoute-ot.
4. Ha az ExpressRoute-kapcsolatcsoport nem ugyanabban az előfizetésben található, vagy ha megadott [egy hitelesítési kulcsot és társazonosítót](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md), válassza a **Hitelesítési kulcsot beváltó kapcsolatcsoport keresése** lehetőséget.
5. Adja meg a következő részleteket:
* **Hitelesítési kulcs** – A kapcsolatcsoport tulajdonosa hozza létre a fent leírt módon
* **Társ-kapcsolatcsoport URI** – A kapcsolatcsoport URI-je, amelyet a kapcsolatcsoport tulajdonosa ad meg, és a kapcsolatcsoport egyedi azonosítójaként szolgál.
* **Útválasztás súlyozása** - [Az útválasztás súlyozása](../expressroute/expressroute-optimize-routing.md) lehetővé teszi bizonyos elérési utak előnyben részesítését, ha különböző társítási helyekről több kapcsolatcsoport csatlakozik ugyanahhoz a központhoz.
6. Kattintson a **Kapcsolatcsoport keresése** elemre, és ha a rendszer megtalálta a kapcsolatcsoportot, válassza ki.
7. A legördülő listából válasszon ki egy vagy több központot, majd kattintson a **Mentés** elemre.

## <a name="vnet"></a>5. A virtuális hálózat csatlakoztatása egy elosztóhoz

Ebben a lépésben a társviszony-kapcsolatot hozzuk létre az elosztó és egy virtuális hálózat között. Ismételje meg a fenti lépéseket minden csatlakoztatni kívánt virtuális hálózat esetében.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolat** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.


## <a name="viewwan"></a>6. A virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.
2. Az Áttekintés lapon a térképen látható pontok mindegyike egy elosztót jelöl. Az elosztók állapotösszegzéséért vigye a mutatót az egyes pontok fölé.
3. Az elosztók és kapcsolatok szakaszában láthatja az elosztók állapotát, helyét, régióját, VPN-kapcsolati állapotát, valamint a bájtban kifejezett be- és kimenő forgalmát.

## <a name="viewhealth"></a>7. Az erőforrás állapotának megtekintése

1. Lépjen a WAN-ra.
2. A WAN lapjának **TÁMOGATÁS + hibaelhárítás** szakaszában kattintson az **Állapot** lehetőségre, és tekintse meg az erőforrást.

## <a name="connectmon"></a>8. Kapcsolatok monitorozása

Létrehozhat egy kapcsolatot az Azure-beli virtuális gépek és a távoli helyek közötti kommunikáció monitorozására. A kapcsolatmonitor beállításával kapcsolatos információkért lásd a [hálózati kommunikáció monitorozását](~/articles/network-watcher/connection-monitor.md) ismertető szakaszt. A forrásmező a virtuális gép IP-címe az Azure-ban, a cél IP-cím pedig a hely IP-címe.

## <a name="cleanup"></a>9. Az erőforrások eltávolítása

Ha már nincs szükség ezekre az erőforrásokra, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális WAN létrehozása
> * Elosztó létrehozása
> * Egy kapcsolatcsoport keresése és társítása a központhoz
> * A kapcsolatcsoport társítása a központ(ok)hoz
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * A virtuális WAN megtekintése
> * Erőforrás állapotának megtekintése
> * Kapcsolatok monitorozása

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
