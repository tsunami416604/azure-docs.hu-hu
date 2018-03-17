---
title: "Ebben az oktatóanyagban elsajátíthatja, hogyan Azure verem ajánlatot előfizetni |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan hozzon létre egy új előfizetést Azure verem szolgáltatásokhoz, és az ajánlat teszteléséhez a tesztcélú virtuális gépek létrehozásával."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: d2adda5613b9a10bc3314045b9d68b0f3196f19a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-and-test-a-subscription"></a>Oktatóanyag: hozzon létre és előfizetés tesztelése
Az oktatóanyag bemutatja, hogyan ajánlatot tartalmazó előfizetés létrehozása és tesztelik azt. A vizsgálathoz be fog jelentkezni a [felhasználói portál](https://portal.local.azurestack.external) felhő rendszergazdaként, az ajánlat előfizetni, és majd a virtuális gép létrehozása.

> [!TIP]
> További információ az összetettebb értékelési élmény, akkor [előfizetés létrehozása egy adott felhasználó számára](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) és majd, hogy a felhasználó a felhasználói portál bejelentkezési. 

Ez az oktatóanyag bemutatja, hogyan létrehozott ajánlat előfizetni a [az oktatóanyag előző](asdk-offer-services.md).

Amiről tanulni fog:

> [!div class="checklist"]
> * Előfizetés egy ajánlatra 
> * Az ajánlat tesztelése

## <a name="subscribe-to-an-offer"></a>Előfizetés egy ajánlatra
Egy olyan felhasználó nevében ajánlatot előfizetni szüksége a bejelentkezés az Azure-verem felhasználói portálra a szolgáltatásokat, amelyeket az Azure-verem üzemeltető felajánlása felderítéséhez.

1. Jelentkezzen be a [felhasználói portál](https://portal.local.azurestack.external) kattintson **egy előfizetés**.

   ![Előfizetés beszerzése](media/asdk-subscribe-services/get-subscription.png)

2. A **Megjelenítendő név** mezőben adja meg az előfizetés nevét. Kattintson a **kínálnak** segítségével válassza ki az elérhető ajánlatokat közül a **válasszon egy ajánlatot** szakaszt, és kattintson a **létrehozása**.

   ![Ajánlat létrehozása](media/asdk-subscribe-services/create-subscription.png)

   > [!TIP]
   > Most frissítenie kell a felhasználói portálra, ahol az előfizetés használatának megkezdéséhez.

3. Az újonnan létrehozott előfizetés megtekintéséhez kattintson **további szolgáltatások**, kattintson a **előfizetések**, majd kattintson az új előfizetés. Miután előfizetett egy ajánlatot, frissítse a portálhoz, és ha új le lett részeként az új előfizetés. Ebben a példában **virtuális gépek** hozzá lett adva.

   ![Előfizetés megtekintése](media/asdk-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Az ajánlat tesztelése
Bejelentkezett a [felhasználói portál](https://portal.local.azurestack.external), tesztelheti az ajánlat által a virtuális gépek az új előfizetési lehetőségek állításához. 

> [!NOTE]
> Ez a vizsgálat szükséges a Windows Server 2016 Datacenter VM lemezképfájlt, amely hozzá lett adva a verem Azure piactér a egy [az oktatóanyag előző](asdk-marketplace-item.md). 

1. Jelentkezzen be a [felhasználói portál](https://portal.local.azurestack.external).

2. A felhasználói portálon kattintson **virtuális gépek** > **Hozzáadás** > **Windows Server 2016 Datacenter**, és kattintson a **létrehozása** .

3. A a **alapjai** területen írja be a **neve**, **felhasználónév**, és **jelszó**, válassza ki a **előfizetés**, Hozzon létre egy **erőforráscsoport** (vagy válasszon egy meglévőt), és kattintson a **OK**.

4. Az a **méret kiválasztása** területen kattintson **A1 szabványos**, és kattintson a **válasszon**.  

5. A beállítások panelen fogadja el az alapértelmezett beállításokat, majd kattintson **OK**.

6. Az a **összegzés** kattintson **OK** a virtuális gép létrehozásához.  

7. Az új virtuális gép megtekintéséhez kattintson **virtuális gépek**, majd keresse meg az új virtuális gép, és kattintson a nevére.

    ![Minden erőforrás](media/asdk-subscribe-services/view-vm.png)

> [!NOTE]
> A virtuális gépek telepítése eltarthat néhány percig.


## <a name="next-steps"></a>További lépések

Mit tudott meg ez az oktatóanyag:

> [!div class="checklist"]
> * Előfizetés egy ajánlatra 
> * Az ajánlat tesztelése

Előzetes megtudhatja, hogyan hozzon létre egy Virtuálisgép-sablon használatával a következő oktatóanyagot.

> [!div class="nextstepaction"]
> [A virtuális gép létrehozása sablonból](asdk-create-vm-template.md)