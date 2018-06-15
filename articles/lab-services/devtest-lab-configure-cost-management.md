---
title: Tekintse meg a labor becsült havi költségtrend Azure DevTest Labs szolgáltatásban |} Microsoft Docs
description: További tudnivalók az Azure DevTest Labs havi becsült költség trend diagram.
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
ms.openlocfilehash: 13535dae82ef2c8896dad7d6221553d15e4e6a95
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787632"
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Tekintse meg a labor becsült havi költségtrend Azure DevTest Labs szolgáltatásban
A DevTest Labs költség felügyeleti funkciója segítségével nyomon követheti a labor költségét. Ez a cikk bemutatja, hogyan használja a **becsült havi Költségtrend** diagram megjelenítése az aktuális naptári hónapra az aktuális naptári hónapra becsült költség-date és a várható befejezési hónap költsége. Ez a cikk is bemutatja, hogyan jobb kezelése érdekében úgy, hogy költségkeret célozza meg, és küszöbértékeket, hogy a elérésekor, eseményindító jelentik az eredményeket a DevTest Labs labor költségeket.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>A havi becsült Költségtrend diagram megtekintése
A havi becsült Költségtrend diagram megtekintéséhez kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Ha szükséges, jelölje be **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából. (Előfordulhat, hogy az irányítópult már látható a labor **összes erőforrás**).
1. Válassza ki a kívánt labor labs listájának megtekintéséhez.  
1. A tesztlabor a **áttekintése** területen válassza **konfigurációs és házirendek**.   
1. A bal oldali alatt **költség követési**, jelölje be **költségtrend**.

   Az alábbi képernyőfelvételen költség diagram példáját mutatja be. 
   
    ![A diagram költség](./media/devtest-lab-configure-cost-management/graph.png)

A **becsült költség** érték az aktuális naptári hónapra becsült költség dátumig. A **becsült költség** számítja ki a labor költség előző az öt napig teljes aktuális naptári hónapra vonatkozó becsült költsége van.

A költségek összegeket a következő egész számra kerekíti. Példa: 

* 5.01 kerekít legfeljebb 6 
* 5.50 kerekít legfeljebb 6
* 5.99 kerekít legfeljebb 6

