---
title: Az Azure IoT Central beépített, csatlakoztatott hulladékgazdálkodási megoldás hivatkozási architektúrája | Microsoft Docs
description: Megtudhatja, hogyan használhatók az Azure IoT Central-vel létrehozott, csatlakoztatott hulladékgazdálkodási megoldás fogalmai.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 763fb82e0725e5a98112af3a79856c3703a5c2c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77016085"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Az összekapcsolt hulladékmonitorozás referenciaarchitektúrája 



A csatlakoztatott hulladékkezelési megoldás az **Azure IoT Central alkalmazás-sablonnal** is létrehozható kick starter IoT alkalmazásként. Ez a cikk magas szintű hivatkozási architektúra-útmutatást nyújt a végpontok közötti megoldás létrehozásához. 

![Csatlakoztatott hulladékgazdálkodási architektúra](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Fogalmak:

1. Eszközök és kapcsolatok  
1. IoT-központ 
2. Bővíthetőség és integrációk
3. Üzleti alkalmazások

Vessünk egy pillantást a kulcsfontosságú összetevőkre, amelyek általában egy részét játszanak a vízfogyasztás figyelésére szolgáló megoldásban.

## <a name="devices-and-connectivity"></a>Eszközök és kapcsolatok 
A nyílt környezetekben, például a hulladék-tárolókban használt eszközök az alacsony teljesítményű, nagy kiterjedésű hálózatokon (LPWAN) keresztül csatlakoztathatók egy külső hálózati szolgáltatón keresztül. Ilyen típusú eszközök esetén az [azure IoT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) használatával az eszköz adatait elküldheti a IoT alkalmazásnak az Azure IoT Centralban. Előfordulhat, hogy az IP-címmel rendelkező eszköz-átjárók képesek, és közvetlenül a IoT Centralhoz tudnak csatlakozni.

## <a name="iot-central"></a>IoT-központ 
Az Azure IoT Central egy IoT alkalmazási platform, amellyel gyorsan elindíthatja és futtathatja a IoT-megoldását. A megoldásokat a harmadik féltől származó szolgáltatásokkal is kiegészítheti, testreszabhatja és integrálhatja.
Miután az intelligens vízeszközeit IoT Centralhoz csatlakoztatotta, az eszköz parancsait és vezérlését, figyelését és riasztását, felhasználói felületét beépített RBAC, konfigurálható elemzéseket tartalmazó irányítópultokat és bővíthetőségi lehetőségeket kap. 

## <a name="extensibility-and-integrations"></a>Bővíthetőség és integrációk 
Kiterjesztheti a IoT alkalmazást IoT Central és opcionálisan:
* a IoT-adatok átalakítása és integrálása speciális elemzésekhez, például a gépi tanulási modellek IoT Central alkalmazásból történő folyamatos exportálásával. 
* a munkafolyamatok automatizálása más rendszerekben a műveletek Microsoft Flow vagy webhookok használatával történő aktiválásával IoT Central alkalmazásból
* a programozott módon IoT Central IoT Central API-kon keresztül férhet hozzá a IoT alkalmazáshoz.

## <a name="business-applications"></a>Üzleti alkalmazások 
A IoT-adatmennyiség használatával számos üzleti alkalmazást lehet kiszolgálni a hulladékkezelési segédprogramban. Ha szeretné megtudni, hogyan csatlakoztathatja a IoT Central csatlakoztatott hulladékgazdálkodási alkalmazást a Field Services szolgáltatással, kövesse a [Dynamics 365 Field Services integrálásával](./how-to-configure-connected-field-services.md) kapcsolatos oktatóanyagot. 

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [hozhat létre csatlakoztatott hulladékgazdálkodási](./tutorial-connected-waste-management.md) IoT Central alkalmazást
* További információ a [IoT Central Government-sablonokról](./overview-iot-central-government.md)
* További információ a IoT Centralről: [IoT Central áttekintése](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)

