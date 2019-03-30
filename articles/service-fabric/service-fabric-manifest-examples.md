---
title: Az Azure Service Fabric tároló application manifest példák |} A Microsoft Docs
description: Ismerje meg, hogyan alkalmazás konfigurálása és a szolgáltatás a Service Fabric-alkalmazás jegyzékfájl beállításai.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 85a3066095cfc30da19b06d26f41bdc156f85832
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664692"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Service Fabric-alkalmazás és a service manifest példák
Ez a szakasz példákat alkalmazás és jegyzékek tartalmazza. Ezek a példák nem jelentenek megjelenítéséhez fontos forgatókönyvet lehetővé tesz, de az elérhető különböző beállításokat és azok használatát. 

A következő index látható funkciókat és a példa manifest(s) azok egy részét.

|Szolgáltatás|Jegyzék|
|---|---|
|[Erőforrások szabályozása](service-fabric-resource-governance.md)|[A Reliable Services-alkalmazás-jegyzékfájl](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [tároló Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Szolgáltatás futtatása egy helyi rendszergazdai fiók](service-fabric-application-runas-security.md)|[A Reliable Services-alkalmazás-jegyzékfájl](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Az összes szolgáltatás kódcsomagok egy alapértelmezett házirend alkalmazása](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[A Reliable Services-alkalmazás-jegyzékfájl](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Hozzon létre felhasználói és rendszerbiztonsági tagok](service-fabric-application-runas-security.md)|[A Reliable Services-alkalmazás-jegyzékfájl](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|adat-csomag szolgáltatáspéldányok közötti megosztása|[A Reliable Services-alkalmazás-jegyzékfájl](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[A Szolgáltatásvégpontok felülbírálása](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[A Reliable Services-alkalmazás-jegyzékfájl](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[A szolgáltatás indításakor parancsfájl futtatása](service-fabric-run-script-at-service-startup.md)|[VotingWeb szolgáltatásjegyzékben](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[HTTPS-végpont meghatározása](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[VotingWeb szolgáltatásjegyzékben](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Deklarálja a konfigurációs csomag](service-fabric-application-and-service-manifests.md)|[VotingData szolgáltatásjegyzékben](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Deklaráljon egy adat-csomag](service-fabric-application-and-service-manifests.md)|[VotingData szolgáltatásjegyzékben](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[A környezeti változók felülbírálása](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Tároló Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Port kontejneru-gazdagép-leképezés konfigurálása](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Tároló Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Tárolóregisztrációs adatbázis hitelesítésének konfigurálása](service-fabric-get-started-containers.md#configure-container-registry-authentication)|[Tároló Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Elszigetelési üzemmód beállítása](service-fabric-get-started-containers.md#configure-isolation-mode)|[Tároló Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[OS build-specifikus tárolórendszerképek megadása](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Tároló Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Környezeti változók beállítása](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Tároló FrontEndService Szolgáltatásjegyzék](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [tároló BackEndService szolgáltatásjegyzékben](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Végpont konfigurálása](service-fabric-get-started-containers.md#configure-communication)|[Tároló FrontEndService Szolgáltatásjegyzék](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [tároló BackEndService Szolgáltatásjegyzék](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [VotingData szolgáltatásjegyzékben](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|Adja át a parancsok a tárolóhoz|[Tároló FrontEndService szolgáltatásjegyzékben](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Tanúsítvány importálása egy tárolóba](service-fabric-securing-containers.md)|[Tároló FrontEndService szolgáltatásjegyzékben](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Kötet illesztőprogram konfigurálása](service-fabric-containers-volume-logging-drivers.md)|[Tároló BackEndService szolgáltatásjegyzékben](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

