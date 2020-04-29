---
title: Az Azure IoT Edge modul beállításainak megadása | Azure piactér
description: Adja meg az ajánlat beállításait egy IoT Edge modulhoz.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d6d2306dfd44bad6e1f903c3cef3eeb4993f4cd9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148037"
---
# <a name="iot-edge-module-offer-settings-tab"></a>IoT Edge modul ajánlat beállításai lap

>[!Important]
>2020. április 13-ától kezdődően megkezdjük a IoT Edge modul-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az [IoT Edge modul létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) című témakör utasításait.

Megnyílik az **IoT Edge-modulok > új ajánlat** oldal, amely a fókuszt az **ajánlat beállításai** lapon jelenik meg.

![IoT Edge-modulok új ajánlatának lapja](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>Az ajánlat identitásának beállításai

Az **ajánlat identitása**területen meg kell adnia az alábbi táblázatban ismertetett mezőkre vonatkozó információkat. A mezőhöz hozzáfűzni kívánt csillag (*) azt jelzi, hogy a név megadása kötelező. 

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Ajánlat azonosítója\***       | Az ajánlathoz tartozó egyedi azonosító (közzétevői profilon belül). Ez az azonosító a termék URL-címeiben és az információkat tartalmazó jelentésekben jelenik meg. Legfeljebb 50 karakterből állhat, és használhat kisbetűket alfanumerikus karaktereket és kötőjeleket (-). (Az azonosító nem végződhet kötőjeltel.) **Megjegyzés:** Ez a mező nem módosítható, miután egy ajánlat élőben üzemel. <br> Ha például a contoso a **IOT-Edge-Module**azonosítójú ajánlatot tesz közzé, az Azure Marketplace URL-címét `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`rendeli hozzá. |
| **Publisher\***     | A szervezet egyedi azonosítója az Azure piactéren. Az összes ajánlathoz társítva kell lennie a közzétevő AZONOSÍTÓjának. Ez az érték nem módosítható az ajánlat mentése után. |
| **Name (Név)\***          | Az ajánlat megjelenítendő neve. Ez a név jelenik meg az Azure Marketplace-en és a Cloud Partner Portal. Legfeljebb 50 karakterből állhat. Javasoljuk, hogy a termék felismerhető márkáját használja. Ne adja meg a szervezet nevét, kivéve, ha ez a termék forgalmazása. Ha más webhelyeken és kiadványokban is ezt az ajánlatot látja, ügyeljen arra, hogy a név pontosan ugyanaz legyen az összes kiadványban. |
|  |  |


Válassza a **Mentés** lehetőséget az ajánlat beállításainak mentéséhez.


## <a name="next-steps"></a>További lépések

Az [SKUs](./cpp-skus-tab.md) lapon konfigurálhatja az ajánlathoz tartozó SKU-ket.
