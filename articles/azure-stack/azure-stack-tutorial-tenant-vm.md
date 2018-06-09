---
title: Virtuális gépek Azure verem felhasználók számára történő elérhetővé |} Microsoft Docs
description: 'Útmutató: virtuális gépek Azure-veremben elérhetővé'
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 9329cb0dbfa24cf239b820573ef7f642cdca9103
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248159"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Útmutató: virtuális gépek felhasználók számára elérhetővé az Azure verem

Rendszergazdaként Azure verem felhő ajánlatokat, amelyek a (más néven bérlői) felhasználók előfizethetnek is létrehozhat. Előfizet egy ajánlatot, a felhasználók az Azure-verem szolgáltatásainak ajánlatot is felhasználhatnak.

Ez az oktatóanyag bemutatja, hogyan hozzon létre egy virtuális gép ajánlatot, majd jelentkezzen be egy olyan felhasználó nevében a ajánlat teszteléséhez.

Amiről tanulni fog:

> [!div class="checklist"]
> * Ajánlat létrehozása
> * Lemezkép hozzáadása
> * Az ajánlat tesztelése

Azure-készletben a szolgáltatások a felhasználók számára előfizetéssel, ajánlatok és tervek érkeznek. Felhasználók előfizethetnek több ajánlatokat. Az ajánlat lehet egy vagy több tervek, és a terv rendelkezhet egy vagy több szolgáltatás.

![Előfizetések ajánlatok és tervek](media/azure-stack-key-features/image4.png)

