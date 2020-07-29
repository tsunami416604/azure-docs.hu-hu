---
title: Az összetevők ismertetése a IoT Plug and Play-modellekben | Microsoft Docs
description: Ismerje meg a IoT Plug and Play DTDL modellek közötti különbséget, amelyek olyan összetevőket és modelleket használnak, amelyek nem használnak összetevőket.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4c41edc477460e6d239688aafe6d7219bed36cd4
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352321"
---
# <a name="iot-plug-and-play-components-in-models"></a>IoT Plug and Play-összetevők a modellekben

A IoT Plug and Play konvenciók esetében az eszköz egy IoT Plug and Play-eszköz, ha a digitális Twins Definition Language (DTDL) modell AZONOSÍTÓját mutatja be, amikor egy IoT hubhoz csatlakozik.

A következő kódrészlet példákat mutat be a modellek azonosítói:

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>Nincsenek összetevők

Egy egyszerű modell nem használ beágyazott vagy lépcsőzetes összetevőket. A fejléc információi és a tartalom szakasza tartalmazza a telemetria, a tulajdonságokat és a parancsokat.

Az alábbi példa egy olyan egyszerű modell részét mutatja be, amely nem használ összetevőket:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Bár a modell nem definiál explicit módon egy összetevőt, úgy viselkedik, mintha egyetlen összetevővel rendelkezik az összes telemetria, tulajdonsággal és parancs-definícióval.

Az alábbi képernyőképen látható, hogyan jelenik meg a modell az Azure IoT Explorer eszközben:

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Alapértelmezett összetevő az Azure IoT Explorerben":::

A modell AZONOSÍTÓját a Device Twin tulajdonság tárolja, amely a következő képernyőképet mutatja:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="Modell azonosítója a digitális Twin tulajdonságban":::

Az összetevők nélküli DTDL modellek hasznos egyszerűsítést biztosítanak egy olyan eszköz számára, amely egyetlen telemetria, tulajdonsággal és paranccsal rendelkezik. Az összetevőket nem használó modellek egyszerűen áttelepíthetik egy meglévő eszközt egy IoT Plug and Play eszközre – létrehoz egy DTDL modellt, amely leírja a tényleges eszközt anélkül, hogy meg kellene határoznia a kívánt összetevőket.

## <a name="multiple-components"></a>Több összetevő

Az összetevők lehetővé teszik a modell felületének összeállítását más felületek szerelvényként.

A [termosztát](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) interfész például modellként van definiálva. Ezt az illesztőfelületet egy vagy több összetevővel is beépítheti, amikor a [hőmérséklet-vezérlő modelljét](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)definiálja. A következő példában a rendszer ezeket az összetevőket hívja meg `thermostat1` és `thermostat2` .

Több összetevővel rendelkező DTDL-modell esetén két vagy több összetevőből álló szakaszt tartalmaz. Mindegyik szakasz a (z) értékre van `@type` állítva, `Component` és explicit módon hivatkozik egy sémára, ahogy az a következő kódrészletben látható:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
... 
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
...
```

Ebben a modellben három összetevő van definiálva a tartalom szakaszban – két `Thermostat` összetevő és egy `DeviceInformation` összetevő. Létezik egy alapértelmezett gyökérszintű összetevő is.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a modell-összetevőket, néhány további erőforrást is talál:

- [Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Modell adattárai](./concepts-model-repository.md)