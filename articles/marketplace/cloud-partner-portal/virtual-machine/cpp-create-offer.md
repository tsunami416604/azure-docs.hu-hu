---
title: Virtuálisgép-ajánlat létrehozása az Azure Piactéren
description: Felsorolja az okat az Azure Marketplace-en egy új virtuális gép (VM) ajánlat létrehozásához szükséges lépéseket.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 9d06809df2774224b61fd3fb643ab628dd2890f6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273987"
---
# <a name="create-virtual-machine-offer"></a>Virtuálisgép-ajánlat létrehozása

> [!IMPORTANT]
> 2020. április 13-tól megkezdjük az Azure virtuálisgép-ajánlatok kezelését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [Azure virtuális gép ajánlatlétrehozása](https://aka.ms/CreateAzureVMoffer) című, az áttelepített ajánlatok kezelésével című útmutatóutasításait.

Ez a szakasz felsorolja az okat a lépéseket, amelyek az Azure Piactér új virtuálisgép-ajánlatkérés létrehozásához szükségesek.  Minden ajánlat saját entitásként jelenik meg az Azure Marketplace-en, és egy vagy több sk-hez van társítva.  A virtuális gép ajánlata a következő eszközcsoportokból és támogató szolgáltatásokból áll: 

![Eszközök a virtuális gép ajánlatához](./media/publishvm_002.png)

ahol:

|  **Eszközcsoport**   |  **Leírás**  |
|  ---------------   |  ---------------  |
|    Termékváltozatok            |  Az ajánlat legkisebb megvásárolható egysége. Egyetlen ajánlat (termékosztály) több termékkészletet is társíthat, hogy különbséget tegyen a támogatott funkciók, a virtuális gép lemezképtípusai és a számlázási modellek között. |
|  Piactér       | Marketing, jogi és vezető menedzsment eszközöket és specifikációkat tartalmaz.  <ul><li> A marketingeszközök közé tartozik az ajánlat neve, leírása és emblémája</li> <li> A jogi eszközök közé tartozik az adatvédelmi szabályzat, a használati feltételek és egyéb jogi dokumentáció</li>  <li> Az érdeklődőkezelési szabályzat lehetővé teszi, hogy adja meg, hogyan kezelje az érdeklődőket az Azure Marketplace végfelhasználói portálról.</li> </ul> |
| Támogatás            | Támogatási kapcsolattartóés házirend-információkat tartalmaz |
| Tesztverzió         | Olyan eszközöket határoz meg, amelyek lehetővé teszik a végfelhasználók számára, hogy teszteljék az ajánlatot, mielőtt megvásárolnák |
|  |  |


## <a name="new-offer-form"></a>Új ajánlat űrlap

Miután bejelentkezett a [Cloud Partner Portal-ba,](https://cloudpartner.azure.com/)kattintson a bal oldali menüsor **+ Új ajánlat** elemére. Az eredményül kapott menüben kattintson a **Virtuális gépek** az **Új ajánlat** űrlap megjelenítéséhez, és indítsa el az új virtuális gép ajánlateszközeinek meghatározását. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Az új virtuális gép felajánlja a felhasználói felület kiválasztását](./media/publishvm_003.png)

> [!WARNING]
> Ha a **Virtuális gépek** beállítás nem jelenik meg, vagy nincs engedélyezve, akkor a fiók nem rendelkezik engedéllyel az ajánlattípus létrehozásához.  Ellenőrizze, hogy megfelelt-e az ajánlattípus összes [előfeltételének,](./cpp-prerequisites.md) beleértve a fejlesztői fiók regisztrálását is.


## <a name="next-steps"></a>További lépések

Az ebben a szakaszban található további témakörök az **Új ajánlat** lap lapjait tükrözik (virtuális gépajánlat-típus esetén).  Minden cikk ismerteti, hogyan használhatja a társított lapon az eszközcsoportok és támogató szolgáltatások az új virtuális gép ajánlat.

- [Ajánlatbeállítási lap](./cpp-offer-settings-tab.md)
- [Termékváltozatok lap](./cpp-skus-tab.md)
- [Tesztverzió lap](./cpp-test-drive-tab.md)
- [Marketplace lap](./cpp-marketplace-tab.md)
- [Támogatási lap](./cpp-support-tab.md)
