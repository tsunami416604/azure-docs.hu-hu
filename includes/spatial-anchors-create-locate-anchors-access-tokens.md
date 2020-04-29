---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67178814"
---
### <a name="access-tokens"></a>Hozzáférési jogkivonatok

A hozzáférési jogkivonatok robusztusabb módszert biztosítanak az Azure térbeli horgonyok hitelesítésére. Különösen, amikor előkészíti az alkalmazást az éles üzembe helyezéshez. Ennek a módszernek az összegzése egy olyan háttér-szolgáltatás beállítása, amelyet az ügyfélalkalmazás biztonságosan tud hitelesíteni. A háttérben elérhető HRE és az Azure térbeli szolgáltatása a biztonságos jogkivonat-szolgáltatással hozzáférési tokent igényelhet. Ezt a tokent ezután a rendszer az ügyfélalkalmazás számára továbbítja, és az SDK-ban használja az Azure térbeli Horgonyokkal való hitelesítéshez.
