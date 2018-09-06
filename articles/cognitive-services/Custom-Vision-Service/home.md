---
title: A Custom Vision Service gépi tanulás áttekintése – Azure Cognitive Services | Microsoft Docs
description: A Custom Vision Service egy Microsoft Cognitive Services-szolgáltatás, amellyel egyéni képosztályozókat hozhat létre az Azure platformon.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: d2daf7c211f9474f5636b6af69c5b700d597aa14
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43285244"
---
# <a name="what-is-the-custom-vision-service"></a>Mi a Custom Vision Service?

A Custom Vision Service egy Microsoft Cognitive-szolgáltatás, amellyel egyéni képosztályozókat hozhat létre. Jelentősen megkönnyíti és felgyorsítja a képosztályozók létrehozását, üzembe helyezését és továbbfejlesztését. A Custom Vision Service REST API-t és egy webes felületet biztosít, ahol képek tölthetők fel, és ahol be lehet tanítani a képosztályozókat.

## <a name="what-does-custom-vision-service-do-well"></a>Mire alkalmas a Custom Vision Service?

A Custom Vision Service akkor működik a legjobban, ha az osztályozni próbált elem kiemelt helyen szerepel a képen. 

Egy osztályozó vagy érzékelő létrehozásához képekre van szükség. Osztályonként 50 kép elegendő a prototípus elindításához. A Custom Vision Service által használt módszerek a jelentős különbségeket érzékelik, így már kevés adattal is neki lehet állni a prototípus készítésének. Ez ugyanakkor azt is jelenti, hogy a Custom Vision Service nem megfelelő olyan esetekben, amelyekben apró különbségeket kell azonosítani, például kisebb repedéseket és horpadásokat minőségbiztosítási célból.

## <a name="next-steps"></a>További lépések

[Osztályozó létrehozása](getting-started-build-a-classifier.md)
