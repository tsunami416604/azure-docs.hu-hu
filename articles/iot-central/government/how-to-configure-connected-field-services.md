---
title: Azure IoT Central-alkalmazás összekötése a Dynamics 365 Field Services szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre teljes körű megoldást az Azure IoT Central és a Dynamics 365 Field Service használatával
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 702de11abc274d1f4001df5b28498928fde71109
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014458"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Teljes körű megoldás létrehozása az Azure IoT Central és a Dynamics 365 Field Service használatával 



Építőként engedélyezheti az Azure IoT Central-alkalmazás integrálását más üzleti rendszerekre. 


Egy csatlakoztatott hulladékkezelési megoldásban például optimalizálhatja a trash Collections-teherautók küldését. Az optimalizálás a csatlakoztatott adattárolók IoT-érzékelők adatainak használatával végezhető el. Az [IoT Central csatlakoztatott hulladékkezelési alkalmazásban](./tutorial-connected-waste-management.md) szabályokat és műveleteket konfigurálhat, és beállíthatja, hogy riasztásokat hozzon létre a Dynamics-mező szolgáltatásban. Ez a forgatókönyv a Power automatizálás használatával valósítható meg, amelyet közvetlenül a IoT Central konfigurál a munkafolyamatok alkalmazások és szolgáltatások közötti automatizálásához. Emellett a Service-tevékenységek alapján a Field Service-ben az információ visszaküldhető az Azure IoT Centralba. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Azure IoT Central-alkalmazás összekötése a Dynamics 365 Field Services használatával 

Az alábbi integrációs folyamatokat egyszerűen megvalósíthatja a tiszta konfigurációs élmény alapján:
* Az Azure IoT Central adatokat küldhet az eszközök rendellenességéről a kapcsolódó mező szolgáltatás (IoT-riasztás) számára a diagnosztizáláshoz.
* A csatlakoztatott mező szolgáltatás olyan eseteket vagy munkarendeléseket hozhat létre, amelyek az eszköz rendellenességei alapján aktiválódnak.
* A csatlakoztatott mező szolgáltatás a leállási incidensek megelőzése érdekében ütemezhet technikusokat a vizsgálathoz.
* Az Azure IoT Central eszköz irányítópultja frissíthető a vonatkozó szolgáltatás-és ütemezési információkkal.


## <a name="next-steps"></a>Következő lépések
* További információ a [IoT Central Government-sablonokról](./overview-iot-central-government.md)
* További információ a [IoT Central](../core/overview-iot-central.md)
* További információ a [Dynamics 365 Field Services szolgáltatásról](/dynamics365/field-service/cfs-iot-overview)
