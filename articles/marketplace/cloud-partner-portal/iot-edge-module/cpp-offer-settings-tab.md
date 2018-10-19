---
title: Ajánlat beállításai az Azure IoT Edge-modul létrehozása |} A Microsoft Docs
description: Az IoT Edge-modul az ajánlat beállításainak konfigurálása.
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
ms.openlocfilehash: 2a2ec41d7e2f21dc1550cd3c56009204842e1641
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431007"
---
# <a name="iot-edge-module-offer-settings-tab"></a>IoT Edge-modul ajánlat beállítások lap

A **IoT Edge-modulok > Új ajánlat** oldal megnyílik az aktuális az a **ajánlat beállítások** fülre. 

![Új ajánlat lap az IoT Edge-modulok](./media/iot-edge-module-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Az ajánlat nastavení Identity

A **ajánlat identitás**, meg kell adnia a mezők kitöltése a következő táblázat ismerteti. Egy csillag (*) hozzáfűzi a mező nevét jelzi, hogy ez szükséges. 

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Ajánlat azonosítója**       | Az ajánlat egyedi azonosítóját (a közzétevő profil) belül. Ez az azonosító a termék URL-címek és insights jelentésekben látható lesz. Maximális hossza legfeljebb 50 karakter hosszúságú, és a kisbetűs alfanumerikus karaktereket és kötőjeleket (-). (Az azonosító nem végződhet kötőjellel.) **Megjegyzés:** ajánlat élesíti után nem lehet módosítani ezt a mezőt. <br> Például, ha a Contoso tesz közzé egy ajánlatot az ajánlat azonosítója **minta-iot-edge-modul**, az Azure Marketplace-en URL-cím által megadott `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`. |
| **Közzétevő**     | A szervezet egyedi azonosítója az Azure piactéren. Az ajánlatokat társítható a közzétevő-azonosítót. Ez az érték nem lehet módosítani, az ajánlat mentése után. |
| **Name (Név)**          | Az ajánlat megjelenített neve. Ez a név jelenik meg az Azure piactéren, és a Cloud Partner portálra. Legfeljebb 50 karakterből állhat. A termék egy könnyen felismerhető névre márkanév használatát javasoljuk. A szervezet neve ne tartalmazza, kivéve, ha ezt hogyan a forgalmazás. Ha ez az ajánlat egyéb webhelyeken és kiadványok vannak marketing, győződjön meg arról, hogy a név pontosan azonos között az összes olyan. |
|  |  |

Válassza ki **mentése** az ajánlat beállításainak mentése.

## <a name="next-steps"></a>További lépések

Használja a [termékváltozatok](./cpp-skus-tab.md) lap segítségével konfigurálhatja a termékváltozatok az ajánlatban.
