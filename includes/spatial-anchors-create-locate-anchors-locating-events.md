---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 5360cbb7bdfbdc59e87366e73a891b5c2583672e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993103"
---
A figyelő létrehozása után az `AnchorLocated` esemény minden kért horgonyra kigyullad. Ez az esemény akkor következik be, amikor egy horgony található, vagy ha a horgony nem található. Ha ez a helyzet történik, az ok az állapotban szerepel. Ha egy figyelőhöz tartozó összes horgonyt feldolgozták, megtalálták vagy nem találják, akkor az `LocateAnchorsCompleted` esemény tüzet fog eredményezni. A figyelők száma legfeljebb 35 azonosító lehet. 
