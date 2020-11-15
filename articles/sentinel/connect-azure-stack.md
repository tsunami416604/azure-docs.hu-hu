---
title: Az Azure Stack hub-beli virtuális gépek beléptetése az Azure Sentinelbe | Microsoft Docs
description: Ez a cikk bemutatja, hogyan építheti ki a Azure Monitor, a frissítés és a konfiguráció kezelése virtuálisgép-bővítményt Azure Stack hub virtuális gépeken, és megkezdheti a figyelést a Sentinel használatával.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 9ff70e7c05ca8de49f560fba3d59f0609785b8c4
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636775"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-azure-sentinel"></a>Azure Stack hub-beli virtuális gépek összekötése az Azure Sentinel szolgáltatással




Az Azure Sentinel használatával egy helyen figyelheti az Azure-ban futó virtuális gépeket és Azure Stack hub-t. A Azure Stack gépek Azure Sentinelbe való ellátásához először hozzá kell adnia a virtuálisgép-bővítményt a meglévő Azure Stack hub-beli virtuális gépekhez. 

Azure Stack hub-gépek összekapcsolását követően válassza ki az irányítópultok katalógusát, amely az adatok alapján felszínen alapuló elemzéseket végez. Ezek az irányítópultok könnyen testreszabhatók az igényeinek megfelelően.



## <a name="add-the-virtual-machine-extension"></a>A virtuálisgép-bővítmény hozzáadása 

Adja hozzá a **Azure monitor, a frissítés és a konfiguráció kezelése** virtuálisgép-bővítményt az Azure stack hub-on futó virtuális gépekhez. 

1. Az új böngésző lapon jelentkezzen be [Azure stack hub-portálra](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Nyissa meg a **virtuális gépek** lapot, és válassza ki azt a virtuális gépet, amelyet az Azure Sentinel használatával szeretne védelemmel ellátni. A virtuális gépek Azure Stack hub-on való létrehozásával kapcsolatos információkért lásd: [Windows Server rendszerű virtuális gép létrehozása az Azure stack hub Portalon](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) vagy [Linux Server rendszerű virtuális gép létrehozása az Azure stack hub portál használatával](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Válassza a **Bővítmények** lehetőséget. Megjelenik a virtuális gépen telepített virtuálisgép-bővítmények listája.
4. Kattintson a **Hozzáadás** fülre. Megnyílik az **új erőforrás** menü panel, és megjeleníti az elérhető virtuálisgép-bővítmények listáját. 
5. Válassza ki a **Azure monitor, a frissítés és a konfiguráció kezelése** bővítményt, és kattintson a **Létrehozás** gombra. Megnyílik a **telepítési bővítmény** konfigurálása ablak.

   ![A Azure Monitor, a frissítés és a konfiguráció felügyeleti beállításai](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Ha nem látja a piactéren felsorolt **Azure monitor, frissítés és konfigurálás felügyeleti** bővítményt, forduljon a Azure stack hub-kezelőhöz, hogy elérhető legyen.

6. Az Azure Sentinel menüben válassza a **munkaterület-beállítások** , majd a **speciális** lehetőséget, és másolja a munkaterület- **azonosítót** és a **munkaterület kulcsát (elsődleges kulcs)**. 
1. A Azure Stack hub **telepítési bővítmény** ablakában illessze be őket a jelzett mezőkbe, majd kattintson **az OK** gombra.
1. A bővítmény telepítésének befejezése után az állapota a **kiépítés sikerességét** jelzi. Akár egy óráig is eltarthat, amíg a virtuális gép megjelenik az Azure Sentinel portálon.

A Windows-ügynök telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Windows rendszerű számítógépek összekapcsolása](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).

Az ügynökkel kapcsolatos problémák Linux rendszeren történő hibaelhárításával kapcsolatban lásd: az [Azure log Analytics Linux-ügynök hibaelhárítása](../azure-monitor/platform/agent-linux-troubleshoot.md).

Az Azure-beli Azure Sentinel-portálon a **Virtual Machines** alatt áttekintheti az összes virtuális gépet és számítógépet, valamint azok állapotát. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, eltávolíthatja a bővítményt a virtuális gépről a Azure Stack hub portálon keresztül.

A bővítmény eltávolítása:

1. Nyissa meg az **Azure stack hub portált**.
2. Nyissa meg a **virtuális gépek** lapot, és válassza ki azt a virtuális gépet, amelyről el szeretné távolítani a bővítményt.
3. Válassza a **bővítmények** lehetőséget, majd a **Microsoft. EnterpriseCloud. monitoring** kiterjesztést.
4. Kattintson az **Eltávolítás** gombra, és erősítse meg a kijelölést.

## <a name="next-steps"></a>További lépések

Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- A [gyakori hibákból](connect-common-event-format.md) származó adatok továbbítása az Azure sentinelbe.
