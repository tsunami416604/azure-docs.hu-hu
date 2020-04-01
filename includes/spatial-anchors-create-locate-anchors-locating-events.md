---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "71180053"
---
Miután a figyelő létre, az `AnchorLocated` esemény tűz minden horgony kért. Ez az esemény akkor következik be, amikor egy horgony található, vagy ha a horgony nem található. Ha ez a helyzet bekövetkezik, az ok az állapotban lesz feltüntetve. Miután az összes horgonyok egy figyelő feldolgozása, talált `LocateAnchorsCompleted` vagy nem található, akkor az esemény tüzet. Figyelőnként legfeljebb 35 azonosító lehet. 
