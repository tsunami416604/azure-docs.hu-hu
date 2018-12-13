---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: eb1fe7f83ed83efe078be0aadf26cc7db6f498e2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52886273"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| [Webes/feldolgozói szerepkörök száma üzemelő példányonként](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Bemeneti végpontok példány](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) száma üzemelő példányonként |25 |25 |
| [Bemeneti végpontok](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) száma üzemelő példányonként |25 |25 |
| [Belső végpont](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) száma üzemelő példányonként |25 |25 |
| [Üzemeltetett szolgáltatási tanúsítványok](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) száma üzemelő példányonként |199 |199 |

<sup>1</sup>minden felhőalapú szolgáltatás webes/feldolgozói szerepkörökkel rendelkezhetnek két üzembe helyezés, egyet az éles és átmeneti. Azt is vegye figyelembe, hogy ezt a korlátot hivatkozik a eltérő szerepkörök (konfiguráció) és nem kiszolgálónként (skálázás) szerepkör példányainak számát.

