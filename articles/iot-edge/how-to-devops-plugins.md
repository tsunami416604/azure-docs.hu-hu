---
title: CI/CD engedélyezése a Jenkins beépülő modullal – Azure IoT Edge | Microsoft Docs
description: A Jenkins Azure IoT Edge-bővítménye lehetővé teszi IoT Edge fejlesztési és üzembe helyezési feladatok integrálását a meglévő DevOps-megoldásba.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76510243"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Azure IoT Edge integrálása Jenkins-folyamatokkal

Azure IoT Edge beépített támogatást nyújt az Azure DevOps és a Azure DevOps Projectshoz, de egy beépülő modult is biztosít a DevOps funkciók kibővítéséhez a Jenkins-ben. A [Jenkins](https://jenkins.io/) egy olyan nyílt forráskódú Automation-kiszolgáló, amely beépülő modulok használatával számos fejlesztési és üzembe helyezési projektet támogat, többek között a IoT Edge.

A Jenkins-hez készült Azure IoT Edge beépülő modul a folyamatos integrációt és a folyamatos üzembe helyezést is középpontba helyezi. Létrehozhat egy olyan létrehozási és leküldéses folyamatot, amely modulokat készít, és leküldi a tárolói lemezképeket a tároló-beállításjegyzékbe. Ezután hozzon létre egy kiadási folyamatot, amely modulokat telepít a IoT Edge-eszközökre.

Mielőtt elkezdi használni a Jenkins-hez készült IoT Edge beépülő modult, szüksége lesz egy IoT hub-ra az Azure-ban és egy tároló-beállításjegyzéket a tárolói lemezképek tárolásához. Az Azure egyszerű szolgáltatásával biztosíthatja a Jenkins közreműködői engedélyeit az IoT hub számára, hogy a beépülő modul létrehozza a IoT Edge-eszközök üzembe helyezését.

Ha készen áll a kezdésre, keresse meg a következőt: a [Azure IoT Edge beépülő moduljának](https://plugins.jenkins.io/azure-iot-edge)telepítési és használati adatai.
