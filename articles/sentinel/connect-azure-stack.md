---
title: Az Azure Stack Virtual Machines üzembe helyezése az Azure Sentinelben | Microsoft Docs
description: Ez a cikk bemutatja, hogyan építheti ki a Azure Monitor, a frissítés és a konfiguráció kezelése virtuálisgép-bővítményt a Azure Stack virtuális gépeken, és megkezdheti a figyelést a Sentinel használatával.
services: sentinel
documentationcenter: na
author: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: rkarlin
ms.openlocfilehash: caaf708b3efd8ffbe059f8ad249b7945d31fd7cc
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601163"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Azure Stack virtuális gépek összekötése az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Az Azure Sentinel segítségével figyelheti az Azure-on futó virtuális gépeket, és Azure Stack egy helyen. A Azure Stack gépek Azure Sentinelbe való ellátásához először hozzá kell adnia a virtuálisgép-bővítményt a meglévő Azure Stack virtuális gépekhez. 

Azure Stack gépek összekapcsolását követően válassza ki az irányítópultok egyik katalógusát, amely az adatok alapján felszínen alapuló elemzéseket végez. Ezek az irányítópultok könnyen testreszabhatók az igényeinek megfelelően.



## <a name="add-the-virtual-machine-extension"></a>A virtuálisgép-bővítmény hozzáadása 

Adja hozzá a **Azure monitor, a frissítés és a konfiguráció kezelése** virtuálisgép-bővítményt a Azure stack futó virtuális gépekhez. 

1. Az új böngésző lapon jelentkezzen be a [Azure stack portálra](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Nyissa meg a **virtuális gépek** lapot, és válassza ki azt a virtuális gépet, amelyet az Azure Sentinel használatával szeretne védelemmel ellátni. A virtuális gépek Azure Stackon való létrehozásával kapcsolatos információkért lásd: Windows Server rendszerű virtuális gép [létrehozása a Azure stack portálon](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) vagy [Linux-kiszolgáló virtuális gép létrehozása a Azure stack portál használatával](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Kattintson az **Extensions** (Bővítmények) gombra. Megjelenik a virtuális gépen telepített virtuálisgép-bővítmények listája.
4. Kattintson a **Hozzáadás** fülre. Megnyílik az **új erőforrás** menü panel, és megjeleníti az elérhető virtuálisgép-bővítmények listáját. 
5. Válassza ki a **Azure monitor, a frissítés és a konfiguráció kezelése** bővítményt, és kattintson a **Létrehozás**gombra. Megnyílik a **telepítési bővítmény** konfigurálása ablak.

   ![A Azure Monitor, a frissítés és a konfiguráció felügyeleti beállításai](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Ha nem látja a piactéren felsorolt **Azure monitor, frissítés és konfigurálás felügyeleti** bővítményt, forduljon a Azure stack-kezelőhöz, hogy elérhető legyen.

6. Az Azure Sentinel menüben válassza a **munkaterület-beállítások** , majd a **speciális**lehetőséget, és másolja a **munkaterület-azonosítót** és a **munkaterület kulcsát (elsődleges kulcs)** . 
1. A Azure Stack **telepítési bővítmény** ablakban illessze be őket a jelzett mezőkbe, majd kattintson **az OK**gombra.
1. A bővítmény telepítésének befejezése után az állapota a kiépítés **sikerességét**jelzi. Akár egy óráig is eltarthat, amíg a virtuális gép megjelenik az Azure Sentinel portálon.

A Windows-ügynök telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Windows rendszerű számítógépek](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)összekapcsolása.

Az ügynökkel kapcsolatos problémák Linux rendszeren történő hibaelhárításával kapcsolatban lásd: az [Azure log Analytics Linux-ügynök hibaelhárítása](../azure-monitor/platform/agent-linux-troubleshoot.md).

Az Azure-beli Azure Sentinel-portálon a **Virtual Machines**alatt áttekintheti az összes virtuális gépet és számítógépet, valamint azok állapotát. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, eltávolíthatja a bővítményt a virtuális gépről a Azure Stack-portálon keresztül.

A bővítmény eltávolítása:

1. Nyissa meg a **Azure stack portált**.
2. Nyissa meg a **virtuális gépek** lapot, és válassza ki azt a virtuális gépet, amelyről el szeretné távolítani a bővítményt.
3. Válasszaa bővítmények lehetőséget, majd a **Microsoft. EnterpriseCloud. monitoring**kiterjesztést.
4. Kattintson az **Eltávolítás**gombra, és erősítse meg a kijelölést.

## <a name="next-steps"></a>További lépések

Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
- A [gyakori hibákból](connect-common-event-format.md) származó adatok továbbítása az Azure sentinelbe.
