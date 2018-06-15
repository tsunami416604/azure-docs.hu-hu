---
title: Távoli figyelés megoldás architekturális lehetőségek - Azure |} Microsoft Docs
description: Ez a cikk ismerteti a használt architekturális és technikai lehetőségeket távoli figyelése
services: iot-suite
suite: iot-suite
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 192a763c01e60d816ae2046587176627fc9d8736
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787352"
---
# <a name="remote-monitoring-architectural-choices"></a>Távoli figyelés architekturális lehetőségek

Az Azure IoT távoli figyelése (RM) egy nyílt forráskódú, MIT licenccel rendelkező, megoldásgyorsító, amely bemutatja a közös IoT-forgatókönyvek esetén például az eszköz kapcsolatot, a kezelés és a adatfolyam feldolgozása, így az ügyfelek a fejlesztési folyamatokkal meggyorsíthatja a.  Erőforrás-kezelő a következő közzétett ajánlott Azure IoT-referenciaarchitektúra [Itt](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).  

Ez a cikk ismerteti a használt architekturális és technikai minden a alrendszereket számára választott RM, és figyelembe veendő alternatívák ismerteti.  Fontos megjegyezni, hogy a műszaki lehetőségeket erőforrás-kezelő nem az egyetlen lehetőség a távoli felügyeleti IoT-megoldás megvalósításához.  A technikai kivitelezéstől készítéséhez sikeres alkalmazásának az alapterv és a képességek felhasználói élmény és a felhasználói megoldás megvalósításának függőleges alkalmazások igényeihez megfelelően kell módosítani.

## <a name="architectural-choices"></a>Az architektúra lehetőségek

### <a name="microservices-serverless-and-cloud-native"></a>Mikroszolgáltatások létrehozására, a kiszolgáló nélküli, és a felhőalapú natív

Az IoT-alkalmazásokhoz felhő natív, mikroszolgáltatási, és kiszolgáló nélküli alapú javasoljuk az architektúra.  Az IoT-alkalmazások különböző alrendszereket kell kialakítani, amelyek egymástól függetlenül telepíthető, és lehetővé válik a egymástól függetlenül különálló szolgáltatásként.  Ezek az attribútumok nagyobb skálázási, nagyobb rugalmasság a frissítés egyes alrendszereket, engedélyezése, és kiválaszthatja a megfelelő technológia / alrendszer alapon rugalmasságot biztosítanak.  Mikroszolgáltatások több technológiákkal valósítható meg. Például használja a tároló technológia, például a Docker kiszolgáló nélküli technológiával, például az Azure Functions, vagy PaaS szolgáltatások, például az Azure App Service szolgáltatások mikroszolgáltatások létrehozására.

## <a name="core-subsystem-technology-choices"></a>Alapvető alrendszer technológia lehetőségek

Ez a szakasz részletesen technológia lehetőségeket RM az egyes a core alrendszerei.

![Core diagramja](media/iot-suite-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Átjáró
Az Azure IoT Hub lesz az erőforrás-kezelő Felhőátjáróhoz.  Az IoT Hub-eszközökkel biztonságos, kétirányú kommunikációs kínál. További tudnivalók az IoT-központ [Itt](https://azure.microsoft.com/services/iot-hub/). Az IoT-eszköz kapcsolat a .NET Core és a Java IoT Hub SDK-k használják.  Az SDK-k kínálnak burkolók körül újrapróbálkozási, például az IoT Hub REST API-t és a leíró forgatókönyvek 

### <a name="stream-processing"></a>Stream-feldolgozás
Az adatfolyam feldolgozása RM összetett szabály feldolgozása Azure Stream Analytics használ.  A felhasználó, aki a egyszerűbb szabályok esetén azt is támogatja az egyszerű szabályok feldolgozása egy egyéni mikroszolgáltatási bár ez nem része a mezőbe telepítési kívüli telepítés. A referencia-architektúrában azt javasolja, hogy az Azure Functions egyszerű szabály feldolgozása és használható Azure Stream Analytics (ASA) az összetett szabály feldolgozása.  

### <a name="storage"></a>Storage
A tároláshoz Cosmos DB szolgál az összes tárolási igényeinek megfelelően: cold tárolási, a meleg tároló, a szabályok tárolási és a riasztást. Végezzük jelenleg áthelyezése az Azure blob storage a referencia-architektúrában által javasolt módon.  Cosmos DB az IoT-alkalmazásokhoz ajánlott általános célú meleg tárolási megoldás, bár sok használati esetek megoldások Azure idő adatsorozat elemzések és az Azure Data Lake például megfelelőek.

### <a name="business-integration"></a>Üzleti integráció
Az erőforrás-kezelő üzleti integrációs riasztásokat, amelyek meleg tárolási kerülnek generációja korlátozódik. További üzleti integrációja a megoldás az Azure Logic Apps integrálásával hajtható végre.

### <a name="user-interface"></a>Felhasználói felület
A webes felhasználói felület a JavaScript reagálni épül.  Egy gyakran használt iparági webes felhasználói felület keretrendszert biztosít és egyéb népszerű keretrendszerek, például a Angular hasonló.  

### <a name="runtime-and-orchestration"></a>Futásidejű és vezénylési
Az alkalmazás futtatókörnyezete, az erőforrás-kezelő alrendszer implementációjában választott Docker tárolók Kubernetes (K8s), mint a horizontális skálázhatóságot az orchestrator.  Ez az architektúra lehetővé teszi, hogy az egyes méretezési definition / alrendszer azonban azt eredményezi azok háromszorosa frissítése virtuális gépek és a tárolók biztonsági szempontból a DevOps költségek.  A Docker & K8s ilyen például a PaaS szolgáltatások (például az Azure App Service) mikroszolgáltatások rendez, vagy az orchestrator használatával a Service Fabric, Vezénylőtípusú, Swarm, stb.

## <a name="next-steps"></a>További lépések
* Az erőforrás-kezelő megoldás üzembe helyezéséhez [Itt](https://www.azureiotsuite.com/).
* Megismerkedhet a Githubon code [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) és [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* További tudnivalók az IoT-Referenciaarchitektúra [Itt](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).
