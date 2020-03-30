---
title: Az Azure ipari IoT áttekintése | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az ipari IoT.This article provides a overview of industrial IoT. Ismerteti a csatlakoztatott gyárat, a gyári kapcsolatot és az IIoT biztonsági összetevőit.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c5d70e5a8784e88d3f2906d500768735dced1ee0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73828123"
---
# <a name="what-is-industrial-iot-iiot"></a>Mi az ipari IoT (IIoT)

Az IIoT a dolgok ipari internete. Az IIoT az IoT alkalmazásával javítja az ipari hatékonyságot a feldolgozóiparban. 

## <a name="improve-industrial-efficiencies"></a>Az ipari hatékonyság javítása

Növelje működési termelékenységét és jövedelmezőségét egy csatlakoztatott gyári megoldásgyorsítóval. Csatlakoztassa és figyelje ipari berendezéseit és eszközeit a felhőben, beleértve a gyárban már működő gépeket is. Elemezze az IoT-adatokat olyan elemzési adatokért, amelyek segítenek a teljes gyárteljesítmény ének növelésében.

Csökkentse a gyári gépek elérésének időigényes folyamatát az OPC Twin segítségével, és összpontosítsa idejét az IIoT-megoldások kiépítésére. Egyszerűsítse a tanúsítványkezelést és az ipari eszközök integrációját az OPC Vaultsegítségével, és legyen biztos abban, hogy az eszközkapcsolat biztonságos. Ezek a mikroszolgáltatások REST-szerű API-t biztosítanak az [Azure Industrial IoT-összetevőkön](https://github.com/Azure/azure-iiot-opc-ua)felül. A szolgáltatás API-t biztosít a peremhálózati modul funkcióinak vezérlését. 

![Az ipari IoT áttekintése](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Az Azure Industrial IoT-szolgáltatásokról a [GitHub-tárházban](https://github.com/Azure/azure-iiot-services)talál további információt.
Ha nem ismeri az Azure IoT Edge-modulok működését, kezdje a következő cikkekkel:
- [Tudnivalók az Azure IoT Edge-ről](../iot-edge/about-iot-edge.md)
- [Azure IoT Edge-modulok](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Csatlakoztatott gyár

[A Connected Factory](../iot-accelerators/iot-accelerators-connected-factory-features.md) a Microsoft Azure Industrial IoT referenciaarchitektúrájának implementációja, amely testre szabható, hogy megfeleljen bizonyos üzleti követelményeknek. A teljes megoldáskód nyílt forráskódú, és elérhető a Connected Factory megoldásgyorsító GitHub-tárházban. Használhatja egy kereskedelmi termék kiindulópontjaként, és percek alatt üzembe helyezhet egy előre elkészített megoldást az Azure-előfizetésében. 

## <a name="factory-floor-connectivity"></a>Gyári kapcsolat a gyárban

Az OPC Twin egy IIoT-összetevő, amely automatizálja az eszközök felderítését és regisztrációját, és az ipari eszközök rest API-kon keresztüli távvezérlését kínálja. AZ OPC Twin az Azure IoT Edge és az IoT Hub segítségével csatlakoztatja a felhőt és a gyári hálózatot. Az OPC Twin lehetővé teszi az IIoT-fejlesztők számára, hogy az IIoT-alkalmazások készítésére összpontosítsanak anélkül, hogy aggódniuk kellene a helyszíni gépek biztonságos elérése miatt.

## <a name="security"></a>Biztonság

Az OPC Vault az OPC UA Global Discovery Server (GDS) implementációja, amely konfigurálhatja, regisztrálhatja és kezelheti az OPC UA-kiszolgáló és a felhőben lévő ügyfélalkalmazások tanúsítványéletciklusának életciklusát. Az OPC Vault leegyszerűsíti a biztonságos eszközkapcsolat megvalósítását és karbantartását az ipari térben. A tanúsítványkezelés automatizálásával az OPC Vault felszabadítja a gyári üzemeltetőket a kapcsolatokkal és tanúsítványkezeléssel kapcsolatos manuális és összetett folyamatok alól.

## <a name="next-steps"></a>További lépések

Most, hogy már bemutatkozott az ipari IoT-ről és annak összetevőiről, itt van a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Mi az az OPC-iker?](overview-opc-twin.md)
