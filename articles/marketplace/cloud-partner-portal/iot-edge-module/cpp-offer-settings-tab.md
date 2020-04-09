---
title: Ajánlatbeállítások egy Azure IoT Edge-modulhoz | Azure Piactér
description: Konfigurálja az IT Edge-modul ajánlatbeállításait.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 7a35277a61217dab2982d6436903cd09abfe8c7d
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984963"
---
# <a name="iot-edge-module-offer-settings-tab"></a>IoT Edge modul ajánlatbeállításai lap

>[!Important]
>2020. április 13-tól megkezdjük az IoT Edge modulajánlatok kezelését a Partner Centerbe. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [IoT Edge-modulajánlat létrehozása](https://aka.ms/AzureCreateIoT) az áttelepített ajánlatok kezeléséhez kövesse az utasításokat.

Megnyílik **az IoT Edge-modulok > új ajánlat** lap, amely az Ajánlat **beállításai** lapon jelenik meg. 

![Új ajánlat lap Az IoT Edge modulokhoz](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>Identitás-beállítások ajánlata

Az **Ajánlat identitása**csoportban meg kell adnia az alábbi táblázatban leírt mezők adatait. A mezőnévhez csatolt csillag (*) azt jelzi, hogy szükség van rá. 

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Ajánlat azonosítója\***       | Az ajánlat egyedi azonosítója (közzétevői profilon belül). Ez az azonosító látható lesz a termék URL-címeiben és elemzési jelentésekben. Legfeljebb 50 karakter hosszú, és kisméretű alfanumerikus karaktereket és kötőjeleket (-) használhat. (Az azonosító nem végződhet kötőjellel.) **Megjegyzés:** Ez a mező nem módosítható, miután az ajánlat éles. <br> Ha például a Contoso közzétesz egy ajánlatot az ajánlatazonosítóminta-iot-edge-modul, `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`hozzá van rendelve az Azure Marketplace **URL-cím.** |
| **Publisher\***     | A szervezet egyedi azonosítója az Azure Marketplace-en. Minden ajánlatának a közzétevő azonosítójához kell kapcsolódnia. Ez az érték nem módosítható az ajánlat mentése után. |
| **név\***          | Az ajánlat megjelenítendő neve. Ez a név jelenik meg az Azure Marketplace-en és a Cloud Partner Portal. Legfeljebb 50 karakterből állhat. Javasoljuk, hogy felismerhető márkanevet használjon a termékéhez. Ne adja meg a szervezet nevét, kivéve, ha a termék forgalomba kerül. Ha ezt az ajánlatot más webhelyeken és kiadványokban forgalmazza, győződjön meg arról, hogy a név pontosan ugyanaz az összes kiadványban. |
|  |  |


Válassza a **Mentés** lehetőséget az ajánlatbeállítások mentéséhez.


## <a name="next-steps"></a>További lépések

Az [SK-k](./cpp-skus-tab.md) lapon konfigurálhatja az ajánlat sk-jeit.
