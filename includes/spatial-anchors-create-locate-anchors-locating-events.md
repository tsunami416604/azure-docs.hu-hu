---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "71180053"
---
A figyelő létrehozása után az `AnchorLocated` esemény minden kért horgonyra kigyullad. Ez az esemény akkor következik be, amikor egy horgony található, vagy ha a horgony nem található. Ha ez a helyzet történik, az ok az állapotban szerepel. Ha egy figyelőhöz tartozó összes horgonyt feldolgozták, megtalálták vagy nem találják, akkor az `LocateAnchorsCompleted` esemény tüzet fog eredményezni. A figyelők száma legfeljebb 35 azonosító lehet. 
