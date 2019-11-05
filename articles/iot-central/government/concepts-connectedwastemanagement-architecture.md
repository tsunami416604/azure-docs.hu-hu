---
title: Az Azure IoT Central beépített, csatlakoztatott hulladékgazdálkodási megoldás hivatkozási architektúrája | Microsoft Docs
description: Megtudhatja, hogyan használhatók az Azure IoT Central-vel létrehozott, csatlakoztatott hulladékgazdálkodási megoldás fogalmai.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2905a7ab7999c66bde0a705ff129fa049617df10
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498869"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Az összekapcsolt hulladékmonitorozás referenciaarchitektúrája 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

A csatlakoztatott hulladékkezelési megoldás az **Azure IoT Central alkalmazás-sablonnal** is létrehozható kick starter IoT alkalmazásként. Ez a cikk magas szintű hivatkozási architektúra-útmutatást nyújt a végpontok közötti megoldás létrehozásához. 

![Csatlakoztatott hulladékgazdálkodási architektúra](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Fogalmak:

1. Eszközök és kapcsolatok  
1. IoT Central 
2. Bővíthetőség és integrációk
3. Üzleti alkalmazások

Vessünk egy pillantást a kulcsfontosságú összetevőkre, amelyek általában egy részét játszanak a vízfogyasztás figyelésére szolgáló megoldásban.

## <a name="devices-and-connectivity"></a>Eszközök és kapcsolatok 
A nyílt környezetekben, például a hulladékok tárolókban használt eszközök általában alacsony teljesítményű, nagy kiterjedésű hálózatokon (LPWAN) keresztül csatlakoztathatók egy külső hálózati szolgáltatón keresztül. Az ilyen típusú eszközök esetében kihasználhatja az [azure IoT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) eszközt, hogy az eszköz adatait a IoT alkalmazásba küldje az Azure IoT Centralban. Előfordulhat, hogy az IP-címmel rendelkező eszköz-átjárók képesek, és közvetlenül a IoT Centralhoz tudnak csatlakozni.

## <a name="iot-central"></a>IoT Central 
Az Azure IoT Central egy IoT alkalmazási platform, amellyel gyorsan elindíthatja és futtathatja a IoT-megoldását. A megoldásokat a harmadik féltől származó szolgáltatásokkal is kiegészítheti, testreszabhatja és integrálhatja.
Az intelligens vízeszközek IoT Centralhoz csatlakoztatása után az eszköz parancs és vezérlés, a figyelés és a riasztások, a felhasználói felület és a beépített RBAC, a konfigurálható elemzések irányítópultok és a bővíthetőségi lehetőségek is elérhetők. 

## <a name="extensibility-and-integrations"></a>Bővíthetőség és integrációk 
Kiterjesztheti a IoT alkalmazást IoT Central és opcionálisan:
* a IoT-adatok átalakítása és integrálása speciális elemzésekhez, például a gépi tanulási modellek IoT Central alkalmazásból történő folyamatos exportálásával. 
* munkafolyamatok automatizálása más rendszerekben a műveletek Microsoft Flow vagy webhookok használatával történő aktiválásával IoT Central alkalmazásból
* a programozott módon IoT Central IoT Central API-kon keresztül férhet hozzá a IoT alkalmazáshoz.

## <a name="business-applications"></a>Üzleti alkalmazások 
A IoT-adatmennyiség használatával számos üzleti alkalmazást lehet kiszolgálni a hulladékkezelési segédprogramban. Ha szeretné megtudni, hogyan csatlakoztathatja a IoT Central csatlakoztatott hulladékgazdálkodási alkalmazást a Field Services szolgáltatással, kövesse a [Dynamics 365 Field Services integrálásával](./how-to-configure-connected-field-services.md) kapcsolatos oktatóanyagot. 

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [hozhat létre csatlakoztatott hulladékgazdálkodási](./tutorial-connected-waste-management.md) IoT Central alkalmazást
* További információ a [IoT Central Government-sablonokról](./overview-iot-central-government.md)
* További információ a IoT Centralről: [IoT Central áttekintése](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)

