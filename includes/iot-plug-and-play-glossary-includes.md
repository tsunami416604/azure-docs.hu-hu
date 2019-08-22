---
author: ChrisGMsft
ms.service: iot-pnp
ms.topic: include
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: 7e8174855800bc6beea8750c32a6dd32588ccd9e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880461"
---
## <a name="iot-plug-and-play-device"></a>IoT Plug and Play eszköz

A IoT Plug and Play-eszközök jellemzően kis méretű, önálló számítástechnikai eszközök, amelyek adatokat gyűjthetnek vagy más eszközöket is felügyelhetnek, valamint olyan szoftvereket vagy belső vezérlőprogramot futtatnak, amelyek egy [eszköz](#device-capability-model)-képességi modellben deklarált képességeket implementálnak.  Például egy IoT Plug and Play eszköz lehet környezeti figyelő eszköz vagy egy intelligens agrigulture öntözési rendszer vezérlője. A felhőben üzemeltetett IoT-megoldások megírhatók a IoT Plug and Play-eszközökről történő parancs-, vezérlési és fogadási adatokra. A IoT Plug and Play eszközök az [Azure Certified for IoT Device Catalog](https://catalog.azureiotsolutions.com/)használatával találhatók meg. A rendszer ellenőrizte az összes IoT Plug and Play eszközt a katalógusban, és rendelkezik egy [eszköz képesség modellel](#device-capability-model).

## <a name="digital-twin"></a>Digitális Twin

A digitális ikrek a IoT Plug and Play-eszközök modelljei.  A digitális ikrek modellezése a [digitális kettős definíciós nyelv](https://aka.ms/DTDL)használatával történik.  Használhatja az Azure IoT API-t a digitális ikrekkel való kommunikációhoz. 

## <a name="digital-twin-definition-language"></a>Digitális kettős definíciós nyelv

A modellek és felületek [IoT Plug and Play eszközökhöz](#iot-plug-and-play-device)való leírásának nyelve.  A Digital [Twin Definition Language](https://aka.ms/DTDL) segítségével leírhatja a [digitális Twin](#digital-twin) funkcióit, és engedélyezheti a IoT platform-és IoT-megoldásokat az entitás szemantikai felhasználása érdekében.

## <a name="device-capability-model"></a>Eszköz képességeinek modellje

Az eszköz képességeinek modellje leírja az eszközt, és meghatározza az eszköz által megvalósított felületek készletét. Hozzon létre egy eszköz-képesség modellt, amely megfelel egy fizikai eszköznek, terméknek vagy SKU-nak.

## <a name="interface"></a>Felület

Az illesztőfelületek az eszköz vagy a digitális Twin által megvalósított kapcsolódó képességeket ismertetik. A felületek újra felhasználhatók a különböző képességi modellekben.

## <a name="property"></a>Tulajdonság

A tulajdonságok olyan adatmezők, amelyek egy olyan [felületen](#interface) vannak meghatározva, amely egy digitális iker bizonyos állapotát jelöli. A tulajdonságokat csak olvasható vagy írható módon deklarálhatja. A csak olvasható tulajdonságokat a IoT Plug and Play eszköz (például sorozatszám) kontextusában futó kód állítja be.  Az írható tulajdonságokat külső entitások, például riasztási küszöbértékek határozzák meg.

## <a name="telemetry"></a>Telemetria

Az illesztőfelületben definiált [](#interface) telemetria mezők a méréseket jelölik. Ezek a mérések jellemzően olyan értékek, mint az érzékelő beolvasása.

## <a name="command"></a>Parancs

Az [illesztőfelületekben](#interface) definiált parancsok olyan metódusokat jelölnek, amelyek a digitális iker-on hajthatók végre. Például egy új eszköz alaphelyzetbe állítására szolgáló parancs.
