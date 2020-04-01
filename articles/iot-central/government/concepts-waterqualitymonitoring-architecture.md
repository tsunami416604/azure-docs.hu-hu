---
title: Referenciaarchitektúra az Azure IoT Central szolgáltatással készített vízminőség-figyelési megoldáshoz| Microsoft dokumentumok
description: Ismerje meg az Azure IoT Central szolgáltatással készített vízminőség-figyelési megoldás fogalmait.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 0c4b13c56a68205195bd5ad4b696d9e01786a8dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77016544"
---
# <a name="water-quality-monitoring-reference-architecture"></a>A vízminőség-monitorozás referenciaarchitektúrája 



A vízminőség-figyelési megoldások az **Azure IoT Central alkalmazássablonnal,** mint kezdő IoT-alkalmazással is kiépíthetők. Ez a cikk egy magas szintű referencia-architektúra útmutatást nyújt a végpontok utáni megoldás létrehozásához. 


![Vízminőség-ellenőrzési architektúra](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

Fogalmak:

1. Eszközök és kapcsolat  
1. IoT-központ 
2. Bővíthetőség és integráció
3. Üzleti alkalmazások

Vessünk egy pillantást a legfontosabb összetevők, amelyek általában szerepet játszanak a vízminőség-ellenőrzési megoldás.

## <a name="devices-and-connectivity"></a>Eszközök és kapcsolat 
Ebben a részben a vízminőség-ellenőrzésben vagy a vízfogyasztás-ellenőrzésben használt eszközökre, általában intelligens vízeszközként hivatkozunk. Intelligens víz eszközök lehetnek áramlásmérők, vízminőség-monitorok, intelligens szelepek, szivárgásérzékelők, és így tovább.

Az intelligens vízmegoldásokban használt eszközöket általában alacsony fogyasztású, nagy kiterjedésű hálózatokon (LPWAN) keresztül, egy külső hálózatüzemeltetőn keresztül kapcsolják össze. Az ilyen típusú eszközök, kihasználhatja az [Azure IoT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) az eszközadatok küldése az IoT-alkalmazás az Azure IoT Central. Azt is megteheti, hogy ip-képes eszközátjárókkal rendelkezik, és közvetlenül csatlakozhat az IoT Centralhoz.

## <a name="iot-central"></a>IoT-központ 
Az Azure IoT Central egy IoT-alkalmazásplatform, amely gyorsan beindítja és futtatja az IoT-megoldást. A megoldást márkajelzéssel, testreszabással és külső szolgáltatásokkal integrálhatja.
Miután csatlakoztatta az intelligens vízi eszközöket az IoT Centralhoz, eszközvezérlést, figyelést és riasztást, beépített RBAC-mal ellátott felhasználói felületet, konfigurálható elemzési irányítópultokat és bővíthetőségi lehetőségeket kap. 

## <a name="extensibility-and-integrations"></a>Bővíthetőség és integráció 
Az IoT-alkalmazást kiterjesztheti az IoT Centralban és opcionálisan:
* alakítsa át és integrálja IoT-adatait fejlett elemzésekhez, például a gépi tanulási modellek betanításához az IoT Central alkalmazásból származó folyamatos adatexportálás révén
* automatizálhatja a munkafolyamatokat más rendszerekben a Microsoft Flow vagy az IoT Central alkalmazás webhookjai nak használatával történő műveletek aktiválásával
* az IoT-alkalmazás iotcentrali alkalmazásán keresztül iotcentrali api-kon keresztül ido-t érhet el

## <a name="business-applications"></a>Üzleti alkalmazások 
Az IoT-adatok számos üzleti alkalmazás működtetéséhez használhatók egy vízi közműn belül. Ha meg szeretné tudni, hogyan kapcsolhatja össze az IoT Central vízminőség-figyelő alkalmazását a helyszíni szolgáltatásokkal, kövesse a [Dynamics 365 Field Services szolgáltatással való integrációról](./how-to-configure-connected-field-services.md)szóló cikket. 


## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [hozhat létre vízminőség-figyelési](./tutorial-water-quality-monitoring.md) IoT Central alkalmazást
* További információ az [IoT központi kormányzati sablonjairól](./overview-iot-central-government.md)
* Ha többet szeretne megtudni az IoT Centralról, olvassa el az [IoT Central áttekintése című témakört.](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)

