---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "67178814"
---
### <a name="access-tokens"></a>Hozzáférési jogkivonatok

A hozzáférési jogkivonatok robusztusabb módszert biztosítanak az Azure térbeli horgonyok hitelesítésére. Különösen, amikor előkészíti az alkalmazást az éles üzembe helyezéshez. Ennek a módszernek az összegzése egy olyan háttér-szolgáltatás beállítása, amelyet az ügyfélalkalmazás biztonságosan tud hitelesíteni. A háttérben elérhető HRE és az Azure térbeli szolgáltatása a biztonságos jogkivonat-szolgáltatással hozzáférési tokent igényelhet. Ezt a tokent ezután a rendszer az ügyfélalkalmazás számára továbbítja, és az SDK-ban használja az Azure térbeli Horgonyokkal való hitelesítéshez.
