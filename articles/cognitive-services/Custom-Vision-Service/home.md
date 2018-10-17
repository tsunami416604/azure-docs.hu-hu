---
title: Mi a Custom Vision Service?
titlesuffix: Azure Cognitive Services
description: A Custom Vision Service egyéni kép osztályozók készítését teszi lehetővé az Azure-felhőben.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 1d3d1a82cf59b06625487fb241a63f51352e18e5
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365859"
---
# <a name="what-is-the-custom-vision-service"></a>Mi a Custom Vision Service?

A Custom Vision Service egy Azure Cognitive-Services szolgáltatás, amellyel egyéni képosztályozókat hozhat létre. Jelentősen megkönnyíti és felgyorsítja a képosztályozók létrehozását, üzembe helyezését és továbbfejlesztését. A Custom Vision Service REST API-t és egy webes felületet biztosít, ahol képek tölthetők fel, és ahol be lehet tanítani a képosztályozókat.

## <a name="what-does-custom-vision-service-do-well"></a>Mire alkalmas a Custom Vision Service?

A Custom Vision Service akkor működik a legjobban, ha az osztályozni próbált elem kiemelt helyen szerepel a képen. 

Egy osztályozó vagy érzékelő létrehozásához képekre van szükség. Osztályonként 50 kép elegendő a prototípus elindításához. A Custom Vision Service által használt módszerek a jelentős különbségeket érzékelik, így már kevés adattal is neki lehet állni a prototípus készítésének. Ez ugyanakkor azt is jelenti, hogy a Custom Vision Service nem megfelelő olyan esetekben, amelyekben apró különbségeket kell azonosítani, például kisebb repedéseket és horpadásokat minőségbiztosítási célból.

## <a name="next-steps"></a>További lépések

[Osztályozó létrehozása](getting-started-build-a-classifier.md)
