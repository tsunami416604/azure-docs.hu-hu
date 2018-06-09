---
title: Ebben az oktatóanyagban elsajátíthatja, hogyan Azure verem ajánlatot előfizetni |} Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozzon létre egy új előfizetést Azure verem szolgáltatásokhoz, és az ajánlat teszteléséhez a tesztcélú virtuális gépek létrehozásával.
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
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 96b82ede71e7957105dce25096d7873ee876211f
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238419"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Oktatóanyag: hozzon létre és előfizetés tesztelése
Az oktatóanyag bemutatja, hogyan ajánlatot tartalmazó előfizetés létrehozása és tesztelik azt. A vizsgálathoz fog majd hozzon létre egy virtuális gépet, az ajánlat előfizetni és jelentkezzen be a Azure verem felhasználói portálra, a felhő rendszergazdájának el.

> [!TIP]
> További információ az összetettebb értékelési élmény, akkor [előfizetés létrehozása egy adott felhasználó számára](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) és majd, hogy a felhasználó a felhasználói portál bejelentkezési. 

Ez az oktatóanyag bemutatja, hogyan Azure verem ajánlatot előfizetni.

Amiről tanulni fog:

> [!div class="checklist"]
> * Előfizetés egy ajánlatra 
> * Az ajánlat tesztelése

## <a name="subscribe-to-an-offer"></a>Előfizetés egy ajánlatra
Egy olyan felhasználó nevében ajánlatot előfizetni szüksége a bejelentkezés az Azure-verem felhasználói portálra a szolgáltatásokat, amelyeket az Azure-verem üzemeltető felajánlása felderítéséhez.

1. Jelentkezzen be a felhasználói portálra, majd kattintson a **egy előfizetés**.

   ![Előfizetés beszerzése](media/azure-stack-subscribe-services/get-subscription.png)

2. A **Megjelenítendő név** mezőben adja meg az előfizetés nevét. Kattintson a **kínálnak** segítségével válassza ki az elérhető ajánlatokat közül a **válasszon egy ajánlatot** szakaszt, és kattintson a **létrehozása**.

   ![Ajánlat létrehozása](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Most frissítenie kell a felhasználói portálra, ahol az előfizetés használatának megkezdéséhez.

3. Az újonnan létrehozott előfizetés megtekintéséhez kattintson **további szolgáltatások**, kattintson a **előfizetések**, majd kattintson az új előfizetés. Miután előfizetett egy ajánlatot, frissítse a portálhoz, és ha új le lett részeként az új előfizetés. Ebben a példában **virtuális gépek** hozzá lett adva.

   ![Előfizetés megtekintése](media/azure-stack-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Az ajánlat tesztelése
A felhasználói portálra való bejelentkezés, tesztelheti az ajánlat által a virtuális gépek az új előfizetési lehetőségek állításához. 

> [!NOTE]
> Ez a vizsgálat szükséges, hogy a Windows Server 2016 Datacenter VM először hozzáadta a verem Azure piactéren. 

1. Jelentkezzen be a felhasználói portálon.

2. A felhasználói portálon kattintson **virtuális gépek** > **Hozzáadás** > **Windows Server 2016 Datacenter**, és kattintson a **létrehozása** .

3. A a **alapjai** területen írja be a **neve**, **felhasználónév**, és **jelszó**, válassza ki a **előfizetés**, Hozzon létre egy **erőforráscsoport** (vagy válasszon egy meglévőt), és kattintson a **OK**.

4. Az a **méret kiválasztása** területen kattintson **A1 szabványos**, és kattintson a **válasszon**.  

5. A beállítások panelen fogadja el az alapértelmezett beállításokat, majd kattintson **OK**.

6. Az a **összegzés** kattintson **OK** a virtuális gép létrehozásához.  

7. Az új virtuális gép megtekintéséhez kattintson **virtuális gépek**, majd keresse meg az új virtuális gép, és kattintson a nevére.

    ![Minden erőforrás](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> A virtuális gépek telepítése eltarthat néhány percig.


## <a name="next-steps"></a>További lépések

Mit tudott meg ez az oktatóanyag:

> [!div class="checklist"]
> * Előfizetés egy ajánlatra 
> * Az ajánlat tesztelése


> [!div class="nextstepaction"]
> [Virtuális gép létrehozása egy közösségi sablonból](azure-stack-create-vm-template.md)