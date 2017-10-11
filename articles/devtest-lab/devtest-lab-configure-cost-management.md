---
title: "Tekintse meg a labor becsült havi költségtrend Azure DevTest Labs szolgáltatásban |} Microsoft Docs"
description: "További tudnivalók az Azure DevTest Labs havi becsült költség trend diagram."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.openlocfilehash: b3ad1ead522908d4b41b7cca98d20ac91664998e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Tekintse meg a labor becsült havi költségtrend Azure DevTest Labs szolgáltatásban
A DevTest Labs költség felügyeleti funkciója segítségével nyomon követheti a labor költségét. Ez a cikk bemutatja, hogyan használja a **becsült havi Költségtrend** diagram megjelenítése az aktuális naptári hónapra az aktuális naptári hónapra becsült költség-date és a várható befejezési hónap költsége. Ebből a cikkből megismerheti, hogyan a havi becsült költség trend diagram megtekintéséhez az Azure portálon.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>A havi becsült Költségtrend diagram megtekintése
A havi becsült Költségtrend diagram megtekintéséhez kövesse az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **több szolgáltatások**, majd válassza ki **DevTest Labs** a listából.
3. Válassza ki a kívánt labor labs listájának megtekintéséhez.   
4. A labor paneljén válassza **beállítások költség**.
5. A tesztlabor a **beállítások költség** panelen válassza **Lab költségtrend**.
6. Az alábbi képernyőfelvételen költség diagram példáját mutatja be. 
   
    ![A diagram költség](./media/devtest-lab-configure-cost-management/graph.png)

A **becsült költség** érték az aktuális naptári hónapra becsült költség dátumig. A **becsült költség** számítja ki a labor költség előző az öt napig teljes aktuális naptári hónapra vonatkozó becsült költsége van.

A költségek összegeket a következő egész számra kerekíti. Példa: 

* 5.01 kerekít legfeljebb 6 
* 5.50 kerekít legfeljebb 6
* 5.99 kerekít legfeljebb 6

Azt jelzi, a diagram felett, a költségek a diagramon látható során a rendszer *becsült* használatával költségek [használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/) díjakat kínál.
Ezenkívül a következők *nem* költség kiszámításakor:

* CSP és Dreamspark-előfizetés használata jelenleg nem támogatott Azure DevTest Labs használja a [Azure számlázási API-k](../billing/billing-usage-rate-card-overview.md) a költség, amely nem támogatja a kriptográfiai Szolgáltató vagy a Dreamspark-előfizetések labor kiszámításához.
* Az ajánlatok díjaival számolva. A Microsoft jelenleg nem használhatják a (lásd az előfizetéshez tartozó), hogy Ön rendelkezik egyeztet Microsoft vagy a Microsoft partnerei ajánlatok díjaival számolva. Használatalapú fizetés díjszabás használjuk.
* A adók
* A kedvezményeket
* A Számlázás pénzneme. Jelenleg a labor költség csak USD pénznemben jelenik meg.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Ennek érdekében a költségek a DevTest Labs szolgáltatásban két dolgot](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Miért költség küszöbértékek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Következő lépések
Az alábbiakban ezután próbálja meg a következőkről:

* [Labor házirendeket definiálhat az](devtest-lab-set-lab-policy.md) -megtudhatja, hogyan használja annak a szabályozására, hogyan használhatók a labor és a virtuális gépek különböző házirendek beállítása. 
* [Hozzon létre egyéni lemezkép](devtest-lab-create-template.md) – a virtuális gépek létrehozásakor megad egy talál, amely lehet, vagy egy egyéni lemezképet, vagy egy Piactéri lemezképhez. Ez a cikk bemutatja, hogyan egyéni lemezkép létrehozása a VHD-fájl.
* [Konfigurálja a piactéren elérhető rendszerkép](devtest-lab-configure-marketplace-images.md) - DevTest Labs támogatja az Azure piactéren elérhető rendszerkép alapján virtuális gépek létrehozását. Ez a cikk bemutatja, hogyan határozhatja meg, ha bármely, az Azure piactéren elérhető rendszerkép lehet egy, amikor a virtuális gépek létrehozásakor használt.
* [Hozzon létre egy virtuális Gépet egy tesztkörnyezetben](devtest-lab-add-vm-with-artifacts.md) -bemutatja, hogyan hozható létre a virtuális gépek alapjául szolgáló lemezképhez (vagy egyéni vagy Marketplace), és hogyan működnek együtt a virtuális gépen.

