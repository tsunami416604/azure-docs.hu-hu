---
title: Az Azure IoT Central alkalmazás csatlakoztatása a Dynamics 365 Field Services szolgáltatással | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre végpontok között megoldást az Azure IoT Central és a Dynamics 365 Field Service szolgáltatással
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7c708268eaeade1cfb625c73ff8758a1941ff5b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157449"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Az Azure IoT Central és a Dynamics 365 Field Service szolgáltatással végponttól végpontig csak a céllal hozhat létre 



Szerkesztőként engedélyezheti az Azure IoT Central alkalmazás más üzleti rendszerekbe való integrálását. 


Például egy csatlakoztatott hulladékgazdálkodási megoldásban optimalizálhatja a kukagyűjtő teherautók feladása. Az optimalizálás a csatlakoztatott hulladéktárolókBól származó IoT-érzékelők adatai alapján végezhető el. Az [IoT Central csatlakoztatott hulladékkezelési alkalmazásban](./tutorial-connected-waste-management.md) konfigurálhatja a szabályokat és a műveleteket, és beállíthatja, hogy riasztásokat hozzon létre a Dynamics Field Service szolgáltatásban. Ez a forgatókönyv a Microsoft Flow használatával érhető el, amely közvetlenül az IoT Centralban lesz konfigurálva az alkalmazások és szolgáltatások közötti munkafolyamatok automatizálásához. Emellett a Field Service szolgáltatási tevékenységei alapján az adatok visszaküldhetők az Azure IoT Centralnak. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Az Azure IoT Central alkalmazás csatlakoztatása a Dynamics 365 Field Services szolgáltatással 

Az alábbi integrációs folyamatok egyszerűen megvalósíthatók a tiszta konfigurációs élmény alapján:
* Az Azure IoT Central az eszközanomáliákról információkat küldhet a Connected Field Service szolgáltatásnak (IoT-riasztásként) a diagnózishoz.
* A Connected Field Service eseteket vagy az eszközanomáliákból kiváltott munkarendeléseket hozhat létre.
* A Connected Field Service a technikusokat ellenőrzésre ütemezheti, hogy megelőzze az állásidő-incidenseket.
* Az Azure IoT Central eszközirányítópultja frissíthető a megfelelő szolgáltatási és ütemezési információkkal.


## <a name="next-steps"></a>További lépések
* További információ az [IoT központi kormányzati sablonjairól](./overview-iot-central-government.md)
* További információ az [IoT Centralról](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* További információ a [Dynamics 365 Field Services szolgáltatásról](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
