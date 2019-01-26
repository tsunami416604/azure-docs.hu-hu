---
title: Engedélyezze a CI/CD a Jenkins beépülő modul használatával – az Azure IoT Edge |} A Microsoft Docs
description: Jenkins az Azure IoT Edge bővítmény lehetővé teszi az IoT Edge devlopment és üzembe helyezési feladatokat integrálható a meglévő fejlesztési és üzemeltetési megoldást.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 173e6ff91acd2ad28d7203b2b5db65e0ee0ecc43
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910829"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Az Azure IoT Edge integrálása a Jenkins-folyamatokban

Az Azure IoT Edge beépített támogatást nyújt a Azure DevOps és az Azure DevOps Projects, de a fejlesztési és üzemeltetési funkcióbővítés Jenkins beépülő modult is tartalmaz. [A Jenkins](https://jenkins.io/) egy nyílt forráskódú automatizáló kiszolgáló által használt beépülő modulok fejlesztését és üzembe helyezését projektek, beleértve az IoT Edge számos különböző típusú támogatásához. 

Az Azure IoT Edge beépülő modul a Jenkins folyamatos integrációs és folyamatos üzembe helyezés összpontosít. Létrehozhat egy build és a leküldéses folyamatot, amely összeállítja a modulokat, valamint leküldéses értesítések a tárolórendszerképeket a tárolóregisztrációs adatbázisba. Ezután hozzon létre egy kiadási folyamatot, amely központilag telepíti a modulokat az IoT Edge-eszközökön. 

Az IoT Edge beépülő modul a Jenkins használatának megkezdése előtt szüksége van egy IoT hubra az Azure és a egy container registry a tárolólemezképek tárolására. Egy egyszerű Azure-szolgáltatás segítségével biztosíthat a Jenkins közreműködői engedélyekkel az IoT hubhoz, úgy, hogy a beépülő modult is az eszközök az IoT Edge üzemelő példány létrehozása. 

Ha készen áll a kezdésre, keresse meg a telepítést, és adatait használja a [Jenkins beépülő modul az Azure IoT Edge](https://plugins.jenkins.io/azure-iot-edge).