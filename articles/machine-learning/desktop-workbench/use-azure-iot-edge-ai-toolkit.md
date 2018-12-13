---
title: Az AI-eszközkészlet használata az Azure IoT Edge |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure IoT Edge AI-eszközkészlet használatával.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 9c4d1be552005419ba2e9758a37e02f15fd20155
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268478"
---
# <a name="use-the-ai-toolkit-for-azure-iot-edge"></a>Az Azure IoT Edge AI-eszközkészlet használata

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Ruházhatja fel mesterséges intelligencia, a machine learning és a bővített analitika az Edge-ben. Az Azure IoT Edge AI eszközkészlet üzembe helyezheti mesterséges Intelligencia és gépi tanulási, ahol az adattárolás helyén. A modell üzembe helyezése, Docker-tároló, egy REST API-val, és majd továbbítsa a modell az edge-eszköz, az Azure IoT Hub szolgáltatással. AI-eszközkészlet az Azure IoT Edge számos lehetőséget kínál, intelligencia, rugalmas számítási és a felhő hatékonyságát az időszakos, korlátozott helyek vagy elérhetetlen.

Az Azure IoT Edge AI-eszközkészlet olyan parancsfájlok, a kód és a tárolók üzembe helyezhető gyűjteménye. Ilyenek például a prediktív karbantartás, képbesorolás, és a beszédfeldolgozáshoz, valamint egyéni modell-üzembehelyezés az Azure Machine Learning és az Azure IoT Hub segítségével. Az eszközkészlet részét képező modellek használható – van, de összes forráskódját, és adatok érhetők el, amellyel a fejlesztők testre szabhatja saját igényeik szerint.

Az AI-eszközkészlet az Azure IoT Edge nyilvános GitHub-adattárban található [aka.ms/AI-eszközkészlet](https://aka.ms/AI-toolkit).