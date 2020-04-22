---
title: Azure IoT protokollátjáró | Microsoft dokumentumok
description: Azure IoT-protokollátjáró használata az IoT Hub képességeinek és protokolltámogatásának bővítéséhez, hogy az eszközök natív módon nem támogatott protokollok használatával kapcsolódhassanak a hubhoz.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: bfd000e2b69f052e25f0ea6cd286b0ca3aef7519
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759706"
---
# <a name="support-additional-protocols-for-iot-hub"></a>További protokollok támogatása az IoT Hubhoz

Az Azure IoT Hub natív módon támogatja a kommunikációt az MQTT, AMQP és HTTPS protokollokon keresztül. Bizonyos esetekben előfordulhat, hogy az eszközök vagy a helyszíni átjárók nem tudják használni a szabványos protokollok egyikét, és protokolladaptációt igényelnek. Ilyen esetekben egyéni átjárót is használhat. Az egyéni átjáró lehetővé teszi a protokoll-adaptáció t ioT Hub végpontok áthidalásával a forgalmat, és az IoT Hub. Az Azure [IoT protokollátjáró](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) egyéni átjáróként használhatja a protokolladaptációt az IoT Hubhoz.

## <a name="azure-iot-protocol-gateway"></a>Azure IoT protokollátjáró

Az Azure IoT protokoll átjáró egy keretrendszer protokoll adaptáció, amely az IoT Hub nagy léptékű, kétirányú eszköz kommunikáció. A protokollátjáró egy átmenő összetevő, amely egy adott protokollon keresztül fogadja az eszközkapcsolatokat. Áthidalja a forgalmat az IoT Hub-hoz az AMQP 1.0-s keresztül.

A protokollátjárót az Azure Service Fabric, az Azure Cloud Services feldolgozói szerepkörei vagy a Windows virtuális gépek használatával rendkívül méretezhető módon telepítheti az Azure-ban. Emellett a protokoll átjáró helyszíni környezetekben, például helyszíni átjárókban is telepíthető.

Az Azure IoT protokoll átjáró tartalmaz egy MQTT protokoll adapter, amely lehetővé teszi az MQTT protokoll viselkedésének testreszabását, ha szükséges. Mivel az IoT Hub beépített támogatást nyújt az MQTT v3.1.1 protokollhoz, csak akkor érdemes az MQTT protokolladapter t, ha protokolltestreszabásra vagy további funkciókra vonatkozó speciális követelményekre van szükség.

Az MQTT adapter bemutatja a más protokolladapterek készítésének programozási modelljét is. Emellett az Azure IoT protokoll átjáró programozási modell lehetővé teszi, hogy csatlakoztasson egyéni összetevők speciális feldolgozás, például az egyéni hitelesítés, üzenet átalakítások, tömörítés/kicsomagolás, vagy az eszközök és az IoT Hub közötti forgalom titkosítása/visszafejtése.

A rugalmasság érdekében az Azure IoT protokoll átjáró és az MQTT-implementáció nyílt forráskódú szoftverprojektben található. A nyílt forráskódú projekt segítségével támogatást adhat a különböző protokollokhoz és protokollverziókhoz, vagy testreszabhatja a megvalósítást a forgatókönyvhöz. 

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure IoT protokoll átjárójáról, valamint arról, hogyan használhatja és telepítheti azt az IoT-megoldás részeként, olvassa el a következő témakört:

* [Azure IoT protokoll átjárótár a GitHubon](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Az Azure IoT protokollátjáró fejlesztői útmutatója](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Ha többet szeretne megtudni az IoT Hub-telepítés megtervezéséről, olvassa el a következő témakört:

* [Összehasonlítás az Eseményközpontokkal](iot-hub-compare-event-hubs.md)

* [Méretezés, magas rendelkezésre állás és vészhelyreállítás](iot-hub-scaling.md)

* [Az IoT Hub fejlesztői útmutatója](iot-hub-devguide.md)