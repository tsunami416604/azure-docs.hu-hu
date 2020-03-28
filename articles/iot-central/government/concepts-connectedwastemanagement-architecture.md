---
title: Referenciaarchitektúra az Azure IoT Central szolgáltatással készített csatlakoztatott hulladékkezelési megoldáshoz| Microsoft dokumentumok
description: Ismerje meg az Azure IoT Central beépített csatlakoztatott hulladékkezelési megoldásának koncepcióit.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 763fb82e0725e5a98112af3a79856c3703a5c2c3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77016085"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Az összekapcsolt hulladékmonitorozás referenciaarchitektúrája 



Egy csatlakoztatott hulladékkezelési megoldás az **Azure IoT Central alkalmazássablont** használja kezdő IoT-alkalmazásként. Ez a cikk egy magas szintű referencia-architektúra útmutatást nyújt a végpontok utáni megoldás létrehozásához. 

![Csatlakoztatott hulladékgazdálkodási architektúra](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Fogalmak:

1. Eszközök és kapcsolat  
1. IoT-központ 
2. Bővíthetőség és integráció
3. Üzleti alkalmazások

Vessünk egy pillantást a legfontosabb összetevők, amelyek általában szerepet játszanak a vízfogyasztás-felügyeleti megoldás.

## <a name="devices-and-connectivity"></a>Eszközök és kapcsolat 
A nyílt környezetben használt eszközök, például a hulladéktárolók, kis teljesítményű, nagy kiterjedésű hálózatokon (LPWAN) keresztül csatlakoztathatók egy külső hálózatüzemeltetőn keresztül. Az ilyen típusú eszközök, az [Azure IoT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) segítségével küldje el az eszköz adatait az IoT-alkalmazás az Azure IoT Central. Azt is megteheti, hogy ip-képes eszközátjárókkal rendelkezik, és közvetlenül csatlakozhat az IoT Centralhoz.

## <a name="iot-central"></a>IoT-központ 
Az Azure IoT Central egy IoT-alkalmazásplatform, amely gyorsan beindítja és futtatja az IoT-megoldást. A megoldást márkajelzéssel, testreszabással és külső szolgáltatásokkal integrálhatja.
Miután csatlakoztatta az intelligens vízi eszközöket az IoT Centralhoz, eszközvezérlést, figyelést és riasztást, beépített RBAC-mal ellátott felhasználói felületet, konfigurálható elemzési irányítópultokat és bővíthetőségi lehetőségeket kap. 

## <a name="extensibility-and-integrations"></a>Bővíthetőség és integráció 
Az IoT-alkalmazást kiterjesztheti az IoT Centralban és opcionálisan:
* alakítsa át és integrálja IoT-adatait a fejlett elemzésekhez, például a gépi tanulási modellek betanításához az IoT Central alkalmazásból származó folyamatos adatexportálás révén. 
* automatizálhatja a munkafolyamatokat más rendszerekben a Microsoft Flow vagy az IoT Central alkalmazás webhookjai nak használatával történő műveletek aktiválásával
* az IoT Central IoT Central ban való iot-alkalmazását az IoT Central API-kon keresztül.

## <a name="business-applications"></a>Üzleti alkalmazások 
Az IoT-adatok segítségével számos üzleti alkalmazás használható egy hulladékkezelő segédprogramon belül. Ha meg szeretné tudni, hogyan kapcsolhatja össze ioT Central-kapcsolattal rendelkező hulladékkezelési alkalmazását a helyszíni szolgáltatásokkal, kövesse a [Dynamics 365 Field Services szolgáltatással való integrációról](./how-to-configure-connected-field-services.md) szóló oktatóanyagot. 

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [hozhat létre csatlakoztatott hulladékgazdálkodási](./tutorial-connected-waste-management.md) IoT Central alkalmazást
* További információ az [IoT központi kormányzati sablonjairól](./overview-iot-central-government.md)
* Ha többet szeretne megtudni az IoT Centralról, olvassa el az [IoT Central áttekintése című témakört.](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)

