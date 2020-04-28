---
title: A távoli figyelési megoldás építészeti lehetőségei – Azure | Microsoft Docs
description: Ez a cikk a távoli monitorozás során végzett építészeti és műszaki döntéseket ismerteti
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "61447173"
---
# <a name="remote-monitoring-architectural-choices"></a>Távoli monitorozási architektúra lehetőségei

Az Azure IoT távoli monitorozási megoldásának gyorsítása egy nyílt forráskódú, MIT licenccel rendelkező megoldás-gyorsító. A IoT-fejlesztési folyamat felgyorsításának elősegítése érdekében az általános IoT-forgatókönyveket, például a következőket jeleníti meg:

- Eszközkapcsolatok
- Eszközfelügyelet
- Stream-feldolgozás

A távoli figyelési megoldás a javasolt [Azure IoT-hivatkozási architektúrát](https://aka.ms/iotrefarchitecture)követi.

Ez a cikk a távoli figyelési alrendszerek főbb építészeti és műszaki lehetőségeit ismerteti. A távoli figyelési megoldásban készített Microsoft technikai döntések azonban nem az egyetlen módszer a távoli figyelési IoT megoldás megvalósítására. Tekintse át a technikai megvalósítási alapkonfigurációt a sikeres alkalmazások létrehozásához, és módosítsa a következőre:

- A szervezeten belül elérhető képességek és tapasztalatok.
- Megfeleljen a vertikális alkalmazás igényeinek.

## <a name="architectural-choices"></a>Az architektúrával kapcsolatos választási lehetőségek

A Microsoft által a IoT-alkalmazásokhoz javasolt architektúra a Felhőbeli natív, a szolgáltatás és a kiszolgáló nélküli rendszer. Egy IoT-alkalmazás különböző alrendszereit különálló szolgáltatásként kell létrehoznia, amelyek egymástól függetlenül telepíthetők és méretezhetők. Ezek az attribútumok nagyobb méretezést, nagyobb rugalmasságot biztosítanak az egyes alrendszerek frissítéséhez, és biztosítják a rugalmasságot, hogy kiválasszák az egyes alrendszerek megfelelő technológiáját.

Több technológiával is implementálhatja a szolgáltatásait. A következő lehetőségek közül választhat például a Service megvalósításához:

- Használjon olyan tároló-technológiát, mint a Docker kiszolgáló nélküli technológiával, például Azure Functions.
- A szolgáltatásait a Pásti-szolgáltatásokban üzemeltetheti, például az Azure App Services.

## <a name="technology-choices"></a>Technológiai lehetőségek

Ez a szakasz részletesen ismerteti a távoli figyelési megoldásban az egyes alapvető alrendszerek esetében végrehajtott technológiai döntéseket.

![Alapdiagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Felhőbeli átjáró

Az Azure IoT Hub a Cloud Gateway távoli figyelési megoldásként használható. [IoT hub](https://azure.microsoft.com/services/iot-hub/) biztonságos, kétirányú kommunikációt biztosít az eszközökkel.

A IoT-eszközök kapcsolatához a következőket használhatja:

- A [IoT hub eszköz SDK](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) -k natív ügyfélalkalmazás megvalósításához az eszközön. Az SDK-k a IoT Hub REST API és az újrapróbálkozások kezelésére szolgáló forgatókönyveket biztosítanak.
- Az Azure IoT Edge integrációja a tárolókban futó egyéni modulok üzembe helyezéséhez és kezeléséhez az eszközökön.
- A IoT Hub automatikus eszközkezelés integrációja a csatlakoztatott eszközök tömeges kezeléséhez.

### <a name="stream-processing"></a>Stream-feldolgozás

Az adatfolyam-feldolgozáshoz a távoli figyelési megoldás Azure Stream Analytics használ az összetett szabályok feldolgozásához. Ha egyszerűbb szabályokat szeretne használni, az egyszerű szabályok feldolgozását támogató egyéni webszolgáltatás van, bár ez a beállítás nem része a beépített telepítésnek. A hivatkozási architektúra az egyszerű szabályok feldolgozásához és az összetett szabályok feldolgozásához Azure Stream Analytics Azure Functions használatát javasolja.

### <a name="storage"></a>Storage

A tároláshoz a távoli figyelési megoldás gyorsító Azure Time Series Insights és Azure Cosmos DB is használ. A Azure Time Series Insights a csatlakoztatott eszközökről IoT Hubről érkező üzeneteket tárolja. A megoldás-gyorsító az összes többi tárterület, például a hűtőházi tárolás, a szabályok definíciói, a riasztások és a konfigurációs beállítások Azure Cosmos DB használja.

Azure Cosmos DB az ajánlott általános célú meleg tárolási megoldás a IoT-alkalmazásokhoz. Azonban a megoldások, például a Azure Time Series Insights és a Azure Data Lake számos felhasználási esethez megfelelőek. A Azure Time Series Insights segítségével mélyebb betekintést nyerhet az idősorozat-érzékelők adataiba a trendek és rendellenességek észlelése révén. Ez a funkció lehetővé teszi a kiváltó okok elemzését és a költséges állásidő elkerülését.

> [!NOTE]
> Time Series Insights jelenleg nem érhető el az Azure China Cloud-ban. Az Azure China Cloud-ban az új távoli figyelési megoldás-gyorsító üzembe helyezések minden tárterülethez Cosmos DB használnak.

### <a name="business-integration"></a>Üzleti integráció

A távoli figyelési megoldásban az üzleti integráció a riasztások előállítására korlátozódik, amelyek a meleg tárolóba kerülnek. A megoldás és a Azure Logic Apps összekapcsolásával mélyebb üzleti integrációs forgatókönyveket valósíthat meg.

### <a name="user-interface"></a>Felhasználói felület

A webes felhasználói felület a JavaScripttel reagál. A reakciós szolgáltatás egy gyakran használt iparági webes felhasználói felületi keretrendszert kínál, és hasonló más népszerű keretrendszerekhez, mint például a szögletes.

### <a name="runtime-and-orchestration"></a>Futtatókörnyezet és előkészítés

A távoli figyelési megoldás Docker-tárolókat használ az alrendszerek Kubernetes való futtatásához a Orchestrator a horizontális skálázáshoz. Ez az architektúra lehetővé teszi az egyes alrendszerek egyedi méretezési definícióit. Ez az architektúra azonban DevOps költségekkel jár, hogy a virtuális gépek és a tárolók naprakészek és biztonságosak legyenek.

A Docker alternatívái többek között a Azure App Service. A Kubernetes-megoldások közé tartoznak a Service Fabric, a DC/OS vagy a Swarm.

## <a name="next-steps"></a>További lépések

* A távoli figyelési megoldás üzembe helyezése [itt](https://www.azureiotsolutions.com/).
* Ismerkedjen meg a GitHub-kóddal a [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) és a [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/)nyelven.  
* További információ a IoT- [hivatkozás architektúráról](https://aka.ms/iotrefarchitecture).
