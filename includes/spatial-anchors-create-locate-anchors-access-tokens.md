---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "67178814"
---
### <a name="access-tokens"></a>Hozzáférési jogkivonatok

Az Access Tokens egy robusztusabb módszer az Azure Spatial Anchors használatával történő hitelesítéshez. Különösen akkor, ha előkészíti az alkalmazást egy éles környezetben. Ennek a megközelítésnek az összefoglalása egy olyan háttérszolgáltatás beállítása, amelyet az ügyfélalkalmazás biztonságosan hitelesíthet. A háttérszolgáltatás futóidő alatt az AAD-vel és az Azure Spatial Anchors biztonságos jogkivonat-szolgáltatással csatlakozik egy hozzáférési jogkivonat kéréséhez. Ezt a jogkivonatot ezután kézbesíti az ügyfélalkalmazás, és az SDK-ban az Azure Spatial Anchors hitelesítéséhez.
