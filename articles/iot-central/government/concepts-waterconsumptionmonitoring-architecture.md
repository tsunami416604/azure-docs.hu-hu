---
title: Referenciaarchitektúra az Azure IoT Central szolgáltatással készített vízfogyasztás-figyelési megoldáshoz| Microsoft dokumentumok
description: Ismerje meg az Azure IoT Central által készített vízfogyasztás-figyelési megoldás fogalmait.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 3a64ca5b0c2a092f895873e097ea6beb9a235a37
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77017700"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>A vízfogyasztás-monitorozás referenciaarchitektúrája 



Vízfogyasztás-figyelési megoldások at az **Azure IoT Central alkalmazássablon,** mint egy kick starter IoT-alkalmazás. Ez a cikk egy magas szintű referencia-architektúra útmutatást nyújt a végpontok utáni megoldás létrehozásához. 

![Vízfogyasztás-figyelő architektúra](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Fogalmak:

1. Eszközök és kapcsolat  
1. IoT-központ 
2. Bővíthetőség és integráció
3. Üzleti alkalmazások

Vessünk egy pillantást a legfontosabb összetevők, amelyek általában szerepet játszanak a vízfogyasztás-felügyeleti megoldás.

## <a name="devices-and-connectivity"></a>Eszközök és kapcsolat 
Ez a rész az intelligens vízmegoldásokhoz használt eszközökre vonatkozik, mint például a vízminőség-ellenőrzés vagy a vízfogyasztás ellenőrzése, általában intelligens vízeszközként. Intelligens víz eszközök lehetnek áramlásmérők, vízminőség-monitorok, intelligens szelepek, szivárgásérzékelők, és így tovább.

Az intelligens vízmegoldásokban használt eszközöket általában alacsony fogyasztású, nagy kiterjedésű hálózatokon (LPWAN) keresztül, egy külső hálózatüzemeltetőn keresztül kapcsolják össze. Az ilyen típusú eszközök, az [Azure IoT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) segítségével küldje el az eszköz adatait az IoT-alkalmazás az Azure IoT Central. Azt is megteheti, hogy ip-képes eszközátjárókkal rendelkezik, és közvetlenül csatlakozhat az IoT Centralhoz.

## <a name="iot-central"></a>IoT-központ 
Az Azure IoT Central egy IoT-alkalmazásplatform, amely gyorsan beindítja és futtatja az IoT-megoldást. A megoldást márkajelzéssel, testreszabással és külső szolgáltatásokkal integrálhatja.
Miután csatlakoztatta az intelligens vízi eszközöket az IoT Centralhoz, eszközvezérlést, figyelést és riasztást, beépített RBAC-mal ellátott felhasználói felületet, konfigurálható elemzési irányítópultokat és bővíthetőségi lehetőségeket kap. 


## <a name="extensibility-and-integrations"></a>Bővíthetőség és integráció 
Az IoT-alkalmazást kiterjesztheti az IoT Centralban és opcionálisan:
* alakítsa át és integrálja IoT-adatait fejlett elemzésekhez, például a gépi tanulási modellek betanításához az IoT Central alkalmazásból származó folyamatos adatexportálás révén
* automatizálhatja a munkafolyamatokat más rendszerekben a Microsoft Flow vagy az IoT Central alkalmazás webhookjai nak használatával történő műveletek aktiválásával
* az IoT-alkalmazás iotcentrali alkalmazásán keresztül iotcentrali api-kon keresztül ido-t érhet el

## <a name="business-applications"></a>Üzleti alkalmazások 
Az IoT-adatok segítségével különböző típusú üzleti alkalmazások at egy vízi közmű. Ha meg szeretné tudni, hogyan kapcsolhatja össze az IoT Central vízfogyasztás-figyelő alkalmazását a helyszíni szolgáltatásokkal, kövesse a [Dynamics 365 Field Services szolgáltatással való integrációról](./how-to-configure-connected-field-services.md) szóló oktatóanyagot. 


## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [hozhat létre vízfogyasztást](./tutorial-water-consumption-monitoring.md) IoT Central alkalmazás
* További információ az [IoT központi kormányzati sablonjairól](./overview-iot-central-government.md)
* Ha többet szeretne megtudni az IoT Centralról, olvassa el az [IoT Central áttekintése című témakört.](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
