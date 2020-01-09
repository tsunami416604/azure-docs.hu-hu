---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 8d13d5d93dba66fa0dce08ffbf569164fb03398d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469899"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Klasszikus VNet létrehozása a Azure Portalban
Az előző forgatókönyv alapján klasszikus VNet létrehozásához kövesse az alábbi lépéseket.

1. Egy böngészőből keresse fel a(z) https://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson az **Erőforrás létrehozása** > **Hálózat** > **Virtuális hálózat** lehetőségre. Figyelje meg, hogy a **telepítési modell kiválasztása** listában már látható a **klasszikus**. 3. Kattintson a **Létrehozás** gombra az alábbi ábrán látható módon.
   
    ![VNet létrehozása az Azure Portalon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. A **virtuális hálózat** panelen írja be a VNet **nevét** , majd kattintson a **címterület**elemre. Konfigurálja a Címterület beállításait a VNet és az első alhálózatra, majd kattintson **az OK**gombra. A következő ábra a CIDR blokk beállításait mutatja be a forgatókönyvhöz.
   
    ![Címterület ablaktábla](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Kattintson az **erőforráscsoport** elemre, és válasszon ki egy erőforráscsoportot a VNet hozzáadásához, vagy kattintson az **Új erőforráscsoport létrehozása** lehetőségre a VNet új erőforráscsoporthoz való hozzáadásához. Az alábbi ábrán egy **TestRG**nevű új erőforráscsoport erőforráscsoport-beállításai láthatók. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../articles/azure-resource-manager/management/overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
   
    ![Erőforráscsoport ablaktábla létrehozása](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. Szükség esetén módosítsa a VNethez tartozó **Előfizetés** és **Hely** beállításokat. 
7. Ha nem szeretné, hogy a VNet csempe megjelenjen a **Kezdőpulton**, tiltsa le **A kezdőpulton rögzít** lehetőséget. 
8. Kattintson a **Létrehozás** elemre, és figyelje meg a **virtuális hálózat létrehozása** nevű csempét az alábbi ábrán látható módon.
   
    ![VNet létrehozása a portálon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Várjon, amíg a VNet létre nem jön, és amikor megjelenik a csempe, kattintson rá a további alhálózatok hozzáadásához.
   
    ![VNet létrehozása a portálon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. A VNet **konfigurációját** az ábrán látható módon kell megtekinteni. 
   
    ![VNet létrehozása a portálon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Kattintson az **Alhálózatok** > **Hozzáadás**elemre, írja be a **nevet** , és adjon meg egy **címtartományt (CIDR blokk)** az alhálózat számára, majd kattintson **az OK**gombra. Az alábbi ábra az aktuális forgatókönyv beállításait mutatja be.
    
    ![VNet létrehozása az Azure Portalon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

