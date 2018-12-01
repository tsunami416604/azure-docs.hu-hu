---
title: Távoli Monitorozási megoldás architektúra lehetőségei – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti a használt architekturális és technikai választások távoli megfigyelés
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 20af014e5a59cb526d5b96e543b10d5b2b6d6937
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679596"
---
# <a name="remote-monitoring-architectural-choices"></a>Távoli Monitorozási architektúra lehetőségei

Az Azure IoT távoli figyelési megoldásgyorsító egy nyílt forráskódú, MIT licenccel rendelkező, megoldásgyorsító. Segítségével felgyorsíthatja az IoT-fejlesztési folyamatot, például általános IoT-forgatókönyveket mutatja:

- Eszközkapcsolatok
- Eszközfelügyelet
- Stream-feldolgozás

A távoli figyelési megoldás követi az ajánlott [Azure IoT-referenciaarchitektúra](https://aka.ms/iotrefarchitecture).

Ez a cikk ismerteti az egyes, a távoli figyelési alrendszerek fő architekturális és technikai lehetőségek. Azonban a műszaki választási lehetőségek a Microsoft végzett a távoli figyelési megoldás nem az egyetlen módszer a távoli figyelési IoT-megoldást valósíthat meg. Érdemes figyelembe veszi a gyakorlati megvalósításról kiindulópontként, amellyel egy sikeres alkalmazás, és módosítania kell, hogy:

- Az elérhető képességek fér, és a szervezet élmény.
- A függőleges alkalmazás igényeinek.

## <a name="architectural-choices"></a>Az architektúrával kapcsolatos választási lehetőségek

Az architektúra, amely a Microsoft azt javasolja, az IoT-alkalmazás felhőbeli natív, mikroszolgáltatások, és kiszolgáló nélküli alapján. Az IoT-alkalmazás, amely központilag telepíthető különálló szolgáltatásokat és a méretezési csoport különböző alrendszereket kialakítson egymástól függetlenül. Ezek az attribútumok meghaladó, nagyobb rugalmasságot biztosítanak a frissítése az egyes alrendszerek engedélyezése, és adjon meg egy megfelelő technológia az egyes alrendszerek kiválasztásához szükséges rugalmasságot.

Több technológiát használó mikroszolgáltatások valósítható meg. Például dönthet mikroszolgáltatások megvalósításához az alábbi lehetőségek közül választhat:

- A például a Docker tárolótechnológiát használja például az Azure Functions kiszolgáló nélküli technológia.
- A PaaS-szolgáltatások, például az Azure App Servicesben mikroszolgáltatásokat helyezhet üzemeltetéséhez.

## <a name="technology-choices"></a>Technológiai lehetőségek

Ez a szakasz részletesen a technológiai lehetőségekhez végzett minden, a core alrendszerek a távoli figyelési megoldás.

![Core-Diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Átjáró

Az Azure IoT Hub használata a távoli figyelési megoldás felhőátjárónak. [Az IoT Hub](https://azure.microsoft.com/services/iot-hub/) biztonságos, kétirányú kommunikációs kínál eszközöket.

IoT-eszköz kapcsolatot használhatja:

- A [az IoT Hub eszközoldali SDK-k](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-device-sdks) az eszköz natív ügyfélalkalmazás megvalósítása. Az SDK-k az IoT Hub – REST API körül burkolókat és a forgatókönyvek, például az újrapróbálkozások kezelésére.
- Az integráció az Azure IoT Edge üzembe helyezése és kezelése az eszközök tárolókban futó egyéni modulok.
- Az integráció az automatikus az Eszközfelügyelet az IoT hubhoz csatlakoztatott eszközök tömeges kezelése.

### <a name="stream-processing"></a>Stream-feldolgozás

Adatfolyam-feldolgozás, a távoli figyelési megoldást használ az Azure Stream Analytics összetett szabály feldolgozása. Egyszerűbb szabályok használni kívánt, van-e egyéni mikroszolgáltatások támogatásával, egyszerű szabály feldolgozása, bár a telepítés nem részei az-az-– azonnali üzembe helyezés. A referenciaarchitektúra azt javasolja, összetett szabály feldolgozása az Azure Functions az egyszerű szabály feldolgozása és az Azure Stream Analytics.

### <a name="storage"></a>Storage

Tárolás a távoli figyelési megoldásgyorsító használja az Azure Time Series Insights és az Azure Cosmos DB-hez. Az Azure Time Series Insights tárolja a csatlakoztatott eszközökről az IoT Hub beérkező üzeneteket. A megoldásgyorsító az Azure Cosmos DB az összes többi tárhelyen, például a ritka elérésű tárolási, a szabályok definíciók, a riasztások és a konfigurációs beállításokat.

Az Azure Cosmos DB az általános célú ajánlott meleg tárolási megoldás az IoT-alkalmazások is. Azonban megoldások, például az Azure Time Series Insights és az Azure Data Lake esetében számos használati esetek is megfelelő. Az Azure Time Series Insights mélyebb betekintést az idősorozat-kinyerhető trendek és rendellenességek is kaphat. Ez a funkció lehetővé teszi egyetlen elvégezheti az alapvető okok elemzését, és elkerülheti a költséges.

> [!NOTE]
> A Time Series Insights jelenleg nem áll rendelkezésre az Azure China felhőben. Új távoli figyelési megoldás gyorsító üzemelő példányok az Azure China felhőben Cosmos DB összes tárolót használja.

### <a name="business-integration"></a>Vállalati integráció

Integráció a távoli figyelési megoldásban a riasztásokat, amelyek kerüljenek a meleg tárolási generációja korlátozódik. A megoldás csatlakoztatása az Azure Logic Apps mélyebb vállalati integrációs forgatókönyvek megvalósításához.

### <a name="user-interface"></a>Felhasználói felület

A webes felhasználói felület a JavaScript React épül. React gyakran használt iparági webes Felhasználóifelület-keretrendszert biztosít, és egyéb népszerű keretrendszereket használó Angular hasonló.

### <a name="runtime-and-orchestration"></a>Futásidejű és vezényléshez

A távoli figyelési megoldás Docker-tárolók használatával futtassa az alrendszerek Kubernetes, az orchestrator, a horizontális skálázás. Ez az architektúra lehetővé teszi, hogy az egyes alrendszerek egyes méretezési definícióit. Azonban ez az architektúra költségekkel DevOps, hogy a virtuális gépek és tárolók naprakészen tartását és védelmét.

Docker, ilyen például a mikroszolgáltatások a PaaS-szolgáltatások, például az Azure App Service-ben üzemeltető. A Kubernetes alternatívái vezénylők, például a Service Fabric segítségével a DC/OS, vagy a Swarm.

## <a name="next-steps"></a>További lépések

* A távoli figyelési megoldás üzembe helyezése [Itt](https://www.azureiotsolutions.com/).
* Ismerje meg a GitHub code [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) és [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* További információ az IoT-Referenciaarchitektúra [Itt](https://aka.ms/iotrefarchitecture).
