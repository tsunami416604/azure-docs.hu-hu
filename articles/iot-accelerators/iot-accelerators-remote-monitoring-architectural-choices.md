---
title: Távoli Monitorozási megoldás architektúra lehetőségei – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti a használt architekturális és technikai választások távoli megfigyelés
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/12/2018
ms.topic: conceptual
ms.openlocfilehash: 709886e77819adca961a44f65fe6402dd7d20d53
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716299"
---
# <a name="remote-monitoring-architectural-choices"></a>Távoli Monitorozási architektúra lehetőségei

Az Azure IoT távoli figyelési megoldásgyorsító egy nyílt forráskódú, MIT-licenccel rendelkező, megoldásgyorsító, amelyben megjelenik az általános IoT-forgatókönyveket, például az eszköz csatlakoztatása, kezelés és adatfolyam-feldolgozás, így az ügyfelek felgyorsíthatja a fejlesztést a folyamat.  A távoli figyelési megoldás a következő közzétett ajánlott az Azure IoT-referenciaarchitektúra [Itt](https://aka.ms/iotrefarchitecture).  

Ez a cikk ismerteti a használt architekturális és technikai választási lehetőségeket, az egyes az alrendszerek a távoli figyelési megoldáshoz, és ismerteti az alternatív megoldások tekinthető.  Fontos megjegyezni, hogy a távoli figyelési megoldás műszaki választott ne legyenek-e az egyetlen módszer a távoli figyelési IoT-megoldást valósíthat meg.  A gyakorlati megvalósításról alaptervet a sikeres alkalmazást készít, és a képességek, a felhasználói élményt és a függőleges alkalmazás igényeinek megfelelően ügyfél megoldás megvalósításának módosítani kell.

## <a name="architectural-choices"></a>Az architektúrával kapcsolatos választási lehetőségek

### <a name="microservices-serverless-and-cloud-native"></a>Mikroszolgáltatások, kiszolgáló nélküli, és a felhőbeli natív

Az architektúra javasoljuk, hogy az IoT-alkalmazások felhőbeli natív, mikroszolgáltatások, és kiszolgáló nélküli alapján.  Az IoT-alkalmazás különböző alrendszereket, amelyek egymástól függetlenül üzembe helyezhető, és képes a független méretezését, különálló szolgáltatásként kell építeni.  Ezek az attribútumok meghaladó, nagyobb rugalmasságot biztosítanak a frissítése az egyes alrendszerek engedélyezze, és rugalmasan választhatja ki a megfelelő technológiát egy alrendszer történik meg.  A Mikroszolgáltatások több technológiáival valósítható meg. Például például a Docker tárolótechnológiát használja például az Azure Functions kiszolgáló nélküli technológia, vagy a PaaS-szolgáltatások, például az Azure App Servicesben mikroszolgáltatásokat üzemeltető.

## <a name="core-subsystem-technology-choices"></a>Core alrendszer technológiai lehetőségek

Ez a szakasz részletesen a technológiai lehetőségekhez végzett minden, a core alrendszerek a távoli figyelési megoldás.

![Core-Diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Átjáró
Az Azure IoT Hub használata a távoli figyelési megoldás felhőátjárónak.  Az IoT Hub-eszközök biztonságos, kétirányú kommunikációs kínál. További információk az IoT hubról [Itt](https://azure.microsoft.com/services/iot-hub/). Az IoT-eszköz kapcsolat a .NET Core és a Java IoT Hub SDK-k használják.  Az SDK-k az IoT Hub – REST API körül burkolókat és a forgatókönyvek, például az újrapróbálkozások kezelésére.

### <a name="stream-processing"></a>Stream-feldolgozás
A stream feldolgozása a távoli figyelési megoldást használ az Azure Stream Analytics összetett szabály feldolgozása.  Egyszerűbb szabályok képtalálatokat van egy egyéni mikroszolgáltatás támogatásával, az egyszerű szabályok feldolgozása Bár a telepítés nem része a box telepítési kívüli. A referenciaarchitektúra az Azure Functions egyszerű szabály feldolgozása és használható az Azure Stream Analytics (ASA) összetett szabály feldolgozási javasolja.  

### <a name="storage"></a>Storage
Tárolás a távoli figyelési megoldásgyorsító használja az Azure Time Series Insights és az Azure Cosmos DB-hez. Az Azure Time Series Insights tárolja a csatlakoztatott eszközökről az IoT Hub beérkező üzeneteket. A megoldásgyorsító az Azure Cosmos DB az összes többi tárhelyen, például a ritka elérésű tárolási, a szabályok definíciók, a riasztások és a konfigurációs beállításokat. Az Azure Cosmos DB az IoT-alkalmazások ajánlott általános célú meleg tárolási megoldás, bár a megoldások, például az Azure Time Series Insights és az Azure Data Lake esetében számos alkalmazási megfelelőek. Az Azure Time Series Insights által gyorsabban trendeket és rendellenességeket, amely lehetővé teszi, hogy egyetlen elvégezheti az alapvető okok elemzését, és elkerülheti a költséges mélyebb betekintést az idősorozat-is kaphat. 

> [!NOTE]
> A távoli figyelési megoldásgyorsító Azure Time Series Insights jelenleg előzetes verzióban és csak [régiók kiválasztása](https://azure.microsoft.com/global-infrastructure/services/). Ha telepíti a távoli figyelési megoldásgyorsító kívül ezekben a régiókban, Cosmos DB beállítás az alapértelmezett tároló.

### <a name="business-integration"></a>Vállalati integráció
Integráció a távoli figyelési megoldásban riasztásokat, amelyek kerüljenek a meleg tárolási generációja korlátozódik. További úgy, hogy integrálja a megoldás az Azure Logic Apps vállalati integrációja végezhető el.

### <a name="user-interface"></a>Felhasználói felület
A webes felhasználói felület a JavaScript React épül.  React gyakran használt iparági webes Felhasználóifelület-keretrendszert biztosít, és egyéb népszerű keretrendszereket használó Angular hasonló.  

### <a name="runtime-and-orchestration"></a>Futásidejű és vezényléshez
A kiválasztott alrendszer végrehajtása a távoli figyelési megoldásban az alkalmazás futtatókörnyezete Docker-tárolók Kubernetesszel való az orchestrator, a horizontális skálázás áll.  Ez az architektúra lehetővé teszi, hogy az egyes méretezési definíció szerint alrendszer azonban felmerülő fejlesztési és üzemeltetési költségek a amiatt, hogy virtuális gépek és tárolók naprakész biztonsági szempontból.  A Docker és a Kubernetes alternatívái üzemeltető PaaS-szolgáltatások (például az Azure App Service) a mikroszolgáltatások, vagy a Service Fabric, DCOS, Swarm, stb. használatával az orchestrator tartalmaznak.

## <a name="next-steps"></a>További lépések
* A távoli figyelési megoldás üzembe helyezése [Itt](https://www.azureiotsolutions.com/).
* Ismerje meg a GitHub code [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) és [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* További információ az IoT-Referenciaarchitektúra [Itt](https://aka.ms/iotrefarchitecture).
