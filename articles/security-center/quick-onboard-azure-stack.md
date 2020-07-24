---
title: Azure Stack virtuális gépek előkészítése Azure Security Center
description: Ez a rövid útmutató bemutatja, hogyan építheti ki a Azure Monitor, a frissítés és a konfiguráció kezelése virtuálisgép-bővítményt egy Azure Stack virtuális gépen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: memildin
ms.openlocfilehash: 67017bdc98b997c728c4d5dcfab92e0804498060
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084007"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Gyors útmutató: a Azure Stack virtuális gépek előkészítése Security Center
Az Azure-előfizetés bevezetését követően engedélyezheti Security Center a Azure Stack-on futó virtuális gépeknek a Azure Stack piactéren való hozzáadásával a **Azure monitor, a frissítés és a konfiguráció kezelése** virtuálisgép-bővítményt.

Ebből a rövid útmutatóból megtudhatja, hogyan adhatja hozzá a **Azure monitor, a frissítés és a konfiguráció kezelése** virtuálisgép-bővítményt egy virtuális gépen (a Linux és a Windows egyaránt támogatott) Azure stack rendszeren.

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

A rövid útmutató elkezdése előtt rendelkeznie kell egy Azure-előfizetéssel Security Center Standard szinten. A frissítési utasításokért lásd az [Azure-előfizetés a Security Center Standard verziójába történő felvételét](security-center-get-started.md) ismertető szakaszt. A standard szintű csomag 30 napig ingyenesen kipróbálható Security Center. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Válassza ki a munkaterületét Azure Security Center

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).
2. A **Microsoft Azure** menüben válassza a **Security Center** elemet. **Security Center – az Áttekintés** megnyílik. 

   ![Security Center – Áttekintés][2]

3. A Security Center főmenüjében válassza az **Első lépések** elemet.
4. Válassza a **Bevezetés** lapot.

   ![Bevezetés][3]

5. Kattintson az **Új nem Azure-beli számítógépek hozzáadása** pont alatt található **Konfigurálás** elemre. Megjelenik a Log Analytics-munkaterületek listája. Ha van ilyen, a lista tartalmazza azt az alapértelmezett munkaterületet is, amelyet a Security Center hozott létre, amikor az automatikus kiépítés engedélyezve volt. Válassza ki ezt a munkaterületet, vagy egy másik munkaterületet, amelyhez a Azure Stack virtuális gép biztonsági adatként jelentést szeretne készíteni.

    ![Nem Azure-beli számítógép hozzáadása](./media/quick-onboard-windows-computer/non-azure.png)

   Megnyílik a **Direct Agent (közvetlen ügynök** ) panel, amely a munkaterület-azonosítóhoz tartozó ügynök és kulcsok letöltésére szolgáló hivatkozást használja az ügynök konfigurálásához.

   >[!NOTE]
   > NEM kell manuálisan letöltenie az ügynököt. Az ügynök virtuálisgép-bővítményként lesz telepítve az alábbi lépésekben.

6. Kattintson a **Munkaterület-azonosító** jobb oldalán található Másolás ikonra, és illessze be az azonosítót a Jegyzettömbbe.

7. Kattintson az **Elsődleges kulcs** jobb oldalán található Másolás ikonra, és illessze be az azonosítót a Jegyzettömbbe.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>A virtuálisgép-bővítmény hozzáadása a meglévő Azure Stack virtuális gépekhez
Most hozzá kell adnia a **Azure monitor, a frissítés és a konfiguráció kezelése** virtuálisgép-bővítményt a Azure stack futó virtuális gépekhez.

1. Az új böngésző lapon jelentkezzen be a **Azure stack** portálra.
2. Nyissa meg a **virtuális gépek** lapot, és válassza ki a Security Center védelemmel ellátni kívánt virtuális gépet. A virtuális gépek Azure Stackon való létrehozásával kapcsolatos információkért tekintse meg ezt a rövid útmutatót [a Windows rendszerű virtuális gépekről](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) , illetve [a Linux rendszerű virtuális gépekről](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Válassza a **Bővítmények** lehetőséget. Megjelenik a virtuális gépen telepített virtuálisgép-bővítmények listája.
4. Kattintson a **Hozzáadás** fülre. Megnyílik az **új erőforrás** menü panel, és megjeleníti az elérhető virtuálisgép-bővítmények listáját. 
5. Válassza ki a **Azure monitor, a frissítés és a konfiguráció kezelése** bővítményt, és kattintson a **Létrehozás**gombra. Megnyílik a **telepítési bővítmény** konfigurációja panel.

>[!NOTE]
> Ha nem látja a piactéren felsorolt **Azure monitor, frissítés és konfigurálás felügyeleti** bővítményt, forduljon a Azure stack-kezelőhöz, hogy elérhető legyen.

6. A **bővítmény** konfigurálása panelen illessze be azt a **munkaterület-azonosítót** és a **munkaterület kulcsát (elsődleges kulcs)** , amelyet a Jegyzettömbbe másolt az előző eljárásban.
7. Ha végzett a szükséges konfigurációs beállítások megadásával, kattintson **az OK**gombra.
8. A bővítmény telepítésének befejezése után az állapota a **kiépítés sikeressége**után jelenik meg. Akár egy óráig is eltarthat, amíg a virtuális gép megjelenik a Security Center-portálon.

A Windows-ügynök telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Windows rendszerű számítógépek összekapcsolása](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Az ügynökkel kapcsolatos problémák Linux rendszeren történő hibaelhárításával kapcsolatban lásd: az [Azure log Analytics Linux-ügynök hibaelhárítása](../azure-monitor/platform/agent-linux-troubleshoot.md).

Mostantól egy helyről felügyelheti az Azure-beli virtuális gépeket és a nem Azure-beli számítógépeket. Az Azure-beli Security Center-portálon a **számítás**alatt tekintse át az összes virtuális gépet és számítógépet, valamint javaslataikat. A Security Center a számítógépek észlelését is felfedi a biztonsági riasztásokban.

  ![Számítás panel][6]

A **Számítás** panelen kétféle ikon található:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Nem Azure-beli számítógép 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure-beli virtuális gép (Azure Stack virtuális gépek ebben a csoportban jelennek meg)

## <a name="clean-up-resources"></a>Erőforrások felszabadítása
Ha már nincs rá szükség, eltávolíthatja a bővítményt a virtuális gépről a Azure Stack-portálon keresztül.

A bővítmény eltávolítása:

1. Nyissa meg a **Azure stack portált**.
2. Nyissa meg a **virtuális gépek** lapot, és válassza ki azt a virtuális gépet, amelyről el szeretné távolítani a bővítményt.
3. Válassza a **bővítmények**lehetőséget, majd a **Microsoft. EnterpriseCloud. monitoring**kiterjesztést.
4. Kattintson az **Eltávolítás**gombra, és erősítse meg a kijelölést az **Igen**gombra kattintva.

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban kiépítte a Azure Monitor, a frissítés és a konfiguráció felügyeleti bővítményt egy Azure Stack-on futó virtuális gépen. Ha többet szeretne megtudni a Security Center használatáról, tekintse meg a biztonsági szabályzat konfigurálásával és az erőforrások biztonságának felmérésével foglalkozó oktatóanyagot is.

> [!div class="nextstepaction"]
> [Oktatóanyag: Biztonsági szabályzatok meghatározása és értékelése](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
