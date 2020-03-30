---
title: Azure IoT Központi mikro-teljesítési központ | Microsoft dokumentumok
description: Ismerje meg, hogyan építhet mikro-teljesítési központ alkalmazást az IoT Central Micro-fulfillment center alkalmazássablonjával
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: f752c77a6a62b9b259a8bb1869ca03ff6a19b1f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77020879"
---
# <a name="micro-fulfillment-center-architecture"></a>Mikro-teljesítési központ architektúra

A mikro-teljesítési központ megoldásai lehetővé teszik a teljesen automatizált teljesítési központ minden aspektusának digitális csatlakoztatását, felügyeletét és kezelését a költségek csökkentése érdekében az állásidő kiküszöbölésével, miközben növeli a biztonságot és az általános hatékonyságot. Ezek a megoldások az IoT Central alkalmazássablonjainak és az alábbi architektúrának az útmutató használatával is létrehozhatók.

![Azure IoT Central Store elemzése](./media/architecture/micro-fulfillment-center-architecture-frame.png)

- Telemetriai adatokat küldő IoT-érzékelők készlete egy átjáróeszközre
- Telemetriai adatokat és összesített elemzéseket küldő átjáróeszközök az IoT Centralnak
- Folyamatos adatexportálás a kívánt Azure-szolgáltatásba a manipuláció érdekében
- Az adatok a kívánt formátumban strukturálhatók, és elküldhetők egy tárolási szolgáltatásnak
- Az üzleti alkalmazások lekérdezhetik az adatokat, és olyan elemzéseket hozhatnak létre, amelyek a kiskereskedelmi műveleteket
 
Vessünk egy pillantást a legfontosabb összetevők, amelyek általában szerepet játszanak a mikro-teljesítési központ megoldás.

## <a name="robotic-carriers"></a>Robothordozók

A mikro-teljesítési központ megoldás valószínűleg egy nagy sor robot hordozók generáló különböző telemetriai jeleket. Ezeket a jeleket egy átjáróeszköz összesítheti, majd elküldheti az IoT Centralnak, ahogy az az architektúradiagram bal oldala tükröződik.  

## <a name="condition-monitoring-sensors"></a>Állapotfigyelő érzékelők

Az IoT-megoldás olyan érzékelőkkel kezdődik, amelyek értelmes jeleket rögzítenek a teljesítési központból. Ez tükröződik a különböző érzékelők a bal szélen az architektúra diagram felett.

## <a name="gateway-devices"></a>Átjáró eszközök

Számos IoT-érzékelő képes nyers jeleket közvetlenül a felhőbe vagy a közelében található átjáróeszközre táplálni. Az átjáróeszköz adatösszesítést hajt végre a peremhálózaton, mielőtt összefoglaló elemzéseket küldene egy IoT Central-alkalmazásnak. Az átjáróeszközök felelősek a parancs- és vezérlőműveletek továbbításáért is az érzékelőeszközökre, ha vannak ilyenek. 

## <a name="iot-central-application"></a>IoT Central alkalmazás

Az Azure IoT Central alkalmazás különböző Típusú IoT-érzékelőkből, robotokból, valamint átjáróeszközökből származó adatokat tölt be a teljesítési központ környezetében, és értelmes elemzéseket hoz létre.

Az Azure IoT Central személyre szabott élményt is biztosít az áruház-üzemeltető számára, amely lehetővé teszi számukra az infrastruktúra-eszközök távoli figyelését és kezelését.

## <a name="data-transform"></a>Adatátalakítás
Az Azure IoT Central alkalmazás egy megoldáson belül konfigurálható nyers vagy összesített elemzések exportálására az Azure PaaS (Platform-as-a-Service) szolgáltatások készletébe, amelyek adatkezelést hajthatnak végre, és gazdagítják ezeket az elemzéseket, mielőtt egy üzleti Alkalmazás. 

## <a name="business-application"></a>Üzleti alkalmazás
Az IoT-adatok a kiskereskedelmi környezetben telepített különböző típusú üzleti alkalmazások működtetéséhez használhatók. A teljesítési központ vezetője vagy alkalmazottja használhatja ezeket az alkalmazásokat az üzleti elemzések megjelenítésére, és valós időben érdemi műveleteket tehet. Ha meg szeretné tudni, hogyan hozhat létre valós idejű Power BI-irányítópultot kiskereskedelmi csapata számára, kövesse az [oktatóanyagot.](./tutorial-in-store-analytics-create-app-pnp.md)

## <a name="next-steps"></a>További lépések
* Ismerkedjen meg a [Micro-fulfillment Center](https://aka.ms/checkouttemplate) alkalmazássablonnal. 
* Tekintse meg az [oktatóanyagot,](https://aka.ms/mfc-tutorial) amely bemutatja, hogyan hozhat létre megoldást a Micro-fulfillment Center alkalmazássablon használatával.
