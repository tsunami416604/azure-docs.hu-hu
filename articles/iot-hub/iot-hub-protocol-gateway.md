---
title: Az Azure IoT-protokollátjáró |} A Microsoft Docs
description: Hogyan használható az Azure IoT-protokollátjáró bővítheti az IoT Hub képességeket és protokoll támogatása eszközök engedélyezéséhez nem támogatott natív módon az IoT Hub által protokollok használatával a hubhoz való csatlakozáshoz.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.openlocfilehash: 9dbb7905c2a0fed65ede610577e0fa11a1deef92
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276235"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Az IoT Hub további protokollok támogatása

Az Azure IoT Hub natív módon támogatja a kommunikációt az MQTT, AMQP és a HTTPS protokollokon keresztül. Bizonyos esetekben eszközök vagy helyszíni átjárók nem képes ezek szabványos protokollok valamelyikével és protokoll eljárni. Ilyen esetekben egy egyéni átjárót is használhatja. Egyéni átjáró adatközponthíd-képzés a forgalmat, és az IoT Hub által lehetővé teszi a protokoll-betanítás az IoT Hub-végpontok. Használhatja a [Azure IoT-protokollátjáró](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) egyéni átjáróként az IoT Hub protokoll-betanítás engedélyezéséhez.

## <a name="azure-iot-protocol-gateway"></a>Az Azure IoT-protokollátjáró

Az Azure IoT-protokollátjáró egy olyan keretrendszer, a tervezett, nagy méretű, kétirányú eszköz kommunikációt az IoT Hub protokoll-betanítás. A protokoll-átjáró egy átadó összetevő, amely az eszköz kapcsolatokat fogad egy adott protokollon keresztül. A forgalom az IoT hubhoz AMQP 1.0-s felett áthidalja azt.

Azure Service Fabric, az Azure Cloud Services feldolgozói szerepkörei vagy a Windows Virtual Machines használatával telepítheti a protokoll-átjáró az Azure-ban rugalmasan méretezhető módon. Emellett a protokoll-átjáró a helyszíni környezetekben, például a helyszíni átjárók is telepíthető.

Az Azure IoT-protokollátjáró magában foglalja az MQTT protokoll adapter, amely lehetővé teszi, hogy szabhatja az MQTT protokoll működését, szükség esetén. Az IoT Hub az MQTT v3.1.1 protokoll beépített támogatást nyújt, mivel csak fontolja meg az MQTT protokoll adapter használatával, ha szükségesek protokoll testreszabások vagy további szolgáltatásokat konkrét követelmények.

Az MQTT-adaptert is a programozási modellt protokoll adapterek más protokollokat mutatja be. Emellett az Azure IoT protokoll átjáró programozási modell lehetővé teszi a speciális feldolgozó, például az egyéni hitelesítési, üzenet átalakítások, tömörítése és kibontása vagy titkosítási/visszafejtési közötti forgalom az egyéni összetevők az eszközök és az IoT hubot.

A rugalmasság érdekében az Azure IoT-protokollátjáró és az MQTT megvalósítási megadott nyílt forráskódú szoftverek-projektben. A nyílt forráskódú projekt segítségével különféle protokollokat és protokoll verziók támogatása, vagy testre szabhatja a megvalósítás a forgatókönyvhöz. 

## <a name="next-steps"></a>További lépések

Az Azure IoT-protokollátjáró, és hogyan használja, és az IoT-megoldás részeként üzembe kapcsolatos további információkért lásd:

* [Az Azure IoT protokoll átjáró tárházban a Githubon](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Az Azure IoT protokoll átjáró fejlesztői útmutatója](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Az IoT Hub üzembe helyezés tervezésével kapcsolatos további tudnivalókért lásd:

* [Összehasonlítás az Event Hubs-zal](iot-hub-compare-event-hubs.md)

* [Méretezés, magas rendelkezésre állású és vész-helyreállítási](iot-hub-scaling.md)

* [Az IoT Hub fejlesztői útmutató](iot-hub-devguide.md)