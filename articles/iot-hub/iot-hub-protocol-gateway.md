---
title: "Az Azure IoT protokoll-átjáró |} Microsoft Docs"
description: "Hogyan használható az Azure IoT protokoll-átjáró IoT-központ képességek és kiterjesztheti protokoll támogatása engedélyezése eszközökhöz a hub nem támogatott natív módon az IoT-központ által protokollok használatával kapcsolódni."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 555e59ae-3136-4533-8ba8-f3a3b6acf648
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: elioda
ms.openlocfilehash: 1ed8ec28b95bbc91b731fd7bb7b3f1f6654e7fcf
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2017
---
# Az IoT-központ további protokollok támogatása
Az Azure IoT Hub natív módon támogatja a kommunikációt a MQTT AMQP vagy HTTPS protokollokon keresztül. Bizonyos esetekben eszközök vagy mező átjárók előfordulhat, hogy nem fogja tudni a szabványos protokollok valamelyikével és protokoll eljárni. Ilyen esetekben egy egyéni gateway használható. Egy egyéni átjáró protokoll kiigazítása az IoT-központok végpontjai teszi lehetővé a forgalmat, és az IoT Hub-hídképzés. Használhatja a [Azure IoT protokoll-átjáró](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) ahhoz, hogy az IoT hub protokoll kiigazítása egyéni átjáróként.

## Az Azure IoT protokoll-átjáró
Az Azure IoT protokoll átjáró keretrendszere, amely készült, nagy méretű, kétirányú eszköz kommunikáció IoT Hub protokoll kiigazítása. A protokoll-átjáró része a csatlakoztatott eszköz kapcsolatok fogadására egy adott protokollon keresztül. Az IoT-központ forgalmat AMQP 1.0-hez képest kulcsösszetevő azt. 

A protokoll-átjáró, az Azure skálázható módon telepítheti a Azure Service Fabric, a Azure Cloud Services feldolgozói szerepkörök vagy a Windows virtuális gépek használatával. Emellett a protokoll-átjáró telepíthető helyszíni környezetekben, például a mező átjárók.

Az Azure IoT protokoll átjáró tartalmaz egy MQTT protokoll adapter, amelyek segítségével testre szabhatja a MQTT protokoll viselkedését, ha szükséges. Az IoT-központ beépített támogatást nyújt a MQTT v3.1.1 protokollt, mert csak érdemes a MQTT protokoll adaptert használ, ha a protokoll testre szabott elem és további funkciók konkrét követelmények szükségesek.

A MQTT adaptert is a programozási modellt protokoll adapterek más protokollokat mutatja be. Ezenkívül az Azure IoT protokoll átjáró programozási modell lehetővé teszi az egyéni összetevők, például az egyéni hitelesítési, az üzenet átalakítások, a tömörítési/kibontása vagy a titkosítási/visszafejtési az eszközök és az IoT-központ közötti forgalom speciális feldolgozásra.

Rugalmasság az Azure IoT protokoll-átjáró és MQTT megvalósítási megadott nyílt forráskódú szoftvereket projektben. A nyílt forráskódú projekt segítségével különböző protokollok és protokollverziója támogatásához, vagy testre szabhatja a megvalósítás a forgatókönyvéhez. 

## Következő lépések
Az Azure IoT protokoll-átjáró és használja, és az IoT-megoldásból részeként telepítésével kapcsolatos további információkért lásd:

* [Az Azure IoT protokoll átjáró tárház a Githubon](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Az Azure IoT protokoll átjáró fejlesztői útmutató](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Az IoT-központ telepítésének tervezése kapcsolatos további információkért lásd:

* [Az Event Hubs összehasonlítása][lnk-compare]
* [Skálázás, magas rendelkezésre állás és vészhelyreállítás][lnk-scaling]
* [IoT Hub fejlesztői útmutató][lnk-devguide]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
