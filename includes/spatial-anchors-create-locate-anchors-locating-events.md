---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "71180053"
---
A figyelő létrehozása után az `AnchorLocated` esemény minden kért horgonyra kigyullad. Ez az esemény akkor következik be, amikor egy horgony található, vagy ha a horgony nem található. Ha ez a helyzet történik, az ok az állapotban szerepel. Ha egy figyelőhöz tartozó összes horgonyt feldolgozták, megtalálták vagy nem `LocateAnchorsCompleted` találják, akkor az esemény tüzet fog eredményezni. A figyelők száma legfeljebb 35 azonosító lehet. 
