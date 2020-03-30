---
title: Az Azure Stack virtuális gépeinek az Azure Sentinelbe való bedeszkázása | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan építheti ki az Azure Monitor, Frissítés és Konfigurációkezelés virtuálisgép-bővítményt az Azure Stack virtuális gépeken, és hogyan figyelheti őket a Sentinelhasználatával.
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
ms.openlocfilehash: a8213bd57936f95870324950204dbd6c1473739a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588518"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Az Azure Stack virtuális gépeinek csatlakoztatása az Azure Sentinelhez




Az Azure Sentinel segítségével egy helyen figyelheti az Azure-on és az Azure Stacken futó virtuális gépeit. Az Azure Stack-gépek azure Sentinel hez való bedeszkázására először hozzá kell adnia a virtuálisgép-bővítményt a meglévő Azure Stack virtuális gépekhez. 

Miután csatlakoztatta az Azure Stack-gépeket, válasszon az irányítópultok egy gyűjteményéből, amely az adatok alapján felületezi az elemzéseket. Ezek az irányítópultok könnyen testreszabhatók az Ön igényeinek megfelelően.



## <a name="add-the-virtual-machine-extension"></a>A virtuálisgép-bővítmény hozzáadása 

Adja hozzá az **Azure Monitor, Update és Configuration Management** virtuálisgép-bővítményt az Azure Stacken futó virtuális gépekhez. 

1. Egy új böngészőlapon jelentkezzen be az [Azure Stack portálra.](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)
2. Nyissa meg a **Virtuális gépek** lapot, válassza ki az Azure Sentinel lel védeni kívánt virtuális gépet. A virtuális gépek Azure Stacken való létrehozásáról a [Windows server virtuális gép létrehozása az Azure Stack portállal](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) vagy egy [Linux-kiszolgáló virtuális gép létrehozása az Azure Stack portál használatával című](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)témakörben talál.
3. Válassza **a Bővítmények lehetőséget**. A virtuális gépen telepített virtuálisgép-bővítmények listája látható.
4. Kattintson a **Hozzáadás** fülre. Megnyílik az **Új erőforrás** menüpanel, és megjeleníti az elérhető virtuálisgép-bővítmények listáját. 
5. Válassza ki az **Azure Monitor, Update és Configuration Management bővítményt,** és kattintson a **Létrehozás gombra.** Megnyílik **a Telepítés bővítmény** konfigurációs ablaka.

   ![Az Azure Monitor, frissítés és konfigurációkezelés beállításai](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Ha nem látja az **Azure Monitor, frissítés és konfigurációkezelés** bővítmény szerepel a piactéren, forduljon az Azure Stack-üzemeltető elérhetővé.

6. Az Azure Sentinel menüben válassza a **Munkaterület beállításait,** majd a **Speciális**lehetőséget, és másolja a **munkaterület-azonosítót** és a **munkaterületi kulcsot (elsődleges kulcs).** 
1. Az Azure Stack **Install bővítmény** ablakában illessze be őket a jelzett mezőkbe, és kattintson az **OK**gombra.
1. A bővítmény telepítése befejezése után az állapota **sikeres kiépítésként jelenik meg.** Akár egy órát is igénybe vehet, amíg a virtuális gép megjelenik az Azure Sentinel portálon.

A Windows-ügynök telepítéséről és konfigurálásáról a [Windows-számítógépek csatlakoztatása című](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)témakörben talál további információt.

Az ügynökekkel kapcsolatos problémák Linux-hibaelhárításáról az [Azure Log Analytics Linux-ügynök hibaelhárítása](../azure-monitor/platform/agent-linux-troubleshoot.md)című témakörben van.

Az Azure Sentinel portálon az Azure-ban, **a virtuális gépek**alatt áttekintést kap az összes virtuális gépről és számítógépről, valamint azok állapotáról. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szükség, eltávolíthatja a bővítményt a virtuális gépről az Azure Stack portálon keresztül.

A bővítmény eltávolítása:

1. Nyissa meg az **Azure Stack Portal t.**
2. Lépjen a **Virtuális gépek** lapra, és válassza ki azt a virtuális gépet, amelyről el szeretné távolítani a bővítményt.
3. Válassza **a Bővítmények**lehetőséget, válassza a **Microsoft.EnterpriseCloud.Monitoring**bővítményt.
4. Kattintson az **Eltávolítás gombra,** és erősítse meg a választást.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Adatokat továbbítson [a Common Error Format készülékekről](connect-common-event-format.md) az Azure Sentinelbe.
