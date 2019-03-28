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
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: f761af3a5a3f08e4da89d8869aea5d666ecd69d0
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517266"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet társított költségek nyomon követése
Ez a cikk nyomon követését a labor költségeinek ismertetése. Azt mutatja be megtekintheti a becsült költségek trent az aktuális hónap a tesztkörnyezethez. A cikk emellett bemutatja, hogyan hónap elejétől számított költség / erőforrás megtekintéséhez a tesztkörnyezetben.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>A havi becsült labor költség trend megtekintése 
Ebben a szakaszban megismerheti, hogyan használható a **havi becsült költség Trend** diagram a jelenlegi naptári hónap becsült költség dátumig és a hónap teljes költségről megtekintése az aktuális hónap. Azt is megtudhatja, hogyan labor költségeinek kezelése érdekében költségkeret célokhoz és küszöbértékekhez tartalmazó beállításával, elérésekor, eseményindító DevTest Labs-jelentik az eredményeket.

A havi becsült költség Trend diagram megtekintéséhez kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Tesztkörnyezetek listájában jelölje ki a labor.  
4. Válassza ki **Konfigurace a zásady** a bal oldali menüben.  
4. Válassza ki **költség trend** a a **költségek nyomon követése** szakaszban a bal oldali menüben. Az alábbi képernyőképen költség diagram egy példát mutat be. 
   
    ![A Cost diagram](./media/devtest-lab-configure-cost-management/graph.png)

    A **becsült költség** értéke a jelenlegi naptári hónap becsült költség dátumig. A **előre jelzett költsége** becsült költsége van a teljes aktuális hónap, a labor költség számítja ki az előző öt nap.

    A költségek összegeket a legközelebbi egész számra kerekíti. Példa: 

   * 5.01 kerekít legfeljebb 6 
   * 5.50 kerekít legfeljebb 6
   * 5.99 kerekít legfeljebb 6

     Meghatározza a diagram felett, a költségek, alapértelmezés szerint a diagram a láthatja azokat *becsült* költségek használatával [használatalapú](https://azure.microsoft.com/offers/ms-azr-0003p/) ajánlja fel díjakat. Is beállíthat, amely szerint a diagramokban jelennek meg a saját költségkeret-beállítási célok [kezelése a költségek célokat a tesztkörnyezethez.](#managing-cost-targets-for-your-lab)

     A következő díjakat *nem* szereplő költség:

   * Kriptográfiai Szolgáltató és a Dreamspark-előfizetések jelenleg nem támogatottak, használja az Azure DevTest Labs a [Azure számlázási API-k](../billing/billing-usage-rate-card-overview.md) a költség, amely nem támogatja a kriptográfiai Szolgáltató vagy a Dreamspark-előfizetések labor kiszámításához.
   * Az ajánlat érvényes. Az ajánlat (látható díját az előfizetéshez tartozó), hogy Ön rendelkezik egyeztetése a Microsoft vagy a Microsoft partnerek jelenleg nem használható. Csak az utólagos elszámolású szolgálnak.
   * Az adókat
   * Slevy
   * A Számlázás pénzneme. Jelenleg a labor költség csak USD pénznemben jelenik meg.

### <a name="managing-cost-targets-for-your-lab"></a>A tesztkörnyezet céljainak költségek kezelése
DevTest Labs lehetővé teszi kontrollja a tesztkörnyezetben, majd megtekintheti a havi becsült költség Trend diagram költségkeret cél beállításával. DevTest Labs segítségével is értesítést küld a célként megadott kiadások vagy a küszöbérték elérésekor. 

1. Az a **költség trend** lapon jelölje be **kezelés cél**.

    ![Cél gomb kezelése](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Az a **kezelés cél** adja meg azokat a költségkeret cél- és a küszöbértékeket. Beállíthatja, hogy egyes kiválasztott küszöbérték készüljön jelentés, a költség trend diagramra vagy webhook értesítést is.

    ![Cél panelen kezelése](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Válassza ki egy adott időszakban, mely során költség célok nyomon követni kívánt.
      - **Havi**: havi költség célok nyomon követi.
      - **Rögzített**: költség célok nyomon követi a kezdő és záró dátuma, a megadott dátumtartományban. Általában ezek az értékek jelölik, mennyi ideig a projekthez való futásra van ütemezve.
   - Adjon meg egy **költség cél**. Például IP-címek fenntartási azt tervezi, hogy a megadott időszakban a jelen labor beváltható.
   - Engedélyezheti vagy tilthatja le semmilyen küszöbérték válassza azt szeretné, akár 125 %-át a megadott, 25 %-os léptékben – jelentett **költség cél**.
      - **Értesítés**: Ha a küszöbértéket, által egy webhook URL-CÍMÉT adja meg, hogy értesítést kap.
      - **Vykreslit v grafu**: Ha ezt a küszöbértéket, az eredmények ábrázolási költség trend gráf is megtekintheti, a havi becsült költség Trend diagram megtekintése leírtak szerint.
   - Ha úgy dönt, hogy **értesítendő** a küszöbértéket, amikor meg kell adnia a webhook URL-CÍMÉT. A költségek Integrációk területen jelölje ki a **Ide kattintva hozzáadhat egyet integrációs**. Adjon meg egy **Webhook URL-cím** a konfigurálás értesítési ablaktáblát, és válassza ki a **OK**.

       ![Konfigurálja az értesítési ablaktáblát](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Ha megad **értesítendő**, meg kell adnia a webhook URL-CÍMÉT.
     - Hasonlóképpen, ha egy webhook URL-CÍMÉT határozza meg, meg kell adnia **értesítési** való **a** a költségek küszöbérték ablaktáblán.
     - Létre kell hoznia egy webhookot előtt írja be ide.  

       További információ a webhookok: [hozzon létre egy webhook vagy API Azure-függvény](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Nézet költségek erőforrás szerint 
A havi költségek trend fejlesztőlaborokban lévő funkcióval láthatja, hogy mennyire van, az aktuális hónap a fordított. Azt is bemutatja a mozaikrétegeknek a költségkeret-beállítási kiadásait az elmúlt hét napban alapján, a hónap végéig. Megtudhatja, miért érdemes a laborban kiadások teljesíti-küszöbértékek korábban használhatja a **költségek erőforrás szerint** funkció, amely a hónap elejétől számított költségeit mutatja **erőforrásonként** egy táblában.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.  
4. Válassza ki **Konfigurace a zásady** a bal oldali menüben.
5. Válassza ki **költségek erőforrás szerint** a a **költségek nyomon követése** szakaszban a bal oldali menüben. Láthatja, hogy az egyes erőforrások társított egy tesztlabor társított költségek. 

    ![Díjak erőforrások szerint](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Ez a funkció révén könnyedén azonosíthatja az erőforrást, amely költségek a legtöbb, hogy műveleteket végezhet a labor-kiadások csökkentése érdekében. Például egy virtuális gép költsége a virtuális gép mérete alapján. Minél nagyobb a virtuális gép, további mérete a költségeket. Megtalálhatja egy virtuális Gépet és a tulajdonosa, így az tudni, miért van szükség az ilyen Virtuálisgép-méretet a virtuális gép tulajdonosa beszélgethet és a egy alkalom, hogy csökkentse a méretét van-e.

[Automatikus leállítási házirend](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown) segítségével állítja le a labor virtuális gépeken, a nap egy adott időpontban a költségek csökkentése érdekében. Azonban a lab-felhasználó is tilthatják le a leállítási szabályzatot, amely növeli a virtuális gép futtatásával járó költségeket. Kiválaszthat egy virtuális Gépet a táblázatban, ha azt rendelkezik lett kilépteti kibővített az automatikus leállítási házirend megtekintéséhez. Ha ez a helyzet, beszélgethet a virtuális gép tulajdonosa, miért érdemes a virtuális gép rendelkezik lett kilépteti kibővített a házirend kereséséhez.
 
## <a name="next-steps"></a>További lépések
Íme néhány az alábbiakkal próbálkozhat a következő:

* [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) – ismerje meg, hogyan állíthatja be annak a szabályozására, hogyan használhatók a labor és annak virtuális gépein használható a különböző házirendeket. 
* [Egyéni lemezkép készítése](devtest-lab-create-template.md) – a virtuális gép létrehozásakor megad egy talál, amely egy egyéni rendszerkép vagy a Piactéri lemezképet. Ez a cikk bemutatja, hogyan hozzon létre egy egyéni rendszerkép VHD-fájlból.
* [Piactér képeinek konfigurálása](devtest-lab-configure-marketplace-images.md) – DevTest Labs támogatja az Azure Marketplace-rendszerképek alapján virtuális gépek létrehozását. Ez a cikk bemutatja, hogyan határozhatja meg, ha van ilyen, az Azure Marketplace-rendszerképek lehet a labor virtuális gépek létrehozásakor használt.
* [Virtuális gép létrehozása tesztkörnyezetben](devtest-lab-add-vm.md) -azt ábrázolja, hogyan hozhat létre egy virtuális Gépet egy alaplemezkép (vagy egyéni vagy a Marketplace-en), és a virtuális gépen összetevők használata.

