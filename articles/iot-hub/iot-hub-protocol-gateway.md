---
title: Az Azure IoT protokoll-átjáró |} Microsoft Docs
description: Hogyan használható az Azure IoT protokoll-átjáró IoT-központ képességek és kiterjesztheti protokoll támogatása engedélyezése eszközökhöz a hub nem támogatott natív módon az IoT-központ által protokollok használatával kapcsolódni.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: elioda
ms.openlocfilehash: 2c90ee899d0002d41ca21ed4a4927470ee53b2e1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635304"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Az IoT-központ további protokollok támogatása
Az Azure IoT Hub natív módon támogatja a kommunikációt a MQTT AMQP vagy HTTPS protokollokon keresztül. Bizonyos esetekben eszközök vagy mező átjárók előfordulhat, hogy nem fogja tudni a szabványos protokollok valamelyikével és protokoll eljárni. Ilyen esetekben egy egyéni gateway használható. Egy egyéni átjáró protokoll kiigazítása az IoT-központok végpontjai teszi lehetővé a forgalmat, és az IoT Hub-hídképzés. Használhatja a [Azure IoT protokoll-átjáró](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) ahhoz, hogy az IoT hub protokoll kiigazítása egyéni átjáróként.

## <a name="azure-iot-protocol-gateway"></a>Az Azure IoT protokoll-átjáró
Az Azure IoT protokoll átjáró keretrendszere, amely készült, nagy méretű, kétirányú eszköz kommunikáció IoT Hub protokoll kiigazítása. A protokoll-átjáró része a csatlakoztatott eszköz kapcsolatok fogadására egy adott protokollon keresztül. Az IoT-központ forgalmat AMQP 1.0-hez képest kulcsösszetevő azt. 

A protokoll-átjáró, az Azure skálázható módon telepítheti a Azure Service Fabric, a Azure Cloud Services feldolgozói szerepkörök vagy a Windows virtuális gépek használatával. Emellett a protokoll-átjáró telepíthető helyszíni környezetekben, például a mező átjárók.

Az Azure IoT protokoll átjáró tartalmaz egy MQTT protokoll adapter, amelyek segítségével testre szabhatja a MQTT protokoll viselkedését, ha szükséges. Az IoT-központ beépített támogatást nyújt a MQTT v3.1.1 protokollt, mert csak érdemes a MQTT protokoll adaptert használ, ha a protokoll testre szabott elem és további funkciók konkrét követelmények szükségesek.

A MQTT adaptert is a programozási modellt protokoll adapterek más protokollokat mutatja be. Ezenkívül az Azure IoT protokoll átjáró programozási modell lehetővé teszi az egyéni összetevők, például az egyéni hitelesítési, az üzenet átalakítások, a tömörítési/kibontása vagy a titkosítási/visszafejtési az eszközök és az IoT-központ közötti forgalom speciális feldolgozásra.

Rugalmasság az Azure IoT protokoll-átjáró és MQTT megvalósítási megadott nyílt forráskódú szoftvereket projektben. A nyílt forráskódú projekt segítségével különböző protokollok és protokollverziója támogatásához, vagy testre szabhatja a megvalósítás a forgatókönyvéhez. 

## <a name="next-steps"></a>További lépések
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
