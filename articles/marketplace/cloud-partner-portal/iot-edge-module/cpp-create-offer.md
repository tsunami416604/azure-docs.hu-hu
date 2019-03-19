---
title: Hozzon létre egy Azure IoT Edge-modul ajánlatot |} A Microsoft Docs
description: Hogyan teheti közzé egy új IoT Edge-modul a Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 69ee0c0add2895b30a60db577cc11c83a0e00bb8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57854772"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Hozzon létre egy új IoT Edge-modul ajánlatunk a Cloud Partner portálra

Ez a cikk azt ismerteti, hogyan hozhat létre, és a egy IoT Edge-modul ajánlat bejegyzés közzététele az Azure Marketplace-en. Minden ajánlat jelenik meg a saját entitás az Azure Marketplace-en, és a egy vagy több termékváltozatok társított.  Az IoT Edge-modul ajánlat az eszközök és a kiegészítő szolgáltatások a következő csoportosítás tevődik össze:

|  **Eszközcsoport**   |  **Leírás**  |
|  ---------------   |  ---------------  |
|    Termékváltozatok            |  A legkisebb telepíthető egység az ajánlat. Egyetlen ajánlatba (termék osztály) az ajánlat társított több termékváltozatok is rendelkezhet. Termékváltozatok segítségével tesz különbséget a támogatott szolgáltatások és a számlázási modellek között. |
|  Piactér       | Marketing, jogi és elégtelen felügyeleti eszközök és előírásokat tartalmazza.  <ul><li> Marketing eszközök tartalmazzák az ajánlat nevét, leírását és emblémák</li> <li> Jogi eszközök közé tartozik egy adatvédelmi szabályzatát, használati feltételeit és egyéb jogi dokumentáció</li>  <li> Elégtelen felügyeleti házirend lehetővé teszi, hogy adja meg, hogyan kezelje az Azure Marketplace-en a végfelhasználói portálon vezet.</li> </ul> |
| Támogatás            | Támogatási kapcsolattartó és házirend információkat tartalmaz |


## <a name="new-offer-form"></a>Új ajánlat űrlap 

Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/), majd válassza ki **+ új ajánlat** a bal oldali menüsáv a. Válassza ki az új ajánlat menü **IoT Edge-modulok** megjelenítéséhez a **új ajánlat** alkotnak, és indítsa el az eszközöket egy új ioT Edge-modul ajánlat meghatározása. 

![Új IoT Edge-modul kínál a felhasználói felület kiválasztása](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>További lépések

A **új ajánlat** oldala az IoT Edge-modul ajánlattípusra lapokat és az űrlap mezőit, amelyek segítségével hozzon létre egy új ajánlat készletét nyújtja. Az alábbi cikkekben egyes azt ismerteti, hogyan adható meg eszközcsoportok és az új IoT Edge-modul ajánlatban támogató szolgáltatások lapja segítségével.

- [Ajánlatbeállítási lap](./cpp-offer-settings-tab.md)
- [Termékváltozatok lap](./cpp-skus-tab.md)
- [Marketplace lap](./cpp-marketplace-tab.md)
- [Támogatási lap](./cpp-support-tab.md)
