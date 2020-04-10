---
title: Store Analytics architektúra
description: Ismerje meg, hogyan hozhat létre egy bolti elemzési alkalmazást az IoT Central Checkout alkalmazássablonjával
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: f1f83fdd73816e6e30c5cac7d193719591bb8dc1
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80999021"
---
# <a name="in-store-analytics-architecture"></a>Üzleten belüli elemzési architektúra



Az üzleten belüli elemzési megoldások lehetővé teszik a különböző feltételek figyelését a kiskereskedelmi környezetben. Ezek a megoldások az IoT Central alkalmazássablonjainak és az alábbi architektúrának az útmutató használatával is létrehozhatók.


![Azure IoT Central Store elemzése](./media/architecture/store-analytics-architecture-frame.png)

- Telemetriai adatokat küldő IoT-érzékelők készlete egy átjáróeszközre
- Telemetriai adatokat és összesített elemzéseket küldő átjáróeszközök az IoT Centralnak
- Folyamatos adatexportálás a kívánt Azure-szolgáltatásba a manipuláció érdekében
- Az adatok a kívánt formátumban strukturálhatók, és elküldhetők egy tárolási szolgáltatásnak
- Az üzleti alkalmazások lekérdezhetik az adatokat, és olyan elemzéseket hozhatnak létre, amelyek a kiskereskedelmi műveleteket
 
Vessünk egy pillantást a legfontosabb összetevők, amelyek általában szerepet játszanak egy in-store elemzési megoldás.

## <a name="condition-monitoring-sensors"></a>Állapotfigyelő érzékelők

Az IoT-megoldás olyan érzékelők készletével kezdődik, amelyek értelmes jeleket rögzítenek egy kiskereskedelmi környezetből. A fenti architektúra diagram bal szélén különböző érzékelők tükrözik.

## <a name="gateway-devices"></a>Átjáró eszközök

Számos IoT-érzékelő képes nyers jeleket közvetlenül a felhőbe vagy a közelében található átjáróeszközre táplálni. Az átjáróeszköz adatösszesítést hajt végre a peremhálózaton, mielőtt összefoglaló elemzéseket küldene egy IoT Central-alkalmazásnak. Az átjáróeszközök felelősek a parancs- és vezérlőműveletek továbbításáért is az érzékelőeszközökre, ha vannak ilyenek. 

## <a name="iot-central-application"></a>IoT Central alkalmazás

Az Azure IoT Central alkalmazás betölt imitátai különböző típusú IoT-érzékelők, valamint átjáró eszközök a kiskereskedelmi környezetben, és létrehoz egy sor értelmes betekintést.

Az Azure IoT Central személyre szabott élményt is biztosít az áruház-üzemeltető számára, amely lehetővé teszi számukra az infrastruktúra-eszközök távoli figyelését és kezelését.

## <a name="data-transform"></a>Adatátalakítás
Az Azure IoT Central alkalmazás egy megoldáson belül konfigurálható nyers vagy összesített elemzések exportálására az Azure PaaS (Platform-as-a-Service) szolgáltatások készletébe, amelyek adatkezelést hajthatnak végre, és gazdagítják ezeket az elemzéseket, mielőtt egy üzleti alkalmazásban leállítanák őket. 

## <a name="business-application"></a>Üzleti alkalmazás
Az IoT-adatok a kiskereskedelmi környezetben telepített különböző típusú üzleti alkalmazások működtetéséhez használhatók. A kiskereskedelmi üzlet vezetője vagy a személyzet tagja használhatja ezeket az alkalmazásokat, hogy vizualizálja az üzleti elemzéseket, és valós időben érdemi műveleteket. Ha meg szeretné tudni, hogyan hozhat létre valós idejű Power BI-irányítópultot kiskereskedelmi csapata számára, kövesse az [oktatóanyagot.](./tutorial-in-store-analytics-create-app.md)

## <a name="next-steps"></a>További lépések
* Ismerkedés az [in-store analytics checkout](https://aka.ms/checkouttemplate) és [az in-store analytics feltételfigyelés](https://aka.ms/conditiontemplate) alkalmazássablonokkal. 
* Vessen egy pillantást a [végpontok között bemutató,](https://aka.ms/storeanalytics-tutorial) amely végigvezeti, hogyan hozhat létre egy megoldást az egyik In Store Analytics alkalmazássablonok.
