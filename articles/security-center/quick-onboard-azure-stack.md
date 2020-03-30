---
title: Alaplapi Azure Stack virtuális gépek az Azure Security Centerbe
description: Ez a rövid útmutató bemutatja, hogyan építheti ki az Azure Monitor, Frissítés és Konfigurációkezelés virtuálisgép-bővítményt egy Azure Stack virtuális gépeken.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: c186dcb2d7d7d423f5c001bdb4d3f3503beebd9c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73686522"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Rövid útmutató: Az Azure Stack virtuális gépeinek üzembe helyezéséről a Security Centerbe
Miután az Azure-előfizetés, engedélyezheti a Security Center az Azure Stacken futó virtuális gépek védelméhez az **Azure Monitor, Update és Configuration Management** virtuálisgép-bővítmény hozzáadásával az Azure Stack piactérről.

Ez a rövid útmutató bemutatja, hogyan adhat hozzá az **Azure Monitor, Update és Configuration Management** virtuálisgép-bővítményt az Azure Stacken futó virtuális gépen (Linux és Windows egyaránt támogatott).

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

A rövid útmutató megkezdése előtt rendelkeznie kell egy Azure-előfizetéssel a Security Center standard szintjén. A frissítési utasításokért lásd az [Azure-előfizetés a Security Center Standard verziójába történő felvételét](security-center-get-started.md) ismertető szakaszt. Kipróbálhatja a Security Center standard szint díjmentesen 30 napig. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Válassza ki a munkaterületet az Azure Security Centerben

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).
2. A **Microsoft Azure** menüben válassza a **Security Center** elemet. Megnyílik a **Security Center – Áttekintés** képernyő. 

   ![Security Center – Áttekintés][2]

3. A Security Center főmenüjében válassza az **Első lépések** elemet.
4. Válassza a **Bevezetés** lapot.

   ![Bevezetés][3]

5. Kattintson az **Új nem Azure-beli számítógépek hozzáadása** pont alatt található **Konfigurálás** elemre. Megjelenik a Log Analytics-munkaterületek listája. Ha van ilyen, a lista tartalmazza azt az alapértelmezett munkaterületet is, amelyet a Security Center hozott létre, amikor az automatikus kiépítés engedélyezve volt. Válassza ki ezt a munkaterületet, vagy egy másik munkaterületet, amelynek azt szeretné, hogy az Azure Stack virtuális gép jelentse a biztonsági adatokat.

    ![Nem Azure-beli számítógép hozzáadása](./media/quick-onboard-windows-computer/non-azure.png)

   A **Közvetlen ügynök** panel megnyílik egy hivatkozást az ügynök és a kulcsok letöltéséhez a munkaterület-azonosító az ügynök konfigurálásakor használható.

   >[!NOTE]
   > Nem kell manuálisan letöltenie az ügynököt. Az ügynök virtuális gépbővítményként lesz telepítve az alábbi lépésekben.

6. Kattintson a **Munkaterület-azonosító** jobb oldalán található Másolás ikonra, és illessze be az azonosítót a Jegyzettömbbe.

7. Kattintson az **Elsődleges kulcs** jobb oldalán található Másolás ikonra, és illessze be az azonosítót a Jegyzettömbbe.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>A virtuálisgép-bővítmény hozzáadása a meglévő Azure Stack virtuális gépekhez
Most hozzá kell adnia az **Azure Monitor, frissítés i és konfigurációkezelés** virtuálisgép-bővítményt az Azure Stacken futó virtuális gépekhez.

1. Egy új böngészőlapon jelentkezzen be az **Azure Stack** portálra.
2. Nyissa meg a **Virtuális gépek** lapot, és válassza ki a Biztonsági központtal védeni kívánt virtuális gépet. A virtuális gépek Azure Stacken való létrehozásáról a [Windows virtuális gépekhez](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) való rövid útmutatóban vagy a [Linux-alapú virtuális gépekhez futó rövid útmutatóban](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)talál tájékoztatást.
3. Válassza **a Bővítmények lehetőséget**. A virtuális gépen telepített virtuálisgép-bővítmények listája látható.
4. Kattintson a **Hozzáadás** fülre. Megnyílik az **Új erőforrás** menüpanel, és megjeleníti az elérhető virtuálisgép-bővítmények listáját. 
5. Válassza ki az **Azure Monitor, Update and Configuration Management bővítményt,** és kattintson a **Létrehozás gombra.** Megnyílik **a Telepítés bővítmény** konfigurációpanel.

>[!NOTE]
> Ha nem látja az **Azure Monitor, frissítés és konfigurációkezelés** bővítmény szerepel a piactéren, kérjük, forduljon az Azure Stack-operátor elérhetővé.

6. A **Bővítmény konfigurációs paneljének** illessze be a **munkaterület-azonosítót** és a **munkaterületi kulcsot (elsődleges kulcs),** amelyet az előző eljárás során a Jegyzettömbbe másolt.
7. Ha végzett a szükséges konfigurációs beállítások megadásával, kattintson az **OK**gombra.
8. A bővítmény telepítése befejeződése után az állapota **sikeres kiépítésként jelenik meg.** A virtuális gép megjelenése akár egy órát is igénybe vehet.

A Windows-ügynök telepítéséről és konfigurálásáról a [Windows-számítógépek csatlakoztatása című](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)témakörben talál további információt.

Az ügynökekkel kapcsolatos problémák Linux-hibaelhárításáról az [Azure Log Analytics Linux-ügynök hibaelhárítása](../azure-monitor/platform/agent-linux-troubleshoot.md)című témakörben van.

Mostantól egy helyről felügyelheti az Azure-beli virtuális gépeket és a nem Azure-beli számítógépeket. Az Azure Biztonsági központ portálján a **Számítási**csoportban áttekintést kap az összes virtuális gépről és számítógépről, valamint azok javaslatait. A Security Center a biztonsági riasztásokban is megjelenik a számítógépek észlelése.

  ![Számítás panel][6]

A **Számítás** panelen kétféle ikon található:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Nem Azure-beli számítógép 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure virtuális gép (az Azure Stack virtuális gépek jelennek meg ebben a csoportban)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szükség, eltávolíthatja a bővítményt a virtuális gépről az Azure Stack portálon keresztül.

A bővítmény eltávolítása:

1. Nyissa meg az **Azure Stack Portal t.**
2. Lépjen a **Virtuális gépek** lapra, és válassza ki azt a virtuális gépet, amelyről el szeretné távolítani a bővítményt.
3. Válassza **a Bővítmények**lehetőséget, válassza a **Microsoft.EnterpriseCloud.Monitoring**bővítményt.
4. Kattintson az **Eltávolítás gombra,** és erősítse meg a kiválasztást az **Igen**gombra kattintva.

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban kiépítette az Azure Monitor, frissítés és konfigurációkezelés bővítményt az Azure Stacken futó virtuális gépen. Ha többet szeretne megtudni a Security Center használatáról, tekintse meg a biztonsági szabályzat konfigurálásával és az erőforrások biztonságának felmérésével foglalkozó oktatóanyagot is.

> [!div class="nextstepaction"]
> [Oktatóanyag: Biztonsági szabályzatok meghatározása és értékelése](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
