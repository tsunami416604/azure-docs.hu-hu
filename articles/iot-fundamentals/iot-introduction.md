---
title: Bevezetés az Azure Things internetes hálózatba (IoT)
description: Bevezetés az Azure IoT és az IoT-szolgáltatások alapjainak ismertetése, beleértve az IoT használatát szemléltető példákat is.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 73eb0b3164a386bb270e42ceba56d5dc7045af1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729009"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Mi az Azure Internet of Things (IoT)?

Az Azure IoT (eszközök internetes hálózata) a Microsoft által felügyelt felhőszolgáltatások gyűjteménye, amelyek az IoT-hez csatlakozó eszközök millióit kötik össze, monitorozzák és irányítják. Egyszerűbben fogalmazva, az IoT-megoldás egy vagy több IoT-eszközből áll, amelyek egy vagy több, a felhőben üzemeltetett háttérszolgáltatással kommunikálnak. 

## <a name="iot-devices"></a>IoT-eszközök

Az IoT-eszköz általában egy áramköri lapból áll, amelyhez wi-fi-t használó érzékelők csatlakoznak az internethez. Például:

* Nyomásérzékelő egy távoli olajszivattyún.
* Hőmérséklet- és páratartalom-érzékelők egy légkondicionáló egységben.
* Gyorsulásmérő a liftben.
* Jelenlétérzékelők egy szobában.

A megoldás elkészítéséhez különböző gyártók tól származó eszközök széles választéka áll rendelkezésre. Az Azure IoT Hubbal tanúsítottan használható eszközök listáját az [Azure Certified for IoT eszközkatalógusában.](https://catalog.azureiotsolutions.com/alldevices) Prototípus-készítéshez használhat olyan eszközöket, mint például az [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) vagy a [Raspberry Pi.](https://www.raspberrypi.org/) A Devkit beépített érzékelőkkel rendelkezik a hőmérséklet, a nyomás, a páratartalom, valamint a giroszkóp, a gyorsulásmérő és a magnetométer számára. A Raspberry Pi segítségével számos különböző típusú érzékelőt csatlakoztat. 

A Microsoft nyílt forráskódú [eszközSDK-kat](../iot-hub/iot-hub-devguide-sdks.md) biztosít, amelyekkel az eszközökön futó alkalmazásokat hozhat létre. Ezek [az SDK-k leegyszerűsítik és felgyorsítják](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) az IoT-megoldások fejlesztését.

## <a name="communication"></a>Kommunikáció

Az IoT-eszközök általában telemetriai adatokat küldenek az érzékelőktől a felhőbeli háttérszolgáltatásokba. Más típusú kommunikáció azonban lehetséges, például egy háttérszolgáltatás, amely parancsokat küld az eszközökre. Az alábbiakban néhány példa az eszközről a felhőbe és a felhőből az eszközre irányuló kommunikációra:

* A mobil hűtőkocsi 5 percenként küldi a hőmérsékletet az IoT Hubba. 

* A háttérszolgáltatás egy parancsot küld egy eszköznek, hogy módosítsa a telemetriai adatok at a probléma diagnosztizálása érdekében. 

* Az eszköz riasztásokat küld az érzékelőiből leolvasott értékek alapján. Például egy vegyi üzemben lévő kötegreaktort figyelő eszköz riasztást küld, ha a hőmérséklet meghaladja az értéket.

* Az eszközök adatokat küldenek az irányítópulton való megjelenítéshez, hogy azokat emberi operátorok megtekinthessék. Egy finomító vezérlőhelyisége például megmutathatja az egyes csövek hőmérsékletét, nyomását és áramlási térfogatát, lehetővé téve az üzemeltetők számára a létesítmény felügyeletét. 

Az [IoT-eszköz SDK-k](../iot-hub/iot-hub-devguide-sdks.md) és az IoT Hub közös [kommunikációs protokollokat,](../iot-hub/iot-hub-devguide-protocols.md) például http, MQTT és AMQP támogatja.

Az IoT-eszközök más jellemzőkkel rendelkeznek más ügyfelekkel, például böngészőkkel és mobilalkalmazásokkal összehasonlítva. Az eszköz SDK-k segítségével biztonságosan és megbízhatóan csatlakoztathatja az eszközöket a háttérszolgáltatáshoz.  Pontosabban, az IoT-eszközök:

* Általában beágyazott, emberi beavatkozást nem igénylő rendszerek (a telefonokkal ellentétben).
* Távoli helyeken is üzembe helyezhetők, ahol a fizikai hozzáférés drága lenne.
* Előfordulhat, hogy csak a megoldás háttérrendszerén keresztül érhetők el.
* Áramellátásuk és feldolgozási erőforrásaik korlátozottak lehetnek.
* A hálózati kapcsolat időszakos, lassú vagy drága lehet.
* Saját fejlesztésű, egyedi vagy iparág-specifikus alkalmazás-protokollokra lehet szükség.

## <a name="back-end-services"></a>Háttérszolgáltatások 

Az IoT-megoldásban a háttérszolgáltatás olyan funkciókat biztosít, mint például:

* Telemetriai adatok fogadása az eszközökről, és meghatározza, hogyan dolgozza fel és tárolja ezeket az adatokat.
* A telemetriai adatok elemzése, akár valós időben, akár a tény után.
* Parancsok küldése a felhőből egy adott eszközre. 
* Eszközök kiépítése és annak szabályozása, hogy mely eszközök csatlakozhatnak az infrastruktúrához.
* Az eszközök állapotának szabályozása és tevékenységeik figyelése.
* Az eszközökre telepített belső vezérlőprogram kezelése.

Például egy olajszivattyú-állomás távoli figyelési megoldásában a felhőháttér-rendszer a szivattyúk telemetriáját használja a rendellenes viselkedés azonosítására. Amikor a háttérszolgáltatás anomáliát azonosít, automatikusan visszaküldheti a parancsot az eszköznek a korrekciós művelet elvégzéséhez. A folyamat létrehoz egy automatizált visszajelzési hurkot az eszköz és a felhő között, amely jelentősen növeli a megoldás hatékonyságát.

## <a name="azure-iot-examples"></a>Példák az Azure IoT-re

Az Azure IoT-t a szervezetek azure IoT-használati módjára vonatkozó valós példákért a [Microsoft Technikai Esettanulmányok az IoT-hez](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)tekintse meg. 

## <a name="next-steps"></a>További lépések

Néhány tényleges üzleti esetet és a használt architektúrát a [Microsoft Azure IoT műszaki esettanulmányai ban.](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)

Néhány mintaprojektet, amelyet kipróbálhat egy IoT DevKit segítségével, tekintse meg az [IoT DevKit projektkatalógusát.](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) 

A különböző szolgáltatások és használatuk részletesebb magyarázatát az [Azure IoT-szolgáltatások és -technológiák](iot-services-and-technologies.md)című témakörben tetszésben lehet.

Az IoT-architektúra részletes ismertetéséhez tekintse át a következő dokumentumot: [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Microsoft Azure IoT-referenciaarchitektúra).
