---
title: Távoli figyelési megoldás architekturális választásai - Azure | Microsoft dokumentumok
description: Ez a cikk a távoli figyelési szolgáltatásban szereplő architekturális és műszaki döntéseket ismerteti.
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447173"
---
# <a name="remote-monitoring-architectural-choices"></a>Távoli monitorozási architektúra lehetőségei

Az Azure IoT Remote Monitoring megoldásgyorsító egy nyílt forráskódú, MIT licenccel rendelkező megoldásgyorsító. Az IoT-fejlesztési folyamat felgyorsítása érdekében gyakori IoT-forgatókönyveket jelenít meg, például:

- Eszközkapcsolatok
- Eszközfelügyelet
- Stream-feldolgozás

A távoli figyelési megoldás az ajánlott [Azure IoT referenciaarchitektúrát](https://aka.ms/iotrefarchitecture)követi.

Ez a cikk ismerteti a legfontosabb architekturális és műszaki döntéseket az egyes távoli figyelési alrendszerek. Azonban a Microsoft által a távoli figyelési megoldásban hozott technikai döntések nem az egyetlen módja annak, hogy távoli figyelési IoT-megoldást valósítson meg. A technikai megvalósítást a sikeres alkalmazás létrehozásának alaptervének kell tekintenie, és módosítania kell azt a következőképpen:

- A szervezetben rendelkezésre álló készségek és tapasztalatok igazítása.
- Feleljen meg a függőleges alkalmazás igényeinek.

## <a name="architectural-choices"></a>Az architektúrával kapcsolatos választási lehetőségek

A Microsoft által egy IoT-alkalmazáshoz ajánlott architektúra natív, mikroszolgáltatás ú és kiszolgáló nélküli. Az IoT-alkalmazások különböző alrendszereit különálló szolgáltatásokként kell felépíteni, amelyeket egymástól függetlenül telepíthet és skálázhat. Ezek az attribútumok nagyobb léptéket, nagyobb rugalmasságot tesznek lehetővé az egyes alrendszerek frissítésében, és rugalmasságot biztosítanak az egyes alrendszerek megfelelő technológiájának kiválasztásához.

A mikroszolgáltatások több technológia használatával valósíthatók meg. A mikroszolgáltatás megvalósításához például az alábbi lehetőségek közül választhat:

- Használjon egy tároló technológiát, például a Docker kiszolgáló nélküli technológiával, például az Azure Functions.
- Mikroszolgáltatások üzemeltetése a PaaS-szolgáltatások, például az Azure App Services.

## <a name="technology-choices"></a>Technológiai lehetőségek

Ez a szakasz az egyes alapvető alrendszerek távoli figyelési megoldásában hozott technológiai döntéseket részletezi.

![Alapvető diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Felhőátjáró

Az Azure IoT Hub a távoli figyelési megoldás felhőbeli átjárójaként használatos. [Az IoT Hub](https://azure.microsoft.com/services/iot-hub/) biztonságos, kétirányú kommunikációt biztosít az eszközökkel.

Az IoT-eszközök kapcsolata, használhatja:

- Az [IoT Hub-eszköz SDK-k](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) egy natív ügyfélalkalmazás megvalósításához az eszközhöz. Az SDK-k az IoT Hub REST API-t kínálnak burkolók, és kezeli a forgatókönyveket, például az újrapróbálkozások.
- Az Azure IoT Edge-el való integráció az eszközökön tárolókban futó egyéni modulok üzembe helyezéséhez és kezeléséhez.
- Az integráció az automatikus eszközfelügyelet az IoT Hub a csatlakoztatott eszközök ömlesztve történő kezeléséhez.

### <a name="stream-processing"></a>Stream-feldolgozás

Az adatfolyam-feldolgozáshoz a távoli figyelési megoldás az Azure Stream Analytics-et használja az összetett szabályfeldolgozáshoz. Ha egyszerűbb szabályokat szeretne használni, van egy egyéni mikroszolgáltatás, amely támogatja az egyszerű szabályfeldolgozást, bár ez a beállítás nem része a beépített telepítésnek. A referenciaarchitektúra az Azure Functions egyszerű szabályfeldolgozást és az Azure Stream Analytics komplex szabályfeldolgozást javasolja.

### <a name="storage"></a>Storage

A tároláshoz a távoli figyelési megoldás gyorsító jatttal használja az Azure Time Series Insights és az Azure Cosmos DB. Az Azure Time Series Insights tárolja az IoT Hubon keresztül érkező üzeneteket a csatlakoztatott eszközökről. A megoldásgyorsító az Azure Cosmos DB-t használja az összes többi tárolóhoz, például a hűtőtároláshoz, a szabálydefiníciókhoz, a riasztásokhoz és a konfigurációs beállításokhoz.

Az Azure Cosmos DB az ajánlott általános célú meleg tárolási megoldás Az IoT-alkalmazások. Azonban a megoldások, például az Azure Time Series Insights és az Azure Data Lake számos használati esethez megfelelőek. Az Azure Time Series Insights segítségével a trendek és anomáliák észlelésével mélyebb betekintést nyerhet az idősorozat-érzékelő adataiba. Ez a funkció lehetővé teszi a kiváltó ok elemzését és a költséges állásidő elkerülését.

> [!NOTE]
> A Time Series Insights jelenleg nem érhető el az Azure China felhőben. Az Azure China felhőben az új távoli figyelési megoldásgyorsítók a Cosmos DB-t használják az összes tárhoz.

### <a name="business-integration"></a>Üzleti integráció

A távoli figyelési megoldás üzleti integrációja a riasztások generálására korlátozódik, amelyek meleg tárolóba kerülnek. A megoldás azure Logic Apps-szel való összekapcsolásával mélyebb üzleti integrációs forgatókönyveket valósíthat meg.

### <a name="user-interface"></a>Felhasználói felület

A webes felhasználói felület javascript react-el készült. A React egy általánosan használt webes felhasználói felületi keretrendszert kínál, és hasonló más népszerű keretrendszerekhez, például az Angularhoz.

### <a name="runtime-and-orchestration"></a>Futásidejű és vezénylési

A távoli figyelési megoldás Docker-tárolókat használ az alrendszerek futtatásához a Kubernetes, mint a horizontális skálázás orchestrator. Ez az architektúra lehetővé teszi az egyes alrendszeregyedi méretezési definíciókat. Ez az architektúra azonban devops-költségekkel jár a virtuális gépek és tárolók naprakészen tartása és biztonsága érdekében.

A Docker alternatívái közé tartozik a mikroszolgáltatások üzemeltetése a PaaS-szolgáltatásokban, például az Azure App Service-ben. A Kubernetes alternatívái közé tartoznak az olyan vezénylők, mint a Service Fabric, a DC/OS vagy a Swarm.

## <a name="next-steps"></a>További lépések

* Telepítse a távoli figyelési megoldást [itt.](https://www.azureiotsolutions.com/)
* Fedezze fel a GitHub-kódot [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) és [Java nyelven.](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/)  
* Az IoT referenciaarchitektúráról itt olvashat [bővebben.](https://aka.ms/iotrefarchitecture)
