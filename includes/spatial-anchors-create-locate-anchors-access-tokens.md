---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110588"
---
### <a name="access-tokens"></a>Hozzáférési jogkivonatok

Hozzáférési jogkivonatok olyan egy robusztusabb módszert az Azure térbeli horgonyok. Különösen, akkor készítse elő az alkalmazás éles környezet. Ez a megközelítés összegzését, hogy állítsa be a háttér-szolgáltatás, amely az ügyfélalkalmazásban biztonságosan hitelesítést. A háttérszolgáltatás felületek és az Azure térbeli horgonyok biztonságos jogkivonat-szolgáltatás-hozzáférési jogkivonat kérése futásidőben AAD-ben. Ez a token majd kézbesíti az ügyfélalkalmazásnak és az SDK-ban térbeli horgonyok Azure-hitelesítésre használható.
