---
title: Tekintse meg a havi becsült laborköltség-trendet az Azure DevTest Labsben
description: Ez a cikk alabor (havi becsült költségtrenddiagram) azure DevTest Labs költségének nyomon követésével kapcsolatos információkat tartalmaz.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2e4fe55fac5edf73e16df05bd38cc2712a94377d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721727"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Laborhoz kapcsolódó költségek nyomon követése az Azure DevTest Labsben
Ez a cikk a labor költségének nyomon követésével kapcsolatos információkat tartalmaz. Ez megmutatja, hogyan tekintheti meg a labor aktuális naptári hónapjára becsült költséget. A cikk azt is bemutatja, hogyan tekintheti meg a hónap-a mai napig erőforrásonkénti költséget a laborban.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>A havi becsült laborköltség-trend megtekintése 
Ebből a szakaszból megtudhatja, hogyan tekintheti meg az aktuális **naptári** hónap aktuális naptári havi becsült költség-to-date és az aktuális naptári hónap tervezett hónapvégi költségét. Azt is megtudhatja, hogyan kezelheti a laborköltségeket olyan költési célok és küszöbértékek beállításával, amelyek elérésekor a DevTest Labs-t az eredmények jelentéséhez aktiválják.

A Havi becsült költség trend diagramjának megtekintéséhez kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
3. A laborok listájából válassza ki a labort.  
4. Válassza a bal oldali menü **Konfiguráció és házirendek parancsát.**  
4. A bal oldali menü **Költségkövetés** szakaszában válassza a **Költségtrend** lehetőséget. A következő képernyőképen egy költségdiagram látható. 
   
    ![Költségdiagram](./media/devtest-lab-configure-cost-management/graph.png)

    A **becsült költségérték** az aktuális naptári hónap becsült költség-adék-dátuma. A **tervezett költség** a teljes aktuális naptári hónap becsült költsége, amelyet az előző öt nap tesztelemköltségével számítanak ki.

    A költségösszegek a következő egész számra lesznek felkerekítve. Példa: 

   * 5,01 fordulók akár 6 
   * 5,50 forduló6-ig
   * 5,99 forduló6-ig

     A diagram felett megírja, hogy a diagramon alapértelmezés szerint látható költségek a *használatalapú* [fizetéses](https://azure.microsoft.com/offers/ms-azr-0003p/) ajánlati díjak használatával becsült költségek. A diagramokon megjelenő saját költési célokat is beállíthatja [a tesztkörnyezet költségcéljainak kezelésével.](#managing-cost-targets-for-your-lab)

     A költségszámítás *nem* tartalmazza a következő költségeket:

   * A CSP- és Dreamspark-előfizetések jelenleg nem támogatottak, mivel az Azure DevTest Labs az [Azure számlázási API-kat](../cost-management-billing/manage/usage-rate-card-overview.md) használja a laborköltség kiszámításához, amely nem támogatja a CSP- vagy a Dreamspark-előfizetéseket.
   * Az ajánlat a díjakat. Jelenleg nem használhatja a Microsofttal vagy a Microsoft-partnerekkel egyeztetett ajánlati díjakat (az előfizetésalatt). A használatalapú fizetésdíj csak használatos.
   * Az Ön adói
   * Az Ön kedvezményei
   * A számlázási pénznem. Jelenleg a labor költség csak USD pénznemben jelenik meg.

### <a name="managing-cost-targets-for-your-lab"></a>A tesztkörnyezet költségcéljainak kezelése
A DevTest Labs lehetővé teszi a laborban felmerülő költségek jobb kezelését egy olyan kiadási cél beállításával, amelyet ezután megtekinthet a havi becsült költség trend diagramjában. DevTest Labs is küldhet értesítést, ha a megadott cél kiadások vagy küszöbérték elérésekor. 

1. A **Költség trend** lapon válassza **a Cél kezelése lehetőséget.**

    ![Cél kezelése gomb](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. A **Cél kezelése** lapon adja meg a költési célt és a küszöbértékeket. Azt is beállíthatja, hogy minden egyes kiválasztott küszöbértéket a költségtrend diagramon vagy egy webhook-értesítésen keresztül jelentsen.You also set whether each selected threshold is reported on the cost trend chart or through a webhook notification.

    ![Célablak tábla kezelése](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Válassza ki azt az időszakot, amely alatt nyomon szeretné követni a költségcélokat.
      - **Havi**: a költségcélokat havonta követi nyomon a függvény.
      - **Javítva**: a költségcélok nyomon követése a kezdési és befejezési dátumokban megadott dátumtartományhoz. Ezek az értékek általában azt jelzik, hogy a projekt várhatóan mennyi ideig fog futni.
   - Adja meg a **célköltséget**. Például, hogy mennyit szeretne költeni erre a laborra a megadott időszakban.
   - Bejelölésével engedélyezheti vagy letilthatja a jelentett küszöbértékeket – 25%-os lépésekben – a megadott **célköltség**125%-át.
      - **Értesítés:** Ha ez a küszöbérték teljesül, a megadott webhook URL-cím értesíti.
      - **Telek diagramon:** Ha ez a küszöbérték teljesül, az eredmények a megtekinthető költségtrendgrafikonon kerülnek nyomtatásra, a Havi becsült költségtrend diagram megtekintése című táblázatban leírtak szerint.
   - Ha úgy dönt, hogy **értesíti,** ha a küszöbérték teljesül, meg kell adnia egy webhook URL-címet. A Költségintegrációk területen válassza a Kattintson ide lehetőséget **az integráció hozzáadásához.** Írjon be egy **Webhook URL-címet** az Értesítés konfigurálása ablaktáblában, majd kattintson az **OK gombra.**

       ![Értesítési ablaktábla konfigurálása](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Ha az **Értesítés megadását**adja meg, meg kell adnia egy webhook URL-címet.
     - Hasonlóképpen, ha megad egy webhook URL-címet, az **Értesítés beállítását** be kell **állítania** a Költségküszöbérték-ablaktáblában.
     - Létre kell hoznia egy webhookot, mielőtt beírna ide.  

       A webhookokról további információt a [Webhook vagy AZ API Azure-függvény létrehozása című témakörben talál.](../azure-functions/functions-create-a-web-hook-or-api-function.md) 

## <a name="view-cost-by-resource"></a>Költség megtekintése erőforrás szerint 
A laborok havi költségtrend-funkciója lehetővé teszi, hogy lássa, mennyit költött az aktuális naptári hónapban. Azt is mutatja, az előrejelzés a kiadások végéig a hónap alapján a kiadások az elmúlt hét napban. Annak megértéséhez, hogy a laborban a kiadások miért érik el a küszöbértékeket korán, használhatja a **költség erőforrásonként** funkciót, amely megmutatja a tábla **erőforrásonkénti** havi költségét.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
3. A laborok listájából válassza ki a kívánt labort.  
4. Válassza a bal oldali menü **Konfiguráció és házirendek parancsát.**
5. A bal oldali menü Költség **erőforrás szerint** lehetőséget kínálva válassza a Költségkövetés szakasz **Költségkövetés** szakaszában. A laborhoz társított egyes erőforrásokhoz kapcsolódó költségek et láthatja. 

    ![Költségek erőforrások szerint](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Ez a funkció segítségével egyszerűen azonosíthatja azokat az erőforrásokat, amelyek a legnagyobb költségekbe kerülnek, így műveleteket végezhet a tesztkörnyezet kiadásainak csökkentése érdekében. Például egy virtuális gép költsége a virtuális gép mérete alapján. Minél nagyobb a virtuális gép mérete, annál több a költség. Könnyen megtalálhatja a virtuális gép és a tulajdonos méretét, így a virtuális gép tulajdonosával beszélgethet, hogy megértse, miért van szükség ilyen virtuálisgép-méretre, és hogy van-e esély a méret csökkentésére.

[Az automatikus leállítási házirend](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) segít csökkenteni a költségeket azáltal, hogy a nap egy adott időszakában leállítja a tesztkörnyezet virtuális gépeit. Azonban egy tesztkörnyezet-felhasználó leiratkozhat a leállítási szabályzat, amely növeli a virtuális gép futtatásának költségeit. A virtuális gép kiválasztásával ellenőrizheti, hogy le van-e választva az automatikus leállítási házirendről. Ebben az esetben a virtuális gép tulajdonosával megtudhatja, hogy miért a virtuális gép le van választva a szabályzatból.
 
## <a name="next-steps"></a>További lépések
Íme néhány dolog, amit megpróbálhat a következő lépésként:

* [Laborszabályzatok meghatározása](devtest-lab-set-lab-policy.md) – Ismerje meg, hogyan állíthatja be a különböző szabályzatok a labor és a virtuális gépek használatának szabályozásához használt. 
* [Egyéni lemezkép létrehozása](devtest-lab-create-template.md) – Virtuális gép létrehozásakor meg kell adnia egy bázist, amely lehet egyéni lemezkép vagy Piactér-lemezkép. Ez a cikk bemutatja, hogyan hozhat létre egyéni lemezképet egy vhd fájlból.
* [Marketplace-rendszerképek konfigurálása](devtest-lab-configure-marketplace-images.md) – A DevTest Labs támogatja a virtuális gépek létrehozását az Azure Marketplace-lemezképek alapján. Ez a cikk bemutatja, hogyan adja meg, hogy mely, ha van ilyen, az Azure Marketplace-lemezképek használható virtuális gépek létrehozásakor egy tesztkörnyezetben.
* [Virtuális gép létrehozása egy tesztkörnyezetben](devtest-lab-add-vm.md) – bemutatja, hogyan hozhat létre virtuális gép egy alaprendszerképből (egyéni vagy Piactér), és hogyan működik a virtuális gép összetevők.

