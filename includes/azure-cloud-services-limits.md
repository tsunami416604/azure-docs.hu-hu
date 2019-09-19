---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "67179493"
---
| Resource | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| [Webes vagy feldolgozói szerepkörök üzembe helyezése](../articles/cloud-services/cloud-services-choose-me.md) <sup>1</sup> |25 |25 |
| [Példány bemeneti végpontok](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) üzembe helyezése |25 |25 |
| [Bemeneti végpontok](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) üzembe helyezése |25 |25 |
| [Belső végpontok](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) üzembe helyezése |25 |25 |
| [Üzemeltetett szolgáltatási tanúsítványok](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) üzembe helyezése |199 |199 |

<sup>1</sup> Minden webes vagy feldolgozói szerepkörrel rendelkező Azure Cloud Service két üzemelő példánytal rendelkezhet, amelyek közül az egyik az üzemi, a másik az előkészítés. Ez a korlát a különálló szerepkörök, azaz a konfiguráció számára vonatkozik. Ez a korlát nem hivatkozik a példányok száma alapján, azaz a skálázásra.

