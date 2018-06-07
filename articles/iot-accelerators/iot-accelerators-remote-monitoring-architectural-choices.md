---
title: Távoli figyelés megoldás architekturális lehetőségek - Azure |} Microsoft Docs
description: Ez a cikk ismerteti a használt architekturális és technikai lehetőségeket távoli figyelése
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/30/2018
ms.topic: conceptual
ms.openlocfilehash: 6c4bf0e4bf0a6c1a791cf762ec9bb44ed5c0b1bd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627688"
---
# <a name="remote-monitoring-architectural-choices"></a>Távoli figyelés architekturális lehetőségek

Az Azure IoT távoli megfigyelési megoldásgyorsító egy nyílt forráskódú, MIT licenccel rendelkező, amely bemutatja a közös IoT-forgatókönyvek esetén például az eszköz kapcsolatot, a kezelés és a adatfolyam feldolgozása, így az ügyfelek felgyorsíthatja a fejlesztési megoldásgyorsító a folyamatot.  A távoli figyelésére szolgáló megoldás a következő közzétett ajánlott Azure IoT-referenciaarchitektúra [Itt](https://aka.ms/iotrefarchitecture).  

A cikk az egyes, a távoli figyelésére szolgáló megoldás a alrendszerek használt architekturális és technikai lehetőségeket ismerteti, valamint figyelembe veendő alternatívák ismerteti.  Fontos megjegyezni, hogy a távoli figyelésére szolgáló megoldás műszaki lehetőségeket nem valósítja meg a távoli felügyeleti IoT-megoldás az egyetlen lehetőség.  A technikai kivitelezéstől készítéséhez sikeres alkalmazásának az alapterv és a képességek felhasználói élmény és a felhasználói megoldás megvalósításának függőleges alkalmazások igényeihez megfelelően kell módosítani.

## <a name="architectural-choices"></a>Az architektúra lehetőségek

### <a name="microservices-serverless-and-cloud-native"></a>Mikroszolgáltatások létrehozására, a kiszolgáló nélküli, és a felhőalapú natív

Az IoT-alkalmazásokhoz felhő natív, mikroszolgáltatási, és kiszolgáló nélküli alapú javasoljuk az architektúra.  Az IoT-alkalmazások különböző alrendszereket kell kialakítani, amelyek egymástól függetlenül telepíthető, és lehetővé válik a egymástól függetlenül különálló szolgáltatásként.  Ezek az attribútumok nagyobb skálázási, nagyobb rugalmasság a frissítés egyes alrendszereket, engedélyezése, és kiválaszthatja a megfelelő technológia / alrendszer alapon rugalmasságot biztosítanak.  Mikroszolgáltatások több technológiákkal valósítható meg. Például használja a tároló technológia, például a Docker kiszolgáló nélküli technológiával, például az Azure Functions, vagy PaaS szolgáltatások, például az Azure App Service szolgáltatások mikroszolgáltatások létrehozására.

## <a name="core-subsystem-technology-choices"></a>Alapvető alrendszer technológia lehetőségek

Ez a szakasz részletesen technológia lehetőségeket a távoli figyelésére szolgáló megoldás az egyes a core alrendszerei.

![Core diagramja](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Átjáró
Az Azure IoT Hub lesz a távoli figyelési megoldást felhőátjáróhoz.  Az IoT Hub-eszközökkel biztonságos, kétirányú kommunikációs kínál. További tudnivalók az IoT-központ [Itt](https://azure.microsoft.com/services/iot-hub/). Az IoT-eszköz kapcsolat a .NET Core és a Java IoT Hub SDK-k használják.  Az SDK-k burkolók körül az IoT Hub REST API-t és a forgatókönyvek használhatók, mint az újrapróbálkozások kezelni.

### <a name="stream-processing"></a>Stream-feldolgozás
Az adatfolyam feldolgozása a távoli figyelésére szolgáló megoldás használja Azure Stream Analytics összetett szabály feldolgozása.  A felhasználó, aki a egyszerűbb szabályok esetén azt is támogatja az egyszerű szabályok feldolgozása egy egyéni mikroszolgáltatási bár ez nem része a mezőbe telepítési kívüli telepítés. A referencia-architektúrában azt javasolja, hogy az Azure Functions egyszerű szabály feldolgozása és használható Azure Stream Analytics (ASA) az összetett szabály feldolgozása.  

### <a name="storage"></a>Storage
Tárolási, az Azure Cosmos DB használt összes tárolási igényeinek: cold tárolási, a meleg tároló, a szabályok tárolási és a riasztást. Végezzük jelenleg áthelyezése az Azure blob storage a referencia-architektúrában által javasolt módon.  Azure Cosmos DB az IoT-alkalmazásokhoz ajánlott általános célú meleg tárolási megoldás, bár sok használati esetek megoldások Azure idő adatsorozat elemzések és az Azure Data Lake például megfelelőek.

### <a name="business-integration"></a>Üzleti integráció
A távoli figyelésére szolgáló megoldás az üzleti integrációs riasztásokat, amelyek meleg tárolási kerülnek generációja korlátozódik. További üzleti integrációja a megoldás az Azure Logic Apps integrálásával hajtható végre.

### <a name="user-interface"></a>Felhasználói felület
A webes felhasználói felület a JavaScript reagálni épül.  Egy gyakran használt iparági webes felhasználói felület keretrendszert biztosít és egyéb népszerű keretrendszerek, például a Angular hasonló.  

### <a name="runtime-and-orchestration"></a>Futásidejű és vezénylési
Az alkalmazás futtatókörnyezete, az alrendszer végrehajtása a távoli figyelésére szolgáló megoldás a választott Kubernetes Docker tárolók, mint a horizontális skálázhatóságot az orchestrator.  Ez az architektúra lehetővé teszi, hogy az egyes méretezési definition / alrendszer azonban azt eredményezi azok háromszorosa frissítése virtuális gépek és a tárolók biztonsági szempontból a DevOps költségek.  Docker és Kubernetes ilyen például a PaaS szolgáltatások (például az Azure App Service) mikroszolgáltatások rendez, vagy az orchestrator használatával a Service Fabric, Vezénylőtípusú, Swarm, stb.

## <a name="next-steps"></a>További lépések
* A távoli figyelésére szolgáló megoldás telepítése [Itt](https://www.azureiotsolutions.com/).
* Megismerkedhet a Githubon code [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) és [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* További tudnivalók az IoT-Referenciaarchitektúra [Itt](https://aka.ms/iotrefarchitecture).
