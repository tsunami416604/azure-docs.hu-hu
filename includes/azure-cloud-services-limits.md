---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 24e327a0b669df247d44e4bf8f05b693abb49990
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224376"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| A [webes vagy feldolgozói szerepkörök telepítése](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Példány bemeneti végpontok](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) üzembe helyezése |25 |25 |
| [Bemeneti végpontok](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) üzembe helyezése |25 |25 |
| [Belső végpontok](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) üzembe helyezése |25 |25 |
| [Üzemeltetett szolgáltatási tanúsítványok](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) üzembe helyezése |199 |199 |

<sup>1</sup> Minden webes vagy feldolgozói szerepkörrel rendelkező Azure Cloud Service két üzemelő példánytal rendelkezhet, amelyek közül az egyik az üzemi, a másik az előkészítés. Ez a korlát a különálló szerepkörök, azaz a konfiguráció számára vonatkozik. Ez a korlát nem hivatkozik a példányok száma alapján, azaz a skálázásra.

