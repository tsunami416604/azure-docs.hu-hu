---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2b6ba1dcddd435c42ad864b8e87175d0e98c9b3a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279686"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| [Webes/feldolgozói szerepkörök száma üzemelő példányonként](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Bemeneti végpontok példány](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) száma üzemelő példányonként |25 |25 |
| [Bemeneti végpontok](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) száma üzemelő példányonként |25 |25 |
| [Belső végpont](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) száma üzemelő példányonként |25 |25 |

<sup>1</sup>minden felhőalapú szolgáltatás webes/feldolgozói szerepkörökkel rendelkezhetnek két üzembe helyezés, egyet az éles és átmeneti. Azt is vegye figyelembe, hogy ezt a korlátot hivatkozik a eltérő szerepkörök (konfiguráció) és nem kiszolgálónként (skálázás) szerepkör példányainak számát.

