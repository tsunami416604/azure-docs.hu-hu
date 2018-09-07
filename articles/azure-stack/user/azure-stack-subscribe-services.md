---
title: Ebben az oktatóanyagban megismerheti, hogyan fizethet elő az Azure Stack-ajánlat |} A Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozzon létre egy új előfizetést az Azure Stack-szolgáltatások és a egy tesztcélú virtuális gép létrehozása az ajánlat teszteléséhez.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/05/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 0e2fa9b01d27d68c1eab9097a20b6e350ba47f99
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028803"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Oktatóanyag: létrehozása és tesztelése egy előfizetést
Ez az oktatóanyag bemutatja, hogyan hozzon létre egy ajánlatot tartalmazó előfizetést, és tesztelje azt. A teszthez lesz majd hozzon létre egy virtuális gépet, előfizetnek az ajánlatra és jelentkezzen be az Azure Stack felhasználói portál a felhő-rendszergazdák el.

> [!TIP]
> További speciális értékelési élményt, is [hozzon létre egy előfizetést, egy adott felhasználó](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) és majd jelentkezzen be, mint a felhasználó a felhasználói portálon. 

Ez az oktatóanyag bemutatja, hogyan fizethet elő az Azure Stack-ajánlat.

Amiről tanulni fog:

> [!div class="checklist"]
> * Előfizetés egy ajánlatra 
> * Az ajánlat tesztelése

## <a name="subscribe-to-an-offer"></a>Előfizetés egy ajánlatra
Előfizetés egy ajánlatra felhasználóként, jelentkezzen be az Azure Stack felhasználói portálra, Fedezze fel a szolgáltatásokat, amelyek az Azure Stack operátorait szerint felajánlották kell.

1. Jelentkezzen be a felhasználói portálra, és kattintson a **előfizetés beszerzése**.

   ![Előfizetés beszerzése](media/azure-stack-subscribe-services/get-subscription.png)

2. A **Megjelenítendő név** mezőben adja meg az előfizetés nevét. Kattintson a **ajánlat** az elérhető ajánlatok a egyikét választhatja ki a **válasszon ajánlatot** szakaszt, és kattintson a **létrehozás**.

   ![Ajánlat létrehozása](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Most frissítenie kell a felhasználói portálra, az előfizetés használatának megkezdéséhez.

3. A létrehozott előfizetés megtekintéséhez kattintson **minden szolgáltatás**.  Ezt követően a **általános** kategória kiválasztása **előfizetések**, majd válassza ki az új előfizetés. Miután előfizetett egy ajánlatra, frissítse a portált tekintse meg, ha új lett az új előfizetés része. Ebben a példában **virtuális gépek** hozzá lett adva.

   ![Előfizetési nézet](media/azure-stack-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Az ajánlat tesztelése
A felhasználói portálra való bejelentkezés, az ajánlat letesztelheti az új előfizetési lehetőségek használatával virtuális gép üzembe helyezésének. 

> [!NOTE]
> Ez a vizsgálat szükséges, hogy egy Windows Server 2016 Datacenter virtuális Gépet az Azure Stack piactéren először bővült. 

1. Jelentkezzen be a felhasználói portálra.

2. Kattintson a felhasználói portál **virtuális gépek** > **Hozzáadás** > **Windows Server 2016 Datacenter**, és kattintson a **létrehozása** .

3. Az a **alapjai** területén írja be egy **neve**, **felhasználónév**, és **jelszó**, válassza ki egy **előfizetés**, Hozzon létre egy **erőforráscsoport** (vagy válasszon ki egy meglévőt), és kattintson a **OK**.

4. Az a **méret kiválasztása** területén kattintson **A1 Standard**, és kattintson a **kiválasztása**.  

5. A beállítások panelen elfogadhatja az alapértelmezett beállításokat, és kattintson a **OK**.

6. Az a **összefoglalás** területén kattintson **OK** a virtuális gép létrehozásához.  

7. Tekintse meg az új virtuális gépet, kattintson a **virtuális gépek**, majd keresse meg az új virtuális gép, és kattintson a nevére.

    ![Minden erőforrás](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> A virtuális gépek üzembe helyezése néhány percet vesz igénybe.


## <a name="next-steps"></a>További lépések

Ismerte meg ebben az oktatóanyagban:

> [!div class="checklist"]
> * Előfizetés egy ajánlatra 
> * Az ajánlat tesztelése


> [!div class="nextstepaction"]
> [Virtuális gép létrehozása egy közösségi sablon](azure-stack-create-vm-template.md)