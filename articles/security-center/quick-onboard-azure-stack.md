---
title: Az Azure Security Center rövid útmutató – felvétele a Security Center az Azure Stack virtuális gépek |} A Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan építheti ki az Azure Monitor, az Update és a konfigurációkezelés virtuálisgép-bővítmény egy Azure Stack virtuális gépeken.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
editor: ''
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: 7a630acee079301b95e7e05f5c5333dd116abb68
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59563794"
---
# <a name="quickstart--onboard-your-azure-stack-virtual-machines-to-security-center"></a>Gyors útmutató:  Felvétele a Security Center az Azure Stack virtuális gépek
Miután felvétele az Azure-előfizetéssel, engedélyezheti a hozzáadásával az Azure Stacken futó virtuális gépek védelme a Security Center a **Azure Monitor, az Update és a konfigurációkezelés** virtuálisgép-bővítményét a Az Azure Stack piactéren.

Ez a rövid útmutató bemutatja, hogyan adhat hozzá a **Azure Monitor, az Update és a konfigurációkezelés** virtuálisgép-bővítmény virtuális gépen (a Linux és Windows támogatottak) az Azure Stacken futó.

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

A Security Center Standard szintű Azure-előfizetéssel kell rendelkeznie a rövid útmutató elindítása előtt. A frissítési utasításokért lásd az [Azure-előfizetés a Security Center Standard verziójába történő felvételét](security-center-get-started.md) ismertető szakaszt. Security Center Standard csomagja az ingyenesen kipróbálhatja 30 napig. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Válassza ki a munkaterületet az Azure Security Centerben

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).
2. A **Microsoft Azure** menüben válassza a **Security Center** elemet. Megnyílik a **Security Center – Áttekintés** képernyő. 

   ![Security Center – Áttekintés][2]

3. A Security Center főmenüjében válassza az **Első lépések** elemet.
4. Válassza a **Bevezetés** lapot.

   ![Bevezetés][3]

5. Kattintson az **Új nem Azure-beli számítógépek hozzáadása** pont alatt található **Konfigurálás** elemre. Megjelenik a Log Analytics-munkaterületek listája. Ha van ilyen, a lista tartalmazza azt az alapértelmezett munkaterületet is, amelyet a Security Center hozott létre, amikor az automatikus kiépítés engedélyezve volt. Válassza ki a munkaterületet, vagy azt szeretné, hogy a jelentés a biztonsági adatok az Azure Stack-virtuális gép egy másik munkaterületet.

    ![Nem Azure-beli számítógép hozzáadása](./media/quick-onboard-windows-computer/non-azure.png)

   A **közvetlen ügynök** panel megnyílik egy hivatkozást az ügynök letöltése és a kulcsok a munkaterület-azonosító használható az ügynök telepítése.

   >[!NOTE]
   > NEM kell manuálisan töltse le az ügynököt. Az alábbi lépéseket a VM-bővítmény, az agent telepítve lesz.

6. Kattintson a **Munkaterület-azonosító** jobb oldalán található Másolás ikonra, és illessze be az azonosítót a Jegyzettömbbe.

7. Kattintson az **Elsődleges kulcs** jobb oldalán található Másolás ikonra, és illessze be az azonosítót a Jegyzettömbbe.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Virtuálisgép-bővítmény hozzáadása a meglévő Azure Stack-virtuális gépekhez
Most fel kell vennie a **Azure Monitor, az Update és a konfigurációkezelés** virtuálisgép-bővítményt a virtuális gépek futtatása az Azure Stack.

1. Egy új böngészőlapon, jelentkezzen be a **Azure Stack** portálon.
2. Nyissa meg a **virtuális gépek** lapon, válassza ki a Security Center a védeni kívánt virtuális gép. Információ a virtuális gép létrehozása az Azure Stacken: [Ez a rövid útmutató a Windows virtuális gépek](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) vagy [ebben a rövid útmutatóban a Linux rendszerű virtuális gépek](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Kattintson az **Extensions** (Bővítmények) gombra. A virtuális gépen telepített virtuális gépi bővítmények listája látható.
4. Kattintson a **Hozzáadás** fülre. A **új erőforrás** menü panel megnyitja és megjeleníti az elérhető virtuálisgép-bővítmények listája. 
5. Válassza ki a **Azure Monitor, az Update és a konfigurációkezelés** bővítményt, és kattintson **létrehozás**. A **bővítmény telepítése** konfigurálása panel megnyílik.

>[!NOTE]
> Ha nem látja a **Azure Monitor, az Update és a konfigurációkezelés** bővítmény megjelenjen a piactéren, vegye fel a kapcsolatot az Azure Stack-operátorokról elérhető legyen.

6. Az a **bővítmény telepítése** konfigurációs panelen illessze be a **munkaterület-Azonosítót** és **Munkaterületkulcsot (elsődleges kulcs)** az előző eljárás során a Jegyzettömbbe másolt.
7.  Ha elkészült, kattintson a szükséges konfigurációs beállítások biztosítása **OK**.
8. A bővítmény a telepítés befejezése után az állapot jelenik meg: **sikeres telepítés**. A virtuális gép megjelenik a Security Center portálon akár egy óráig is eltarthat.

A telepítése és konfigurálása a Windows-ügynököt további információkért lásd: [csatlakozás Windows-számítógépek](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Linux-ügynök problémák hibaelhárításához lásd [hibaelhárítása az Azure Log Analytics Linux-ügynök](../azure-monitor/platform/agent-linux-troubleshoot.md).

Mostantól egy helyről felügyelheti az Azure-beli virtuális gépeket és a nem Azure-beli számítógépeket. A biztonsági Center az Azure portal alatt **számítási**, áttekintheti az összes virtuális gépek és számítógépek és a javaslatot. A Security Center is Felfed, ezeket a számítógépeket a biztonsági riasztásokban bármely észlelését.

  ![Számítás panel][6]

A **Számítás** panelen kétféle ikon található:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Nem Azure-beli számítógép 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Az Azure virtuális gép (az Azure Stack virtuális gépek fogja megjeleníteni az ehhez a csoporthoz)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, eltávolíthatja a bővítményt a virtuális gépről az Azure Stack portálon keresztül.

A bővítmény eltávolítása:

1. Nyissa meg a **az Azure Stack Portal**.
2. Lépjen a **virtuális gépek** lapra, jelölje be a virtuális gép, amelyről szeretné eltávolítani a bővítményt.
3. Válassza ki **bővítmények**, jelölje ki a bővítményt **Microsoft.EnterpriseCloud.Monitoring**.
4. Kattintson a **Eltávolítás**, és győződjön meg róla, kijelölés kattintva **Igen**.

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban kiépítette az Azure Monitor, az Update és a konfigurációkezelés kiterjesztése az Azure Stacken futó virtuális gépen. Ha többet szeretne megtudni a Security Center használatáról, tekintse meg a biztonsági szabályzat konfigurálásával és az erőforrások biztonságának felmérésével foglalkozó oktatóanyagot is.

> [!div class="nextstepaction"]
> [Oktatóanyag: Biztonsági szabályzatok meghatározása és értékelése](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
