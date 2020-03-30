---
title: Példák az Azure Service Fabric alkalmazásjegyzék-jegyzékbe
description: Ismerje meg, hogyan konfigurálhatja az alkalmazás- és szolgáltatásjegyzék-beállításokat egy Service Fabric-alkalmazáshoz.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6f44d2ddaac11545a3a3dc52fd20e02263c6ad67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451642"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>A Service Fabric-alkalmazás- és szolgáltatásjegyzék-példák
Ez a szakasz példákat tartalmaz az alkalmazás- és szolgáltatásjegyzékek. Ezek a példák nem fontos forgatókönyvek megjelenítésére szolgálnak, hanem a rendelkezésre álló különböző beállítások és azok használatának bemutatására. 

Az alábbiakban a megjelenített funkciók indexe látható, és a példajegyzék(ek)nek, amelyek nek a részét képezik.

|Szolgáltatás|Jegyzék|
|---|---|
|[Erőforrások szabályozása](service-fabric-resource-governance.md)|[Megbízható szolgáltatások alkalmazásjegyzéke](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [tárolóalkalmazás-jegyzékfájl](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Szolgáltatás futtatása helyi rendszergazdai fiókként](service-fabric-application-runas-security.md)|[Megbízható szolgáltatások alkalmazásjegyzéke](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Alapértelmezett házirend alkalmazása az összes szolgáltatáskódcsomagra](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Megbízható szolgáltatások alkalmazásjegyzéke](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Felhasználó- és csoportrendszerbiztonsági tagok létrehozása](service-fabric-application-runas-security.md)|[Megbízható szolgáltatások alkalmazásjegyzéke](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|adatcsomag megosztása szolgáltatáspéldányok között|[Megbízható szolgáltatások alkalmazásjegyzéke](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Szolgáltatásvégpontok felülbírálása](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Megbízható szolgáltatások alkalmazásjegyzéke](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Parancsfájl futtatása a szolgáltatás indításakor](service-fabric-run-script-at-service-startup.md)|[VotingWeb szolgáltatás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[HTTPS-végpont definiálása](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[VotingWeb szolgáltatás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Konfigurációs csomag deklarálása](service-fabric-application-and-service-manifests.md)|[VotingData szolgáltatás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Adatcsomag deklarálása](service-fabric-application-and-service-manifests.md)|[VotingData szolgáltatás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Környezeti változók felülbírálása](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Tárolóalkalmazás jegyzékfájlja](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Tárolóport-állomás hozzárendelés konfigurálása](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Tárolóalkalmazás jegyzékfájlja](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Tárolóregisztrációs adatbázis hitelesítésének konfigurálása](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Tárolóalkalmazás jegyzékfájlja](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Elkülönítési mód beállítása](service-fabric-get-started-containers.md#configure-isolation-mode)|[Tárolóalkalmazás jegyzékfájlja](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Operációs rendszer buildelés-specifikus tárolórendszerképeinek megadása](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Tárolóalkalmazás jegyzékfájlja](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Környezeti változók beállítása](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Container FrontEndService szolgáltatásjegyzék](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [Container BackEndService szolgáltatásjegyzék](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Végpont konfigurálása](service-fabric-get-started-containers.md#configure-communication)|[Container FrontEndService szolgáltatásjegyzék](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [Container BackEndService szolgáltatásjegyzék](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [VotingData szolgáltatás jegyzékfájl](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|parancsok átadják a tárolónak|[Container FrontEndService szolgáltatásjegyzék](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Tanúsítvány importálása tárolóba](service-fabric-securing-containers.md)|[Container FrontEndService szolgáltatásjegyzék](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Kötet-illesztőprogram konfigurálása](service-fabric-containers-volume-logging-drivers.md)|[Tároló BackEndService szolgáltatásjegyzék](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

