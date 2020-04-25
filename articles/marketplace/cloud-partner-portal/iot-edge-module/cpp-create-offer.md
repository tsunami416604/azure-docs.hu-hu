---
title: Azure IoT Edge modul ajánlatának létrehozása | Azure piactér
description: Új IoT Edge modul közzététele a piactéren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d8f4300f45fc626c104055f987d123301ba8f39d
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148071"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Hozzon létre egy új IoT Edge modul-ajánlatot a Cloud Partner Portal

>[!Important]
>2020. április 13-ától kezdődően megkezdjük a IoT Edge modul-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az [IoT Edge modul létrehozása](https://docs.microsoft.com//azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) című témakör utasításait.

Ez a cikk bemutatja, hogyan hozhat létre és tehet közzé egy IoT Edge modul ajánlati bejegyzést az Azure Marketplace-en. Minden ajánlat saját entitásként jelenik meg az Azure Marketplace-en, és egy vagy több SKU-hoz van társítva.  Az IoT Edge modul ajánlata az eszközök és a támogató szolgáltatások következő csoportjaiból áll:

|  **Eszközcsoport**   |  **Leírás**  |
|  ---------------   |  ---------------  |
|    Termékváltozatok            |  Egy ajánlat legkisebb üzembe helyezhető egysége. Egyetlen ajánlat (Termékkód) több SKU-t is társíthat az ajánlathoz. A SKU-ket a támogatott szolgáltatások és számlázási modellek megkülönböztetésére használhatja. |
|  Piactér       | Marketing, jogi és vezető felügyeleti eszközöket és specifikációkat tartalmaz.  <ul><li> A marketing-eszközök közé tartozik az ajánlat neve, leírása és emblémája</li> <li> A jogi eszközök közé tartozik az adatvédelmi szabályzat, a használati feltételek és egyéb jogi dokumentáció</li>  <li> Az érdeklődői felügyeleti szabályzat segítségével megadhatja, hogyan kezelheti az érdeklődőket az Azure Marketplace végfelhasználói portálon.</li> </ul> |
| Támogatás            | A kapcsolattartási és a házirend-információkat tartalmazza |


## <a name="new-offer-form"></a>Új ajánlat űrlapja 

Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/), majd a bal oldali menüsoron válassza az **+ új ajánlat** lehetőséget. Az új ajánlat menüben válassza ki **IoT Edge modulokat** az **új ajánlat** űrlap megjelenítéséhez, és kezdje el az eszközök definiálásának folyamatát egy új IoT Edge-modulhoz. 

![Az új IoT Edge modul a felhasználói felület kijelölését kínálja](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>További lépések

A IoT Edge modul ajánlatának **új ajánlat** lapja az új ajánlat létrehozásához használni kívánt lapokat és űrlapmezőket tartalmaz. Az alábbi cikkekből megtudhatja, hogyan használhatja a lapot az új IoT Edge modul-ajánlathoz tartozó eszközcsoport és támogató szolgáltatások definiálásához.

- [Ajánlatbeállítási lap](./cpp-offer-settings-tab.md)
- [Termékváltozatok lap](./cpp-skus-tab.md)
- [Marketplace lap](./cpp-marketplace-tab.md)
- [Támogatási lap](./cpp-support-tab.md)
