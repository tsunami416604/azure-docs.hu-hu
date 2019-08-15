---
title: Az Azure Service Fabric Container Application manifest-példák | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja az alkalmazások és szolgáltatások jegyzékfájljának beállításait egy Service Fabric alkalmazáshoz.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 10419240f730a6b68f1161f158f7f903a98a9933
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035602"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Service Fabric alkalmazás-és szolgáltatás-jegyzékfájl – példák
Ez a szakasz az alkalmazás-és szolgáltatás-jegyzékfájlok példáit tartalmazza. Ezek a példák nem mutatják be a fontos forgatókönyveket, azonban a különböző elérhető beállításokat és azok használatát ismertetik. 

Az alábbiakban láthatók a megjelenített funkciók és a példaként szolgáló jegyzékfájl (ok) indexe.

|Funkció|Jegyzékfájl|
|---|---|
|[Erőforrások szabályozása](service-fabric-resource-governance.md)|[Reliable Services alkalmazás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [Container Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Szolgáltatás futtatása helyi rendszergazdai fiókkal](service-fabric-application-runas-security.md)|[Reliable Services alkalmazás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Alapértelmezett szabályzat alkalmazása az összes szolgáltatási kód csomagjaira](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Reliable Services alkalmazás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Felhasználói és csoporttagok létrehozása](service-fabric-application-runas-security.md)|[Reliable Services alkalmazás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|adatcsomag megosztása a szolgáltatási példányok között|[Reliable Services alkalmazás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Szolgáltatási végpontok felülbírálása](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Reliable Services alkalmazás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Parancsfájl futtatása a szolgáltatás indításakor](service-fabric-run-script-at-service-startup.md)|[VotingWeb szolgáltatás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[HTTPS-végpont definiálása](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[VotingWeb szolgáltatás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Konfigurációs csomag deklarálása](service-fabric-application-and-service-manifests.md)|[VotingData szolgáltatás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Adatcsomag deklarálása](service-fabric-application-and-service-manifests.md)|[VotingData szolgáltatás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Környezeti változók felülbírálása](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Container Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Tároló port – gazdagép társításának konfigurálása](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Container Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Tároló beállításjegyzék-hitelesítésének konfigurálása](service-fabric-get-started-containers.md#configure-container-registry-authentication)|[Container Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Elkülönítési mód beállítása](service-fabric-get-started-containers.md#configure-isolation-mode)|[Container Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[OPERÁCIÓSRENDSZER-Build-specifikus lemezképek meghatározása](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Container Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Környezeti változók beállítása](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Tároló FrontEndService szolgáltatás jegyzékfájlja](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [tároló BackEndService szolgáltatás jegyzékfájlja](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Végpont konfigurálása](service-fabric-get-started-containers.md#configure-communication)|[Tároló FrontEndService szolgáltatás jegyzékfájlja](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [tároló BackEndService szolgáltatás jegyzékfájlja](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [VotingData szolgáltatás jegyzékfájlja](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|parancsok továbbítása a tárolóhoz|[Tároló FrontEndService szolgáltatásának jegyzékfájlja](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Tanúsítvány importálása tárolóba](service-fabric-securing-containers.md)|[Tároló FrontEndService szolgáltatásának jegyzékfájlja](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[A kötet-illesztőprogram konfigurálása](service-fabric-containers-volume-logging-drivers.md)|[Tároló BackEndService szolgáltatásának jegyzékfájlja](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

