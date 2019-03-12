---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: acf5a7e8d7feebaac3d82629c5b561a22112de0f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553593"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| [Webes vagy feldolgozói szerepkörök száma üzemelő példányonként](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Példány bemeneti végpontok](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) száma üzemelő példányonként |25 |25 |
| [Bemeneti végpontok](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) száma üzemelő példányonként |25 |25 |
| [Belső végpont](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) száma üzemelő példányonként |25 |25 |
| [Üzemeltetett szolgáltatási tanúsítványok](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) száma üzemelő példányonként |199 |199 |

<sup>1</sup>minden Azure Cloud Service, webes és feldolgozói szerepkörökhöz lehet két üzembe helyezés, egyet az éles és átmeneti. Ez a korlát számára a különböző szerepkörök, vagyis a configuration vonatkozik. Ezt a korlátot nem hivatkozik szerepkörönként, példányok, skálázás.

