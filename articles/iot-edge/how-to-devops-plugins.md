---
title: Ci/CD engedélyezése a Jenkins beépülő modullal – Azure IoT Edge | Microsoft dokumentumok
description: Az Azure IoT Edge-bővítmény a Jenkins lehetővé teszi, hogy integrálja az IoT Edge fejlesztési és üzembe helyezési feladatokat a meglévő DevOps-megoldás.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510243"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrálja az Azure IoT Edge-et a Jenkins-folyamatokba

Az Azure IoT Edge beépített támogatást nyújt az Azure DevOps és az Azure DevOps-projektekhez, de egy bővítményt is biztosít a DevOps funkció Jenkinsre való bővítéséhez. [A Jenkins](https://jenkins.io/) egy nyílt forráskódú automatizálási kiszolgáló, amely bővítményeket használ számos fejlesztési és telepítési projekt támogatására, beleértve az IoT Edge-et is.

A Jenkins Azure IoT Edge beépülő modulja a folyamatos integrációra és a folyamatos üzembe helyezésre összpontosít. Létrehozhat egy build- és leküldéses folyamatot, amely modulokat épít, és leküldéses a tárolórendszerképeket a tároló beállításjegyzékbe. Ezután hozzon létre egy kiadási folyamatot, amely modulokat telepít az IoT Edge-eszközökre.

Mielőtt elkezdené használni az IoT Edge-bővítményt a Jenkins számára, szüksége van egy IoT hub az Azure-ban, és egy tároló beállításjegyzék a tárolórendszerképek tárolására. Az Egyszerű Azure-szolgáltatás használatával adjon Jenkins közreműködői engedélyeket az IoT hubhoz, hogy a bővítmény üzembe helyezéseket hozhasson létre az IoT Edge-eszközökhöz.

Ha készen áll az első lépésekre, keresse meg a telepítést, és használja a [Jenkins Azure IoT Edge bővítményének](https://plugins.jenkins.io/azure-iot-edge)részleteit.
