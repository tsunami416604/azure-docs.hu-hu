---
title: Virtuális gépek Azure verem felhasználók számára történő elérhetővé |} Microsoft Docs
description: Az oktatóanyag segítséget nyújt a virtuális gépek Azure-veremben elérhetővé
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: af97f32736959f8ebf8f3c4fbca400d6b0c41f3e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>Virtuális gépek Azure verem felhasználók számára történő elérhetővé
Rendszergazdaként Azure verem felhő ajánlatokat, amelyek a (más néven bérlői) felhasználók előfizethetnek is létrehozhat. Használja az előfizetését, felhasználók tudják felhasználni Azure verem szolgáltatások.

Ez a cikk bemutatja, hogyan hozzon létre egy ajánlatot, és tesztelik azt. A vizsgálathoz fog majd hozza létre a virtuális gépek az előfizetéshez, az ajánlat előfizetni és jelentkezzen be a portál egy olyan felhasználó nevében el.

Amiről tanulni fog:

> [!div class="checklist"]
> * Ajánlat létrehozása
> * Lemezkép hozzáadása
> * Az ajánlat tesztelése


Azure-készletben a szolgáltatások a felhasználók számára előfizetéssel, ajánlatok és tervek érkeznek. Felhasználók előfizethetnek több ajánlatokat. Ajánlatok lehet egy vagy több terveket, és tervek rendelkezhet egy vagy több szolgáltatást.

![Előfizetések ajánlatok és tervek](media/azure-stack-key-features/image4.png)

