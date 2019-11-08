---
title: Az Azure Industrial IoT áttekintése | Microsoft Docs
description: Ez a cikk az ipari IoT áttekintését tartalmazza. Ismerteti a csatlakoztatott gyárat, a gyári padlót és a biztonsági összetevőket a IIoT-ben.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c5d70e5a8784e88d3f2906d500768735dced1ee0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828123"
---
# <a name="what-is-industrial-iot-iiot"></a>Mi az ipari IoT (IIoT)?

A IIoT az ipari eszközök internetes hálózata. A IIoT az ipari hatékonyságot a IoT alkalmazásával fokozza a feldolgozóipari iparágban. 

## <a name="improve-industrial-efficiencies"></a>Az ipari hatékonyság javítása

Növelje működési termelékenységét és jövedelmezőségét egy csatlakoztatott gyári megoldás-gyorsító révén. Ipari felszereléseit és eszközeit – többek között a már működő gyári gépeket is – a felhőben csatlakoztathatja és figyelheti. IoT-adatainak elemzésével értékes információkhoz juthat, amelyek a teljes gyártás teljesítményét is kedvezően befolyásolhatják.

Csökkentse a Factory Floor Machines és az OPC Twin szolgáltatásokhoz való hozzáférés időigényes folyamatát, és a IIoT-megoldások kiépítésének idejére koncentráljon. Egyszerűsítheti a Tanúsítványkezelőt és az iparági eszközök integrációját az OPC-tárolóval, és bízik benne, hogy az adategység-kapcsolat biztonságos. Ezek a szolgáltatások egy REST-szerű API-t biztosítanak az [Azure Industrial IoT összetevőihez](https://github.com/Azure/azure-iiot-opc-ua). A Service API lehetővé teszi az Edge-modul funkcióinak vezérlését. 

![Ipari IoT – áttekintés](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Az Azure Industrial IoT szolgáltatásaival kapcsolatos további információkért lásd a GitHub- [tárházat](https://github.com/Azure/azure-iiot-services).
Ha nem ismeri a Azure IoT Edge moduljainak működését, kezdje a következő cikkekkel:
- [Tudnivalók az Azure IoT Edge-ről](../iot-edge/about-iot-edge.md)
- [Azure IoT Edge modulok](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Csatlakoztatott gyár

A [csatlakoztatott gyár](../iot-accelerators/iot-accelerators-connected-factory-features.md) a Microsoft Azure Industrial IoT-referenciájának olyan implementációja, amely testreszabható az adott üzleti igények kielégítése érdekében. A teljes megoldás kódja nyílt forráskódú, és elérhető a csatlakoztatott Factory Solution Accelerator GitHub-tárházban. Használhatja kereskedelmi termék kiindulásaként is, és percek alatt üzembe helyezhet egy előre elkészített megoldást az Azure-előfizetésében. 

## <a name="factory-floor-connectivity"></a>Gyári padló kapcsolat

Az OPC Twin egy IIoT-összetevő, amely automatizálja az eszközök felderítését és regisztrálását, és a REST API-kon keresztül biztosítja az ipari eszközök távvezérlését. Az OPC Twin, Azure IoT Edge és IoT Hub használatával csatlakozik a felhőhöz és a gyári hálózathoz. Az OPC Twin lehetővé teszi a IIoT-fejlesztők számára, hogy a helyszíni gépek biztonságos elérésével kapcsolatos gondok nélkül összpontosítsanak a IIoT-alkalmazások létrehozására.

## <a name="security"></a>Biztonság

Az OPC-tár az OPC ua Global Discovery Server (GDS) implementációja, amely konfigurálhatja, regisztrálhatja és kezelheti a tanúsítványok életciklusát az OPC UA-kiszolgáló és az ügyfélalkalmazások számára a felhőben. Az OPC-tároló leegyszerűsíti a biztonságos eszközök kapcsolatának megvalósítását és karbantartását az ipari térben. A Tanúsítványkezelő automatizálásával az OPC-tároló felszabadítja a gyári operátorokat a kapcsolat-és tanúsítványkezelőhöz kapcsolódó manuális és összetett folyamatokkal.

## <a name="next-steps"></a>További lépések

Most, hogy már bevezette az iparági IoT és annak összetevőit, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Mi az OPC Twin](overview-opc-twin.md)
