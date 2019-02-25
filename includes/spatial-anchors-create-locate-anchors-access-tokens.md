---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 0e4c0b6d622fe0d8d20692ec6c0c889779e4601a
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753036"
---
### <a name="access-tokens"></a>Hozzáférési jogkivonatok

Hozzáférési jogkivonatok olyan egy robusztusabb módszert az Azure térbeli horgonyok. Különösen, akkor készítse elő az alkalmazás éles környezet. Ez a megközelítés összegzését, hogy állítsa be a háttér-szolgáltatás, amely az ügyfélalkalmazásban biztonságosan hitelesítést. A háttérszolgáltatás adapterek futásidőben AAD-ben és a egy hozzáférési jogkivonat kérése az Azure térbeli horgonyok STS szolgáltatással. Ez a token majd kézbesíti az ügyfélalkalmazásnak és az SDK-ban térbeli horgonyok Azure-hitelesítésre használható.