További tudnivalókért lásd: [szolgáltatások és-Azure verem fogalmak](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Ajánlat létrehozása

Most kaphat a dolgok készen áll a felhasználók számára. Ha a folyamat elindításához először kéri ajánlatot, akkor a terv, végül kvóták létrehozása.

3. **Ajánlat létrehozása**

   Ajánlatok olyan beállításcsoportok, egy vagy több terveket tartalmazhatnak, amelyek a felhasználóknak, hogy a megvásárolt, illetve fizethetnek elő telepített szolgáltatók.

   a. [Jelentkezzen be a](azure-stack-connect-azure-stack.md) , a felhő rendszergazdájához, és kattintson a portál **új** > **kínál + tervek** > **kínálnak**.
   ![Új ajánlat](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. Az a **új ajánlat** területen adja meg **megjelenített név** és **erőforrásnév**, és majd ki kell jelölnie egy új vagy meglévő **erőforráscsoport**. A megjelenítendő név az ajánlat rövid neve. Csak a felhő üzemeltetője tekintheti meg az erőforrás nevét. Ezt a nevet használják a rendszergazdák az ajánlattal mint Azure Resource Manager-erőforrással végzett műveletekhez.

   ![Megjelenített név](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. Kattintson a **tervek kiinduló**, majd a a **terv** területen kattintson **Hozzáadás** hozzáadása egy új tervet az ajánlat.

   ![A csomag hozzáadása](media/azure-stack-tutorial-tenant-vm/image03.png)

   d. Az a **új megtervezése** területen adja meg **megjelenített név** és **erőforrásnév**. A megjelenített nevének értéke a terv valódi neve, amelyet a felhasználók. Csak a felhő üzemeltetője tekintheti meg az erőforrás nevét. A felhő üzemeltetői az Azure Resource Manager erőforrásként tervvel működését nevét is.

   ![Megjelenített név tervezése](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. Kattintson a **szolgáltatások**, jelölje be **Microsoft.Compute**, **Microsoft.Network**, és **Microsoft.Storage**, majd kattintson az **Válasszon**.

   ![Szolgáltatások megtervezése](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. Kattintson a **kvóták**, majd válassza ki az első szolgáltatás legyen kvóta. Az infrastruktúra-szolgáltatási kvótához kövesse az alábbi lépéseket a számítási, hálózati és tárolási szolgáltatások.

   Ebben a példában azt először hozzon létre egy kvótát a számítási szolgáltatás. A névtér, jelölje ki a **Microsoft.Compute** névtér, majd **hozzon létre új kvóta**.
   
   ![Új kvóta létrehozása](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. Az a **kvóta létrehozása** szakaszt, írjon be egy nevet a kvótát, és állítsa be a kívánt paramétereket a kvótát, majd kattintson a **OK**.

   ![Kvóta neve](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. Most, **Microsoft.Compute**, válassza ki a létrehozott kvótát.

   ![Válassza a kvóta](media/azure-stack-tutorial-tenant-vm/image08.png)

   Ismételje meg ezeket a lépéseket a hálózati és tárolási szolgáltatások, és kattintson a **OK** a a **kvóták** szakasz.

   i. Kattintson a **OK** a a **új tervet** szakasz.

   j. Az a **terv** szakaszt, válassza ki az új tervet, és kattintson **válasszon**.

   k. Az a **új ajánlat** kattintson **létrehozása**. Megjelenik egy értesítés, az ajánlat létrehozásakor.

   l. Az irányítópult menüben kattintson **kínál** és kattintson a létrehozott ajánlatot.

   m. Kattintson az **Állapot módosítása**, majd a **Nyilvános** elemre.

   ![Nyilvános állapota](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Lemezkép hozzáadása

Ahhoz, azok kiépíthetnek virtuális gépeket, hozzá kell adnia az Azure-verem Piactéri lemezkép. A kép érintenek, beleértve a Linux-lemezképek, az Azure piactérről is hozzáadhat.

Ha olyan csatlakoztatott forgatókönyvekben működik, és ha az Azure-ral regisztrált az Azure-verem-példányt, majd letöltheti a Windows Server 2016 Virtuálisgép-lemezkép az Azure piactérről ismertetett lépések segítségével a [letöltési piactér az Azure-ból elemek Azure verem](azure-stack-download-azure-marketplace-item.md) témakör.

További információ a különböző elemek hozzáadása a piactér: [az Azure-verem piactér](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Az ajánlat tesztelése

Most, hogy létrehozta az ajánlatot, tesztelheti. Jelentkezzen be a felhasználó és az ajánlat előfizetni, és adja hozzá a virtuális gép.

1. **Előfizetés egy ajánlatra**

   Most már bejelentkezhet a portálra ajánlatot előfizetni felhasználóként.

   a. Jelentkezzen be a felhasználói portálra, a felhasználó, és kattintson **egy előfizetés**.
   - Egy integrált rendszer, az URL-cím attól függően változik, az üzemeltető régió és külső tartomány nevét, és formátumban lesz https://portal.&lt; *régió*&gt;.&lt; *FQDN*&gt;.
   - Az Azure verem szoftverfejlesztői készlet használata, a portál címe https://portal.local.azurestack.external.

   ![Előfizetés beszerzése](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. A a **megjelenített név** mezőben, írja be az előfizetés nevét, kattintson a **kínálnak**, kattintson az offers egyikére a **válasszon egy ajánlatot** szakaszt, és kattintson a  **Hozzon létre**.

   ![Ajánlat létrehozása](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. A létrehozott előfizetés megtekintéséhez kattintson **további szolgáltatások**, kattintson a **előfizetések**, majd kattintson az új előfizetés.  

   Miután előfizetett egy ajánlatot, frissítse a portálhoz, és milyen szolgáltatásokat az új előfizetés részét képezik.

2. **Virtuális gép kiépítése**

   Most már bejelentkezhet a portálra az előfizetés virtuális gép kiépítése felhasználóként. 

   a. Jelentkezzen be a felhasználói portál egy olyan felhasználó nevében.
      - Egy integrált rendszer, az URL-cím attól függően változik, az üzemeltető régió és külső tartomány nevét, és formátumban lesz https://portal.&lt; *régió*&gt;.&lt; *FQDN*&gt;.
   - Az Azure verem szoftverfejlesztői készlet használata, a portál címe https://portal.local.azurestack.external.

   b.  Az irányítópulton kattintson **új** > **számítási** > **Windows Server 2016 Datacenter próbaverzió**, és kattintson a **létrehozása**.

   c. A a **alapjai** területen írja be a **neve**, **felhasználónév**, és **jelszó**, válassza ki a **előfizetés**, Hozzon létre egy **erőforráscsoport** (vagy válasszon egy meglévőt), és kattintson a **OK**.

   d. Az a **méret kiválasztása** területen kattintson **A1 szabványos**, és kattintson a **válasszon**.  

   e. Az a **beállítások** kattintson **virtuális hálózati**. Az a **válasszon virtuális hálózati** kattintson **hozzon létre új**. Az a **virtuális hálózat létrehozása** szakaszt, fogadja el az alapértelmezett beállításokat, és kattintson a **OK**. Az a **beállítások** kattintson **OK**.

   ![Virtuális hálózat létrehozása](media/azure-stack-provision-vm/image04.png)

   f. Az a **összegzés** kattintson **OK** a virtuális gép létrehozásához.  

   g. Az új virtuális gép megtekintéséhez kattintson **összes erőforrás**, majd keresse meg a virtuális gép, és kattintson a nevére.

    ![Minden erőforrás](media/azure-stack-provision-vm/image06.png)

Mit tudott meg ez az oktatóanyag:

> [!div class="checklist"]
> * Ajánlat létrehozása
> * Lemezkép hozzáadása
> * Az ajánlat tesztelése

> [!div class="nextstepaction"]
> [Webes, mobil és API-alkalmazások felhasználók számára elérhetővé az Azure-verem](azure-stack-tutorial-app-service.md)