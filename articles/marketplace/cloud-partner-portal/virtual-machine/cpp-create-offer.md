---
title: Virtuálisgép-ajánlat létrehozása az Azure Marketplace-en
description: Az új virtuálisgép-(VM-) ajánlat Azure Marketplace-hez való létrehozásához szükséges lépéseket sorolja fel.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 1e10bd22b91b51fb811601e49fad06d8f8b30127
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817101"
---
# <a name="create-virtual-machine-offer"></a>Virtuálisgép-ajánlat létrehozása

Ez a szakasz azokat a lépéseket sorolja fel, amelyek szükségesek az új virtuálisgép-ajánlati kérelem létrehozásához az Azure Marketplace-en.  Minden ajánlat saját entitásként jelenik meg az Azure Marketplace-en, és egy vagy több SKU-hoz van társítva.  A virtuálisgép-ajánlat a következő, az eszközök és a támogató szolgáltatások csoportjaiból áll: 

![VIRTUÁLIS gépekre vonatkozó ajánlat eszközei](./media/publishvm_002.png)

ahol

|  **Eszközcsoport**   |  **Leírás**  |
|  ---------------   |  ---------------  |
|    SKUs            |  Egy ajánlat legkisebb megvásárolható egysége. Egyetlen ajánlatnak (termékkategória) több SKU-t is hozzá lehet rendelni, hogy megkülönböztethető legyen a támogatott funkciók, a virtuálisgép-rendszerképek típusai és a számlázási modellek. |
|  Piactér       | Marketing, jogi és vezető felügyeleti eszközöket és specifikációkat tartalmaz.  <ul><li> A marketing-eszközök közé tartozik az ajánlat neve, leírása és emblémája</li> <li> A jogi eszközök közé tartozik az adatvédelmi szabályzat, a használati feltételek és egyéb jogi dokumentáció</li>  <li> Az érdeklődői felügyeleti szabályzat segítségével megadhatja, hogyan kezelheti az érdeklődőket az Azure Marketplace végfelhasználói portálon.</li> </ul> |
| Támogatás            | A kapcsolattartási és a házirend-információkat tartalmazza |
| Tesztverzió         | Azokat az eszközöket határozza meg, amelyek lehetővé teszik a végfelhasználók számára az ajánlat tesztelését a vásárlás előtt |
|  |  |


## <a name="new-offer-form"></a>Új ajánlat űrlapja

Miután bejelentkezett a [Cloud Partner Portalba](https://cloudpartner.azure.com/), kattintson az **+ új ajánlat** elemre a bal oldali menüsorban. Az eredményül kapott menüben kattintson a **Virtual Machines** elemre az **új ajánlat** űrlapjának megjelenítéséhez, és az új virtuálisgép-ajánlathoz tartozó eszközök definiálási folyamatának megkezdéséhez. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Új virtuális gép ajánlat felhasználói felület kiválasztása](./media/publishvm_003.png)

> [!WARNING]
> Ha a **Virtual Machines** lehetőség nem látható vagy nincs engedélyezve, akkor a fióknak nincs engedélye az ajánlat típusának létrehozásához.  Ellenőrizze, hogy teljesítette-e az ajánlat típusának összes [előfeltételét](./cpp-prerequisites.md) , beleértve a fejlesztői fiók regisztrálását is.


## <a name="next-steps"></a>További lépések

Az ebben a szakaszban szereplő témakörök az **új ajánlat** oldalának lapfüleit tükrözik (virtuálisgép-ajánlat típusa esetén).  Minden cikk azt ismerteti, hogyan használható a társított lap az új virtuálisgép-ajánlathoz tartozó eszközcsoport-csoportok és támogató szolgáltatások definiálásához.

- [Ajánlatbeállítási lap](./cpp-offer-settings-tab.md)
- [Termékváltozatok lap](./cpp-skus-tab.md)
- [Tesztverzió lap](./cpp-test-drive-tab.md)
- [Marketplace lap](./cpp-marketplace-tab.md)
- [Támogatási lap](./cpp-support-tab.md)
