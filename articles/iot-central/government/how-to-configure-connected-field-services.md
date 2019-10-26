---
title: Azure IoT Central-alkalmazás összekötése a Dynamics 365 Field Services szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre teljes körű megoldást az Azure IoT Central és a Dynamics 365 Field Service használatával
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 5b387027b8975048bf9cf27dd9ada617bba94969
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957593"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Teljes körű megoldás létrehozása az Azure IoT Central és a Dynamics 365 Field Service használatával 

Építőként engedélyezheti az Azure IoT Central-alkalmazás integrálását más üzleti rendszerekre. 


Egy csatlakoztatott hulladékkezelési megoldásban például optimalizálhatja a trash Collections teherautók küldését a csatlakoztatott IoT-érzékelők adatai alapján. Az [IoT Central csatlakoztatott hulladékkezelési alkalmazásban](./tutorial-connected-waste-management.md) szabályokat és műveleteket konfigurálhat, és beállíthatja, hogy riasztásokat hozzon létre a Dynamics-mező szolgáltatásban. Ez a forgatókönyv Microsoft Flow használatával valósul meg, amelyet közvetlenül a IoT Central konfigurálhat a munkafolyamatok alkalmazások és szolgáltatások közötti automatizálásához. Emellett a Service-tevékenységek alapján is visszaküldheti az adatokat az Azure IoT Centralba. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Azure IoT Central-alkalmazás összekötése a Dynamics 365 Field Services használatával 

Az alábbi végpontok közötti integrációs folyamat egyszerűen megvalósítható a tiszta konfigurációs élmény alapján:
* Az Azure IoT Central adatokat küldhet az eszközök rendellenességéről a kapcsolódó mező szolgáltatás (IoT-riasztás) számára a diagnosztizáláshoz.
* A csatlakoztatott mező szolgáltatás olyan eseteket vagy munkarendeléseket hozhat létre, amelyek az eszköz rendellenességei alapján aktiválódnak.
* A csatlakoztatott mező szolgáltatás a leállási incidensek megelőzése érdekében ütemezhet technikusokat a vizsgálathoz.
* Az Azure IoT Central eszköz irányítópultja frissíthető a vonatkozó szolgáltatás-és ütemezési információkkal.


## <a name="next-steps"></a>Következő lépések
* További információ a [IoT Central Government-sablonokról](./overview-iot-central-government.md)
* További információ a [IoT Central](https://docs.microsoft.com/azure/iot-central/overview-iot-central)
* További információ a [Dynamics 365 Field Services szolgáltatásról](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
