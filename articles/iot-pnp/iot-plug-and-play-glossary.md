---
title: Szószedet – IoT Plug and Play előzetes verzió | Microsoft Docs
description: Fogalmak – a IoT Plug and Play előzetes verziójának általános feltételeinek glosszáriuma.
author: dominicbetts
ms.author: dobett
ms.date: 07/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d8efec0517cd0b4bdcba643a0936b474593d58c4
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475464"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>A IoT használati feltételeinek szószedete Plug and Play előzetes verzió

A IoT Plug and Play cikkeiben használt általános feltételek definíciói.

## <a name="azure-iot-explorer-tool"></a>Az Azure IoT Explorer eszköze

Az Azure IoT Explorer egy grafikus eszköz, amellyel kezelheti és tesztelheti a [IoT Plug and Play eszközöket](#iot-plug-and-play-device). Miután telepítette az eszközt a helyi gépre, a következőt használhatja:

- Tekintse meg az [IoT hub](#azure-iot-hub)-hoz csatlakoztatott eszközöket.
- Kapcsolódjon egy IoT Plug and Play eszközhöz.
- Tekintse meg az eszköz [összetevőit](#component).
- Az eszköz által küldött [telemetria](#telemetry) megtekintése.
- Az eszköz [tulajdonságainak](#properties)használata.
- Hívja meg az eszköz [parancsait](#commands).

## <a name="azure-iot-hub"></a>Azure IoT Hub

Az IoT Hub egy olyan felügyelt szolgáltatás, amely a felhőn fut, és amely az IoT-alkalmazás és az általa felügyelt eszközök közötti kétirányú kommunikáció üzenetközpontjaként működik. A [IoT Plug and Play-eszközök](#iot-plug-and-play-device) csatlakozni tudnak egy IoT hubhoz. Az IoT-megoldás egy IoT hubot használ a következő engedélyezéséhez:

- Eszközök, amelyekkel telemetria küldhetnek egy felhőalapú megoldásba.
- Felhőalapú megoldás a csatlakoztatott eszközök kezelésére.

## <a name="azure-iot-device-sdk"></a>Azure IoT-eszköz SDK

Több nyelvhez is vannak eszközök SDK-k, amelyek segítségével IoT Plug and Play eszköz ügyfélalkalmazások hozhatók létre.

## <a name="commands"></a>Parancsok

Az [illesztőfelületekben](#interface) definiált parancsok olyan metódusokat jelölnek, amelyek a [digitális iker](#digital-twin)-on hajthatók végre. Például egy eszköz újraindítására szolgáló parancs.

## <a name="component"></a>Összetevő

Az összetevők lehetővé teszik a modell [felületének](#interface) összeállítását más felületek szerelvényként. Az [eszköz modelljei](#device-model) több felületet is kombinálhatók összetevőkként. Előfordulhat például, hogy egy modell egy kapcsoló összetevőt és egy termosztát-összetevőt is tartalmaz. Egy modellben több összetevő is használhatja ugyanazt az illesztőfelület-típust. Előfordulhat például, hogy egy modell két termosztát-összetevőt tartalmaz.

## <a name="connection-string"></a>Kapcsolati sztring

A kapcsolati karakterláncok a végponthoz való kapcsolódáshoz szükséges adatokat ágyazzák össze. A kapcsolatok karakterlánca általában tartalmazza a végpontok és a biztonsági információk címeit, de a kapcsolatok karakterlánc-formátumai eltérőek a szolgáltatások között. A IoT Hub szolgáltatáshoz két típusú kapcsolattípus van társítva:

- Az eszköz kapcsolati karakterláncai lehetővé teszik, hogy a [IoT Plug and Play eszközök](#iot-plug-and-play-device) csatlakozzanak az IoT hub eszközre irányuló végpontokhoz. Az eszközön a kapcsolati sztring használatával biztonságos kapcsolat hozható létre egy IoT hubhoz.
- IoT Hub kapcsolati karakterláncok lehetővé teszik a háttér-megoldások és-eszközök számára, hogy biztonságosan kapcsolódjanak a IoT hub szolgáltatáshoz csatlakozó végpontokhoz. Ezek a megoldások és eszközök kezelik az IoT hubot és az ahhoz csatlakoztatott eszközöket.

## <a name="device-model"></a>Eszközmodell

Az eszköz modellje leírja a [IoT Plug and Play eszközét](#iot-plug-and-play-device) , és meghatározza az eszközt alkotó [összetevőket](#component) . Egy egyszerű eszköz-modell nem rendelkezik külön összetevőkkel, és egyetlen gyökérszintű felület definícióját tartalmazza. Egy összetettebb eszköz-modell több összetevőt is tartalmaz. Az eszköz modellje általában egy fizikai eszköz, termék vagy SKU-nak felel meg. Az eszköz modelljét a [Digital Twins Definition Language 2-es verziójának](#digital-twins-definition-language) használatával határozhatja meg.

## <a name="device-builder"></a>Eszköz-szerkesztő

Az Eszközkezelő egy [eszköz modelljét](#device-model) és [felületét](#interface) használja, amikor programkódot implementál egy [IoT Plug and Play eszközön](#iot-plug-and-play-device)való futtatáshoz. Az eszközök építői jellemzően az [Azure IoT Device SDK](#azure-iot-device-sdk) -k egyikét használják az eszköz ügyfélszoftverének megvalósításához, de erre nincs szükség.

## <a name="device-modeling"></a>Eszköz modellezése

Az [Eszközkezelő](#device-builder) a [digitális ikrek definíciós nyelvét](#digital-twins-definition-language) használja egy [IoT Plug and Play-eszköz](#iot-plug-and-play-device)képességeinek modellezéséhez. A [megoldás-szerkesztő](#solution-builder) konfigurálhat egy IoT-megoldást a modellből.

## <a name="digital-twin"></a>Digitális Twin

A digitális Twin egy [IoT Plug and Play eszköz](#iot-plug-and-play-device)modellje. A digitális Twin modell a [digitális ikrek definíciós nyelvét](#digital-twins-definition-language)használja. Az [Azure IoT-eszköz SDK](#azure-iot-device-sdk) -k használatával a digitális ikrek a futási időben is kommunikálhatnak. Beállíthat például egy tulajdonságot egy digitális Twin-eszközön, és az SDK közli ezt a változást a felhőben lévő IoT-megoldással.

## <a name="digital-twin-change-events"></a>Digitális kettős változási események

Ha egy [IoT Plug and Play eszköz](#iot-plug-and-play-device) csatlakozik egy [IoT hubhoz](#azure-iot-hub), a hub útválasztási képességével digitális kettős változtatásokról is küldhet értesítéseket. Ha például egy [tulajdonság](#properties) értéke megváltozik egy eszközön, IoT hub küldhet értesítést egy végpontnak, például egy Event hub-nak.

## <a name="digital-twins-definition-language"></a>Digitális ikrek definíciós nyelve

A modellek és felületek [IoT Plug and Play eszközökhöz](#iot-plug-and-play-device)való leírásának nyelve. A Digital [Twins Definition Language 2-es verziójának](https://github.com/Azure/opendigitaltwins-dtdl) használatával ismertesse a [digitális Twin](#digital-twin) funkcióit, és lehetővé teszi a IoT platform és a IoT megoldások számára az entitás szemantikai kihasználását.

## <a name="digital-twin-route"></a>Digitális kettős útvonal

Egy [IoT-hubhoz](#azure-iot-hub) beállított útvonal, amely a [digitális kettős változási eseményeket](#digital-twin-change-events) és végpontokat, például egy Event hubot szolgáltat.

## <a name="interface"></a>Interfész

Az illesztőfelületek a [IoT Plug and Play eszköz](#iot-plug-and-play-device) vagy a [digitális Twin](#digital-twin)szolgáltatás által megvalósított kapcsolódó képességeket ismertetik. A különböző [eszközökön](#device-model)keresztül is felhasználhat felületeket. Ha egy illesztőfelületet egy eszköz modelljében használ, az meghatározza az eszköz [összetevőjét](#component) .

## <a name="iot-hub-query-language"></a>IoT Hub lekérdezés nyelve

A IoT Hub lekérdezési nyelv több célra szolgál. Használhatja például a nyelvet az IoT hub-ban regisztrált eszközök keresésére, vagy a [digitális kettős útválasztási](#digital-twin-route) viselkedés pontosítására.

## <a name="iot-plug-and-play-device"></a>IoT Plug and Play eszköz

A IoT Plug and Play-eszközök általában egy kis méretű, önálló számítástechnikai eszköz, amely adatokat gyűjt, vagy más eszközöket irányít, valamint az [eszköz modelljét](#device-model)implementáló szoftvert vagy belső vezérlőprogramot futtat.  Például egy IoT Plug and Play eszköz lehet környezeti figyelő eszköz vagy egy intelligens mezőgazdasági öntözési rendszer vezérlője. Felhőalapú IoT-megoldást is írhat a IoT Plug and Play eszközökről származó adatok parancsára, vezérlésére és fogadására.

## <a name="iot-plug-and-play-conventions"></a>Az IoT Plug and Playhez kapcsolódó konvenciók

A IoT Plug and Play- [eszközöknek](#iot-plug-and-play-device) a megoldással való adatcserére vonatkozó [konvenciókat](concepts-convention.md) kell követniük.

## <a name="model-id"></a>Modellazonosító

Ha egy IoT Plug and Play eszköz csatlakozik egy IoT Hubhoz, akkor az általa megvalósított [DTDL](#digital-twins-definition-language) modell **azonosítóját** küldi el. Ez lehetővé teszi, hogy a megoldás megtalálja az eszköz modelljét.

## <a name="model-repository"></a>Modelladattár

A [modell tárháza](concepts-model-repository.md) az [eszközök modelljeit](#device-model) és [felületeit](#interface)tárolja.

## <a name="model-repository-rest-api"></a>Modell tárháza REST API

API a modell adattárának kezeléséhez és interakcióhoz. Használhatja például az API-t az [eszközök modelljeinek](#device-model)hozzáadásához és kereséséhez.

## <a name="properties"></a>Tulajdonságok

A tulajdonságok olyan adatmezők, amelyek egy olyan [felületen](#interface) vannak meghatározva, amely egy digitális iker bizonyos állapotát jelöli. A tulajdonságokat csak olvasható vagy írható módon deklarálhatja. A csak olvasható tulajdonságokat, például a sorozatszámot a [IoT Plug and Play eszközön](#iot-plug-and-play-device) futó kód állítja be.  Az írható tulajdonságok, például a riasztási küszöbértékek általában a felhőalapú IoT-megoldás alapján vannak beállítva.

## <a name="shared-access-signature"></a>Közös hozzáférésű jogosultságkód

A közös hozzáférésű aláírások az SHA-256 biztonságos kivonatokon vagy URI-kon alapuló hitelesítési mechanizmusok. A közös hozzáférésű aláírás hitelesítésének két összetevője van: egy közös hozzáférési házirend és egy közös hozzáférési aláírás (más néven token). A [IoT Plug and Play-eszközök](#iot-plug-and-play-device) közös hozzáférési aláírással hitelesítik magukat az [IoT hub](#azure-iot-hub)használatával.

## <a name="solution-builder"></a>Megoldás-szerkesztő

A megoldás-szerkesztő létrehozza a megoldás hátterét. A megoldás-szerkesztő általában az Azure-erőforrásokkal, például a [IoT hub](#azure-iot-hub) és a [Model repositorykkal](#model-repository)működik.

## <a name="telemetry"></a>Telemetria

Az [illesztőfelületben](#interface) definiált telemetria mezők a méréseket jelölik. Ezek a mérések jellemzően olyan értékek, mint az érzékelő által az [IoT Plug and Play eszköz](#iot-plug-and-play-device) által küldött adatstreamek.
