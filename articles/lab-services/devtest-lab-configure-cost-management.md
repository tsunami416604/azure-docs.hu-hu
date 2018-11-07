---
title: A havi becsült labor költség trend megtekintése az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: További információ az Azure DevTest Labs havi becsült költség trend diagram.
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: e616df772bf11d1247f96c78bea2392252f5e5d0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259751"
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>A havi becsült labor költség trend megtekintése az Azure DevTest Labs szolgáltatásban
A Cost Management szolgáltatás a DevTest Labs segítségével nyomon követheti a labor költségét. Ez a cikk bemutatja, hogyan használhatja a **havi becsült költség Trend** diagram a jelenlegi naptári hónap becsült költség dátumig és a hónap teljes költségről megtekintése az aktuális hónap. Ez a cikk emellett bemutatja, hogyan kontrollja tesztlabor beállításával költségkeret célokhoz és küszöbértékekhez, elérésekor, eseményindító DevTest Labs-jelentik az eredményeket.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>A havi becsült költség Trend diagram megtekintése
A havi becsült költség Trend diagram megtekintéséhez kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Ha szükséges, válassza ki a **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából. (Előfordulhat, hogy a tesztkörnyezet már látható az irányítópult alatt **összes erőforrás**).
1. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.  
1. A laborgyakorlat **áttekintése** területen válassza **Konfigurace a zásady**.   
1. A bal oldali alatt **költségek nyomon követése**, jelölje be **költség trend**.

   Az alábbi képernyőfelvételhez költség diagram egy példát mutat be. 
   
    ![A Cost diagram](./media/devtest-lab-configure-cost-management/graph.png)

A **becsült költség** értéke a jelenlegi naptári hónap becsült költség dátumig. A **előre jelzett költsége** becsült költsége van a teljes aktuális hónap, a labor költség számítja ki az előző öt nap.

A költségek összegeket a legközelebbi egész számra kerekíti. Példa: 

* 5.01 kerekít legfeljebb 6 
* 5.50 kerekít legfeljebb 6
* 5.99 kerekít legfeljebb 6

Meghatározza a diagram felett, a költségek, alapértelmezés szerint a diagram a láthatja azokat *becsült* költségek használatával [használatalapú](https://azure.microsoft.com/offers/ms-azr-0003p/) ajánlja fel díjakat. Is beállíthat, amely szerint a diagramokban jelennek meg a saját költségkeret-beállítási célok [kezelése a költségek célokat a tesztkörnyezethez.](#managing-cost-targets-for-your-lab)

Ezenkívül a következők *nem* szereplő költség:

* Kriptográfiai Szolgáltató és a Dreamspark-előfizetések jelenleg nem támogatottak, használja az Azure DevTest Labs a [Azure számlázási API-k](../billing/billing-usage-rate-card-overview.md) a költség, amely nem támogatja a kriptográfiai Szolgáltató vagy a Dreamspark-előfizetések labor kiszámításához.
* Az ajánlat érvényes. Az ajánlat (látható díját az előfizetéshez tartozó), hogy Ön rendelkezik egyeztetése a Microsoft vagy a Microsoft partnerek jelenleg nem használható. Csak az utólagos elszámolású szolgálnak.
* Az adókat
* Slevy
* A Számlázás pénzneme. Jelenleg a labor költség csak USD pénznemben jelenik meg.

## <a name="managing-cost-targets-for-your-lab"></a>A tesztkörnyezet céljainak költségek kezelése
DevTest Labs lehetővé teszi kontrollja a tesztkörnyezetben, majd megtekintheti a havi becsült költség Trend diagram költségkeret cél beállításával. DevTest Labs segítségével is értesítést küld a célként megadott kiadások vagy a küszöbérték elérésekor. 

1. A laborgyakorlat **áttekintése** ablaktáblán válassza előbb **Konfigurace a zásady**.
1. A bal oldali alatt **költségek nyomon követése**, jelölje be **költség trend**.

    ![Cél gomb kezelése](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. Az a **költség trend** ablaktáblán válassza **kezelés cél**.

    ![Cél gomb kezelése](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. A kezelés cél panelen adja meg a kívánt költségkeret cél és a küszöbértékeket. Beállíthatja, hogy egyes kiválasztott küszöbérték készüljön jelentés, a költség trend diagramra vagy webhook értesítést is.

    ![Cél panelen kezelése](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Válassza ki egy adott időszakban, mely során költség célok nyomon követni kívánt.
      - **Havi**: havi költség célok nyomon követi.
      - **Rögzített**: költség célok nyomon követi a kezdő dátum és végső dátummezők megadott dátumtartományra vonatkozóan. Általában ezt meg az mennyi ideig a projekthez való futásra van ütemezve.
   - Adjon meg egy **költség cél**. Előfordulhat például, ez mennyi azt tervezi, hogy a megadott időszakban a jelen labor beváltható.
   - Engedélyezheti vagy tilthatja le semmilyen küszöbérték válassza azt szeretné, akár 125 %-át a megadott, 25 %-os léptékben – jelentett **költség cél**.
      - **Értesítés**: a küszöbértéket, amikor értesítést kap, adja meg a webhook URL-cím szerint.
      - **Vykreslit v grafu**: Ha ezt a küszöbértéket, az eredmények ábrázolási is megtekintheti, költség trend gráfokon leírtak szerint [a havi becsült költség Trend diagram megtekintése](#viewing-the-monthly-estimated-cost-trend-chart).
   - Ha úgy dönt, hogy **értesítendő** a küszöbértéket, amikor meg kell adnia a webhook URL-CÍMÉT. A költségek Integrációk területen jelölje ki a **Ide kattintva hozzáadhat egyet integrációs**.

      A konfigurálás értesítési panelen adjon meg egy Webhook URL-CÍMÉT, majd **OK**.

       ![Konfigurálja az értesítési ablaktáblát](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Ha megad **értesítendő**, meg kell adnia a webhook URL-CÍMÉT.
      - Hasonlóképpen, ha egy webhook URL-CÍMÉT határozza meg, meg kell adnia **értesítési** való **a** a költségek küszöbérték ablaktáblán.
      - Létre kell hoznia egy webhookot előtt írja be ide.  

      További információ a webhookok: [hozzon létre egy webhook vagy API Azure-függvény](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [A DevTest Labs szolgáltatásban a költségek érdekében két dolgot](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Miért érdemes a költségek küszöbértékei?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>További lépések
Íme néhány az alábbiakkal próbálkozhat a következő:

* [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) – ismerje meg, hogyan állíthatja be annak a szabályozására, hogyan használhatók a labor és annak virtuális gépein használható a különböző házirendeket. 
* [Egyéni lemezkép készítése](devtest-lab-create-template.md) – a virtuális gép létrehozásakor megad egy talál, amely egy egyéni rendszerkép vagy a Piactéri lemezképet. Ez a cikk bemutatja, hogyan hozzon létre egy egyéni rendszerkép VHD-fájlból.
* [Piactér képeinek konfigurálása](devtest-lab-configure-marketplace-images.md) – DevTest Labs támogatja az Azure Marketplace-rendszerképek alapján virtuális gépek létrehozását. Ez a cikk bemutatja, hogyan határozhatja meg, ha van ilyen, az Azure Marketplace-rendszerképek lehet a labor virtuális gépek létrehozásakor használt.
* [Virtuális gép létrehozása tesztkörnyezetben](devtest-lab-add-vm.md) -azt ábrázolja, hogyan hozhat létre egy virtuális Gépet egy alaplemezkép (vagy egyéni vagy a Marketplace-en), és a virtuális gépen összetevők használata.

