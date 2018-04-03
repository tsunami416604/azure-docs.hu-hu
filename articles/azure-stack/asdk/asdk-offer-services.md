---
title: Ebben az oktatóanyagban létrehoz egy Azure verem ajánlat |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy Azure verem ajánlatot, beleértve a csomagok és a kvóták.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 8bcc2f3077e79ff83ac2e90db0bb0fa53ae83adc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="tutorial-offer-azure-stack-iaas-services"></a>Oktatóanyag: Azure verem IaaS szolgáltatást kínál.
Rendszergazdaként Azure verem felhő ajánlatokat, amelyek a (más néven bérlői) felhasználók előfizethetnek is létrehozhat. Használja az előfizetését, felhasználók tudják felhasználni Azure verem szolgáltatások.

Ez az oktatóanyag bemutatja, hogyan hozzon létre egy ajánlatot ahhoz, hogy a felhasználók számára a feltöltött verem Azure piactér Windows Server 2016 Datacenter lemezképen alapuló virtuális gépek létrehozását a [az oktatóanyag előző](asdk-marketplace-item.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ajánlat létrehozása
> * Csomag létrehozása
> * Kvóták beállítása
> * Nyilvános set vonatkozó ajánlatot

Azure-készletben a szolgáltatások a felhasználók számára előfizetéssel, ajánlatok és tervek érkeznek. Felhasználók előfizethetnek több ajánlatokat. Ajánlatok lehet egy vagy több terveket, és tervek rendelkezhet egy vagy több szolgáltatást, az alábbi ábrán látható módon:

![Előfizetések ajánlatok és tervek](media/asdk-offer-services/sop.png)

Kezelőként Azure verem szolgáltatásokat kínáló először kéri ajánlatot, akkor a terv, végül kvóták létrehozásához. Az ajánlat létrehozása után Azure verem felhasználók majd előfizethetnek ajánlatokat a felhasználói portálon.

## <a name="create-an-offer"></a>Ajánlat létrehozása
**Kínál** egy vagy több terveket tartalmazhatnak, amelyek Azure verem operátorok bevezetését a megvásárolt, illetve a jelentésre való előfizetéshez felhasználók csoportja.

1. Jelentkezzen be a [verem Azure portal](https://adminportal.local.azurestack.external) felhő rendszergazdaként.

2. Kattintson a **új** > **kínál + tervek** > **kínálnak**.

   ![Új ajánlat](media/asdk-offer-services/new-offer.png)

2. Az a **új ajánlat** területen adja meg **megjelenített név** és **erőforrásnév**, és majd ki kell jelölnie egy új vagy meglévő **erőforráscsoport**. A megjelenített nevének értéke az ajánlat nyilvános rövid nevet, amelyet a felhasználók. Csak a felhő üzemeltetője tekintheti meg az erőforrás nevét, az Azure Resource Manager erőforrásként ajánlat működéséhez rendszergazdák által használt.

   ![Megjelenített név](media/asdk-offer-services/offer-display-name.png)


## <a name="create-a-plan"></a>Csomag létrehozása
Az ajánlat alapvető adatok megadása után hozzá kell adnia legalább egy referenciaterv ajánlatot. 

**Tervek** engedélyezése Azure verem operátorok csoport szolgáltatásokhoz, és a kapcsolódó kvótákat, a felhasználók számára.

1. Kattintson a **tervek kiinduló**, majd a a **terv** területen kattintson **Hozzáadás** hozzáadása egy új tervet az ajánlat.

   ![A csomag hozzáadása](media/asdk-offer-services/new-plan.png)

2. Az a **új megtervezése** területen adja meg **megjelenített név** és **erőforrásnév**. A megjelenített nevének értéke a terv valódi neve, amelyet a felhasználók. Csak a felhő üzemeltetője tekintheti meg az erőforrás nevét, a felhő üzemeltetői használják az Azure Resource Manager erőforrásként tervet dolgozhat.

   ![Megjelenített név tervezése](media/asdk-offer-services/plan-display-name.png)

3. Ezután válassza ki a szolgáltatásokat a terv szerepeltetni. IaaS szolgáltatást kínál, kattintson a **szolgáltatások**, jelölje be **Microsoft.Compute**, **Microsoft.Network**, és **Microsoft.Storage**, majd Kattintson a **válasszon**.

   ![Szolgáltatások megtervezése](media/asdk-offer-services/select-services.png)


## <a name="set-quotas"></a>Kvóták beállítása
Most, hogy az ajánlat, amely tartalmazza a szolgáltatások terv rendelkezik, meg kell adni a kvóták esetében. 

**Kvóták** határozza meg, hogy a felhasználó a felajánlott tervben szereplő minden egyes szolgáltatáshoz felhasználhat erőforrások mennyisége.

1. Kattintson a **kvóták**, majd válassza ki a szolgáltatást, amelyekhez kvóta. 

   Első lépésként hozzon létre egy kvótát a számítási szolgáltatás. A névtér, jelölje ki a **Microsoft.Compute** névtér, majd **hozzon létre új kvóta**.
   
   ![Új kvóta létrehozása](media/asdk-offer-services/create-quota.png)

2. Az a **kvóta létrehozása** szakaszban, írjon be egy nevet a kvótát, a kvóta a kívánt paramétereket állítja be, és kattintson **OK**.

   ![Kvóta neve](media/asdk-offer-services/quota-properties.png)

3. Válassza ki a létrehozott kvótát a **Microsoft.Compute** szolgáltatás.

   ![Válassza a kvóta](media/asdk-offer-services/set-quota.png)

4. Ismételje meg az 1-3 kvótáit a hálózati és tárolási szolgáltatások, és kattintson az OK gombra a kvóták szakasz. Ezután kattintson **OK** a a **új tervet** szakasz a terv telepítés befejezéséhez. 

   ![Kvóták minden beállítás](media/asdk-offer-services/all-quotas-set.png)

5. Véglegesítse az ajánlat kattintva **létrehozása** a terv szakasz. Megjelenik egy értesítés, amikor az ajánlat sikeresen jön létre, és elérhető ajánlatot állapottal jelenik.

   ![Az ajánlat létrehozása](media/asdk-offer-services/offer-complete.png)

## <a name="set-offer-to-public"></a>Nyilvános set vonatkozó ajánlatot
Ajánlatok a felhasználók számára, amikor egy ajánlatot előfizetni kiválasztása közzé kell tenni. 

Ajánlatok lehet:
- **Nyilvános**: az összes felhasználó elől.
- **Személyes**: csak a felhő rendszergazdák számára látható. A terv vagy ajánlat létrehozásakor elkészítése során hasznos, vagy ha a felhő rendszergazdája szeretné a felhasználók minden előfizetés létrehozása.
- **Leszerelve:** Új előfizetők már nem választhatják. A felhő rendszergazdájához használható leszerelt jövőbeli előfizetések megelőzni, de a jelenlegi előfizetők változatlanul hagyja.

> [!TIP]
> Az ajánlat módosításai nincsenek közvetlenül a felhasználók számára látható. A módosítások megtekintéséhez a felhasználóknak problémájuk jelentkezzen ki, és jelentkezzen be újra a [felhasználói portál](https://portal.local.azurestack.external) az új ajánlat megjelenítéséhez.

Az új ajánlat beállítása a nyilvánosság számára: 
   - 1803 vagy újabb verziója: 
     1. Az irányítópult menüben kattintson **kínál** és kattintson a létrehozott ajánlatot.

     2. Kattintson a **kisegítő állapot**, és kattintson a **nyilvános**.

        ![Állapot módosítása](media/asdk-offer-services/change-state.png)

     3. Az ajánlat lesz elérhető az Azure-verem felhasználói portálon.


   - A verzió 1803 előtt:  
     1. Az irányítópult menüben kattintson **kínál** és kattintson a létrehozott ajánlatot.

     2. Kattintson az **Állapot módosítása**, majd a **Nyilvános** elemre.

        ![Nyilvános állapota](media/asdk-offer-services/set-public.png)

     3. Az ajánlat lesz elérhető az Azure-verem felhasználói portálon.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Ajánlat létrehozása
> * Csomag létrehozása
> * Kvóták beállítása
> * Nyilvános set vonatkozó ajánlatot

A következő oktatóanyag áttekintésével megismerheti, hogyan fizethet elő az imént létrehozott Azure verem felhasználóként ajánlat továbblépés.

> [!div class="nextstepaction"]
> [Előfizetés egy ajánlatra](asdk-subscribe-services.md)