További tudnivalókért lásd: [szolgáltatások és-Azure verem fogalmak](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Ajánlat létrehozása

Ajánlatok olyan beállításcsoportok, egy vagy több terveket tartalmazhatnak, amelyek a felhasználóknak, hogy a megvásárolt, illetve fizethetnek elő telepített szolgáltatók. Ajánlat létrehozásának folyamatán számos lépést tartalmaz. Először is megkéri ajánlatot, akkor a terv, végül kvóták létrehozása.

1. [Jelentkezzen be a](azure-stack-connect-azure-stack.md) , a felhő rendszergazdájához, és válassza ki a portál **új** > **kínál + tervek** > **kínálnak**.

   ![Új ajánlat](media/azure-stack-tutorial-tenant-vm/image01.png)

2. A **új ajánlat**, adjon meg egy **megjelenített név** és **erőforrásnév**, és majd ki kell jelölnie egy új vagy meglévő **erőforráscsoport**. A megjelenítendő név az ajánlat rövid neve. Csak a felhő üzemeltetője tekintheti meg az erőforrás nevét. Ezt a nevet használják a rendszergazdák az ajánlattal mint Azure Resource Manager-erőforrással végzett műveletekhez.

   ![Megjelenített név](media/azure-stack-tutorial-tenant-vm/image02.png)

3. Válassza ki **tervek kiinduló**, és a a **terv** szakaszban jelölje be **Hozzáadás** ajánlatot hozzáadása egy új tervet.

   ![A csomag hozzáadása](media/azure-stack-tutorial-tenant-vm/image03.png)

4. Az a **új megtervezése** területen adja meg **megjelenített név** és **erőforrásnév**. A megjelenített nevének értéke a terv valódi neve, amelyet a felhasználók. Csak a felhő üzemeltetője tekintheti meg az erőforrás nevét. A felhő üzemeltetői az Azure Resource Manager erőforrásként tervvel működését nevét is.

   ![Megjelenített név tervezése](media/azure-stack-tutorial-tenant-vm/image04.png)

5. Válassza ki **szolgáltatások**. A szolgáltatások listából válasszon **Microsoft.Compute**, **Microsoft.Network**, és **Microsoft.Storage**. Válasszon **válasszon** ezek a szolgáltatások hozzáadása a tervet.

   ![Szolgáltatások megtervezése](media/azure-stack-tutorial-tenant-vm/image05.png)

6. Válassza ki **kvóták**, majd válassza ki az első szolgáltatás, amelyet szeretne létrehozni egy kvótát. Az infrastruktúra-szolgáltatási kvóta használja az alábbi példa útmutatóként a számítási, hálózati és tárolási szolgáltatások kvóták konfigurálásához.

   - Először hozzon létre egy kvótát a számítási szolgáltatás. A névtér listában válassza ki a **Microsoft.Compute** , és válassza **hozzon létre új kvóta**.

     ![Új kvóta létrehozása](media/azure-stack-tutorial-tenant-vm/image06.png)

   - A **kvóta létrehozása**, adjon meg egy nevet a kvótát. Módosíthatja, vagy fogadja el a kvóta értékek a létrehozni kívánt kvótához látható bármelyikét. Ebben a példában, hogy fogadja el az alapértelmezett beállításokat, és válassza **OK**.

     ![Kvóta neve](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Válasszon **Microsoft.Compute** a névtér listában, és válassza ki a létrehozott kvótát. Ez a kvóta a számítási szolgáltatás hivatkozásokat tartalmaz.

     ![Válassza a kvóta](media/azure-stack-tutorial-tenant-vm/image08.png)

      Ismételje meg ezeket a lépéseket a hálózati és tárolási szolgáltatások. Amikor elkészült, válassza ki a **OK** a **kvóták** kvóták mentéséhez.

7. A **új tervet**, jelölje be **OK**.

8. A **terv**, válassza ki az új tervet, majd **válasszon**.

9. A **új ajánlat**, jelölje be **létrehozása**. Megjelenik egy értesítés, az ajánlat létrehozásakor.

10. Az irányítópult menüben válassza ki **kínál** , majd válassza ki a létrehozott ajánlat használni.

11. Válassza ki **állapotváltoztatási**, majd **nyilvános**.

    ![Nyilvános állapota](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Lemezkép hozzáadása

Ahhoz, azok kiépíthetnek virtuális gépeket, hozzá kell adnia az Azure-verem Piactéri lemezkép. A kép érintenek, beleértve a Linux-lemezképek, az Azure piactérről is hozzáadhat.

Ha olyan csatlakoztatott forgatókönyvekben működik, és ha az Azure-ral regisztrált az Azure-verem-példányt, majd letöltheti a Windows Server 2016 Virtuálisgép-lemezkép az Azure piactérről ismertetett lépések segítségével a [letöltési piactér az Azure-ból elemek Azure verem](azure-stack-download-azure-marketplace-item.md) témakör.

További információ a különböző elemek hozzáadása a piactér: [az Azure-verem piactér](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Az ajánlat tesztelése

Most, hogy létrehozta az ajánlatot, tesztelheti. Lesz jelentkezik be, és a felhasználó az ajánlat előfizetni, és adja meg az egy virtuális gépet.

1. **Előfizetés egy ajánlatra**

   a. Jelentkezzen be a felhasználói portál felhasználói fiókkal, és válassza ki a **egy előfizetés** csempére.
   - Egy integrált rendszer, az URL-cím attól függően változik, az üzemeltető régió és külső tartomány nevét, és formátumban lesz https://portal.&lt; *a régióban*&gt;.&lt; *FQDN*&gt;.
   - Az Azure verem szoftverfejlesztői készlet használata, a portál címe https://portal.local.azurestack.external.

   ![Előfizetés beszerzése](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. A **egy előfizetés**, adjon meg egy nevet az előfizetéshez a **megjelenített név** mező. Válassza ki **kínálnak**, majd kattintson az offers a **válasszon egy ajánlatot** lista. Kattintson a **Létrehozás** gombra.

   ![Ajánlat létrehozása](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Válassza ki, ha az előfizetés **további szolgáltatások**, majd válassza ki **előfizetések**. Válassza ki az új előfizetés, hogy mely szolgáltatások-előfizetés részét.

   >[!NOTE]
   >Miután előfizetett egy ajánlatot, lehetséges, hogy frissíti a portálhoz, és milyen szolgáltatásokat az új előfizetés részét képezik.

2. **Virtuális gép kiépítése**

   A felhasználói portálról létesíthet a virtuális gépek az új előfizetés.

   a. Jelentkezzen be a felhasználói portál felhasználói fiókkal.
      - Egy integrált rendszer, az URL-cím attól függően változik, az üzemeltető régió és külső tartomány nevét, és formátumban lesz https://portal.&lt; *a régióban*&gt;.&lt; *FQDN*&gt;.
   - Az Azure verem szoftverfejlesztői készlet használata, a portál címe https://portal.local.azurestack.external.

   b.  Az irányítópultra, válassza ki a **új** > **számítási** > **Windows Server 2016 Datacenter próbaverzió**, majd válassza ki **létrehozása**.

   c. A **alapjai**, adja meg a következő információkat:
      - Adjon meg egy **neve**
      - Adjon meg egy **felhasználónév**
      - Adjon meg egy **jelszó**
      - Válasszon egy **előfizetés**
      - Hozzon létre egy **erőforráscsoport** (vagy válasszon egy meglévőt.) 
      - Válassza ki **OK** menteni ezeket az információkat.

   d. A **méret kiválasztása**, jelölje be **A1 szabványos**, majd **kiválasztása**.  

   e. A **beállítások**, jelölje be **virtuális hálózati**.

   f. A **válasszon virtuális hálózati**, jelölje be **hozzon létre új**.

   g. A **virtuális hálózat létrehozása**, fogadja el az alapértelmezett beállításokat, és válassza ki **OK**.

   h. Válassza ki **OK** a **beállítások** a hálózati konfiguráció mentéséhez.

   ![Virtuális hálózat létrehozása](media/azure-stack-provision-vm/image04.png)

   i. A **összegzés**, jelölje be **OK** a virtuális gép létrehozásához.  

   j. Az új virtuális gépet, jelölje ki a **összes erőforrás**. Keresse meg a virtuális gép, és jelölje ki a nevet a keresési eredmények között.

   ![Minden erőforrás](media/azure-stack-provision-vm/image06.png)

## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Ajánlat létrehozása
> * Lemezkép hozzáadása
> * Az ajánlat tesztelése

Előzetes tovább az oktatóanyaghoz, megtudhatja, hogyan:
> [!div class="nextstepaction"]
> [SQL-adatbázisok Azure verem felhasználók számára történő elérhetővé](azure-stack-tutorial-sql-server.md)