Azt jelzi, a diagram felett, a költségek, alapértelmezés szerint a diagram a látja a rendszer *becsült* használatával költségek [használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/) díjakat kínál. Azt is beállíthatja a saját költségeik célokat, a diagramok által megjelenített [kezelése, a költség célokat a tesztkörnyezethez.](#managing-cost-targets-for-your-lab)

Ezenkívül a következők *nem* költség kiszámításakor:

* CSP és Dreamspark-előfizetés használata jelenleg nem támogatott Azure DevTest Labs használja a [Azure számlázási API-k](../billing/billing-usage-rate-card-overview.md) a költség, amely nem támogatja a kriptográfiai Szolgáltató vagy a Dreamspark-előfizetések labor kiszámításához.
* Az ajánlatok díjaival számolva. Jelenleg nem használható a (lásd az előfizetéshez tartozó), hogy Ön rendelkezik egyeztet Microsoft vagy a Microsoft partnerei ajánlatok díjaival számolva. Csak a használatalapú fizetés díjszabás szolgálnak.
* A adók
* A kedvezményeket
* A Számlázás pénzneme. Jelenleg a labor költség csak USD pénznemben jelenik meg.

## <a name="managing-cost-targets-for-your-lab"></a>A tesztkörnyezet költség célok kezelése
DevTest Labs lehetővé teszi majd megtekintheti a havi becsült Költségtrend diagram költségkeret target beállításával jobb kezelése érdekében a költségeket a tesztkörnyezetben. DevTest Labs szolgáltatásban is küldheti el Önnek értesítést a megadott cél költségeik vagy küszöbérték elérésekor. 

1. A tesztkörnyezet a **áttekintése** ablaktáblán válassza előbb **konfigurációs és házirendek**.
1. A bal oldali alatt **költség követési**, jelölje be **költségtrend**.

    ![Cél gomb kezelése](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. Az a **költségtrend** ablaktáblán válassza előbb **kezelése cél**.

    ![Cél gomb kezelése](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. A kezelés cél panelen adja meg a kívánt költségkeret cél és a küszöbértékeket. Is beállíthatja, hogy minden egyes megadott küszöbértéknél jelentett, a költség trend diagramon vagy webhook értesítést.

    ![Cél ablaktábla kezelése](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Válassza ki a időintervalluma költség célok nyomon követni kívánt időszak.
      - **Havi**: havonta költség célok nyomon követi.
      - **Rögzített**: költség célok követi a kezdő dátum és végső dátummezők megadott dátumtartományra vonatkozóan. Általában ez előfordulhat, hogy megfeleljen mennyi ideig a projekt futásra nem ütemezték.
   - Adjon meg egy **céloz költség**. Előfordulhat például, ez milyen mértékű azt tervezi, hogy egy meghatározott időszakban ebben a tesztkörnyezetben.
   - Válassza ki az engedélyezni vagy letiltani a küszöbérték azt szeretné, akár 125 %-át a megadott – 25 %-os lépésekben – jelentett **céloz költség**.
      - **Értesítés**: Ezzel a küszöbértékkel teljesülésekor értesíti, a webhook URL-CÍMÉT, akkor adja meg.
      - **A diagram megrajzolásához**: Ha ezt a küszöbértéket teljesül, az eredmények ábrázolási megtekintheti, költség trend gráf leírtak [megtekintése a havi becsült Költségtrend diagram](#viewing-the-monthly-estimated-cost-trend-chart).
   - Ha úgy dönt, hogy **értesítendő** amikor teljesül a küszöbértéket, meg kell adnia a webhook URL-CÍMÉT. A költségek Integrációk területen jelölje ki a **integrációs hozzáadásához kattintson ide**.

      A Webhook URL-CÍMÉT adja meg a konfigurálás értesítési ablaktáblát, és válassza ki **OK**.

       ![Értesítési ablaktáblát konfigurálása](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Ha megad **értesítendő**, meg kell adnia a webhook URL-címet.
      - Hasonlóképpen, ha a webhook URL-CÍMÉT határozza meg, meg kell adnia **értesítési** való **a** a költség küszöbérték ablaktáblán.
      - Létre kell hoznia egy webhook előtt írja be ide.  

      További információ a webhookok: [webhook vagy API Azure-függvény létrehozása](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Ennek érdekében a költségek a DevTest Labs szolgáltatásban két dolgot](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Miért költség küszöbértékek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>További lépések
Az alábbiakban ezután próbálja meg a következőkről:

* [Labor házirendeket definiálhat az](devtest-lab-set-lab-policy.md) -megtudhatja, hogyan használja annak a szabályozására, hogyan használhatók a labor és a virtuális gépek különböző házirendek beállítása. 
* [Hozzon létre egyéni lemezkép](devtest-lab-create-template.md) – a virtuális gépek létrehozásakor megad egy talál, amely lehet, vagy egy egyéni lemezképet, vagy egy Piactéri lemezképhez. Ez a cikk bemutatja, hogyan egyéni lemezkép létrehozása a VHD-fájl.
* [Konfigurálja a piactéren elérhető rendszerkép](devtest-lab-configure-marketplace-images.md) - DevTest Labs támogatja az Azure piactéren elérhető rendszerkép alapján virtuális gépek létrehozását. Ez a cikk bemutatja, hogyan határozhatja meg, ha bármely, az Azure piactéren elérhető rendszerkép lehet egy, amikor a virtuális gépek létrehozásakor használt.
* [Hozzon létre egy virtuális Gépet egy tesztkörnyezetben](devtest-lab-add-vm.md) -bemutatja, hogyan hozható létre a virtuális gépek alapjául szolgáló lemezképhez (vagy egyéni vagy Marketplace), és hogyan működnek együtt a virtuális gépen.

