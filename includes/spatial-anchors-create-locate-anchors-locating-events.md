---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180053"
---
A figyelő létrehozása után az `AnchorLocated` esemény minden kért horgonyra kigyullad. Ez az esemény akkor következik be, amikor egy horgony található, vagy ha a horgony nem található. Ha ez a helyzet történik, az ok az állapotban szerepel. Ha egy figyelőhöz tartozó összes horgonyt feldolgozták, megtalálták vagy nem `LocateAnchorsCompleted` találják, akkor az esemény tüzet fog eredményezni. A figyelők száma legfeljebb 35 azonosító lehet. 
