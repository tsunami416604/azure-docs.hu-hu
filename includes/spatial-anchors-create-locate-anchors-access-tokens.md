---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: c9b4e0fdf95c2bb4888a3a11820f1785d8545bd4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993077"
---
### <a name="access-tokens"></a>Hozzáférési jogkivonatok

A hozzáférési jogkivonatok robusztusabb módszert biztosítanak az Azure térbeli horgonyok hitelesítésére. Különösen, amikor előkészíti az alkalmazást az éles üzembe helyezéshez. Ennek a módszernek az összegzése egy olyan háttér-szolgáltatás beállítása, amelyet az ügyfélalkalmazás biztonságosan tud hitelesíteni. A háttérben elérhető HRE és az Azure térbeli szolgáltatása a biztonságos jogkivonat-szolgáltatással hozzáférési tokent igényelhet. Ezt a tokent ezután a rendszer az ügyfélalkalmazás számára továbbítja, és az SDK-ban használja az Azure térbeli Horgonyokkal való hitelesítéshez.
